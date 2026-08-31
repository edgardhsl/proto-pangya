# Security policy

`proto-pangya` parses untrusted binary network input. Memory safety, resource safety and controlled failure are core project requirements.

## Reporting a vulnerability

Please avoid opening a public issue for a vulnerability that could enable denial of service, unbounded resource consumption, sensitive-data exposure or other practical exploitation.

Prefer GitHub's private vulnerability reporting / Security Advisory flow when it is available for this repository. If that option is unavailable, contact the repository maintainer privately through the contact information available on the maintainer's GitHub profile.

Include enough information to reproduce the problem, such as the affected revision, minimal input or fixture, expected behavior and observed impact. Do not include unnecessary credentials or private packet captures.

## Security expectations

Network-controlled input must not cause panic, out-of-bounds access, uncontrolled allocation, unbounded decompression, infinite loops, deadlocks, hidden unbounded queues or unbounded goroutine creation.

Verbose logging and evidence capture must not automatically expose known secrets or credentials.

## Supported versions

The project is currently in pre-v1 development. Until tagged releases exist, security fixes target the latest `main` branch unless documented otherwise.
