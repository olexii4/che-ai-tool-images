# che-ai-tool-images

UBI10-based init container images that inject AI CLI tools into Eclipse Che DevWorkspaces.

## Images

| Tool | Pattern | Image |
|------|---------|-------|
| [Claude Code](https://claude.ai/code) | init | `quay.io/oorel/claude-code:next` |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | bundle | `quay.io/oorel/gemini-cli:next` |
| [OpenCode](https://opencode.ai) | init | `quay.io/oorel/opencode:next` |

All images are built for `linux/amd64` and `linux/arm64`.

---

## Usage in a DevWorkspace

### Claude Code

```yaml
components:
  - name: injected-tools
    volume:
      size: 256Mi
  - name: claude-code-injector
    container:
      image: quay.io/oorel/claude-code:next
      command: ["/bin/cp"]
      args: ["/usr/local/bin/claude", "/injected-tools/claude"]
      memoryLimit: 128Mi
      mountSources: false
      volumeMounts:
        - name: injected-tools
          path: /injected-tools

commands:
  - id: install-claude-code
    apply:
      component: claude-code-injector

events:
  preStart:
    - install-claude-code
```

The editor container must mount the `injected-tools` volume to access the binary.

### Gemini CLI

```yaml
components:
  - name: injected-tools
    volume:
      size: 256Mi
  - name: gemini-cli-injector
    container:
      image: quay.io/oorel/gemini-cli:next
      command: ["/bin/sh"]
      args: ["-c", "cp -a /opt/gemini-cli/. /injected-tools/gemini-cli/"]
      memoryLimit: 256Mi
      mountSources: false
      volumeMounts:
        - name: injected-tools
          path: /injected-tools

commands:
  - id: install-gemini-cli
    apply:
      component: gemini-cli-injector

events:
  preStart:
    - install-gemini-cli
```

The editor container must mount the `injected-tools` volume to access the tool at `/injected-tools/gemini-cli/bin/gemini`.

### OpenCode

```yaml
components:
  - name: injected-tools
    volume:
      size: 256Mi
  - name: opencode-injector
    container:
      image: quay.io/oorel/opencode:next
      command: ["/bin/cp"]
      args: ["/usr/local/bin/opencode", "/injected-tools/opencode"]
      memoryLimit: 128Mi
      mountSources: false
      volumeMounts:
        - name: injected-tools
          path: /injected-tools

commands:
  - id: install-opencode
    apply:
      component: opencode-injector

events:
  preStart:
    - install-opencode
```

The editor container must mount the `injected-tools` volume to access the binary.

---

## Structure

```
dockerfiles/
├── claude-code/Dockerfile
├── gemini-cli/Dockerfile
└── opencode/Dockerfile
```

---

## CI/CD

| Workflow | Trigger | What it does |
|----------|---------|-------------|
| `next-build-multiarch.yml` | Push to `main` | Single Quay.io login; builds and pushes all 3 images for amd64 + arm64 |

### Required Secrets

| Secret | Description |
|--------|-------------|
| `QUAY_USERNAME` | Quay.io username |
| `QUAY_PASSWORD` | Quay.io password or robot token |

---

## License

EPL-2.0 — see [LICENSE](LICENSE).
