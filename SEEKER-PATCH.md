# Seeker REALITY TLS extension

Base: upstream rustls v/0.23.35, commit 7768cd2b44049e040685d48318d13bfa7f7d32a8.

This fork keeps two small extension points used by Seeker's native REALITY
transport. It does not implement REALITY authentication inside rustls.

- `ClientConfig::client_hello_customizer`: optional `ClientHelloCustomizer`
  callback before ClientHello transcript hashing and transmission. Defaults to
  `None`, preserving normal TLS behavior. Customized bytes are parsed, the echoed
  session ID and sent extension list are synchronized, and the exact encoded
  bytes are retained for both the transcript and the wire.
- `ActiveKeyExchange::as_any`: optional access to a custom key exchange object;
  defaults to `None`. Seeker uses it to authenticate with the same ephemeral
  X25519 key that is used by the TLS key exchange.

The customizer requires a fresh handshake: no resumption, early data, ECH or
HelloRetryRequest. Callers must preserve the random, key shares and supported
algorithms, and provide their own protocol-specific certificate verifier.

Seeker contains the REALITY authentication, X25519MLKEM768 exchange and
certificate checks, plus tests for the modified transcript and session ID,
authenticated client/server handshakes, rejected cover certificates, malformed
configurations and hybrid key exchange. Its Cargo dependency pins this fork to
an exact Git commit; branch updates do not automatically change deployed code.

When updating upstream, retain these extension points and rerun Seeker's TLS,
REALITY, Vision and WebSocket tests. The upstream licenses are retained.
