<p align="center">
  <picture>
    <!-- When the user prefers dark mode, show the white logo -->
    <source media="(prefers-color-scheme: dark)" srcset="./images/Blueprint-logo-white.png">
    <!-- When the user prefers light mode, show the black logo -->
    <source media="(prefers-color-scheme: light)" srcset="./images/Blueprint-logo-black.png">
    <!-- Fallback: default to the black logo -->
    <img src="./images/Blueprint-logo-black.png" width="35%" alt="Project logo"/>
  </picture>
</p>


<div align="center">

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
[![Speaches](https://img.shields.io/badge/Speaches-%F0%9F%8E%A4-yellow)](https://github.com/speaches-ai/speaches/)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![](https://dcbadge.limes.pink/api/server/YuMNeuKStr?style=flat)](https://discord.gg/YuMNeuKStr) <br>

[Blueprints Hub](https://blueprints.mozilla.ai/)
| [Documentation](https://github.com/mozilla-ai/speech-to-text/blob/main/README.md)
| [Contributing](CONTRIBUTING.md)

🤝 **_This Blueprint was a result of an [EleutherAI](https://www.eleuther.ai/) <> [mozilla.ai](https://www.mozilla.ai/) collaboration, as part of their work on [Open Datasets for LLM Training](https://blog.mozilla.org/en/mozilla/dataset-convening/)_**.

</div>

# Transcribe audio files using [Speaches](https://github.com/speaches-ai/speaches/)

This tutorial will guide you through setting up and using [Speaches](https://github.com/speaches-ai/speaches/) to transcribe audio files from the command line or a locally hosted demo UI. Speaches is an OpenAI API-compatible server that provides streaming transcription, translation, and speech generation capabilities.

You can use Speaches either through a web interface using Docker or via the command line by installing the OpenAI CLI tool.

## Using [Docker](https://docs.docker.com/engine/install/) to run Speaches

We can use pre-built images of Speaches to bypass manual installation. This is the recommended way to run Speaches, as it simplifies the setup process and ensures that you have all the necessary dependencies. Note that you have a choice between using the CPU or GPU version depending on your hardware.

Create a volume to store the downloaded models (so they persist even if you restart the container):

```bash
sudo docker volume create hf-hub-cache
```

#### For CUDA (if you have a compatible NVIDIA GPU):

```bash
sudo docker run \
  --rm \
  --detach \
  --publish 8000:8000 \
  --name speaches \
  --volume hf-hub-cache:/home/ubuntu/.cache/huggingface/hub \
  --gpus=all \
  ghcr.io/speaches-ai/speaches:latest-cuda
```

#### For CPU (if you don't have a compatible GPU):

```bash
sudo docker run \
  --rm \
  --detach \
  --publish 8000:8000 \
  --name speaches \
  --volume hf-hub-cache:/home/ubuntu/.cache/huggingface/hub \
  ghcr.io/speaches-ai/speaches:latest-cpu
```

This will pull the necessary Docker image and start the Speaches server in the background. The server will be accessible at `http://localhost:8000`.

When you're done, you can stop the Speaches server with:

```bash
sudo docker stop speaches
```

This will stop the container, but thanks to the volume we created, the downloaded models will be preserved for future use.


## Using the CLI version

Speaches is designed to be compatible with the OpenAI API. To have more control over our output, we can use the OpenAI CLI as our Speaches client. Let's install the OpenAI command-line interface to interact with the Speaches server:

```bash
pip install openai
```

### Set Up Environment Variables

Even though Speaches doesn't require an API key, the OpenAI client does. Configure these environment variables:

```bash
# Set the base URL to your local Speaches server
export OPENAI_BASE_URL=http://localhost:8000/v1/

# Use any non-empty string as the API key
export OPENAI_API_KEY="cant-be-empty"
```

> **Note**: The API key doesn't need to be a valid OpenAI key, but it cannot be empty due to the OpenAI client requirements.

### Transcribe an Audio File

Now you're ready to transcribe your first audio file. Make sure you have an audio file ready (we'll use `sample.mp3` in this example).

#### Basic Transcription:

```bash
openai api audio.transcriptions.create -m Systran/faster-whisper-medium -f sample.mp3 --response-format text > sample.txt
```

This command will:
- Connect to your local Speaches server
- Use the `Systran/faster-whisper-medium` model for transcription
- Transcribe the `sample.mp3` file
- Return the result as plain text

### Advanced Options:

Speaches supports various options for transcription. Here are some useful ones:

1. **Choosing a Different Model**: 
   
   Speaches supports various Whisper models. For higher accuracy (but slower transcription):
   
   ```bash
   openai api audio.transcriptions.create -m Systran/faster-whisper-large-v3 -f sample.mp3 --response-format text > sample.txt
   ```
   
   For faster transcription (but potentially lower accuracy):
   
   ```bash
   openai api audio.transcriptions.create -m Systran/faster-whisper-tiny -f sample.mp3 --response-format text > sample.txt
   ```

2. **Getting JSON Output with Timestamps**:

   ```bash
   openai api audio.transcriptions.create -m Systran/faster-whisper-medium -f sample.mp3 --response-format verbose_json > sample.json
   ```

3. **Specifying the Language** (can improve accuracy):

   ```bash
   openai api audio.transcriptions.create -m Systran/faster-whisper-medium -f sample_fr.mp3 --language fr --response-format text > sample_fr.txt
   ```

4. **Creating SRT Subtitle Files**

    SRT (SubRip Text) files are commonly used for subtitles in videos. Speaches can generate these directly from your audio files:

    ```bash
    openai api audio.transcriptions.create -m Systran/faster-whisper-medium -f sample.mp3 --response-format srt > sample.srt
    ```

## Using custom Models from Hugging Face

Speaches can use any compatible fine-tuned Whisper model from Hugging Face which uses the ctranslate2 architecture: https://huggingface.co/models?other=ctranslate2.

```bash
openai api audio.transcriptions.create -m smcproject/vegam-whisper-medium-ml -f sample_ml.mp3 --language ml --response-format text > sample_ml.txt
```

For example, [this model](https://huggingface.co/smcproject/vegam-whisper-medium-ml) works particularly well for Malayalam audio because it was fine-tuned on Mozilla's Common Voice Malayalam subset.

For other languages or specific domains, you can search for fine-tuned Whisper models (faster-whisper variant!) on Hugging Face and use them in the same way.

## Troubleshooting

### No output from transcription:
- Ensure your audio file is in a supported format
- Check that your audio file actually contains speech
- Try a different model (e.g., switch from a language-specific model to a general one)

### Error connecting to the server:
- Verify the Speaches container is running with `sudo docker ps`
- Check the logs with `sudo docker logs speaches`
- Make sure your OPENAI_BASE_URL is correct

### Model download issues:
- The first time you use a model, Speaches will download it automatically
- This might take some time depending on your internet connection and the model size

## Next Steps

Once you're comfortable with basic transcription, you might want to explore other features of Speaches:

- **Translation**: Translating audio from one language to English
- **Text-to-Speech**: Converting text to spoken audio
- **Voice Chat**: Creating audio-based conversations with LLMs

Check out the [Speaches documentation](https://speaches.ai) for more information on these advanced features.


### Hardware requirements

- An audio file in a supported format: `mp3`, `mp4`, `mpeg`, `mpga`, `m4a`, `wav`, and `webm`
- **System requirements**:
  - OS: Linux, macOS, Windows ([WSL](https://docs.docker.com/desktop/features/wsl/))
  - Python 3.10 or higher
  - Docker
  - Minimum RAM: 16GB
  - Disk space: 40GB
  - 
## License

This project is licensed under the Apache 2.0 License. See the [LICENSE](LICENSE) file for details.
