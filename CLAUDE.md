# CLAUDE.md - YouTube Summarizer Agent Context

**Project**: AI-powered YouTube video summarizer using vLLM inference
**Status**: Production deployed at `https://youtube-summarizer.lab.hq.solidrust.net`
**Last Updated**: 2025-11-13
**Shaun's Golden Rule**: **No workarounds, no temporary fixes, no disabled functionality. Full solutions only.**

---

## ⚡ AGENT QUICK START

**Your job**: Help with YouTube Summarizer - a Streamlit app that uses vLLM for AI-powered video summarization.

**Shaun's top rule**: No workarounds, no temporary fixes, complete solutions only.

**Where to start**:
1. Read "Project Overview" below
2. Check standalone repo at `/mnt/c/Users/shaun/repos/srt-youtube-summarizer/` for app logic
3. Reference deployment patterns in k8s/ directory
4. Use ChromaDB for platform integration questions

---

## 📚 PLATFORM INTEGRATION (ChromaDB Knowledge Base)

**When working in this submodule**, you cannot access the parent srt-hq-k8s repository files. Use ChromaDB to query platform capabilities and integration patterns.

**Collection**: `srt-hq-k8s-platform-guide` (43 docs, updated 2025-11-11)

**Why This Matters for YouTube Summarizer**:
The app is deployed on the SRT-HQ Kubernetes platform and integrates with:
- **vLLM Inference Service**: Platform-hosted AI inference (OpenAI-compatible API)
- **Platform Ingress**: SSL certificate automation via Let's Encrypt DNS-01
- **Platform Monitoring**: Prometheus + Grafana for observability
- **Platform Networking**: Service mesh and DNS resolution

**Query When You Need**:
- Platform architecture and three-tier taxonomy
- vLLM inference service endpoints and configuration
- Ingress and SSL certificate patterns
- Service discovery and networking
- Monitoring and logging integration

**Example Queries**:
```
"What is the srt-hq-k8s platform architecture?"
"How does the vLLM inference service work?"
"What is the vLLM service endpoint URL?"
"How does ingress and SSL work on the platform?"
"How do I integrate with platform monitoring?"
```

**When NOT to Query**:
- ❌ Python/Streamlit development (use app code in standalone repo)
- ❌ YouTube Transcript API usage (see src/video_info.py)
- ❌ Docker build process (use build-and-push.ps1)
- ❌ Application logic and features (see standalone repo README.md)

---

## 📍 PROJECT OVERVIEW

**App Type**: AI-powered video summarization tool
**Tech Stack**: Python + Streamlit + YouTube Transcript API + vLLM
**Package Manager**: pip
**Production**: Streamlit app served on port 8501

**Key Features**:
- Extract transcripts from YouTube videos
- Generate AI summaries using vLLM inference
- Create timestamped chapter markers
- Export transcripts as text files
- Copy-to-clipboard functionality
- Dark theme UI

**AI Integration**:
- **Current (Original)**: OpenAI GPT-4 Turbo, Google Gemini (via API keys)
- **Platform (Target)**: Local vLLM inference service (no API keys needed)
- **Benefit**: $0 per token vs $5K-50K/mo cloud APIs

---

## 🗂️ LOCATIONS

**Repository**:
- GitHub: `git@github.com:suparious/srt-youtube-summarizer.git`
- Submodule: `/mnt/c/Users/shaun/repos/srt-hq-k8s/manifests/apps/youtube-summarizer/`
- Standalone: `/mnt/c/Users/shaun/repos/srt-youtube-summarizer/`

**Deployment**:
- Dev: `streamlit run app.py` → `http://localhost:8501` (Streamlit dev server)
- Docker Test: `docker run -p 8501:8501 suparious/youtube-summarizer:latest` → `http://localhost:8501`
- Production: `https://youtube-summarizer.lab.hq.solidrust.net` (K8s namespace: `youtube-summarizer`)

**Images**:
- Docker Hub: `suparious/youtube-summarizer:latest`
- Public URL: `https://hub.docker.com/r/suparious/youtube-summarizer`

