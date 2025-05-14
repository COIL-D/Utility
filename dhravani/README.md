# Dhravani Dataset Download Utility

This utility downloads and prepares the Language specific subset of the Dhravani dataset from Hugging Face. It supports downloading audio files, filtering by language, and packaging the results into a ZIP archive.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/COIL-D/Utility/blob/main/dhravani/unofficial_dataset_download_method.ipynb)

Additionally You can try these.

```
!huggingface-cli download coild/dhravani  --repo-type dataset --local-dir ./coild_dhravani/ 
```

OR

```
from huggingface_hub import snapshot_download
import os
import zipfile
import shutil

# Configuration
repo_id = "coild/dhravani"  # Replace with your username and dataset name
language = "te"                    # Language to download (e.g., "en" or "es")
output_zip = "te_dataset.zip"      # Output ZIP file name
token = "your_huggingface_token"   # Replace with your Hugging Face token (optional for public datasets)

# Create a temporary directory
temp_dir = "temp_download"
os.makedirs(temp_dir, exist_ok=True)

try:
    # Download the entire language directory
    snapshot_download(
        repo_id=repo_id,
        repo_type="dataset",
        local_dir=temp_dir,
        allow_patterns=f"{language}/*",  # Only download files in the en/ directory
        token=token,
        max_workers=8  # Increase parallel downloads (adjust based on your system)
    )

    # Create a ZIP file
    with zipfile.ZipFile(output_zip, "w", zipfile.ZIP_DEFLATED) as zipf:
        for root, _, files in os.walk(os.path.join(temp_dir, language)):
            for file in files:
                file_path = os.path.join(root, file)
                arcname = os.path.relpath(file_path, temp_dir)
                zipf.write(file_path, arcname)

    print(f"ZIP file created: {output_zip}")

finally:
    # Clean up temporary directory
    shutil.rmtree(temp_dir, ignore_errors=True)
```
