# Maya-AI
Maya — A gentle, supportive chatbot for conversation, reflection, and mental wellness.

# Maya AI - Fine-tuned Mistral 7B Telegram Bot (Kaggle Project)

This repository contains the code for **Maya**, a Telegram chatbot fine-tuned from the Mistral 7B Instruct model. The goal was to create an AI with a thoughtful, supportive personality, incorporating ideas from the Bhagavad Gita, mental health conversations, and other diverse text sources.

This project was developed and fine-tuned primarily within the **Kaggle notebook environment**.

## What it Does

*   Connects to Telegram to provide a conversational AI experience.
*   Aims to respond with the unique "Maya" persona developed during fine-tuning.
*   Uses the powerful **Mistral 7B Instruct** model as its foundation.
*   Employs **QLoRA** (4-bit quantization + Low-Rank Adaptation) for memory-efficient fine-tuning and inference, making it possible to run on capable GPUs (like those available on Kaggle).

## Technical Overview

*   **Base Model:** `mistralai/Mistral-7B-Instruct-v0.1` (or the specific version used, e.g., from `/kaggle/input/mistral/...`)
*   **Fine-tuning Method:** QLoRA via Hugging Face libraries (`transformers`, `peft`, `accelerate`, `bitsandbytes`).
*   **Training Data:** A custom mix including Gita commentary, mental health dialogues, Reddit data, personality prompts, etc. (details in the Kaggle notebook if shared).
*   **Bot Framework:** `python-telegram-bot`.

## Running This Bot (Recommended Method: Base + Adapter)

After fine-tuning, the most reliable way to get the intended "Maya" responses (avoiding potential issues from merging quantized models) is to load the base model and the trained adapter separately. This bot script (`bot.py` in this repo) is set up for this approach.

**To run this *on Kaggle* (or a similar powerful GPU environment):**

1.  **Set Up Kaggle Notebook:**
    *   Create a new Kaggle notebook.
    *   Enable a GPU accelerator (e.g., T4 x2, P100 - needs sufficient VRAM, **~10-12GB+ recommended** for running Mistral 7B 4-bit + adapter).
    *   Ensure Internet access is enabled in the notebook settings (for installing packages and connecting to Telegram).

2.  **Add Data:**
    *   **Base Model:** Add the *original* Mistral 7B Instruct dataset as input (e.g., `/kaggle/input/mistral/...`).
    *   **Adapter Checkpoint:** Add your *trained adapter checkpoint* dataset as input. This is the folder (e.g., `checkpoint-2350`) saved during fine-tuning, containing files like `adapter_model.safetensors`, `adapter_config.json`. Let's assume you name this input dataset `maya-adapter-checkpoint`.
    *   **Code:** Add the code from this repository as input, or clone it within the notebook.

3.  **Set Up Secrets:**
    *   Go to "Add-ons" -> "Secrets" in the notebook editor.
    *   Create a secret named `TOKEN` (all caps) and paste your Telegram Bot token as the value.
    *   Make sure the secret is **attached (toggled ON)** for the session.

4.  **Configure `bot.py`:**
    *   Open the `bot.py` script within the notebook editor.
    *   Update the paths:
        ```python
        # Points to the original base model input directory
        BASE_MODEL_PATH = "/kaggle/input/mistral/pytorch/7b-instruct-v0.1-hf/1" # Adjust if your input name differs
        # Points to the folder containing your downloaded adapter checkpoint files
        ADAPTER_CHECKPOINT_PATH = "/kaggle/input/maya-adapter-checkpoint/checkpoint-2350" # Adjust folder/input name as needed
        ```

5.  **Install Libraries:** Run a notebook cell to install the required Python libraries (use compatible versions - you might need newer ones than your original fine-tuning if base `peft` or `transformers` versions caused issues loading the adapter; start with latest and troubleshoot if needed):
    ```python
    # Example: Use multi-step install if facing torch/torchvision issues
    # Step 1: Upgrade HF stack
    !pip install -q -U transformers datasets accelerate peft bitsandbytes trl huggingface_hub Jinja2 sentencepiece kaggle_secrets
    # Step 2: Install compatible PyTorch set (adjust cuXXX if needed)
    !pip install -q --upgrade torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
    # Step 3: Clear cache
    !pip cache purge -q
    print("Libraries installed/updated. Restarting session might be needed AFTER this cell if you encounter CUDA errors later.")
    # !! IMPORTANT: Manually restart via Run -> Restart Session if CUDA errors appear when loading model !!
    ```

6.  **Run the Bot Cell:** Execute the main notebook cell containing the `bot.py` code. Monitor the logs in the cell output. It should:
    *   Load the token.
    *   Load the base Mistral 7B model (quantized).
    *   Load the adapter onto the base model using `PeftModel`.
    *   Start polling Telegram.

## Why Not Use the Merged Model Files?

While the fine-tuning script *creates* merged files (e.g., in `Mayadevi-merged-final`), loading *these* specific files can sometimes lead to the fine-tuned personality feeling weak or lost. This is often due to small precision errors accumulating when combining the adapter with the already memory-saving 4-bit base model weights. Loading the base and adapter separately dynamically avoids this merging step and usually gives results closer to the pre-merge validation.

## Hardware Needs (Kaggle Context)

*   **GPU:** Kaggle T4 x2 or P100 (16GB VRAM) is generally required for loading Mistral 7B (4-bit) + adapter. Even then, loading can be tight. Ensure no other notebooks are consuming GPU resources on your account.
*   **RAM:** Standard Kaggle session RAM is usually sufficient.

## Contributing / Future

Make it's fine-tuning more efficient with more optimized and larger datasets.

## License

MIT License

Copyright (c) 2025 Spaghetti Sauce

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