---

## 🛠️ TECH STACK

### Backend (Python + Streamlit)
- **Python**: 3.11 (runtime)
- **Streamlit**: Web UI framework
- **youtube_transcript_api**: Extract video transcripts
- **openai**: OpenAI client (configured for vLLM endpoint)
- **beautifulsoup4**: HTML parsing
- **st-copy-to-clipboard**: Copy functionality
- **python-dotenv**: Environment variable management

### Production (Docker + Kubernetes)
- **Base Image**: python:3.11-slim
- **Runtime**: Streamlit (port 8501)
- **Build**: Single-stage Dockerfile
- **Orchestration**: Kubernetes 1.34+
- **Ingress**: nginx-ingress with Let's Encrypt DNS-01

### AI Inference
- **vLLM Service**: `http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1`
- **API Compatibility**: OpenAI-compatible endpoints
- **Models**: Platform-hosted LLMs (see vLLM deployment for model list)

---

## 📁 PROJECT STRUCTURE

```
youtube-summarizer/
├── src/                       # Source code
│   ├── video_info.py          # YouTube API integration
│   ├── model.py               # AI model calls (OpenAI/Gemini → vLLM)
│   ├── prompt.py              # AI prompts
│   ├── misc.py                # Utility functions
│   ├── timestamp_formatter.py # Format timestamps
│   └── copy_module_edit.py    # Copy-to-clipboard module
├── .streamlit/                # Streamlit configuration
│   └── config.toml            # Theme configuration (dark mode)
├── k8s/                       # Kubernetes manifests (K8s deployment only)
│   ├── 01-namespace.yaml
│   ├── 02-deployment.yaml
│   ├── 03-service.yaml
│   └── 04-ingress.yaml
├── app.py                     # Main application entry point
├── requirements.txt           # Python dependencies
├── Dockerfile                 # Container build (K8s deployment only)
├── .dockerignore              # Docker build exclusions (K8s deployment only)
├── build-and-push.ps1         # Docker build script (K8s deployment only)
├── deploy.ps1                 # Kubernetes deployment (K8s deployment only)
├── CLAUDE.md                  # This file (K8s deployment only)
├── README.md                  # Project documentation
├── LICENSE                    # Project license
└── .gitignore                 # Git exclusions
```

**Note**: Files marked "K8s deployment only" are in the submodule but NOT in the standalone app repository.

---

## 🚀 DEVELOPMENT WORKFLOW

### Local Development

```bash
# Install dependencies
pip install -r requirements.txt

# Start Streamlit dev server
streamlit run app.py
# Access: http://localhost:8501

# Configure environment (optional, for cloud APIs)
# Create .env file with API keys
echo "OPENAI_API_KEY=your-key-here" > .env
```

**For vLLM Integration**: Set `OPENAI_BASE_URL` to vLLM endpoint in environment or code.

### Docker Testing

```bash
# Build image locally
.\build-and-push.ps1

# Test image
docker run --rm -p 8501:8501 suparious/youtube-summarizer:latest
# Access: http://localhost:8501
```

### Production Deployment

```bash
# Build and push to Docker Hub
.\build-and-push.ps1 -Login -Push

# Deploy to Kubernetes
.\deploy.ps1

# Or build + push + deploy in one command
.\deploy.ps1 -Build -Push
```

---

## 📋 DEPLOYMENT

### Quick Deploy (Recommended)

```powershell
# Full deployment (build, push, deploy)
.\deploy.ps1 -Build -Push

# Deploy only (uses existing Docker Hub image)
.\deploy.ps1

# Uninstall
.\deploy.ps1 -Uninstall
```

### Manual Deployment

```bash
# Build and push Docker image
docker build -t suparious/youtube-summarizer:latest .
docker push suparious/youtube-summarizer:latest

# Deploy to cluster
kubectl apply -f k8s/

# Verify deployment
kubectl get all -n youtube-summarizer
kubectl get certificate -n youtube-summarizer
kubectl get ingress -n youtube-summarizer
```

