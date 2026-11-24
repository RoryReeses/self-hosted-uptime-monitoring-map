# Self-Hosted Uptime Monitoring Map

> **Decision:** Use this map to separate endpoint probes, dead-man checks, public status, and incident operations before choosing components; skip it if you want repository popularity to stand in for an outage-path test.
>
> **For:** solo operators, home-lab maintainers, and small SRE teams  ·  **Not for:** buyers seeking a hosted-service ranking or an untested production guarantee

This is a non-commercial, source-traceable decision guide. It normalizes upstream declarations; it does not claim that repository reachability proves functionality, security, maintenance, or fitness.

**Start here:** [Download the service map](assets/availability-layer-map.csv) — twelve projects, located upstream evidence, and explicit unknowns.

## Choose by workload

- Want a broad home-lab UI: start with Uptime Kuma, then test notification delivery and database restore.
- Want versioned checks: compare Gatus with Monika; the former is a service, the latter is a CLI runner.
- Want only cron/dead-man coverage: use Healthchecks rather than forcing a pull monitor into that job.
- Already run Prometheus: Blackbox Exporter supplies probe metrics but not incident communication.
- Need an integrated operations suite: OneUptime spans telemetry, on-call, and status pages, at a much larger footprint.

## Trace the outage path

Place the detector outside the failure domain. A job heartbeat, pull probe and public status page answer different questions.

## Detection and communication layers

- **Detection with an operator UI** — Uptime Kuma, Statping-ng, and Vigil combine probes with an operator-facing surface; verify their notification and state-backup paths independently.
- **Checks as code** — Gatus and Monika suit reviewable definitions; Upptime additionally couples scheduling and publishing to GitHub Actions and Pages.
- **Narrow components** — Healthchecks owns pushed job heartbeats, Blackbox Exporter exposes probe metrics, and Cachet publishes status; none alone replaces the other two roles.
- **Incident platforms** — openstatus and OneUptime add communication or incident operations; confirm which self-hosted components and edition-specific features are actually required.

## Service map

