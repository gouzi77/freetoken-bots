# FreeToken-Bots

**Conservative discovery and verification of zero-priced OpenRouter models, built for [Pi](https://pi.dev) coding-agent workflows.**

Free models on OpenRouter rotate constantly: a model that is free today can be repriced, delisted, or swapped tomorrow, and a stale model list turns into failed runs and `402` errors. FreeToken-Bots keeps a fresh, *verified* list of genuinely free models so agent workflows that depend on free tiers don't silently break.

Developed and used daily in a real multi-agent setup (personal-assistant child agents running on Pi) since October 2026.

## What it does

- **Scan** — pulls the OpenRouter model catalog and classifies every model by its actual pricing fields.
- **Strict free check** — a model counts as free only when *all* price fields are zero: prompt, completion, request, and cache prices. Anything non-zero or missing means "not free".
- **Fail-closed eligibility** — a model enters the approved pool only when a trustworthy source verifies its total parameter count (≥ 100B in our pool policy). If the API doesn't state a size, the model stays in `needs_parameter_verification`. It never guesses a parameter count from a model name.
- **Diff reports** — each scan is compared with the previous report, surfacing newly listed, delisted, and repriced models, plus changes to the eligible pool.
- **Sync (limited)** — `sync --dry-run` shows what would change in your Pi configuration. Automatic Pi config mutation is intentionally not unattended yet; see Roadmap.

## Safety posture

- Python standard library only. No dependencies, no telemetry, nothing phones home.
- API keys are read from Pi's own local auth store (`~/.pi/agent/auth.json`) and are never printed, logged, or sent anywhere except OpenRouter itself.
- Scanning is read-only against the OpenRouter public catalog API.

## Requirements

- Python 3.10+
- [Pi](https://pi.dev) installed (for the pool/status integration): `curl -fsSL https://pi.dev/install.sh | sh`
- An OpenRouter API key (only needed for probing/sync features; plain catalog scans work without one)

## Usage

```bash
# Fresh scan; writes a report to ~/.pi/agent/freetoken-bots.json
python3 scripts/radar.py scan

# Show the current pool status from the last scan
python3 scripts/radar.py status

# Preview what a Pi sync would change (does not mutate anything)
OPENROUTER_API_KEY=... python3 scripts/radar.py sync --dry-run
```

For the list to stay useful, schedule a scan (cron, launchd, or a systemd timer — any of them works). A scan older than 24 hours should be treated as stale.

## Tests

```bash
python3 -m unittest discover -s tests
```

## Roadmap

- Transactional Pi config updates with schema detection and rollback
- Tool-call probing for candidate models before pool admission
- Per-model health history across scans

## 中文简介

FreeToken-Bots 是一个保守型的 OpenRouter 免费模型发现与校验工具，为 Pi 编程智能体工作流而写。免费模型名单变化很快，本工具只认“所有价格字段都为零”的真免费模型，参数规模无法核实的模型一律不进可用池（fail-closed，宁可错杀不猜测），并对每次扫描做差异报告。在真实的多智能体个人助手环境中每日使用中（2026 年 10 月起）。纯 Python 标准库实现，无遥测，密钥只存本机。

## License

MIT — see [LICENSE](LICENSE).
