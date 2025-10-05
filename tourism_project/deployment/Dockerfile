# Dockerfile
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# --- Create non-root user and writable HOME ---
RUN useradd -m -u 1000 appuser
ENV HOME=/home/appuser
WORKDIR $HOME/app

# Pre-create Streamlit dirs (and any cache) *before* first import
RUN mkdir -p $HOME/.streamlit $HOME/.cache/streamlit && chown -R appuser:appuser $HOME

# --- Install deps ---
COPY tourism_project/deployment/requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# --- Copy app code (adjust path if different) ---
COPY . .
RUN chown -R appuser:appuser $HOME

# --- Drop privileges ---
USER appuser

# --- Streamlit env (must be set before the process starts) ---
ENV STREAMLIT_CONFIG_DIR=$HOME/.streamlit \
    STREAMLIT_CACHE_DIR=$HOME/.cache/streamlit \
    STREAMLIT_SERVER_HEADLESS=true \
    STREAMLIT_SERVER_ADDRESS=0.0.0.0 \
    STREAMLIT_BROWSER_GATHER_USAGE_STATS=false \
    # Spaces will pass $PORT; fall back to 7860 locally
    STREAMLIT_SERVER_PORT=${PORT:-7860}

EXPOSE 7860

# Use the PORT env if provided by the platform
CMD streamlit run tourism_project/deployment/app.py --server.port=${PORT:-7860} --server.address=0.0.0.0
