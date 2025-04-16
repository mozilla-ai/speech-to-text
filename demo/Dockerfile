# Base image directly using the pre-built speaches image
FROM ghcr.io/speaches-ai/speaches:latest-cpu

USER ubuntu
ENV HOME=/home/ubuntu \
    PATH=/home/ubuntu/.local/bin:$PATH

# Ensure the HuggingFace cache directory exists and has proper permissions
RUN mkdir -p $HOME/.cache/huggingface/hub

# Create a volume for the HuggingFace cache
VOLUME ["$HOME/.cache/huggingface/hub"]

# Environment variables
ENV UVICORN_HOST=0.0.0.0
ENV UVICORN_PORT=7860

# Expose the port that the server listens on
EXPOSE 7860

# Command to run the server
CMD ["uvicorn", "--factory", "speaches.main:create_app"]