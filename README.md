# Classroom Network Management System

A client-server classroom management system implemented in C for a computer
networks university project. The server accepts TCP connections from students
and professors, exposes a separate UDP administration interface, stores users
and classes in POSIX shared memory, and uses UDP multicast to distribute
messages to class subscribers.

## Academic context

This repository contains a bachelor's university project for **[COURSE]** at
**[UNIVERSITY]**, completed in **[YEAR]**.

## Main features

- TCP client connections for student and professor accounts.
- Login against a semicolon-separated configuration file.
- Student operations:
  - List available classes.
  - Subscribe to a class, subject to its capacity.
  - List subscribed classes and their multicast addresses.
- Professor operations:
  - Create classes with a specified capacity.
  - Send messages to a class through UDP multicast.
- UDP administration operations:
  - Authenticate an administrator.
  - Add and remove users.
  - List configured users and roles.
  - Request server shutdown.
- Shared state for users and classes using POSIX shared memory.
- A [GNS3 topology](./ProjetoRC.gns3) for the network simulation.

## Technologies

- C
- POSIX sockets: TCP, UDP, and UDP multicast
- POSIX threads, processes, semaphores, and shared memory
- IPv4 networking
- GNS3 topology configuration

No compiler, library, or language versions are explicitly specified in the
repository.

## Architecture and project structure

The server starts a TCP listener for student/professor clients and a UDP
listener for administrators. User and class state is kept in a shared memory
object. A professor's class messages are sent to the class multicast address,
which subscribed clients join after receiving the subscription response.

```text
.
├── cliente_final.c       # TCP client and multicast receiver
├── serverFinal.c         # TCP/UDP server and shared-memory lifecycle
├── comandos_cliente.c    # Student/professor class commands
├── comandos_admin.c      # Administrator commands and user persistence
├── dados.h               # Shared data structures and function declarations
├── file.txt              # Initial user configuration
├── ProjetoRC.gns3        # GNS3 network topology
└── RCMetaFinal.pdf       # Project report/documentation
```

## Getting started

### Prerequisites

The source uses POSIX APIs such as `fork`, `pthread`, `shm_open`, semaphores,
and Unix socket headers. A POSIX-compatible C environment is therefore
required. The repository does not specify a supported operating system,
compiler version, or dependency manager.

### Build

No Makefile, CMake configuration, or other build script is included. The
following commands are **inferred from the source files and have not been
verified by running them**:

```sh
gcc serverFinal.c comandos_admin.c comandos_cliente.c -o class_server -pthread
gcc cliente_final.c -o class_client -pthread
```

Depending on the platform and toolchain, additional system libraries or
compiler options may be required.

### Configuration

The server expects a configuration-file path as its third argument. Each line
of the file is parsed in this format:

```text
<username>;<password>;<role>
```

Supported roles used by the implementation are `administrador`, `aluno`, and
`professor`. Use placeholders or locally created credentials; do not publish
real passwords.

### Run

The server entry point requires two different ports:

```text
./class_server <tcp_class_port> <udp_admin_port> <config_file>
```

For example, using placeholders:

```text
./class_server <TCP_PORT> <UDP_PORT> file.txt
```

The source validates both ports as values from 1024 through 65535 and rejects
equal port numbers.

The TCP client entry point is:

```text
./class_client <server_host> <tcp_class_port>
```

No administrator client program or scripted launch configuration is included,
so the exact command or tool used to send UDP administration datagrams must be
selected separately.

## Usage

After connecting with the TCP client, authenticate with:

```text
LOGIN <username> <password>
```

Student commands:

```text
LIST_CLASSES
LIST_SUBSCRIBED
SUBSCRIBE_CLASS <class_name>
```

Professor commands:

```text
LIST_CLASSES
CREATE_CLASS <class_name> <capacity>
SEND <class_name> <message>
```

The UDP administration protocol supports these commands after administrator
login:

```text
LOGIN <username> <password>
ADD_USER <username> <password> <administrador|aluno|professor>
DEL <username>
LIST
QUIT_SERVER
```

Class creation assigns multicast addresses in the `239.0.0.x` range. The
client includes multicast-receiving logic for subscribed classes, although the
current client/server success-message formats do not match, so this flow should
be verified and corrected before relying on it.

## Testing

No automated tests, test framework configuration, or CI workflow was found in
the repository. The system should therefore be tested manually with a running
server, one or more TCP clients, and an appropriate UDP client for the
administration interface.

## My contribution

[DESCRIBE YOUR CONTRIBUTION]

## Limitations and potential improvements

### Observed limitations

- There is no documented build system, dependency specification, or automated
  test suite.
- Credentials are read from and written to a plain-text configuration file.
- The implementation relies on POSIX APIs and is not documented as portable to
  Windows.
- The client starts its multicast receiver only when a response begins with
  `ACCEPTED`, while the server's subscription handler currently returns a
  different success message.
- Several runtime settings, including shared-memory and semaphore names,
  multicast ranges, and buffer limits, are hard-coded.
- The repository does not include an administrator client or a complete
  operator guide.

### Potential improvements

- Add a reproducible build configuration and automated protocol tests.
- Replace plain-text credential storage with a properly designed authentication
  and password-storage mechanism.
- Add explicit validation and bounds checks for all command fields and class
  capacities.
- Improve connection lifecycle handling, synchronization, and cleanup for
  concurrent clients.
- Provide a dedicated administration client and document the GNS3 lab setup.

## Authors and acknowledgements

- Authors: [ADD AUTHORS]
- Academic supervision or acknowledgements: [ADD ACKNOWLEDGEMENTS]
