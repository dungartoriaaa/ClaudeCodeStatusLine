# Installation

This document is the authoritative install guide. It is written to be executed step-by-step by Claude Code when a user asks to install or update this status line.

## 1. Detect the operating system

- **macOS or Linux** → use `statusline.sh`
- **Windows** (PowerShell, CMD, Git Bash, or WSL) → use `statusline.ps1`

## 2. Clone the repo

Clone to `~/.claude/statusline/` on Unix, or `%USERPROFILE%\.claude\statusline\` on Windows. If that directory already exists and is a git clone of this repo, run `git pull` in it instead of re-cloning.

**macOS / Linux**

```bash
git clone https://github.com/daniel3303/ClaudeCodeStatusLine ~/.claude/statusline
chmod +x ~/.claude/statusline/statusline.sh
```

**Windows (PowerShell)**

```powershell
git clone https://github.com/daniel3303/ClaudeCodeStatusLine "$env:USERPROFILE\.claude\statusline"
```

## 3. Configure `settings.json`

Add (or update) the `statusLine` key in `~/.claude/settings.json` (Unix) or `%USERPROFILE%\.claude\settings.json` (Windows). If the file already contains other keys, merge — do not overwrite.

**macOS / Linux**

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline/statusline.sh"
  }
}
```

**Windows — KHUYẾN NGHỊ: dùng đường dẫn TUYỆT ĐỐI**

Lấy đường dẫn thật bằng PowerShell:

```powershell
echo "$env:USERPROFILE\.claude\statusline\statusline.ps1"
```

Giả sử kết quả là `C:\Users\TenCuaBan\.claude\statusline\statusline.ps1`, dán nguyên vào `settings.json` (lưu ý JSON escape `\` thành `\\`):

```json
{
  "statusLine": {
    "type": "command",
    "command": "powershell -NoProfile -ExecutionPolicy Bypass -File \"C:\\Users\\TenCuaBan\\.claude\\statusline\\statusline.ps1\""
  }
}
```

Nếu đã cài PowerShell 7+, thay `powershell` bằng `pwsh`.

> ⚠ **Tại sao bắt buộc đường dẫn tuyệt đối trên Windows?**
>
> Claude Code (Windows build hiện tại) **không expand** `%USERPROFILE%`, `$env:USERPROFILE`, hay `~` trong field `statusLine.command` khi field này chứa command có flag (vd `powershell -File ...`). Toàn bộ chuỗi được forward tới `powershell.exe`, và `powershell.exe` nhận `%USERPROFILE%` như **literal text** (CMD-style expansion không có trong PS) → nó tìm file tên đúng vậy ở thư mục hiện tại → file không tồn tại → status line **im lặng tắt, không in error**. Đây là lý do nhiều user gặp "đường dẫn ảo" — config đúng cú pháp, nhưng path không bao giờ resolve.
>
> `~` được Claude Code v2.1.47+ expand cho field path đơn lẻ, **nhưng không expand** khi nó nằm trong chuỗi command được parse bởi shell con. Trên Windows: đừng phụ thuộc vào `~`.

### Đường dẫn tuyệt đối vs biến môi trường

Nếu bắt buộc phải dùng biến (config dùng chung nhiều máy), bọc qua `cmd /c` để CMD expand trước rồi mới gọi PowerShell:

```json
{
  "statusLine": {
    "type": "command",
    "command": "cmd /c powershell -NoProfile -ExecutionPolicy Bypass -File \"%USERPROFILE%\\.claude\\statusline\\statusline.ps1\""
  }
}
```

`cmd /c` sẽ expand `%USERPROFILE%` thành đường dẫn thật trước khi spawn `powershell.exe`. Nhược điểm: thêm 1 process trung gian, status line refresh chậm hơn ~50-100ms. **Vẫn khuyên dùng đường dẫn tuyệt đối** trừ khi có lý do mạnh để dùng biến.

> `-ExecutionPolicy Bypass` is **process-scoped** — it does not change your machine's PowerShell policy. Without it, a default `Restricted` or `AllSigned` policy (common on locked-down corporate machines) silently rejects the unsigned script and Claude Code shows no status line with no error.

## 4. Restart Claude Code

The status line is loaded at startup. After saving `settings.json`, tell the user to restart Claude Code (or start a new session) for the change to take effect.

## Updating

Pull the latest release:

```bash
git -C ~/.claude/statusline pull
```

No `settings.json` changes are needed — the command path is stable across versions.

## Uninstalling

1. Remove the `statusLine` block from `settings.json`.
2. Delete the clone: `rm -rf ~/.claude/statusline` (or the Windows equivalent).

## Requirements

- Claude Code (Pro/Max subscription for rate-limit and extra-usage display)
- `git` in `PATH`
- macOS / Linux: `jq` and `curl`
- Windows: PowerShell 5.1+ (default on Windows 10/11)

If `jq` is missing on macOS/Linux, install it with the system package manager (`brew install jq`, `apt install jq`, etc.).
