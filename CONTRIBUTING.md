# Contributing to the Awesome-Octopus Ecosystem 🐙🤝

Thank you for your interest in contributing to **Awesome-Octopus**! This repository serves as the definitive architecture guide, product specification hub, case study catalog, and workflow index for the Octopus ecosystem (`OctopusStudio`, `octocut`, `octolimb`, and `OctopusMCP-Manager`).

---

## 📑 Contribution Categories

We welcome contributions across four distinct directories:

1. **Products (`products/`)**: Deep-dive technical specifications, APIs, and benchmarks for tools in the Octopus ecosystem.
2. **Features (`features/`)**: Reusable technical capability blueprints, DAG implementations, kinematics, and security patterns.
3. **Systems & Blueprints (`systems/`)**: Architecture diagrams and data flows illustrating how multiple Octopus tools interconnect to form complete cyber-physical or digital platforms.
4. **Case Studies (`case-studies/`)**: Detailed, quantitative real-world deployment stories with problem statements, architecture flows, metrics, and lessons learned.
5. **Workflows (`workflows/`)**: Declarative DAG JSON/YAML automation recipes ready to run on the Octopus execution engine.

---

## 📐 Style & Formatting Guidelines

- **GitHub Markdown Standards**: Use clean GitHub-flavored markdown with consistent headers (`#`, `##`, `###`), tables, code blocks with syntax highlighting (`typescript`, `python`, `json`, `yaml`), and ASCII/box-drawing architecture diagrams.
- **Quantitative Metrics**: Case studies and specifications must include measurable metrics (e.g. latency, throughput, error rates, RAM/CPU footprints).
- **Interconnection Clarity**: When proposing new systems or case studies, explicitly define how components communicate (protocols, data formats, RPC interfaces).

---

## 🛠 Submission Checklist

Before opening a pull request, please verify:

- [ ] New files are categorized in the appropriate directory (`products/`, `features/`, `systems/`, `case-studies/`, or `workflows/`).
- [ ] File names follow the kebab-case taxonomy (e.g., `prod-05-my-tool.md`, `cs-06-edge-robotics.md`, `wf-auto-backup.md`).
- [ ] The master table of contents in `README.md` is updated with links to the new files.
- [ ] Code samples are type-safe and syntactically valid.
- [ ] No private credentials, internal hostnames, or API keys are included.

---

## 📜 Code of Conduct

Please maintain a collaborative, respectful, and constructive environment for all contributors.
