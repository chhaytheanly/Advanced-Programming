# Programming for Data Science

Programming for Data Science is a Go-based project for learning and applying
the algorithms and engineering practices used to build reliable data-structure
software. The project will grow from small, testable implementations into a
containerized service that can be built locally and deployed to a remote Linux
host with Podman and SSH.

## Goals

- Implement fundamental data structures and algorithms in Go.
- Practice clean APIs, error handling, complexity analysis, and testing.
- Keep each implementation easy to benchmark, review, and extend.
- Package the project as a reproducible OCI container image.
- Deploy and operate the application on a remote machine through SSH and
  Podman.

## Planned topics

The exact exercises may evolve, but the project is intended to cover:

- Arrays, slices, linked lists, stacks, and queues
- Hash tables, sets, and collision handling
- Trees, binary search trees, heaps, and priority queues
- Graph representations and traversal algorithms
- Searching, sorting, recursion, and dynamic programming
- Time and space complexity, benchmarks, and practical trade-offs

## Technology stack

- **Go** for implementation, tests, and benchmarks
- **Go modules** for dependency management
- **Podman** for building and running OCI containers
- **SSH** for secure remote deployment and administration
- **Git** for source control and collaboration

## Repository layout

The layout will be expanded as the project develops. A possible structure is:

```text
.
├── cmd/                 # Executable entry points
├── internal/             # Application-specific packages
├── pkg/                  # Reusable data-structure packages
├── tests/                # Integration and end-to-end tests
├── Containerfile         # Podman/OCI image definition
├── go.mod
└── README.md
```

## Getting started

Install Go and Podman using the instructions for your operating system. Then
clone the repository and run the standard Go checks:

```bash
git clone <repository-url>
cd advanced-programming

go test ./...
go vet ./...
go run ./...
```

If the project exposes a server or command-line application, its package
entry point and configuration options will be documented here as they are
added.

## Building with Podman

The project will use a `Containerfile` so that local and remote builds use the
same instructions. Once that file is available, build and run the image with:

```bash
podman build -t programming-for-data-structures:latest .
podman run --rm -p 8080:8080 \
  programming-for-data-structures:latest
```

Choose a non-conflicting host port if port `8080` is already in use. Avoid
putting secrets in the image; provide runtime configuration through environment
variables or a protected secrets mechanism.

## Deployment with SSH and Podman

Deployment is intended to use a remote Linux host where Podman is installed
and the deploy user has permission to run containers. A typical workflow is:

1. Build and test the Go project locally.
2. Build and tag the container image with an immutable version, such as a Git
   commit SHA.
3. Push the image to a container registry, or transfer it to the server.
4. Connect to the server using SSH keys.
5. Pull the new image, stop the previous container, and start the new version.
6. Verify the service and retain a rollback tag.

Example remote commands:

```bash
ssh deploy@<server> 'podman pull <registry>/<image>:<tag>'
ssh deploy@<server> 'podman stop programming-for-data-structures || true'
ssh deploy@<server> 'podman rm programming-for-data-structures || true'
ssh deploy@<server> \
  'podman run -d --name programming-for-data-structures \
    --restart=always -p 8080:8080 \
    <registry>/<image>:<tag>'
ssh deploy@<server> \
  'podman ps --filter name=programming-for-data-structures'
```

Replace placeholders with project-specific values. In production, prefer a
systemd/Quadlet unit or another explicitly managed service definition instead
of relying only on a one-off shell command. Configure firewall rules, logging,
health checks, and TLS according to the deployment environment.

## Development workflow

Before opening a change:

```bash
gofmt -w .
go test ./...
go vet ./...
go test -bench=. ./...
```

New data structures should include:

- A clear package-level API and documented invariants
- Tests for normal, empty, duplicate, boundary, and invalid-input cases
- Complexity notes for important operations
- Benchmarks where performance is part of the exercise

## Configuration and security

- Do not commit private keys, tokens, passwords, or production configuration.
- Use SSH keys and a restricted deployment account.
- Pin image versions in deployment environments rather than using `latest`.
- Run containers as a non-root user when practical.
- Keep the host, Podman, Go dependencies, and base images updated.

## License

No license has been selected for this repository yet. Add a license before
redistributing the project or accepting external contributions.
