# Writeup: PCTF - Word Sea Adventures

**Category:** Forensics / Steganography  
**Tools Used:** `binwalk`, `steghide`, `strings`, `cat`  
**Challenge Prompt:** The challenge file was a Word document named `word_sea_adventures.docx` containing a story about SpongeBob.

---

## 1. Initial Analysis & The Critical Hint

The first step was to open the document. While it contained a fun SpongeBob story, the text itself provided a crucial forensic clue:

> “Word documents share a similar secret: although they appear as a single file, they are really like little “zipped-up” bottles of fun.”

This is a direct reference to the Office Open XML (OOXML) format. Files like `.docx`, `.xlsx`, and `.pptx` are essentially ZIP archives containing XML files for the document's structure and content, a hint to treat the file as an archive.

---

## 2. File Structure Analysis with Binwalk

Instead of viewing the file as a document, we treated it as a binary container. Using `binwalk`, a tool for analyzing and extracting embedded files, we inspected its internal structure.

Command:
```bash
binwalk word_sea_adventures.docx
```

Findings:  
The output confirmed the ZIP archive structure. However, among the standard files, three images with non-standard, manually inserted names stood out:
- `crab.jpg`
- `sponge.jpg`
- `squid.jpg`

In a typical Word document, images are automatically named like `image1.png` or `image2.jpeg`. These custom names were a clear indicator that they were the focus of the challenge.

---

## 3. Extracting the Artifacts

To retrieve these suspicious images, we used `binwalk` to extract all embedded files.

Command:
```bash
binwalk --extract --dd=".*" word_sea_adventures.docx
```

This command created a directory (typically `_word_sea_adventures.docx.extracted`) containing the entire contents of the ZIP archive. Navigating to the `word/media/` folder confirmed the presence of our three custom-named images.

---

## 4. Steganography Analysis

With the images extracted, the next step was to find the hidden flag. We started with a simple check for cleartext data.

Method 1: Strings (Failed)  
Command:
```bash
strings squid.jpg | grep "pctf"
```
This returned no results, confirming the flag was not hidden in plain text within the image.

Method 2: Steghide (Success)  
We then turned to `steghide`, a common tool for hiding data in JPEG and BMP files. We first used the `info` command to check each image for embedded data without extracting it.

Commands & Findings:
```bash
steghide info crab.jpg
# Output: Embedded file "decoy2.txt" (encrypted with Rijndael-128, CBC)

steghide info sponge.jpg
# Output: Embedded file "decoy1.txt" (encrypted with Rijndael-128, CBC)

steghide info squid.jpg
# Output: Embedded file "flag.txt" (106.0 Byte)
```

This was the breakthrough. `crab.jpg` and `sponge.jpg` contained encrypted decoy files, but `squid.jpg` contained `flag.txt`. When prompted for a passphrase, pressing Enter (using an empty passphrase) allowed extraction.

---

## 5. Flag Capture

We proceeded to extract the payload from `squid.jpg`.

Command:
```bash
steghide extract -sf squid.jpg
```

After confirming the extraction with an empty passphrase, the file `flag.txt` was written to the current directory.

Final step — read the file:
```bash
cat flag.txt
```

Flag Found:
```
pctf{st3g0_squ1dward_d0cument}
```

---

## Conclusion

This challenge was a classic example of "forensics by the book":
1. Heed the hint in the document to treat the DOCX as an archive.
2. Use `binwalk` to inspect and extract embedded files.
3. Analyze extracted images with steganography tools — `steghide` revealed the embedded `flag.txt` (empty passphrase).

The flag `pctf{st3g0_squ1dward_d0cument}` confirms the steganography route in a Squidward-themed document.