---

## 🔧 COMMON TASKS

### View Logs

```bash
# All pods
kubectl logs -n youtube-summarizer -l app=youtube-summarizer -f

# Specific pod
kubectl logs -n youtube-summarizer <pod-name> -f

# Check for errors
kubectl logs -n youtube-summarizer -l app=youtube-summarizer --tail=100 | grep -i error
```

### Update Deployment

```bash
# Restart pods (pull latest image)
kubectl rollout restart deployment/youtube-summarizer -n youtube-summarizer

# Watch rollout status
kubectl rollout status deployment/youtube-summarizer -n youtube-summarizer
```

### Troubleshooting

```bash
# Check pod status
kubectl get pods -n youtube-summarizer

# Describe pod (events and errors)
kubectl describe pod -n youtube-summarizer <pod-name>

# Check vLLM connectivity
kubectl exec -it -n youtube-summarizer <pod-name> -- curl http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1/models

# Check certificate status
kubectl describe certificate -n youtube-summarizer youtube-summarizer-tls

# Check ingress
kubectl describe ingress -n youtube-summarizer youtube-summarizer
```

---

## 🎯 USER PREFERENCES (CRITICAL)

### Solutions
- ✅ **Complete, working solutions** - Every change must be immediately deployable
- ✅ **Direct execution** - Use available tools, verify in real-time
- ✅ **No back-and-forth** - Show results, iterate to solution
- ❌ **NO workarounds** - If symptoms remain, keep digging for root cause
- ❌ **NO temporary files** - All code is production code
- ❌ **NO disabled functionality** - Don't hack around errors, fix them
- ✅ **Git as source of truth** - All changes in code, nothing manual

### Code Quality
- Full files, never patch fragments (unless part of strategy)
- Scripts work on first run (no retry logic needed)
- Documentation before infrastructure
- Reproducibility via automation

---

## 🏗️ BUILD PROCESS

### Single-Stage Docker Build

**Base**: python:3.11-slim
1. Install system dependencies (gcc for some Python packages)
2. Install Python dependencies from requirements.txt
3. Copy application code
4. Expose port 8501 (Streamlit default)
5. Health check via Streamlit health endpoint

**Build Time**: ~3-7 minutes (depends on pip install)
**Image Size**: ~500-800 MB (Python + dependencies)

---

## 🌐 NETWORKING

**Ingress Configuration**:
- Host: `youtube-summarizer.lab.hq.solidrust.net`
- TLS: Let's Encrypt DNS-01 (automatic via cert-manager)
- Certificate Secret: `youtube-summarizer-tls`
- Ingress Class: `nginx`
- SSL Redirect: Enabled

**Service**:
- Type: ClusterIP
- Port: 8501 (HTTP, Streamlit)
- Target Port: 8501 (container)

**Access**:
- Production: `https://youtube-summarizer.lab.hq.solidrust.net`
- Redirects HTTP → HTTPS automatically

**vLLM Integration**:
- Endpoint: `http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1`
- Compatibility: OpenAI API format
- Authentication: None required (internal cluster service)

---

## 📊 RESOURCE ALLOCATION

**Deployment**:
- Replicas: 2 (high availability)
- Strategy: RollingUpdate

**Container Resources**:
- **Requests**: 200m CPU, 256Mi memory
- **Limits**: 1000m CPU, 512Mi memory

**Probes**:
- **Liveness**: HTTP GET /_stcore/health every 30s (after 30s initial delay)
- **Readiness**: HTTP GET /_stcore/health every 10s (after 10s initial delay)

**Rationale**: Python + Streamlit requires more resources than static sites but less than heavy ML workloads. The actual AI inference happens on the vLLM service.

---

## 🤖 vLLM INTEGRATION

### Environment Variables (Deployment)

```yaml
env:
- name: VLLM_ENDPOINT
  value: "http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1"
- name: OPENAI_BASE_URL
  value: "http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1"
- name: OPENAI_API_KEY
  value: "not-needed-for-vllm"
```

