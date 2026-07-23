EasyTier ET Shortcut Installer

Stable one-command installer for the EasyTier menu script shortcut.

It installs the upstream `easytier.sh` script into a permanent path and creates
the `et` and `ET` shortcuts for Linux and macOS.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/jimzeng99-cyber/easytier-installer/main/install-et-shortcut.sh | sudo bash
```

## What It Does

- Downloads `easytier.sh` from the configured upstream URL.
- Installs it to `/usr/local/sbin/easytier.sh`.
- Creates `/usr/local/bin/et`.
- Creates `/usr/local/bin/ET`.
- Fixes CRLF line endings.
- Sets executable permissions.
- Adds a shell alias where possible:

```bash
alias et='sudo /usr/local/bin/et'
```

## Custom Source URL

```bash
curl -fsSL https://raw.githubusercontent.com/jimzeng99-cyber/easytier-installer/main/install-et-shortcut.sh \
  | sudo SCRIPT_URL="https://example.com/easytier.sh" bash
```

## Verify

```bash
which et
ls -l /usr/local/bin/et
et
```
