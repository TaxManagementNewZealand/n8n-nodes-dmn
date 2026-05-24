# Publishing Guide: @taxmanagementnz/n8n-nodes-dmn

Step-by-step guide for maintaining and publishing this package, and installing it in n8n.

---

## Pre-requisites

| Requirement | Notes |
|---|---|
| Node.js ≥ 18 | `node --version` to check |
| npm ≥ 9 | `npm --version` to check |
| GitHub account | Must have access to [TaxManagementNewZealand](https://github.com/TaxManagementNewZealand) org |
| npmjs.com account | Must be a member of the `@taxmanagementnz` org on [npmjs.com](https://www.npmjs.com) |
| SSH key set up | For GitHub SSH access — [GitHub docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) |

---

## 1. Fork the upstream repo (first time only)

This package is a fork of [`that-one-tom/n8n-nodes-dmn`](https://github.com/that-one-tom/n8n-nodes-dmn).

If the fork under `TaxManagementNewZealand` doesn't exist yet:

1. Go to [https://github.com/that-one-tom/n8n-nodes-dmn](https://github.com/that-one-tom/n8n-nodes-dmn)
2. Click **Fork** → set the owner to **TaxManagementNewZealand**
3. Leave the repo name as `n8n-nodes-dmn`
4. Click **Create fork**

The fork lives at: `https://github.com/TaxManagementNewZealand/n8n-nodes-dmn`

---

## 2. Clone the fork locally

```bash
git clone git@github.com:TaxManagementNewZealand/n8n-nodes-dmn.git
cd n8n-nodes-dmn
```

Verify the remote is set correctly:

```bash
git remote -v
# origin  git@github.com:TaxManagementNewZealand/n8n-nodes-dmn.git (fetch)
# origin  git@github.com:TaxManagementNewZealand/n8n-nodes-dmn.git (push)
```

---

## 3. Install dependencies

```bash
npm install
```

---

## 4. Branding fields (already applied — for reference only)

The following fields in `package.json` and `nodes/DMN/Dmn.node.json` have already been updated in this fork. You do not need to change them again unless they need updating.

**`package.json`**

```json
{
  "name": "@taxmanagementnz/n8n-nodes-dmn",
  "author": {
    "name": "Tax Management New Zealand",
    "email": "benjamin.s@tmnz.co.nz"
  },
  "homepage": "https://github.com/TaxManagementNewZealand/n8n-nodes-dmn",
  "repository": {
    "type": "git",
    "url": "https://github.com/TaxManagementNewZealand/n8n-nodes-dmn.git"
  }
}
```

**`nodes/DMN/Dmn.node.json`**

```json
{
  "resources": {
    "primaryDocumentation": [
      { "url": "https://github.com/TaxManagementNewZealand/n8n-nodes-dmn" }
    ]
  }
}
```

---

## 5. Make changes (if applicable)

If you are pulling in upstream changes or making modifications:

```bash
# Pull upstream changes into your fork
git remote add upstream git@github.com:that-one-tom/n8n-nodes-dmn.git
git fetch upstream
git merge upstream/main
```

Resolve any conflicts, then re-apply the branding fields in step 4 if they were overwritten.

---

## 6. Build

Compile TypeScript to `dist/` and copy icons:

```bash
npm run build
```

Expected: exits with code 0, no errors. The `dist/` directory is populated.

To watch for changes during development:

```bash
npm run dev
```

---

## 7. Test

Run the manual test suite (tests the decision logic against realistic scenarios):

```bash
node test-dmn.js
```

Run the linter:

```bash
npm run lint
```

---

## 8. Bump the version

Before publishing a new release, increment the version in `package.json` following [semver](https://semver.org):

- **Patch** (`0.1.15` → `0.1.16`): bug fixes
- **Minor** (`0.1.15` → `0.2.0`): new features, backwards compatible
- **Major** (`0.1.15` → `1.0.0`): breaking changes

```bash
npm version patch   # or minor / major
```

This updates `package.json` and creates a git tag automatically.

---

## 9. Publish to npm

### 9a. Log in to npm

```bash
npm login
```

Enter your npmjs.com credentials. Confirm you are logged in:

```bash
npm whoami
```

Confirm your account has access to the `@taxmanagementnz` org:

```bash
npm org ls @taxmanagementnz
```

### 9b. Run pre-publish checks

```bash
npm run prepublishOnly
```

This runs a full build and strict lint check. Fix any errors before proceeding.

### 9c. Publish

```bash
npm publish --access public
```

The `--access public` flag is required for scoped packages (`@taxmanagementnz/...`) to be publicly accessible on npmjs.com.

Verify the package is live:

```bash
npm view @taxmanagementnz/n8n-nodes-dmn
```

### 9d. Push the version tag

```bash
git push origin main --tags
```

---

## 10. Install in n8n

Once published, a team member can install the package into their n8n instance.

### Self-hosted n8n (Docker / bare metal)

SSH into the n8n host and install into the n8n data directory:

```bash
# Default path for Docker deployments
cd /home/<user>/.n8n

npm install @taxmanagementnz/n8n-nodes-dmn
```

Then restart n8n. The **DMN Decision Table** node will appear in the node palette under **Data Transformation → Helpers**.

### n8n Desktop

Open n8n Desktop → **Settings** → **Community Nodes** → **Install a community node**

Enter: `@taxmanagementnz/n8n-nodes-dmn`

Click **Install**.

### n8n Cloud

Community nodes are not supported on n8n Cloud as of writing. Use self-hosted n8n instead.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `npm publish` fails with 404 | Ensure `@taxmanagementnz` org exists on npmjs.com and you are a member |
| `npm publish` fails with 403 | Your account doesn't have publish rights — ask an org admin |
| `tsc: command not found` | Run `npm install` first |
| Node not appearing in n8n | Restart n8n after installing; check the n8n logs for load errors |
| Merge conflicts after upstream pull | Re-apply branding fields from step 4 |
