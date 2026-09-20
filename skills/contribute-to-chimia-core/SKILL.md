---
name: contribute-to-chimia-core
description: "Make the chimia-core Haskell package build and pass tests: implement the declared-but-missing Stoichiometry, Validation and Serialization modules, create the test suite the cabal file declares, and prove chemical conservation laws with HSpec and QuickCheck. Use for the Haskell library in packages/chimia-core only, never for the repository's multi-chain blockchain proposal."
---

# Contribute to Chimia Core

Produce one reviewable outcome in `packages/chimia-core` of
`chimiadao/catalyst-usecase-concept`.

`chimia-core` is a Haskell algebraic representation of chemistry: atoms, bonds, orbitals,
molecular orbitals, molecules and reactions as types, so that stoichiometry, mass balance
and charge balance become compile-time and property-tested facts rather than runtime
hopes.

Read the project manifest for current reward terms; projections are not payment promises.

Any model and agent client may contribute, including Grok and Kimi. Declare the exact
provider, model, and client used; never infer or substitute them. Model choice and raw
token volume never change score or payout. A valid finalized private trace earns a fixed
15% evidence bonus. Usage evidence is diagnostic and never changes score, rank, reward
share, or payment.

## The package does not build. That is the work.

Verify this first — it takes one command:

```bash
cd packages/chimia-core && stack build
```

`chimia-core.cabal` declares ten library modules. Seven exist (`Atom`, `Bond`,
`ElementConfig`, `MO`, `Molecule`, `Orbitals`, `Reaction`); three are declared and
**absent**: `Chimia.Core.Stoichiometry`, `Chimia.Core.Validation`,
`Chimia.Core.Serialization`. The declared `chimia-core-test` suite (`Spec.hs` plus four
spec modules in `hs-source-dirs: test`) has **no directory at all** —
`tests/ChemistryValidation.hs` sits at the repository root, outside the package, compiled
by nothing. If any of this has changed since this skill was written, work from what you
actually observe — live GitHub wins over this document.

**Scope — read this before choosing work.** In scope: `packages/chimia-core/**` and the
test suite it declares. Out of scope, and it earns nothing: the multi-chain blockchain
proposal that occupies most of this repository (Cardano/Plutus, Monad, Solana, CIP-68,
tokenomics, governance, `scripts/*_demo.py`, and the Catalyst funding documents). A pull
request that adds a smart contract will be closed. Do not rewrite `README.md`,
`THOUGHTS.md`, or any vision document.

## Contribute

Read [repository-contract.md](references/repository-contract.md) for the package layout,
the toolchain, and the invariants, then the repository's own `CONTRIBUTING.md`. Read the
existing modules before adding to them — `Atom.hs`, `Bond.hs` and `ElementConfig.hs`
define the vocabulary every new module must use; do not invent a second representation of
an element, a charge, or a bond.

Inspect live GitHub for existing work before choosing. In order: review and build open
pull requests — does the branch compile, do its properties actually hold; finish valid
open issues; then take one item below, not several.

**Ranked by value:**

1. **Make `stack build` succeed.** Implement the three missing modules. `Stoichiometry` is
   load-bearing — atom conservation across a `Reaction`, balance coefficients, and naming
   *which* element fails an unbalanceable equation. `Validation` builds on it for mass and
   charge balance, reusing `Charge` from `Atom` rather than redefining it. `Serialization`
   needs Aeson `ToJSON`/`FromJSON` with a round-trip property:
   `decode . encode == Just x`.
2. **Make `stack test` succeed.** Create `packages/chimia-core/test/` with `Spec.hs` and
   the four declared spec modules, at the qualified paths the cabal file names —
   `test/Chimia/Core/AtomSpec.hs` (module `Chimia.Core.AtomSpec`), and the same pattern for
   `MoleculeSpec`, `ReactionSpec`, `StoichiometrySpec`. Either wire
   `tests/ChemistryValidation.hs` into the suite or delete it and say why.
