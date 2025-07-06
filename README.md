# 🤖 MLGuardian AI Model – Log-Based Cyberattack Classification

This is the AI engine behind the **MLGuardian** cyber attack detection system. It parses and labels HTTP logs, then trains a BERT-based model to classify cyberattacks like SQLi, XSS, and more.

📦 This module is used inside the [MLGuardian Dashboard](../dashboard) to provide real-time AI-powered predictions.

---

## 📁 Project Structure


ml_model/
├── log_processor.py # Parses & labels logs from scanners
├── model_fine_tuning.py # Fine-tunes BERT for classification
├── balanced_df_labeled.csv # Final dataset used for training
├── id2label.json # Maps model outputs to class names
├── checkpoint/ # (Optional) saved model folder



---

## 💡 What This Module Does

### ✅ 1. Preprocessing & Labeling

The `log_processor.py` script:

- Parses Apache/Nginx-style logs from tools like:
  - [Acunetix](https://www.acunetix.com)
  - W3AF
  - Netsparker
- Combines `url + referrer` for detection
- Labels each request using regex patterns:
  - `sql_injection`
  - `xss`
  - `command_injection`
  - `path_traversal`
  - `normal`
- Balances the dataset by downsampling `normal` traffic
- Outputs CSV files used for training

---

### 🤖 2. Model Training (BERT Fine-Tuning)

The `model_fine_tuning.py` script:

- Uses HuggingFace Transformers with `bert-base-uncased`
- Tokenizes inputs as:
  ```text
  <url> [SEP] <referrer>
  ```
- Trains a multi-class classifier

- Saves model checkpoint and config

- Evaluates on validation/test set


🧾 Classes & Labeling
Output labels:

normal

sql_injection

xss

command_injection

path_traversal

Saved in: id2label.json for easy use during inference.



| URL                          | Referrer                                     | Label              |
| ---------------------------- | -------------------------------------------- | ------------------ |
| /index.html                  | [https://google.com](https://google.com)     | normal             |
| /login?user=admin' OR 1=1    | [https://evil.com](https://evil.com)         | sql\_injection     |
| /ping?ip=8.8.8.8 && rm -rf / | [https://admin.biz](https://admin.biz)       | command\_injection |
| /profile?bio=\<svg/onload=>  | [https://fake.social](https://fake.social)   | xss                |
| /read?file=../../etc/passwd  | [https://testhost.com](https://testhost.com) | path\_traversal    |






