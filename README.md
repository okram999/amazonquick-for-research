# Agricultural research with Amazon Quick — a one-week walkthrough

This repository is everything you need to build a complete research workflow in
your own Amazon Quick instance. It follows one research week — *from weather
station to grower brief* — built on **AZMet** (the Arizona Meteorological
Network), a public weather API, using real data from 2021 through
September 30, 2026.

The worked example is cotton irrigation, but the pattern — ask a question in
plain language, ground the answer in your own data and a published method, and
show every step — applies to any research domain. Swap in your own dataset and
rulebook and the same five days apply.

> **Personas are illustrative.** Elena (irrigation researcher), Marcus
> (Extension agent) and Priya (research technician) are fictional. Only
> research and Extension staff use Quick; growers receive what it produces.

## The week

| Day | Theme | What you build | Folder |
|-----|-------|----------------|--------|
| **Monday** | Data | Upload a cleaned weather dataset and ask questions of it | [`monday-data/`](monday-data/) |
| **Tuesday** | Evidence | Put the lab's open-access papers in an S3 knowledge base | [`tuesday-literature/`](tuesday-literature/) |
| **Wednesday** | Analysis | A custom agent that calls a live API and applies a published method | [`wednesday-agent/`](wednesday-agent/) |
| **Thursday** | Sharing | Assemble a Space so the team works from the same context | [`thursday-spaces/`](thursday-spaces/) |
| **Friday** | Repeat | A scheduled flow that drafts a weekly advisory | [`friday-flow/`](friday-flow/) |

Each folder has its own `README.md` with step-by-step instructions.

## What you need

- An Amazon Quick account (the Professional or Enterprise tier covers agents,
  flows and knowledge bases). A free trial is enough to try the walkthrough.
- For Tuesday: an Amazon S3 bucket in the **same AWS account** as your Quick
  account, so the knowledge base needs only the admin-console grant and no
  bucket policy.
- No programming is required. The connectors are built from the OpenAPI
  specification files included here; the agent and flow are built in the Quick
  UI.

## Start here — create the Space first

Before Monday, create one **Space** to hold everything the week produces. Each
day then adds to this same Space, so by Thursday the team already has the data,
the library and the method in one place.

1. In Amazon Quick, go to **Spaces → Create space**.
2. Name it something like **"Ag Research — Cotton Water"** and give it a short
   description.
3. Leave it open in a tab. As you work through the week, add to it:
   - **Monday:** upload the weather CSV to the Space.
   - **Tuesday:** link the S3 knowledge base (the paper library) to the Space.
   - **Wednesday:** the custom agent uses this Space as its knowledge source.
   - **Thursday:** upload the Research report PDF; run the two prompts here.
   - **Friday:** save approved advisories back to the Space.

Creating the Space up front means you never have to go back and re-home files —
they accumulate in the right place as you go.

## Suggested order

Work through the days in order — each one builds on the last:

1. **Create the Space** (above) — the shared home for the whole week.
2. **Monday** gives you the dataset the whole week uses.
3. **Tuesday** adds the literature the analysis leans on.
4. **Wednesday** is the core: the custom agent and the AZMet connector.
5. **Thursday** combines Monday's data, Tuesday's library and Wednesday's
   method in one shared Space.
6. **Friday** reuses the Wednesday connector in a scheduled flow.

## Data and sources

All weather data are derived from the Arizona Meteorological Network (AZMet),
`api.azmet.arizona.edu`, which serves daily observations from January 1, 2021.
The literature and its licensing are listed in
[`tuesday-literature/SOURCES.md`](tuesday-literature/SOURCES.md).

This is a learning/workshop project, not affiliated with or endorsed by AZMet

## A note on the method

The cotton water-and-yield estimate is a **published FAO formula with stated
assumptions, not a validated yield model**. Read its results as a planning
range, not a forecast. The assumptions and limits are spelled out in the
Wednesday method card.

## License

This repository is released under the **Apache License 2.0** (see `LICENSE`).
The bundled papers in `tuesday-literature/papers/` remain under their own
licenses — see [`tuesday-literature/SOURCES.md`](tuesday-literature/SOURCES.md).
Amazon Quick, AWS service names and the AZMet network are the property of their
respective owners.