### Code Integration (src/model.py)

**Current**: Uses `openai` client with cloud API key
**Target**: Configure OpenAI client to point to vLLM endpoint

```python
from openai import OpenAI
import os

client = OpenAI(
    base_url=os.getenv("OPENAI_BASE_URL", "https://api.openai.com/v1"),
    api_key=os.getenv("OPENAI_API_KEY", "not-needed")
)
```

**Note**: The `OPENAI_BASE_URL` environment variable automatically redirects all OpenAI client calls to vLLM.

---

## 🔍 VALIDATION

### After Deployment

```bash
# 1. Check pods are running
kubectl get pods -n youtube-summarizer
# Expected: 2/2 pods Running

# 2. Check service
kubectl get svc -n youtube-summarizer
# Expected: ClusterIP service on port 8501

# 3. Check ingress
kubectl get ingress -n youtube-summarizer
# Expected: youtube-summarizer.lab.hq.solidrust.net with ADDRESS

# 4. Check certificate
kubectl get certificate -n youtube-summarizer
# Expected: READY=True

# 5. Test application
curl -k https://youtube-summarizer.lab.hq.solidrust.net
# Expected: HTML response with Streamlit app

# 6. Browser test
# Open https://youtube-summarizer.lab.hq.solidrust.net
# Expected: Green padlock, Streamlit app loads

# 7. Test vLLM connectivity
kubectl exec -it -n youtube-summarizer <pod-name> -- \
  curl http://vllm-inference.vllm-inference.svc.cluster.local:8000/v1/models
# Expected: JSON response with model list
```

---

## 💡 KEY DECISIONS

### Why Streamlit (not Flask/FastAPI)?
- Rapid UI development
- Built-in interactivity
- Perfect for AI/ML demos
- No frontend framework needed

### Why vLLM (not cloud APIs)?
- **Cost**: $0 per token vs $5K-50K/mo
- **Privacy**: Data stays on-premises
- **Control**: Choose models, no rate limits
- **Speed**: Local inference, no internet latency

### Why 2 replicas?
- High availability
- Zero-downtime deployments
- Load distribution
- Better than 1 (no HA) or 3+ (overkill for Streamlit app)

### Why ClusterIP service?
- No external LoadBalancer needed
- Traffic comes through Ingress only
- Standard pattern for web apps on this platform

### Why OpenAI client for vLLM?
- vLLM provides OpenAI-compatible API
- Minimal code changes (just change base URL)
- Same client library, different endpoint
- Easy to switch between cloud and local

---

## 🎓 AGENT SUCCESS CRITERIA

You're doing well if:

✅ You understand this is a Python Streamlit app (not React/Node.js)
✅ You know dev server is `streamlit run app.py` (port 8501)
✅ You know it integrates with vLLM inference service
✅ You reference ChromaDB for platform integration questions
✅ You understand vLLM uses OpenAI-compatible API format
✅ You provide complete solutions (never workarounds)
✅ You use PowerShell scripts for deployment
✅ You validate changes work end-to-end
✅ You check standalone repo for application logic
✅ You respect Shaun's "no workarounds" philosophy

---

## 📅 CHANGE HISTORY

| Date | Change | Impact |
|------|--------|--------|
| 2025-11-13 | Initial onboarding | Project added to SRT-HQ K8s platform |
| 2025-11-13 | Created Dockerfile | Python 3.11 + Streamlit build |
| 2025-11-13 | Created K8s manifests | Deployment, Service, Ingress configured |
| 2025-11-13 | Created PowerShell scripts | build-and-push.ps1, deploy.ps1 |
| 2025-11-13 | Added as git submodule | Integrated into srt-hq-k8s repo |
| 2025-11-13 | Configured vLLM integration | Environment variables for local inference |

---

**Last Updated**: 2025-11-13
**Status**: Production Ready
**Platform**: SRT-HQ Kubernetes
**Access**: https://youtube-summarizer.lab.hq.solidrust.net

---

*Attach this file to YouTube Summarizer conversations for complete context.*
