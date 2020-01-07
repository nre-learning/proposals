# NRE Labs Technical Re-Launch Plan

We're re-launching the NRE Labs site in early 2020, and as a result, there are a bunch of technical things
that we need to do to pull this off. There will be other activities that will need to take place for the re-launch
such as marketing and community-related objectives. This document will focus exclusively on the technical execution
plan for the relaunch.

Here's a summary of what we're launching:

- Redesigned web site that fully integrates existing blog, pages, and NRE Labs application
- Redesigned `antidote-web` including widgetization, mobile support, performance improvements, and UX
- New, simpler domain - https://nrelabs.io.
- New, simpler, easier to follow documentation focused entirely on the NRE Labs curriculum - https://docs.nrelabs.io

## Execution Plan

A key component to this execution plan is that we'll be building everything on a new domain, `nrelabs.io`.
This will give us a chance to build everything in a safe place before we decide to go live, and will allow us to
avoid all kinds of messiness in trying to ensure the old site isn't disrupted during the final stages of
development. For the most part, the old site will be left alone, until we're ready to go with the new site,
at which point we'll add redirects from the old domain to the new one.

### Phase 1 - Preparation

- [x] Deploy new docs PoC at https://docs.nrelabs.io
- [x] Stand up new site at https://nrelabs.io
- [ ] Kick off 0.5.0 platform release cycle
- [ ] Kick off 1.0.1 curriculum release cycle (Fix existing issues, Check compatibility w/0.5.0, any new lessons)
- [ ] Finish new docs
- [ ] Move existing deployment to new `prod-old` namespace
- [ ] Get webssh2 changes merged upstream or into an nre-learning fork
- [ ] Add webssh2 image to platform deployment once selfmedicate is configured properly with an ingress
- [ ] Finish webssh2 deployment in selfmedicate https://github.com/nre-learning/antidote-selfmedicate/pull/69
- [ ] Implement cloudflare maintenance page https://github.com/nre-learning/antidote-ops/issues/16
- [ ] Merge remaining Bitovi work to `antidote-web` and `nre-blog`
- [ ] Deploy latest platform code to nrelabs.io and test. Work out any issues.
- [ ] **Finish development** for Antidote 0.5.0 and NRE Labs 1.0.1 **before continuing**
- [ ] Create 0.5.0 release of Antidote
- [ ] Create Curriculum 1.0.1 release (in parallel w/0.5.0)
- [ ] Deploy releases to `prod` and kick off formal testing on new domain.
- [ ] Hold a formal "go/no-go" one week prior to launch

### Phase 2 - Go-Live

- [ ] Add redirects via cloudflare (all are 302 for the time being)
  - `labs.networkreliability.engineering` -> `nrelabs.io`
  - `networkreliability.engineering` -> `nrelabs.io`
  - `community.networkreliability.engineering` -> `discuss.nrelabs.io`
- [ ] Testing

### Phase 3 - Clean-Up

- [ ] Convert new nrelabs.io cloudflare stuff to terraform
- [ ] Move old domain underneath new cloudflare account as well
- [ ] Update any links on Juniper or NRE Labs properties that use the old domain
- [ ] Downgrade netlify plan if possible
- [ ] Fix nightly builds and PTR