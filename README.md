# YouTube Video Summarizer & Q&A Bot

A simple Gradio app that fetches YouTube transcripts, creates embeddings and a FAISS index, and uses an IBM Watsonx LLM to provide:
- A concise one-paragraph summary of a YouTube video's transcript
- Answers to user questions grounded in the video's transcript

This repository contains `ytbot.py` which implements the app and the core logic.

> NOTE: This project uses IBM Watsonx (watsonx.ai) models and the LangChain ecosystem. The sample code in `ytbot.py` shows a minimal integration pattern — you will need valid IBM Watsonx credentials and potentially adjust the integration to match your IBM environment and package versions.

## Features
- Fetch transcripts for YouTube videos (via youtube_transcript_api)
- Preprocess and split transcript text into chunks
- Create embeddings using Watsonx embeddings model
- Build FAISS index for similarity search
- Use Watsonx LLM to generate summaries and answer questions
- Simple Gradio UI for interacting with the bot

## Files
- `ytbot.py` — main application file (Gradio UI + all functions)

(See the code for function-level comments and implementation.)

## Requirements

Minimum packages (use virtualenv or conda):
- Python 3.8+
- gradio
- youtube_transcript_api
- langchain
- langchain_community (for FAISS wrapper)
- ibm_watsonx_ai (IBM SDK used in this repo; package name may differ / be private to IBM)
- langchain_ibm
- faiss-cpu (or faiss, depending on your platform)
- python-dotenv (recommended for env vars)

Example (pip):
```
pip install gradio youtube_transcript_api langchain langchain_community faiss-cpu python-dotenv
```

Important: The IBM Watsonx/ibm_watsonx_ai packages may be distributed differently or require special installation. Check IBM documentation for the correct packages and credentials setup in your environment.

## Configuration / Credentials

This project expects to call IBM Watsonx services. The example `ytbot.py` uses a helper `setup_credentials()` that hardcodes a URL and project id:

- Default URL in code: `https://us-south.ml.cloud.ibm.com`
- Default project id in code: `skills-network`

In a real deployment you will typically need:
- An IBM Cloud API key or other credentials
- The correct service URL for your instance
- A valid Watsonx project id
- Appropriate IAM or service-role permissions

Recommended approach:
1. Create a `.env` file (not committed) with:
   - IBM_WATSONX_APIKEY=<your_api_key>
   - IBM_WATSONX_URL=<service_url>
   - IBM_WATSONX_PROJECT_ID=<project_id>

2. Modify `setup_credentials()` in `ytbot.py` to read these environment variables (e.g., using `os.getenv()` or `python-dotenv`) and build `Credentials`/client properly.

Security: Never commit API keys to the repository.

## Quickstart

1. Clone this repository:
   git clone https://github.com/alankudakkad17/YouTube_Video_Summarizer-Q-A-_Bot.git
   cd YouTube_Video_Summarizer-Q-A-_Bot

2. Create and activate a virtual environment (recommended).

3. Install dependencies (example):
   pip install -r requirements.txt
   (If there's no requirements file, install required packages listed above.)

4. Configure credentials (see Configuration section).

5. Run the app:
   python ytbot.py

6. Open the Gradio UI at:
   http://localhost:7860

UI usage:
- Paste a YouTube URL (the current regex in `ytbot.py` expects a standard `https://www.youtube.com/watch?v=<id>` URL).
- Click "Summarize Video" to get a one-paragraph summary.
- Ask a question in the question box and click "Ask a Question" to get answers based on the transcript.

## Known limitations & notes

- URL handling: The helper `get_video_id()` currently matches URLs of the form:
  `https://www.youtube.com/watch?v=<11-char-id>`.
  It does not support shortened `youtu.be/` URLs or additional query parameters. Update the regex if you need broader support.

- Transcript shape: `youtube_transcript_api` returns transcript entries (typically dicts with keys like `text`, `start`, `duration`). The current `process()` function assumes object attribute access (`i.text`, `i.start`). If you encounter issues, change `process()` to use dictionary access `i['text']` and `i['start']`.

- IBM Watsonx integration: `setup_credentials()` in this code is illustrative and hardcodes a URL and project id. Most deployments require an API key and proper credentials object initialization. Consult IBM docs for up-to-date setup.

- FAISS and embeddings: Make sure the embeddings model and FAISS are compatible. If you use GPU versions of FAISS, install the correct package. Embedding model identifiers in code (e.g., `EmbeddingTypes.IBM_SLATE_30M_ENG.value`) may need to be adjusted or replaced depending on the IBM SDK version.

- Rate limits, privacy, and compliance: Respect YouTube's terms of service and any data/privacy requirements when storing or processing transcript content.

## Troubleshooting

- Missing transcripts: Some videos don't have transcripts or have transcripts disabled — the app will return "No transcript available".
- Module import errors: Verify that the correct IBM packages are installed and that package names/versions match the SDK you have access to.
- FAISS errors: If you have trouble installing or importing faiss, try `faiss-cpu` on CPU-only machines. On macOS/Windows you may need platform-specific wheels.

## Extending & Improving

Suggestions for improvements:
- Add support for multiple YouTube URL formats and playlist handling.
- Persist FAISS indices and embeddings to disk to avoid recomputing them every run.
- Add better handling for missing or generated transcripts vs. manual transcripts.
- Add streaming or progressive summarization for extremely long transcripts.
- Add unit tests and CI checks.

## Contributing

Contributions, issues and feature requests are welcome. Please open an issue or submit a PR with improvements.

## License

Specify a license for the project (e.g., MIT). The repository currently does not contain a license file — add one if you want to share the project.

## Contact

If you have questions about the code, open an issue in the repository.
