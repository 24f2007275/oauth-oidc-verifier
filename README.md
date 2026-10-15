# OAuth 2.0 / OIDC Token Verification Service

A small FastAPI service for the assigned RS256 JWT verification task.

## Endpoint

`POST /verify`

Request JSON:

```json
{"token": "<JWT string>"}
```

A valid token gets HTTP 200 with `valid`, `email`, `sub`, and `aud`. Invalid, expired, wrong-issuer, wrong-audience, or tampered tokens get HTTP 401 and `{"valid": false}`.

## Run locally

```bash
python -m venv .venv
source .venv/bin/activate       # Windows PowerShell: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000
```

Test the endpoint at `http://localhost:8000/docs` or with:

```bash
curl -X POST http://localhost:8000/verify \
  -H 'Content-Type: application/json' \
  -d '{"token":"not-a-real-token"}'
```

## Deploy on Render

1. Create a GitHub repository and upload `main.py`, `requirements.txt`, and `README.md` to the repository root.
2. In Render, choose **New → Web Service** and connect that repository.
3. Select Python as the runtime, set build command to `pip install -r requirements.txt`, and set start command to `uvicorn main:app --host 0.0.0.0 --port $PORT`.
4. Deploy. Submit the full public URL ending in `/verify`, e.g. `https://your-service.onrender.com/verify`.

The verifier only allows RS256, verifies the signature using the assignment's public key, checks the exact issuer and audience, and requires a non-expired `exp` claim.
