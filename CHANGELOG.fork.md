# Changelog — Fork

Fork: [dungartoriaaa/ClaudeCodeStatusLine](https://github.com/dungartoriaaa/ClaudeCodeStatusLine)
Upstream: [daniel3303/ClaudeCodeStatusLine](https://github.com/daniel3303/ClaudeCodeStatusLine)

Tài liệu các thay đổi của fork so với upstream. Mọi thay đổi đều tập trung vào `statusline.ps1` (bản Windows PowerShell).

## [fork-1.4.2] — 2026-05-11

Dựa trên upstream v1.4.2.

### Thêm

- **Thanh progress bar 10 ô** (`■■■■■■■■■□`) cho cả 3 chỉ số: tokens, 5h rate limit, 7d rate limit. Đây là tính năng từ v1.3.0 bị upstream bỏ ở v1.4.x — fork khôi phục lại.
  - Hàm mới `Get-UsageBar([int]$pct)` trả về 10 ô tô màu theo phần trăm (xanh / vàng / cam / đỏ tuỳ ngưỡng).
  - Bar được chèn vào giữa label và phần trăm, ví dụ: `5h ■■■■■■■■■□ 99% @6:00pm`.
- **Set UTF-8 output encoding** ở đầu script:
  ```powershell
  [Console]::OutputEncoding = [System.Text.Encoding]::UTF8
  $OutputEncoding = [System.Text.Encoding]::UTF8
  ```
  Cần thiết để Windows PowerShell 5.1 in đúng ký tự `■` `□`, nếu không sẽ ra ký tự lỗi `�-�`.
- **File được lưu dưới dạng UTF-8 with BOM** (`EF BB BF`) để PS 5.1 đọc đúng ký tự non-ASCII trong source.

### Vị trí thay đổi trong `statusline.ps1`

| Dòng (xấp xỉ) | Thay đổi |
|---|---|
| 3–4 | Thêm 2 dòng set `OutputEncoding = UTF8` |
| 60–66 | Thêm function `Get-UsageBar` |
| ~152 | Tokens line: chèn `$(Get-UsageBar $pctUsed)` |
| ~351, ~403 | 5h line: chèn `$(Get-UsageBar $fiveHourPct)` |
| ~359, ~412 | 7d line: chèn `$(Get-UsageBar $sevenDayPct)` |

## Cách đồng bộ với upstream

```powershell
git remote add upstream https://github.com/daniel3303/ClaudeCodeStatusLine.git
git fetch upstream
git merge upstream/main
# Giải quyết conflict trong statusline.ps1 nếu có
```
