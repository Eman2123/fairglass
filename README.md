# FairGlass

### Proof-of-Fair AI Hiring on Midnight

FairGlass is a privacy-first hiring prototype that uses a **Midnight Compact smart contract** to cryptographically prove that an AI-assisted hiring decision followed an agreed fairness policy, without exposing candidate data.

> **Midnight Hackathon · August 2026**  
> **Team:** Eman · Lastos · sumap · Donalsien · Yashasvi

> **Important:** The AI scoring model is **simulated**. It is a deterministic Python function, not a trained ML model.

---

## Why FairGlass?

AI hiring systems can use attributes that a hiring policy explicitly forbids. FairGlass separates **decision-making from proof**:

- Fair scoring uses only allowed attributes.
- A biased scoring mode demonstrates a policy violation.
- The Midnight contract verifies whether the policy was followed.
- A successful verification produces a **fairness receipt**.
- Candidate information stays off-chain.

**FairGlass proves that a policy was followed, not that a candidate was hired.**

---

## Core Features

| Feature | Description |
| --- | --- |
| **Fair AI Scoring** | Scores candidates using skills and experience |
| **Policy Enforcement** | Rejects proofs when forbidden attributes affect a decision |
| **Privacy by Design** | Keeps candidate data and witness data off-chain |
| **Fairness Receipts** | Produces a verifiable receipt when the policy passes |
| **Commitment-Based IDs** | Uses fresh SHA-256 commitments instead of exposing candidate IDs |
| **Biased Model Demo** | Shows how forbidden attributes are detected |
| **Local Proof Flow** | Witness data is consumed by the local proof server |

---

## How It Works

```text
Candidate Data
      │
      ▼
┌───────────────────┐
│ Fair / Biased     │
│ Scoring Model     │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│ Fairness Policy   │
│ + Decision        │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│ Midnight Compact  │
│ Smart Contract    │
└─────────┬─────────┘
          │
      ┌───┴───┐
      ▼       ▼
     PASS    FAIL
      │       │
      ▼       ▼
  Receipt   No Receipt
```

The contract verifies the policy without storing the underlying candidate information.

---

## Privacy Model

> **Show proof, not data.**

### Public

- Policy hash
- Decision
- Timestamp
- Candidate ID commitment

### Private

- Candidate name
- Age
- Gender
- Skills and experience witness data
- Commitment nonce

Each receipt uses:

```text
idCommitment = SHA256(domain || nonce || candidateId)
```

A fresh 32-byte nonce is generated for every receipt. Therefore, repeated screenings of the same candidate produce unrelated commitments.

The nonce is returned to the employer as the commitment opening. It is never written on-chain or sent to the proof server.

---

## Demo

### 1. Start the backend

```bash
cd backend
python app.py
```

Backend: `http://localhost:5000`

### 2. Start the frontend

From the repository root:

```bash
python3 -m http.server 8080 --directory frontend
```

Frontend: `http://localhost:8080`

> **Do not open `frontend/index.html` directly with `file://`.** The frontend must be served over HTTP so browser requests to the backend work correctly.

### 3. Try the two models

**Fair Model**
- Uses skills and years of experience.
- The policy passes.
- A fairness receipt is issued.

**Biased Model**
- Intentionally uses age.
- Age is forbidden by the policy.
- The proof fails and no fairness receipt is issued.

Compare candidates `c4` and `c5` across both runs to see the effect of the forbidden attribute.

### Privacy page

With the app running, open:

```text
http://localhost:8080/privacy.html
```

Run the screening twice and compare commitments to inspect the hiding/binding behavior.

---

## Policy

**Allowed attributes**
- Skills
- Years of experience

**Forbidden attributes**
- Name
- Age
- Gender

All candidate data in `data/` is synthetic.

---

## Project Structure

```text
fairglass/
├── frontend/     # HTML/CSS/JS dashboard + receipt pages
├── backend/      # Flask service + proof bridge
├── scoring/      # Fair and biased scoring functions
├── contract/     # Midnight Compact policy contract
├── data/         # Synthetic candidate data
└── docs/         # Demo runbook and documentation
```

---

## Tech Stack

- **Midnight Compact** — policy verification
- **Python** — scoring and backend services
- **Flask** — backend API
- **HTML / CSS / JavaScript** — frontend
- **SHA-256** — candidate ID commitments
- **Synthetic JSON** — demo data

---

## Setup

Clone the repository:

```bash
git clone https://github.com/Eman2123/fairglass.git
cd fairglass
```

Create the backend environment:

```bash
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd ..
```

For Windows-specific instructions, see `backend/BACKEND_SETUP.md` if present.

For the Midnight contract, follow:

```text
contract/README.md
```

---

## Testing

Run the backend tests:

```bash
cd backend
python test_backend.py
```

---

## Honest Limitations

- The scoring model is a deterministic Python simulation, not a trained ML model.
- The commitment uses SHA-256 and is intended to provide computational hiding and binding.
- It is **not** a Pedersen commitment and provides no homomorphic properties.
- All demo candidate data is synthetic.

---

## Team

| Member | Role | Area |
| --- | --- | --- |
| **Eman** | Tech Lead + Frontend | `frontend/` |
| **Lastos** | Compact Engineer | `contract/` |
| **Donalsien (KADHACK)** | Integrator / Backend | `backend/`, `scoring/`, `contract/bridge/` |
| **sumap** | Frontend + Data | `frontend/`, `data/` |
| **Yashasvi** | Docs, Video + Submission | `docs/` |

---

## Documentation

- [Demo Runbook](docs/DEMO_RUNBOOK.md)
- [Backend Setup](backend/BACKEND_SETUP.md)
- [Compact Contract](contract/README.md)
- [Scoring Notes](scoring/README.md)

---

## AI Disclosure

The team used AI coding assistants, including Claude Code, during the hackathon. Architecture, product decisions, and integration decisions were made by the team.

---

## License

This project was created as a hackathon prototype.
