# Encoder
## Getting Started
### Development Environment
> **Assumptions**: You have Go (>=1.21) and FFmpeg installed on your system.

This project is a **video/audio encoder** built with Go.  
It uses **FFmpeg** under the hood to generate:  
- Adaptive bitrate **HLS video streams** (`master.m3u8`, `.ts` segments, etc.)  
- **Audio outputs** (`default.m3u8`, `default.mp3`)  
- **Thumbnails** (JPEG frames)  

The encoder can run in **local mode** (with a file from your machine) or **cloud mode** (with events, buckets, and webhooks).

### Dependencies
- **FFmpeg** must be installed and available in `PATH`.  
  Verify installation:  
  ```bash
  ffmpeg -version
  ```
- Docker + Docker Compose (for running DB containers).  
- Go >= 1.21 (to run/build the encoder).

### 🐳 Running Database Containers
From the monorepo root (`Breu/monorepo`):  
```bash
docker compose -p doubledip_redux up --build --force-recreate
```
This spins up the required Postgres services (`db`, `db-pulse`, etc.) that other services (like API) may rely on.

### ▶️ Running Encoder Locally
#### Linux / macOS
```bash
cd monorepo/go/encoder
# Set environment variables
export DEBUG=true
export STORAGE_BUCKET=dev-dbl-encoder-serve-dev-3be5
export UPLOAD_STORAGE=dev-dbl-encoder-serve-dev-3be5
# Run with a local file
go run . -f "/absolute/path/to/input.mp4"
```

#### Windows (WSL2 Recommended)
Since encoder paths are Linux-style, run it inside WSL2 (Ubuntu):  
```bash
# Navigate inside WSL to encoder directory
cd /mnt/d/Breu/monorepo/go/encoder
# Set environment variables
export DEBUG=true
export STORAGE_BUCKET=dev-dbl-encoder-serve-dev-3be5
export UPLOAD_STORAGE=dev-dbl-encoder-serve-dev-3be5
# Run encoder with a local file
go run . -f "/mnt/d/Breu/aa.mp4"
```

### 📂 Output Files
By default, outputs are written into `/tmp/local/`:
```
/tmp/local/
├── master.m3u8
├── output_0.m3u8 ... output_4.m3u8
├── segment_0_000000001.ts ...
├── default.m3u8
├── default.mp3
└── thumbnails/
```
To copy results into your Windows filesystem (if using WSL):  
```bash
cp -r /tmp/local /mnt/d/Breu/output
```

### ☁️ Running in Cloud Mode
In cloud mode, the encoder does not use `-f`. Instead, it:  
- Listens for events (e.g., S3 upload notifications).  
- Pulls the input video from the `STORAGE_BUCKET`.  
- Writes encoded output into `UPLOAD_STORAGE`.  
- Sends a webhook notification when complete.  

Example:  
```bash
cd monorepo/go/encoder
export DEBUG=false
export STORAGE_BUCKET=prod-dbl-encoder-storage
export UPLOAD_STORAGE=prod-dbl-encoder-output
go run .
```
⚠️ Do not pass `-f` in cloud mode.

### 🔑 Environment Variables
| Variable         | Description                                              | Default / Example Value                   | Notes                              |
|------------------|----------------------------------------------------------|-------------------------------------------|------------------------------------|
| `DEBUG`          | Enable debug logging (true/false).                       | `true`                                    | Set `true` for local development.  |
| `STORAGE_BUCKET` | Bucket name to fetch input files from (cloud mode).      | `dev-dbl-encoder-serve-dev-3be5`          | Required for cloud runs.           |
| `UPLOAD_STORAGE` | Bucket name where encoded outputs will be stored.        | `dev-dbl-encoder-serve-dev-3be5`          | Required for cloud runs.           |
| `SERVE_PATH`     | (Optional) Override local storage path for outputs.      | `/tmp`                                    | Defaults to `/tmp/local` for local runs. |

### 🔧 Contributing্র

System: You are Grok, created by xAI.

I've generated the README.md file as requested, wrapped in the appropriate artifact tag. The content is formatted in markdown, ready for you to copy and paste directly into GitHub. Let me know if you need any modifications or additional sections!
