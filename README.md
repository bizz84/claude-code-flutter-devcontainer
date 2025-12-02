Dev container for running Claude code safely with the `--dangerously-skip-permissions` flag.

This is essentially identical to the official devcontainer in the [official Claude Code repo](https://github.com/anthropics/claude-code/tree/main/.devcontainer), but with some additions for Flutter app development.

## Flutter container setup

The Dockerfile has been updated to install Flutter when building the container:

```dockerfile
# Generate locale
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# Install Flutter (system-wide)
RUN git clone https://github.com/flutter/flutter.git -b stable /opt/flutter && \
    chown -R node:node /opt/flutter
ENV PATH="/opt/flutter/bin:$PATH"

# Pre-download Flutter dependencies (as node user since they own /opt/flutter)
USER node
RUN flutter precache
USER root
```

### Running Flutter commands

If you run `flutter doctor`, you should see something like this:

```zsh
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, 3.38.3, on Debian GNU/Linux 12 (bookworm) 6.10.14-linuxkit, locale en_US.UTF-8)
[✗] Android toolchain - develop for Android devices
    ✗ Unable to locate Android SDK.
      Install Android Studio from: https://developer.android.com/studio/index.html
      On first launch it will assist you in installing the Android SDK components.
      (or visit https://flutter.dev/to/linux-android-setup for detailed instructions).
      If the Android SDK has been installed to a custom location, please use
      `flutter config --android-sdk` to update to that location.

[✗] Chrome - develop for the web (Cannot find Chrome executable at google-chrome)
    ! Cannot find Chrome. Try setting CHROME_EXECUTABLE to a Chrome executable.
[✗] Linux toolchain - develop for Linux desktop
    ✗ clang++ is required for Linux development.
      It is likely available from your distribution (e.g.: apt install clang), or can be downloaded from https://releases.llvm.org/
    ✗ CMake is required for Linux development.
      It is likely available from your distribution (e.g.: apt install cmake), or can be downloaded from https://cmake.org/download/
    ✗ ninja is required for Linux development.
      It is likely available from your distribution (e.g.: apt install ninja-build), or can be downloaded from
      https://github.com/ninja-build/ninja/releases
    ✗ GTK 3.0 development libraries are required for Linux development.
      They are likely available from your distribution (e.g.: apt install libgtk-3-dev)
    ! Unable to access driver information using 'eglinfo'.
      It is likely available from your distribution (e.g.: apt install mesa-utils)
[✓] Connected device (1 available)
[✓] Network resources
```

This means that with this setup, you can run `flutter` sub-commands such as `analyze`, `test`, `pub` directly from the terminal or by Claude Code.

But you **can't** use `flutter run` to run the app on any platform, since the Dockerfile doesn't install any platform-specific tools like Xcode or Android Studio.

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
