# YouTube Summarizer - Kubernetes Deployment

AI-powered YouTube video summarizer using vLLM inference on SRT-HQ Kubernetes platform.

**Production**: https://youtube-summarizer.lab.hq.solidrust.net

---

## Quick Start

### Development

```bash
# Install dependencies
pip install -r requirements.txt

# Run locally
streamlit run app.py

# Access: http://localhost:8501 (Streamlit dev server with auto-reload)
```

### Docker (Local Testing)

```bash
# Build image
.\build-and-push.ps1

# Test image
docker run --rm -p 8501:8501 suparious/youtube-summarizer:latest
# Access: http://localhost:8501
```

### Kubernetes (Production)

**Automated** (Recommended):
```powershell
.\deploy.ps1 -Build -Push
```

**Manual**:
```bash
# Build and push image
docker build -t suparious/youtube-summarizer:latest .
docker push suparious/youtube-summarizer:latest

# Deploy to cluster
kubectl apply -f k8s/

# Verify
kubectl get all -n youtube-summarizer
kubectl get certificate -n youtube-summarizer
kubectl get ingress -n youtube-summarizer
```

---

## Maintenance

### Update Deployment

```bash
# Rolling update
kubectl rollout restart deployment/youtube-summarizer -n youtube-summarizer

# Watch status
kubectl rollout status deployment/youtube-summarizer -n youtube-summarizer
```

### View Logs

```bash
# All pods
kubectl logs -n youtube-summarizer -l app=youtube-summarizer -f

# Specific pod
kubectl logs -n youtube-summarizer <pod-name> -f

# Check for errors
kubectl logs -n youtube-summarizer -l app=youtube-summarizer --tail=100 | grep -i error
```

### Troubleshooting

```bash
# Check pod status
kubectl get pods -n youtube-summarizer

# Describe pod
kubectl describe pod -n youtube-summarizer <pod-name>

# Check vLLM connectivity
kubectl exec -it -n youtube-summarizer <pod-name> -- \
  curl http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1/models

# Check certificate
kubectl describe certificate -n youtube-summarizer youtube-summarizer-tls

# Check ingress
kubectl describe ingress -n youtube-summarizer youtube-summarizer
```

---

## Architecture

**Tech Stack**:
- Python 3.11 + Streamlit (web UI framework)
- YouTube Transcript API (video transcript extraction)
- vLLM (local AI inference via OpenAI-compatible API)
- Kubernetes (orchestration)
- Let's Encrypt (SSL certificates via DNS-01)

**Resources**:
- **Replicas**: 2 (high availability)
- **CPU**: 200m request, 1000m limit
- **Memory**: 256Mi request, 512Mi limit

**Networking**:
- **URL**: https://youtube-summarizer.lab.hq.solidrust.net
- **Port**: 8501 (Streamlit)
- **Ingress**: nginx-ingress with SSL redirect
- **Certificate**: Automatic via cert-manager (DNS-01)

**vLLM Integration**:
- **Endpoint**: `http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1`
- **API Format**: OpenAI-compatible
- **Authentication**: None (internal cluster service)
- **Cost**: $0 per token (local inference)

---

## App Features

- **Extract transcripts** from YouTube videos (via YouTube Transcript API)
- **Generate AI summaries** using local vLLM inference ($0 per token)
- **Create timestamped chapters** with clickable YouTube links
- **Export transcripts** as downloadable text files
- **Copy-to-clipboard** functionality for easy sharing
- **Dark theme UI** optimized for extended use

**AI Models**: Platform-hosted LLMs via vLLM service (see vLLM deployment for model list)

---

## Files Overview

**Kubernetes Deployment Files** (this submodule only):
- `Dockerfile` - Python 3.11 + Streamlit container
- `build-and-push.ps1` - Docker build and publish script
- `deploy.ps1` - Kubernetes deployment script
- `k8s/` - Kubernetes manifest files
- `CLAUDE.md` - AI assistant context
- `README-K8S.md` - This file

**App Files** (from upstream repository):
- `app.py` - Main Streamlit application
- `src/` - Source code (video extraction, AI models, prompts)
- `requirements.txt` - Python dependencies
- `.streamlit/config.toml` - Streamlit configuration (dark theme)

---

## Useful Commands

```bash
# Get all resources
kubectl get all,certificate,ingress -n youtube-summarizer

# Check deployment status
kubectl rollout status deployment/youtube-summarizer -n youtube-summarizer

# Restart deployment (pull latest image)
kubectl rollout restart deployment/youtube-summarizer -n youtube-summarizer

# Port forward (local testing)
kubectl port-forward -n youtube-summarizer deployment/youtube-summarizer 8501:8501
# Access: http://localhost:8501

# Test vLLM endpoint from pod
kubectl exec -it -n youtube-summarizer <pod-name> -- \
  curl http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1/models

# Uninstall
.\deploy.ps1 -Uninstall
```

---

## Environment Variables

**Production (K8s deployment)**:
```yaml
VLLM_ENDPOINT: http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1
OPENAI_BASE_URL: http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1
OPENAI_API_KEY: not-needed-for-vllm
```

**Local development** (optional, for cloud APIs):
```bash
# Create .env file
OPENAI_API_KEY=your-openai-key-here
GOOGLE_GEMINI_API_KEY=your-gemini-key-here
```

---

## Links

- **Production**: https://youtube-summarizer.lab.hq.solidrust.net
- **Docker Hub**: https://hub.docker.com/r/suparious/youtube-summarizer
- **GitHub**: https://github.com/suparious/srt-youtube-summarizer
- **Platform**: https://github.com/SolidRusT/srt-hq-k8s
- **vLLM Service**: `http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1`

---

**Last Updated**: 2025-11-13
**Deployed By**: SRT-HQ Kubernetes Platform
