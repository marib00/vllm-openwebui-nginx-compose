# vLLM OpenWebUI NGINX Docker Compose

This repository provides a Docker Compose setup that integrates vLLM (a fast LLM inference engine), Open WebUI (a user-friendly web interface), and NGINX (as a reverse proxy with SSL termination).

## Overview

This project creates a secure, ready-to-deploy large language model inference stack with the following components:

- **vLLM**: High-performance inference engine with OpenAI-compatible API
- **Open WebUI**: User-friendly web interface for interacting with language models
- **NGINX**: Secure reverse proxy with SSL termination

The setup uses Docker's internal networking to secure the components and only exposes the NGINX ports to the outside world.

## Prerequisites

- Docker and Docker Compose
- NVIDIA GPU with compatible drivers
- NVIDIA Container Toolkit installed
- SSL certificate and key (self-signed or from a certificate authority)

## Setup Instructions

1. **Clone this repository**
   ```bash
   git clone https://github.com/yourusername/vllm-openwebui-nginx-compose.git
   cd vllm-openwebui-nginx-compose
   ```

2. **Set up SSL certificates**
   
   Place your SSL certificate and key files in the `certs` directory:
   - `certs/server.crt` - SSL certificate
   - `certs/server.key` - SSL private key
   
   Note: These files are gitignored for security reasons.

   If you need to generate a self-signed certificate:
   ```bash
   mkdir -p certs
   openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout certs/server.key -out certs/server.crt
   ```

3. **Configure your environment**
   
   Create a `.env` file with your Hugging Face token (optional):
   ```bash
   echo "HF_TOKEN=your_huggingface_token" > .env
   ```

4. **Configure the model (optional)**
   
   Edit the `docker-compose.yml` file to change the model used by vLLM:
   ```yaml
   command: --model your_preferred_model #Add additional parameters as needed
   ```

5. **Configure NGINX (optional)**
   
   If you have a domain name, update the `nginx/default.conf` file:
   ```conf
   server_name your-domain.com; # Replace _ with your domain
   ```

## Usage

1. **Start the services**
   ```bash
   docker compose up -d
   ```

2. **Access the interface**
   
   Open your web browser and navigate to:
   - https://localhost (or your domain name)

3. **Access the API directly**
   
   The vLLM API is available at:
   - https://localhost/vllm-api/ (or https://your-domain.com/vllm-api/)
   
   For example:
   - https://localhost/vllm-api/v1/chat/completions

## Notes

- First startup may take significant time as models are downloaded from Hugging Face.
- The vLLM server has a health check with a long start period to accommodate model loading.
- All user data from Open WebUI is stored in a Docker volume for persistence.

## Configuration Details

### Docker Compose Network Architecture

The setup uses Docker's internal networking to secure communication between services:

- Only the NGINX container exposes ports to the host (80 and 443)
- vLLM and Open WebUI are only accessible through the NGINX reverse proxy
- All services are on the same internal Docker network

### Volumes

- `/scratch/hf_cache:/root/.cache/huggingface` - Cache Hugging Face models
- `open-webui_data:/app/backend/data` - Persist Open WebUI data

## Troubleshooting

- **SSL Issues**: Verify that your SSL certificate and key are properly placed in the `certs` directory.
- **Model Loading**: The first launch may take considerable time as models are downloaded.
- **GPU Availability**: Ensure NVIDIA runtime is properly configured for Docker.

## License

MIT
