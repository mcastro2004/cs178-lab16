# CS 178 — Lab 16: Serverless Image Processing

📖 **[Lab instructions](https://hackmd.io/@profmoore/cs178-lab16)**

Starter repo for Lab 16. Fork this repo, follow the lab guide above, and configure your bucket names in `app.py`.

## Quick start

**Your one job:** open `app.py` and replace the two placeholder bucket name strings near the top with your actual S3 bucket names (Exercise 1).

## Files

| File                             | Role                                       | Who edits it                                            |
| -------------------------------- | ------------------------------------------ | ------------------------------------------------------- |
| `app.py`                         | Flask web server                           | **You** — replace bucket name placeholders (Exercise 1) |
| `templates/index.html`           | Upload form + result display               | Pre-built                                               |
| `static/index.css`               | Styles for the web interface               | Pre-built                                               |
| `lambda_function.py`             | Lambda function: flips the image           | Pre-built — read and understand it                      |
| `lambda_function_rekognition.py` | Lambda function: flip + Rekognition labels | Stretch goal — fill in the `TODO`                       |
| `requirements.txt`               | Python dependencies for Flask app          | Pre-built                                               |
| `.github/workflows/deploy.yml`   | GitHub Actions deploy to EC2               | Pre-built                                               |

## Running locally

```bash
pip install -r requirements.txt
python3 app.py
# open http://localhost:8888 in your browser
```

On Windows, use `python app.py` if `python3` isn't recognized. If you have trouble running locally, GitHub Codespaces works too.

## Deploying to EC2

Make sure you've cloned this repo into `~/cs178-lab16` on your EC2 instance and set up the three GitHub Actions secrets (`EC2_HOST`, `EC2_USER`, `EC2_PRIVATE_KEY`) — see the lab instructions for details.

Then push to `main` and GitHub Actions handles the rest:

```bash
git add app.py
git commit -m "configure bucket names for lab 16"
git push
```

Visit `http://[your-ec2-ip]:8888` to verify. Watch the **Actions** tab on GitHub to confirm the deploy ran green before checking.

## Troubleshooting

| Symptom                                      | Likely cause                          | Fix                                                                                |
| -------------------------------------------- | ------------------------------------- | ---------------------------------------------------------------------------------- |
| Nothing appears in processed bucket          | Lambda timed out or ran out of memory | Check CloudWatch logs; set timeout to 15s and memory to 512 MB                     |
| `Runtime.ImportModuleError` in CloudWatch    | Pillow not in the zip                 | Re-package zip with `PIL/` folder at root level                                    |
| `KeyError: 'Records'` on test event          | Expected — code is healthy            | Move on, this is the good error                                                    |
| "Configurations overlap" when adding trigger | Stale S3 event notification           | Delete old notification in S3 → bucket → Properties → Event notifications          |
| App loads but images don't appear            | Bucket names wrong in `app.py`        | Double-check `SOURCE_BUCKET` and `PROCESSED_BUCKET` match your actual bucket names |
| Port 8888 not reachable on EC2               | Security group missing rule           | Add inbound TCP rule for port 8888 in EC2 security group                           |