| Project | Role | Probe and heartbeat ownership | Alert and communication surface | Deployment prerequisites |
|---|---|---|---|---|
| Uptime Kuma | all-in-one monitor | - Monitoring uptime for HTTP(s) / TCP / HTTP(s) Keyword / HTTP(s) Json Query / Websocket / Ping / DNS Record / Push / Steam Game Server / Docker Containers | Unknown in the frozen upstream README | - Monitoring uptime for HTTP(s) / TCP / HTTP(s) Keyword / HTTP(s) Json Query / Websocket / Ping / DNS Record / Push / Steam Game Server / Docker Containers |
| Gatus | configuration-as-code monitor | Gatus is a developer-oriented health dashboard that gives you the ability to monitor your services using HTTP, ICMP, TCP, and even DNS queries as well as evalua… | checks can be paired with alerting via Slack, Teams, PagerDuty, Discord, Twilio and many more. | You can also use Docker Hub if you prefer: console docker run -p 8080:8080 --name gatus twinproduction/gatus:stable |
| Statping-ng | monitor plus status page | Unknown in the frozen upstream README | Statping-ng strives to remain future-proof and remain intact if a failure is created. Your Statping-ng service should not be running on the same instance you're… | Statping-ng is a very lightweight application and is available for Linux, Mac, and Windows. The Docker image is only ~20Mb so you know that this application won… |
| Healthchecks | heartbeat monitor | Healthchecks is a cron job monitoring service. It listens for HTTP requests and email messages ("pings") from your cron jobs and scheduled tasks ("checks"). Whe… | When a ping does not arrive on time, Healthchecks sends out alerts. | and pre-built Docker images are available. |
| Upptime | Git-hosted monitor | I built Upptime because, like many developers, I needed an uptime monitor and status page for my startup Pabio that was affordable, flexible, and completely und… | - Slack notifications are sent on updates | Unknown in the frozen upstream README |
| Cachet | status publisher | Cachet, the open source self-hosted status page system. | Cachet, the open source self-hosted status page system. | - A supported database: MariaDB, MySQL, PostgreSQL or SQLite |
| Vigil | monitor plus status page | HTTP / TCP / SSH / ICMP services: Vigil frequently probes an HTTP, TCP, SSH or ICMP target and checks for reachability | Microservices Status Page. Monitors a distributed infrastructure and sends alerts (Slack, SMS, etc.). | Unknown in the frozen upstream README |
| Cabot | historical alerting monitor | Cabot is a free, open-source, self-hosted infrastructure monitoring platform that provides some of the best features of PagerDuty, Server Density, Pingdom and N… | It provides a web interface that allows you to monitor services (e.g. "Stage Redis server", "Production ElasticSearch cluster") and send telephone, sms or hipch… | Cabot is written in Python and uses Django, Bootstrap, Font Awesome and a whole host of other goodies under the hood. |
| openstatus | monitor and incident platform | Unknown in the frozen upstream README | Monitors, status pages, and notification channels are declared in YAML or Terraform, applied from the CLI or CI, and operable from Claude, ChatGPT, or Cursor ov… | - Open source & self-hostable — AGPL-3.0, private-locations run in a single 8.5MB Docker image |
| Monika | synthetic-check CLI | Monika is a command line application for synthetic monitoring. The name Monika stands for "Monitoring Berkala", which means "periodic monitoring" in the Indones… | Unknown in the frozen upstream README | Monika is a Node.js application written in TypeScript using the oclif framework. It was developed on node 20 (LTS), and npm v10. |
| Blackbox Exporter | probe exporter | will return metrics for a HTTP probe against google.com. The probesuccess metric indicates if the probe succeeded. Adding a debug=true parameter will return deb… | Unknown in the frozen upstream README | Images are published to [Quay][quay] (quay.io/prometheus/blackbox-exporter), [Docker Hub][hub] (prom/blackbox-exporter) and the [GitHub Container Registry][ghcr… |
| OneUptime | observability and incident suite | Unknown in the frozen upstream README | OneUptime is an open-source observability platform. It checks that your websites, APIs and servers work, collects the logs, metrics and traces your apps send, p… | You need a Linux server with at least 8 GB of memory. On a new Ubuntu, Debian or RHEL-based server, one command installs Docker, Git and Node.js where they are … |

Values are short excerpts from the frozen upstream README, not feature-test scores. The CSV retains the located evidence for each cell. “Unknown” means the retrieved document did not establish that axis; it is not a claim that the feature is absent.

## Assemble the minimal stack

A practical small stack can be Gatus or Uptime Kuma for probes, Healthchecks for jobs, and Cachet only when public communication must be separated. Prometheus users can instead scrape Blackbox Exporter and route alerts through their existing alert pipeline. Do not deploy two schedulers merely because both can issue HTTP requests.

## Adoption notes by project

### Uptime Kuma

- **Choose it for:** Home labs wanting a broad graphical default.
- **License at the frozen source:** [MIT](https://raw.githubusercontent.com/louislam/uptime-kuma/b0cd0f1daf708a6c04f074c925a20e0229e80258/LICENSE).
- **Adoption test:** Test notification delivery and restore the instance database before depending on it.
- **Source:** [louislam/uptime-kuma](https://github.com/louislam/uptime-kuma).

### Gatus

- **Choose it for:** Operators who review checks in version control.
- **License at the frozen source:** [Apache-2.0](https://raw.githubusercontent.com/TwiN/gatus/655e6f0dc0aca8de5b5018e8e521912fffc2c576/LICENSE).
- **Adoption test:** Suites and remote instances are marked alpha or experimental upstream; do not make them a production requirement without a targeted test.
- **Source:** [TwiN/gatus](https://github.com/TwiN/gatus).

### Statping-ng

- **Choose it for:** A compact combined monitoring and public-status surface.
- **License at the frozen source:** [GPL-3.0](https://raw.githubusercontent.com/statping-ng/statping-ng/c02149f0a5f67fec1e60cfa3537478cd50899b15/LICENSE).
- **Adoption test:** The mobile app is not maintained by the statping-ng project; test only the delivery clients you plan to support.
- **Source:** [statping-ng/statping-ng](https://github.com/statping-ng/statping-ng).

### Healthchecks

- **Choose it for:** Jobs that report success instead of accepting probes.
- **License at the frozen source:** [BSD-3-Clause](https://raw.githubusercontent.com/healthchecks/healthchecks/ac59dec93c5179cdff471894704f360d6ee5e02a/LICENSE).
- **Adoption test:** Back up the database and test late, duplicate, and missing-ping semantics with the real scheduler.
- **Source:** [healthchecks/healthchecks](https://github.com/healthchecks/healthchecks).

### Upptime

- **Choose it for:** Teams accepting GitHub as both scheduler and public record.
- **License at the frozen source:** [MIT](https://raw.githubusercontent.com/upptime/upptime/461c367dc189d2e181bfe93b88ac48b3147c32f7/LICENSE).
- **Adoption test:** Actions quotas, repository visibility, and provider outage coupling must be acceptable.
- **Source:** [upptime/upptime](https://github.com/upptime/upptime).

### Cachet

- **Choose it for:** Teams that already have a detector and need a public status page.
- **License at the frozen source:** [Cachet custom source-available license; standalone redistribution restricted](https://raw.githubusercontent.com/cachethq/cachet/873e8223f611e9aaaae8a1122959f6d062b51e35/LICENSE.md).
- **Adoption test:** This is a status communication component. The frozen Cachet license restricts standalone redistribution; assess the exact release and packaging terms rather than assuming BSD licensing.
- **Source:** [cachethq/cachet](https://github.com/cachethq/cachet).

### Vigil

- **Choose it for:** Operators wanting one compact probe-to-status path.
- **License at the frozen source:** [MPL-2.0](https://raw.githubusercontent.com/valeriansaliou/vigil/ff91dcf158da72546d74947bfeb50c77489d7f2f/LICENSE.md).
- **Adoption test:** Confirm the current notifier set and persistence/restore procedure; compact scope is not full incident management.
- **Source:** [valeriansaliou/vigil](https://github.com/valeriansaliou/vigil).

### Cabot

- **Choose it for:** Readers studying an established alerting design, not selecting a default.
- **License at the frozen source:** [MIT](https://raw.githubusercontent.com/arachnys/cabot/eb0b3544f8c8ab2dee4643df191da346a941734f/LICENSE).
- **Adoption test:** Upstream says it is not actively maintained and asks for maintainers; do not shortlist it for new production use without a maintained fork and security review.
- **Source:** [arachnys/cabot](https://github.com/arachnys/cabot).

### openstatus

- **Choose it for:** Teams wanting monitors and status communication under one API.
- **License at the frozen source:** [AGPL-3.0](https://raw.githubusercontent.com/openstatusHQ/openstatus/9201d65119690517789f3f3ebcd43c7760114248/LICENSE).
- **Adoption test:** Separate managed-only/global-probe claims from the components available in a self-hosted installation.
- **Source:** [openstatusHQ/openstatus](https://github.com/openstatusHQ/openstatus).

### Monika

- **Choose it for:** Teams wanting a small runner in CI or an existing scheduler.
- **License at the frozen source:** [MIT](https://raw.githubusercontent.com/hyperjumptech/monika/4599802a3bcf23070579b6d5e4ac95ab33d553de/LICENSE.txt).
- **Adoption test:** It is a runner rather than a durable incident system; supply scheduling, history, and escalation elsewhere.
- **Source:** [hyperjumptech/monika](https://github.com/hyperjumptech/monika).

### Blackbox Exporter

- **Choose it for:** Prometheus operators who need black-box metrics.
- **License at the frozen source:** [Apache-2.0](https://raw.githubusercontent.com/prometheus/blackbox_exporter/ab759898ee9eee75f577691be4595c99e6cba577/LICENSE).
- **Adoption test:** It supplies metrics, not alert routing or a status page; timeout and network-location semantics belong to the Prometheus design.
- **Source:** [prometheus/blackbox_exporter](https://github.com/prometheus/blackbox_exporter).

### OneUptime

- **Choose it for:** Teams deliberately consolidating several operations layers.
- **License at the frozen source:** [Apache-2.0 community code plus separately licensed ee directory](https://raw.githubusercontent.com/OneUptime/oneuptime/6b92c10bef6fb3f3d5a4de95f5f58786b0f0ea47/LICENSE).
- **Adoption test:** Upstream states an 8 GB minimum for its quick self-host path; the ee directory has a separate enterprise license and must not be described as community functionality.
- **Source:** [OneUptime/oneuptime](https://github.com/OneUptime/oneuptime).

## Prove the outage path

1. Locate a probe outside the monitored host and record the expected failure signal.
2. Pause one cron job and confirm its heartbeat deadline independently.
3. Fail a real notification destination and inspect retries/escalation.
4. Rebuild detector state and public status state on a disposable host.

## What this guide covers

Service availability detection and status communication, not general telemetry or AI-agent evaluation.

## Adjacent guides

- [self-hosted-notification-routing-index](https://github.com/brownrasr/self-hosted-notification-routing-index) — Use it when monitoring events need deduplication, escalation, and delivery across channels.
- [agent-observability-field-guide](https://github.com/duthdoll45/agent-observability-field-guide) — Use it when the monitored workload is an LLM or agent system and trace-level diagnosis is required.
- [self-hosted-container-management-map](https://github.com/Hervieux39AC/self-hosted-container-management-map) — Use it when the monitoring stack will run beside other containerized services and needs a recoverable control plane.

## Located sources and update policy

[`sources.json`](sources.json) binds each cited declaration to a commit, document, exact line range and local snapshot hash. [`assets/projects.json`](assets/projects.json) holds the normalized roles and decision axes. Current HEAD reachability is recorded separately; it is not a maintenance or security assessment. Licensing is version- and directory-specific. Re-read the selected release before adoption.

Run the local evidence and package checks, then opt into public-source reachability:

```bash
python3 scripts/verify_sources.py
python3 scripts/verify_sources.py --online
```

These checks validate source binding, excerpts, hashes, language, the decision asset and nonempty public content. They do not certify feature behavior, migration success or restore success. No popularity score, paid placement or affiliate ranking is used. Restricted-license and historical examples are explicit contrasts, not silently recommended open-source defaults.

If this saved you a decision, star the repo — it is how the next reader finds it.
