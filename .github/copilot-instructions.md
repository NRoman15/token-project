# Copilot instructions — blockchain-developer-bootcamp

Purpose: quick, actionable orientation so an AI coding agent can be productive in this repository.

Big picture
- Two main concerns live side-by-side: a Hardhat smart-contract project and a Create React App frontend.
  - Smart contracts: `contracts/` (example: `contracts/Token.sol`) compiled/deployed with Hardhat.
  - Deploy scripts: `scripts/1_deploy.js` uses `hre.ethers` and prints deployed addresses.
  - Tests: `test/` contains Hardhat/Chai tests (note: `test/Lock.js` references a `Lock` contract not present here).
  - Frontend: `src/` is a CRA app (React) — standard `npm start` dev flow.

Key files to inspect when making changes
- `hardhat.config.js` — solidity version and Hardhat plugins (dotenv loaded here).
- `contracts/Token.sol` — main contract in repo, uses `decimals = 18` and multiplies `totalSupply` by `10**decimals` in the constructor.
- `scripts/1_deploy.js` — canonical deploy pattern; construct name/symbol/supply and call `deploy()`.
- `test/Lock.js` — example tests using `@nomicfoundation/hardhat-toolbox` helpers (time, loadFixture) and Chai matchers.
- `package.json` — shows two separate workflows: CRA scripts (`npm start`, `npm run build`) and dev dependencies for Hardhat.

Developer workflows (commands)
- Install deps: `npm install`
- Compile contracts: `npx hardhat compile`
- Run tests (Hardhat): `npx hardhat test`
- Run a local node: `npx hardhat node` (use this to persist contracts across scripts)
- Deploy to local node: `npx hardhat run --network localhost scripts/1_deploy.js`
- Quick one-off deploy to ephemeral Hardhat network: `npx hardhat run scripts/1_deploy.js`
- Frontend dev: `npm start` (CRA on port 3000)

Project-specific patterns & conventions
- Solidity: project-level version declared in `hardhat.config.js` as `0.8.28`. Contracts use `pragma ^0.8.0` — be conservative when changing the version.
- Deploy scripts are prefixed with numbers (e.g., `1_deploy.js`) — follow this ordering for multi-step deploy flows.
- Token conventions: `Token.sol` uses `decimals = 18` and expects max supply as a human-friendly integer string in the deploy script (script multiplies it).
- Debugging: contracts import `hardhat/console.sol` — you can leave console logs in during local debugging but remove before public/mainnet deploys.
- Tests use Hardhat toolbox helpers and snapshot fixtures (`loadFixture`). Prefer these helpers when authoring new tests.

Integration points & environment
- `hardhat.config.js` loads environment variables via `dotenv`. If adding network deploys, expect to read `PRIVATE_KEY`, `RPC_URL`, etc., from `.env`.
- Frontend ↔ contracts: there is no single canonical address/config file in the repo. When adding frontend contract wiring, commit a small JSON with deployed addresses (e.g., `frontend/src/config/contracts.json`) and update `scripts/1_deploy.js` to write it.

Notes, caveats, and GOTCHAs
- `test/Lock.js` references a `Lock` contract that is not present — tests may be boilerplate. Do not assume tests reflect the current contract set; update or remove them when adding/removing contracts.
- `package.json` CRA scripts are unrelated to Hardhat commands; use `npx hardhat` for blockchain tasks and `npm start` for the UI.
- Keep Solidity changes minimal: update `hardhat.config.js` and re-run `npx hardhat compile`.

How to contribute changes safely (agent checklist)
1. Run `npm install` locally.
2. Compile: `npx hardhat compile` — fix compile errors before writing deployment or test changes.
3. Run tests: `npx hardhat test` — if tests fail because they expect missing contracts, either update tests or add the contracts explicitly.
4. For local manual testing: start `npx hardhat node` in one terminal and deploy with `npx hardhat run --network localhost scripts/1_deploy.js` from another.
5. If changing deploy scripts, preserve the existing console output pattern (script prints the deployed address) so the developer can copy it into the frontend.

If anything here is unclear or you want me to include additional examples (e.g., a sample `Lock.sol`, example `.env` variables, or a sample frontend contract JSON), say which example and I'll add it.
