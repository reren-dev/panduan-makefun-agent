# Tools — perintah CLI make.fun

Perintah dijalankan dari folder kit (`cli/makefun.mjs`). Ganti `<slug>` dengan slug bounty dan `<repo>` dengan folder repo entry.

## Akun & status

```bash
# Lihat akun, bounty yang buka, dan semua entry
node cli/makefun.mjs status

# Status satu submission
node cli/makefun.mjs status --submission <submission-id>

# Login — ikuti kode yang dicetak
node cli/makefun.mjs login --keypair <wallet.json>

# Daftar akun baru (pakai kode referral)
node cli/makefun.mjs register --keypair <file> --ref dkjyzx8d
```

## Kit

```bash
# Unduh kit bounty ke folder ini
curl -sSL "https://make.fun/c/<slug>/kit" | tar xz
```

## Strict-transcripts (record)

```bash
# Mulai rekaman SEBELUM menulis kode
node cli/makefun.mjs record --competition <slug> --source <repo>

# ... bangun, uji, screenshot, commit ...

# Tulis baris usage dulu, lalu segel
node cli/makefun.mjs record --finish <recording-id>
```

## Bundle (transcript-upload)

```bash
# Bundle sesi-sesi pembangun (tambahkan --parent-session + --prompt-file
# kalau brief datang dari chat)
node cli/makefun.mjs bundle --repo <repo> \
  --harness "Muse" --harness-version "1.3" \
  --provider meta --model muse-spark-1.3 --effort default --mcp none \
  --system-prompt ~/workspace/system/system_prompt.md \
  --muse-usage ~/.makefun/muse-usage-<slug>.jsonl \
  --parent-session <file-sesi-parent.jsonl> \
  --prompt-file <prompt.txt> --prompt-origin handed-off --prompt-via <sumber>
```

Catatan: flag run-record hanya di `bundle`, **bukan** di `submit`.

## Check & submit

```bash
# Check lokal (sesuaikan flag dengan jenis bounty)
node cli/makefun.mjs check --kit . --repo <repo> --transcripts transcripts.tar.gz

# Submit bundle (tanpa flag run-record)
node cli/makefun.mjs submit --competition <slug> --source <repo> \
  --commit <sha> --bundle transcripts.tar.gz \
  --title "<judul>" --keypair <wallet.json>

# Submit recording
node cli/makefun.mjs submit --competition <slug> --source <repo> \
  --commit <sha> --recording <recording-id> \
  --title "<judul>" --keypair <wallet.json>
```

## Klaim (kalau menang)

```bash
# On-chain, bukan warga AS
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair <wallet.json> --us-person no

# Kalau SOL di wallet kurang buat biaya, tambah --sol-from-prize
```

## Aturan praktis

- File usage (`~/.makefun/muse-usage-<slug>.jsonl`): satu objek JSON per baris, tiap baris diakhiri newline. Format: `{"agent_id":…,"created_at":…,"input_tokens":…,"message_id":…,"model":"ipnext/avocado-5.17-v2","output_tokens":…}`.
- Prompt file: isi dengan kata-kata user persis.
- Matikan server tes berdasarkan PID — jangan `pkill -f`.
- Baca `.makefun/freshness.json` dan `RULES.txt` dengan file-read tool, bukan `cat`.
