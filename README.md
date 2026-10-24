# Travel Reimbursement Approval Agent

Assignment submission. The agent goes through travel expense claims, checks them against the company travel policy
and decides whether to approve, partially approve, reject or send each one for manual review.

The whole thing lives in `ankitsingh.ipynb`: code, outputs, dashboard and design notes. This file just covers how to
get it running.

## Setup

You need Python 3.10+ and a Groq API key. The free tier is enough, and you can get a key at console.groq.com.

```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

On Mac/Linux, activate with `source .venv/bin/activate` instead.

Then copy `.env.example` to `.env` and put your key in it. `GROQ_MODEL` is optional; I used `openai/gpt-oss-120b`.

## Running

```
jupyter notebook ankitsingh.ipynb
```

and do Restart & Run All. A full run takes a minute or two because each claim goes through the LLM.

No key? It still runs. It falls back to the rule-based checks, gives the same decisions, and doesn't use the LLM.

## How it works

The LLM (through LangChain) gets the claim and decides which checks to run: policy lookup, eligibility, receipts,
per-diem limits, approval threshold and the 30-day window. The checks are normal Python functions, so the amounts are
always calculated in code and never by the model.

Before a decision is accepted I compare it with what the rules alone would decide. If they don't match, or the model
isn't confident, the claim goes to manual review. That seemed like the safer default for anything involving money.

## Results

- CLM-001: approved, 1110
- CLM-002: rejected (spa + minibar)
- CLM-003: partially approved, 840 paid / 100 deducted because the hotel was over $200/night
- CLM-004: manual review (business class flight, missing hotel receipt, over $2000)
- CLM-005: manual review (no receipt, and it's not clear whether the meal limit applies to a dinner for 4)

Tests are in section 9 of the notebook and run with everything else.