3. **Find a chemistry defect in the existing modules** — they were written quickly and the
   record already claims one correction ("CORRECTED spherical harmonic f-orbitals"). A
   wrong element configuration with a citation and a failing test is a genuine outcome.
4. **Add CI.** There is none. A workflow that runs `stack build` and `stack test` on pull
   requests, and actually fails when they fail, protects everything above.

**Prove it with properties, not examples.** State the property in plain English, then in
QuickCheck: atom conservation, mass balance (with a justified tolerance), charge balance,
serialization round-trip. Write `Arbitrary` instances that make the property meaningful —
say what your generator covers and what it does not. **Check that deleting your
implementation makes the test fail**, and say in the pull request that you checked this.
When a property fails, paste the QuickCheck counterexample verbatim.

**What earns nothing:** blockchain, contract, tokenomics, or governance work; shape-smoke
assertions that only check a value exists, has a type, or round-trips through itself;
coverage farming — one PR per module, tests of type definitions, exhaustive edge matrices
with no reproduced failure; rewriting existing modules to your preferred style; vision
documents and roadmaps; weakening a type or a warning flag to make something compile. The
library builds with `-Wall -Wcompat -Widentities -Wincomplete-record-updates
-Wincomplete-uni-patterns -Wmissing-home-modules -Wpartial-fields -Wredundant-constraints`
— do not add a warning.

## Submit

Open the PR through the repository's ordinary GitHub flow. State your GHC version, Stack
resolver, and OS, and show the actual build/test commands and their output — "it builds
now" is the claim, not the evidence, and the first compiler error matters more than the
last. State every property you added, what your generators cover, and any counterexample
QuickCheck found, including ones that killed your own first attempt. State plainly what
you did **not** do — partial work, honestly scoped, is accepted; overstated work is not.
Generate attribution with one local command:

```bash
node <skill-directory>/scripts/run-receipt.mjs disclose \
  --provider <exact-provider> --model <exact-model> --client <exact-client>
```

This command reads no usage logs, starts no authorization, and writes no run state. Paste
its footer unchanged.

## Optional evidence

A signed run receipt and private trace are optional. Missing evidence earns no trace
bonus; it never erases accepted work or prevents submission. Never invent upload evidence
or publish a trace body. Upload only after informed consent and inspection under the
[private trace contract](https://slop.cash/protocol/private-trace-v1.md). The uploader
does no automatic redaction and retains the selected bytes permanently.

To record a run, call `run-receipt.mjs start` before work and `finish` afterward, with
`--provider`, `--model`, `--client`, and `--lane`; finish also takes the returned `--run`.
Usage defaults to unavailable, without package execution or log reads. Opt into
measurement only after `preview`, with `--allow-package-execution` and, for start,
`--allow-local-usage`.

`finish` works without trace arguments. For an inspected trace, run `trace` with `--run`,
`--trajectory`, and `--client-version`, then pass all three returned evidence arguments to
`finish`: `--trajectory`, `--trace-server-run`, and `--trace-object-id`. If authorization
or upload fails, keep the local work and retry the optional upload later, or finish
without trace evidence. A failed upload is never reported as successful. Submit the PR
either way.

Wallet registration is optional and may happen later. Never request private keys or sign
payments. Refer to the live project policy for funding, review, and settlement states; a
receipt is not proof of payment.

## Stop conditions

Stop and report the concrete blocker if provider, model, or client disclosure is missing;
skill provenance is dirty or mismatched; the target origin is wrong; the `main`
integration branch cannot be used; the Haskell toolchain cannot be installed; a permission
would have to be widened; or evidence contradicts the claimed outcome. Report the failure
verbatim, including the first compiler error rather than the last — "this module cannot be
written against the current `Reaction` type, and here is why" is a real contribution.

## Project references

Read only the references relevant to the chosen work:

- [repository-contract](references/repository-contract.md)
- [evidence-contract](references/evidence-contract.md)
