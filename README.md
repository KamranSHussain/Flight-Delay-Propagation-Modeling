# Flight Delay Propagation Modeling

This project studies how delays carry from one flight leg to the next for the same aircraft. The core idea is to model each aircraft's operating day as a sequence of delay states, then estimate how likely it is to move between those states using a Markov chain.

The full-year 2024 analysis is the main reference point in this repo because the 2025 sample was affected by an unprecedented federal government shutdown period, which makes that window less useful for broad comparison or generalization.

## What This Repo Covers

The analysis is split into two notebooks:

- `july_25_delay_analysis.ipynb`: baseline analysis on the July 2025 dataset
- `2024_delay_analysis.ipynb`: full-year extension with monthly and airline-level comparisons

Both notebooks follow the same general pipeline:

1. Check data quality and missingness patterns
2. Standardize local times and convert schedule information to UTC for ordering
3. Define an operational day (5:00 AM to 5:00 AM)
4. Assign each flight to a delay state
5. Build per-aircraft, per-day flight chains
6. Estimate the one-step transition matrix
7. Compute and visualize stationary distributions

## Delay State Definitions

States are encoded as follows:

- `0`: On-time (`ARR_DELAY < 15`)
- `1`: Delayed (`ARR_DELAY >= 15`)
- `2`: Significant delay (`ARR_DELAY >= 60`)
- `3`: Extreme departure delay (`DEP_DELAY >= 120`)
- `4`: Cancelled flight
- `5`: Diverted flight

## Data

Source: BTS Marketing Carrier On-Time Performance data

- Public portal: https://www.transtats.bts.gov/tables.asp?QO_VQ=EFD&QO_anzr=Nv4yv0r
- Main local file: `data/flight_delays.csv`
- Full-year monthly files: `data/2024_data/*.csv`

Key fields used include:

- Flight date and schedule fields (`FL_DATE`, `CRS_DEP_TIME`, `CRS_ARR_TIME`)
- Actual timing and delays (`DEP_TIME`, `ARR_TIME`, `DEP_DELAY`, `ARR_DELAY`)
- Operational outcomes (`CANCELLED`, `DIVERTED`)
- Aircraft identifier (`TAIL_NUM`)
- Route metadata (`ORIGIN`, `DEST`)
- Carrier identifier (`OP_CARRIER_AIRLINE_ID`)

## Repository Structure

```text
Flight-Delay-Propagation-Modeling/
├── july_25_delay_analysis.ipynb
├── 2024_delay_analysis.ipynb
├── README.md
├── requirements.txt
└── data/
	├── flight_delays.csv
	└── 2024_data/
		├── 2024_jan_flights.csv
		├── ...
		└── 2024_dec_flights.csv
```

## Setup

1. Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Launch Jupyter:

```bash
jupyter notebook
```

Then run either notebook top-to-bottom depending on whether you want the baseline or full-year analysis.

## Outputs You Can Expect

- Missingness diagnostics and data-quality checks
- Departure and delay-time distribution plots
- Chain-length distributions by aircraft-day
- Transition matrices and transition network visualizations
- Stationary distribution summaries
- Monthly and airline comparison charts (in the 2024 notebook)

## Notes for Interpretation

- Transition probabilities are empirical and reflect the chosen filters and time period.
- Stationary distributions represent long-run equilibrium under the estimated transition matrix.
- Results can shift across seasons, network mix, and airline operations.
