#!/usr/bin/env bash
set -Eeuo pipefail

SCRIPT_URL="${SCRIPT_URL:-https://raw.githubusercontent.com/ceocok/c.cococ/refs/heads/main/easytier.sh}"
INSTALL_DIR="${INSTALL_DIR:-/usr/local/sbin}"
BIN_DIR="${BIN_DIR:-/usr/local/bin}"
SCRIPT_PATH="${SCRIPT_PATH:-$INSTALL_DIR/easytier.sh}"

log() {
  printf '[install-et] %s\n' "$*"
}

die() {
  printf '[install-et] ERROR: %s\n' "$*" >&2
  exit 1
}

need_root() {
  if [ "${EUID:-$(id -u)}" -ne 0 ]; then
    die "please run as root, for example: sudo bash $0"
  fi
}

detect_os() {
  case "$(uname -s 2>/dev/null || true)" in
    Linux) OS_NAME="linux" ;;
    Darwin) OS_NAME="macos" ;;
    *) OS_NAME="unknown" ;;
  esac
}

download_file() {
  local url="$1"
  local target="$2"

  if command -v curl >/dev/null 2>&1; then
    curl -fsSL "$url" -o "$target"
  elif command -v wget >/dev/null 2>&1; then
    wget -qO "$target" "$url"
  else
    die "curl or wget is required"
  fi
}

ensure_shell_profile_alias() {
  local user_name user_home profile alias_line

  user_name="${SUDO_USER:-}"
  if [ -z "$user_name" ] || [ "$user_name" = "root" ]; then
    user_home="${HOME:-/root}"
  else
    user_home="$(eval "printf '%s' ~$user_name")"
  fi

  [ -d "$user_home" ] || return 0

  if [ -f "$user_home/.zshrc" ]; then
    profile="$user_home/.zshrc"
  else
    profile="$user_home/.bashrc"
  fi

  alias_line="alias et='sudo $BIN_DIR/et'"

  if [ "$(id -u)" -eq 0 ] && [ "$user_home" != "/root" ]; then
    if ! grep -Fq "$alias_line" "$profile" 2>/dev/null; then
      printf '\n%s\n' "$alias_line" >> "$profile"
      chown "$user_name" "$profile" 2>/dev/null || true
      log "added user alias to $profile"
    fi
  fi
}

main() {
  need_root
  detect_os

  log "detected system: $OS_NAME"
  log "installing EasyTier menu script from:"
  log "$SCRIPT_URL"

  mkdir -p "$INSTALL_DIR" "$BIN_DIR"

  local tmp_file
  tmp_file="$(mktemp)"
  trap 'rm -f "$tmp_file"' EXIT

  download_file "$SCRIPT_URL" "$tmp_file"

  if [ ! -s "$tmp_file" ]; then
    die "downloaded script is empty"
  fi

  sed -i.bak 's/\r$//' "$tmp_file" 2>/dev/null || sed -i 's/\r$//' "$tmp_file"
  rm -f "$tmp_file.bak"

  install -m 0755 "$tmp_file" "$SCRIPT_PATH"
  ln -sfn "$SCRIPT_PATH" "$BIN_DIR/et"
  ln -sfn "$SCRIPT_PATH" "$BIN_DIR/ET"

  ensure_shell_profile_alias

  log "installed script: $SCRIPT_PATH"
  log "created shortcut: $BIN_DIR/et"
  log "created shortcut: $BIN_DIR/ET"
  log "verification:"
  ls -l "$SCRIPT_PATH" "$BIN_DIR/et" "$BIN_DIR/ET"

  printf '\n'
  log "done. Try one of these:"
  printf '  et\n'
  printf '  sudo et\n'
  printf '  sudo %s/et\n' "$BIN_DIR"
}

main "$@"
