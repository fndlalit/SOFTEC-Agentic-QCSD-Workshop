# Pushing this as the SOFTEC repo

From this folder, with your own GitHub login on your machine:

```bash
git init -b main
git add .
git commit -m "SOFTEC Asia 2026 edition: AQE 3.14.1, terminal-based knowledge-graph warm-up, prerequisites"
gh repo create fndlalit/SOFTEC-Agentic-QCSD-Workshop --public --source=. --push
```

If you would rather review as a diff first, push to a branch and open a PR against it:

```bash
git checkout -b softec-2026
git push -u origin softec-2026
gh pr create --fill
```

## What changed from the NTD repo

- `agentic-qe` pin moved from 3.10.1 to 3.14.1
- Clone URL and all NTD naming replaced
- New "Before you arrive" section: Node 22.13 minimum, global install rights, coding agent, token budget, setup at home
- Warning to clone close to the home folder (the indexer skips files more than ten directories deep)
- Setup gained a step 4: build the knowledge graph in the terminal, with the numbers to expect
- LAB Exercise 0 rewritten as three terminal commands with an expected-output table, replacing the MCP steps
- Exercise 0's "embedding stats" step removed: no MCP tool exposes it in 3.10.1 or 3.14.1
- Exercise 0 now warns that relationship extraction calls the model once per file when a provider is configured
- Exercise 1 notes that `qcsd-ideation-swarm` is installed as a skill, not a command file
- Fleet size corrected from "30+ agents" to "60 agents and 86 skills"
