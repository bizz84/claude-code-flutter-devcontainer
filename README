
Dev container for running Claude code safely with the `--dangerously-skip-permissions` flag.

This is essentially identical to the official devcontainer in the [official Claude Code repo](https://github.com/anthropics/claude-code/tree/main/.devcontainer), but with some additions for Flutter app development.

## Flutter container setup

The Dockerfile has been updated to install Flutter when building the container:

```dockerfile
# Install Flutter (system-wide)
RUN git clone https://github.com/flutter/flutter.git -b stable /opt/flutter
ENV PATH="/opt/flutter/bin:$PATH"

# Pre-download Flutter dependencies
RUN flutter precache
```

## Useful aliases

To make development faster, I have defined a bunch of useful aliases inside a file called `.zshrc_dev`:

```zsh
# Aliases for Flutter commands
alias fclean="flutter clean"
alias fpg="flutter pub get"
alias fpu="flutter pub upgrade"

alias brb="dart run build_runner build -d"
alias brw="dart run build_runner watch -d"

alias fpgbrb="fpg && brb"
alias fpgbrw="fpg && brw"

# Aliases for Claude Code
alias c-dsp='claude --dangerously-skip-permissions'

# Custom prompt or other configurations
echo "🚀 Flutter Dev Environment Ready!"
```

To use these aliases in the container, copy the file to your home directory:

```bash
cp .zshrc_dev ~/.zshrc_dev
```

Then, rebuild and reopen the container, and the file will be loaded automatically.

### [LICENSE: MIT](LICENSE.md)