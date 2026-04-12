# Flight Delay Propagation Modeling

This project models how flight delays propagate across an aircraft's daily sequence of flights using a discrete-time Markov chain. The workflow cleans and standardizes U.S. domestic on-time performance records, constructs per-aircraft operating-day chains, estimates transition probabilities between delay states, and computes the long-run stationary distribution.

## Project Objective

Airline operations are highly interconnected: one delayed leg can affect the next flight assigned to the same aircraft. This repository quantifies that propagation effect by:

- Assigning each flight to a delay-related state.

- Building ordered flight sequences (chains) per aircraft per operating day.
- Estimating the one-step transition matrix between states.
- Computing the stationary distribution to characterize long-run system behavior.

## Dataset

Source: BTS Marketing Carrier On-Time Performance data (July 2025)

- Public portal: https://www.transtats.bts.gov/tables.asp?QO_VQ=EFD&QO_anzr=Nv4yv0r
- Local file in this repo: `data/flight_delays.csv`

Core fields used include:

- Flight date and local schedule times (`FL_DATE`, `CRS_DEP_TIME`, `CRS_ARR_TIME`)
- Actual timing and delays (`DEP_TIME`, `ARR_TIME`, `DEP_DELAY`, `ARR_DELAY`)
- Operational outcomes (`CANCELLED`, `DIVERTED`)
- Aircraft identifier (`TAIL_NUM`)
- Routing metadata (`ORIGIN`, `DEST`)

## Methodology Overview

The notebook in `flight_delay_analysis.ipynb` follows this pipeline:

1. Data quality and missingness analysis
2. Time normalization (local timestamps -> UTC) for cross-timezone ordering
3. Operating-day definition and chain assignment by `TAIL_NUM`
4. State assignment for each flight
5. Transition matrix estimation with empirical frequencies
6. Network visualization of state transitions
7. Stationary distribution estimation via eigen decomposition

### State Definitions

- `0`: On-time (`ARR_DELAY < 15` minutes)
- `1`: Delayed (`ARR_DELAY >= 15` minutes)
- `2`: Significant delay (`ARR_DELAY >= 60` minutes)
- `3`: Extreme departure delay (`DEP_DELAY >= 120` minutes)
- `4`: Cancelled
- `5`: Diverted

## Repository Structure

```text
Flight-Delay-Propagation-Modeling/
├── flight_delay_analysis.ipynb   # Main analysis notebook
├── README.md                     # Project documentation
├── requirements.txt              # Python dependencies
└── data/
	└── flight_delays.csv         # Input dataset
```

## Setup

### 1. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Launch Jupyter

```bash
jupyter notebook
```

Open `flight_delay_analysis.ipynb` and run cells top-to-bottom.

## Outputs

The notebook produces:

- Missingness and distribution diagnostics
- Delay-time visualizations
- Chain-length distribution plots
- Markov transition matrix for delay states
- Directed graph of state transitions
- Stationary distribution summary and pie chart

## Interpretation Notes

- Transition probabilities are empirical and specific to the selected time window and filters.
- The stationary distribution is a model-based long-run equilibrium under the estimated transition dynamics.
- Results can vary across months, carriers, and network composition.
