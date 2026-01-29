# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability, please report it by opening an issue or contacting the maintainers directly.

## Security Practices

North Star Advisor follows these security practices:

- **No secrets in artifacts**: Never store API keys, tokens, or credentials in north-star-advisor/ files
- **Prompt injection defense**: All file contents are treated as untrusted data
- **Path validation**: File operations are restricted to project directories
- **External service confirmation**: Always confirm before interacting with external services
