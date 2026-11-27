# DemoLab for Devin

> Modified by Chris Wozniczek for Devin, 2026; original: [google-gemini/demolab](https://github.com/google-gemini/demolab).

DemoLab for Devin is an unofficial port of Google's Gemini DemoLab. It packages two reusable skills in one Devin plugin for Devin CLI, Devin Desktop (including the Devin Next beta), and the Devin web app (cloud sessions). It is not endorsed by Google or Cognition.

## Skills

| Skill | Use it for |
| --- | --- |
| [`deep-research`](skills/deep-research/SKILL.md) | Multi-angle investigations and comparisons that need primary sources and a cited report. For simple factual lookups, ask Devin directly. |
| [`gemini-frontend-design`](skills/gemini-frontend-design/SKILL.md) | Designing or redesigning a web interface, from layout and typography to imagery, motion, and visual review. |

The frontend skill is unchanged from upstream. The research skill uses Devin's tools and writes its report as a Markdown file.

## Installation

Use one of the following methods. Once installed in your personal scope, the plugin is available in new web, CLI, and Desktop sessions signed in to the same account, subject to your organization's plugin policy.

### Devin CLI

Install and sign in to the [Devin CLI](https://docs.devin.ai/cli). To install the plugin on this machine only, run:

```sh
devin plugins install --local chris-wozniczek/demolab
```

To sync the plugin to your other devices and cloud sessions instead, omit `--local`. This records the install in your Devin Cloud personal manifest:

```sh
devin plugins install chris-wozniczek/demolab
```

For development, install from a checkout instead. This links the folder on your machine only, without syncing it to the cloud:

```sh
cd /path/to/demolab
devin plugins install --local .
```

Start a new session after installing or editing the linked skills.

### Devin Desktop (including Devin Next beta)

In the Agents window, open **Customize → Plugins** and install from the GitHub repository `chris-wozniczek/demolab` or select a local checkout for development. If you installed the plugin in your personal scope from the CLI or web, it syncs to Desktop when you sign in. See [Devin Desktop customizations](https://docs.devin.ai/desktop/devin-local/customizations#plugins).

### Devin web app (cloud sessions)

Open [Customize](https://app.devin.ai/customize) from the sidebar and choose **Plugins**. For your own sessions, select **Myself**. Choose **Add plugin → From repository**, enter `chris-wozniczek/demolab`, and install. Start a new cloud session to use the skills. If the web app cannot index the repository, check repository access and the indexing status in **Plugin settings**. See the [web plugin guide](https://docs.devin.ai/product-guides/plugins).

## Usage

Type a slash command followed by your brief:

```text
/demolab-devin:deep-research Compare how three European capitals expanded their cycling networks since 2020. Use city transport plans and official data to distinguish announced projects from completed ones.
```

```text
/demolab-devin:gemini-frontend-design Redesign our bike-share dashboard so riders can find an available bike near them. Preserve the existing API and check the layout on desktop and mobile.
```

You can also describe the task in plain language and let Devin choose a relevant skill. Use the slash command to select one explicitly.

For research, look for a Markdown report in the working directory or attached to the session, with a short chat summary. If the session cannot write a file, Devin can return the report in chat. For frontend design, give Devin the project, audience, and constraints so it can tailor the implementation and review.

## Model selection and research cost

Choose any model available in your Devin model picker. The plugin does not pin a model or define a custom subagent. Upstream tailored the frontend skill for Gemini 4 and evaluated the research skill with Gemini 4; neither skill requires it, and upstream evaluations are not results for this port.

In Devin CLI and Devin Desktop, the research skill asks for the built-in `subagent_general` profile, which inherits the parent agent's model and includes `webfetch` for reading primary sources directly. If you select a Gemini 4 model, those workers use it too. The skill does not use `subagent_explore`, which uses Devin's default subagent routing or configured override and cannot fetch arbitrary URLs (`web_search` only).

In a cloud session, the skill uses the available subagent tool. The CLI/Desktop inheritance rule does not establish which model a cloud worker uses, so do not assume cloud workers inherit your selection. If subagents are unavailable or disabled, Devin runs the research tracks directly.

On prompt-based plans, each CLI/Desktop general subagent consumes additional credits at the parent model's rate, like another session on that model. More workers can mean more spend. For a lighter run, ask Devin to do the research directly without subagents. See the [Devin subagents guide](https://docs.devin.ai/cli/subagents#which-model-does-a-subagent-use).

### Web fetch permissions for background research

In Devin CLI and Devin Desktop, background subagents inherit tool permissions you have already granted and automatically deny any tool that still requires confirmation. In **Normal** or **Accept Edits** mode, `webfetch` prompts by default, so background `deep-research` workers will have `webfetch` auto-denied and fall back to `web_search` snippets instead of reading primary sources directly.

To let background research workers fetch pages directly, do one of the following:

- Run in **Smart** mode (or **Bypass** / sandbox **Autonomous** mode).
- Pre-approve web fetches by adding `"Fetch(domain:*)"` to `permissions.allow` in `~/.config/devin/config.json`, or by choosing **Yes, always allow all web fetches** the first time Devin prompts for a web fetch.

See the [Devin permissions guide](https://docs.devin.ai/cli/reference/permissions) for details.

## Updating

To refresh a repository-installed copy on your machine, run `devin plugins update demolab-devin`. In the web app, **Customize → Plugins → Plugin settings → Reindex plugins** refreshes the listing after source changes. Start a new session to load the updated plugin.

To bring upstream changes into this fork, add the upstream remote once and fetch it:

```sh
git remote add upstream https://github.com/google-gemini/demolab.git
git fetch upstream
git diff HEAD...upstream/main -- skills
```

Review the upstream skill diffs before merging `upstream/main` or applying selected changes. Keep the Devin manifest, research tool instructions, and attribution notices. Then install the updated checkout locally and check that both skills are discoverable before publishing.

## License and attribution

Distributed under the [Apache License 2.0](LICENSE). See [NOTICE](NOTICE) for upstream credits. Product names identify the original work and compatible tools; the license does not grant trademark rights or imply endorsement.
