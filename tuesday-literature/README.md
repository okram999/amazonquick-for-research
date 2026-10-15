# Tuesday — Evidence

**Goal:** put the lab's open-access papers in an Amazon S3 knowledge base so
Quick Research (and the Wednesday agent) can cite them.

## The papers

`papers/` holds four open-access documents:

- `fao56_crop_evapotranspiration_1998.pdf` — FAO-56, the crop ET method (1998).
- `fao56_crop_evapotranspiration_2025_revised.pdf` — the 2025 revision.
- `fao66_crop_yield_response_to_water.pdf` — FAO-66, which restates the FAO-33
  yield-response method and the cotton Ky values.
- `agronomy_2025_flood_deficit_cotton.pdf` — a 2025 Arizona flood-irrigated
  deficit trial (MDPI, open access).

**Thorp et al. (2020) is not bundled** — it is an Elsevier-licensed article, so
it is listed by DOI in [`SOURCES.md`](SOURCES.md) for you to download your own
copy. This follows the rule the talk uses for the lab library: only
open-access or lab-authored PDFs go in; no licensed journal articles.

## Steps

1. **Upload the PDFs to S3.** Put the four files (and your own copy of Thorp
   2020, if you have redistribution rights) in an S3 bucket in the **same AWS
   account** as your Quick account. Use an `s3://bucket-name` style URI — not a
   console link or an object ARN.
2. **Grant Quick read access.** In the Quick admin console: **Permissions → AWS
   resources → Amazon S3 → Select S3 buckets** and tick your bucket. For a
   same-account bucket with default encryption and no restrictive policy, no
   bucket policy is needed — Quick gets read-only access from the grant alone.
3. **Create the knowledge base** from that S3 location and let it index.
4. **Link the knowledge base to a Space** (see Thursday) so the team and the
   agents can search it.

## Cross-account or KMS buckets

If the bucket is in a *different* AWS account, add a bucket policy granting the
Quick service role (`aws-quicksight-service-role-v0`) `s3:GetObject`,
`s3:GetObjectVersion`, `s3:ListBucket`, `s3:ListBucketVersions` and
`s3:GetBucketLocation` on **both** the bucket ARN and the bucket ARN with `/*`.
If the bucket uses a customer-managed KMS key, add a key policy as well. For a
same-account bucket you can ignore both.

## Use it

With the knowledge base linked, Quick Research can draw on these papers. A good
test question:

> What does published field research say about deficit irrigation of cotton in
> the Arizona low desert, and when in the season does a water shortfall cost
> the most yield?

A sound report should surface Thorp 2020 and the 2025 Agronomy trial, and note
that full irrigation was needed from peak bloom onward.
