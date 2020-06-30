# Containers

## Define command argument container

When you override the default Entrypoint and Cmd, these rules apply:

If you do not supply command or args for a Container, the defaults defined in the Docker image are used.

If you supply a command but no args for a Container, only the supplied command is used. **The default EntryPoint and the default Cmd defined in the Docker image are ignored**.

If you supply only args for a Container, the default Entrypoint defined in the Docker image is run with the args that you supplied.

If you supply a command and args, **the default Entrypoint and the default Cmd defined in the Docker image are ignored**. Your command is run with your args.
