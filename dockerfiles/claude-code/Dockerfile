# Copyright (c) 2026 Red Hat, Inc.
# This program and the accompanying materials are made
# available under the terms of the Eclipse Public License 2.0
# which is available at https://www.eclipse.org/legal/epl-2.0/
#
# SPDX-License-Identifier: EPL-2.0
#
# Contributors:
#   Red Hat, Inc. - initial API and implementation

FROM alpine:3.21 AS builder

ARG CLAUDE_CODE_VERSION=2.1.81
ARG TARGETARCH

RUN apk add --no-cache curl

RUN set -e && \
    case "${TARGETARCH}" in \
      amd64) PLATFORM="linux-x64" ;; \
      arm64) PLATFORM="linux-arm64" ;; \
      *) echo "Unsupported architecture: ${TARGETARCH}" && exit 1 ;; \
    esac && \
    curl -fsSL -o /usr/local/bin/claude \
      "https://storage.googleapis.com/claude-code-dist-86c565f3-f756-42ad-8dfa-d59b1c096819/claude-code-releases/${CLAUDE_CODE_VERSION}/${PLATFORM}/claude" && \
    chmod +x /usr/local/bin/claude

# Stage 2: Minimal runtime image
FROM registry.access.redhat.com/ubi10/ubi-minimal:10.0

COPY --from=builder /usr/local/bin/claude /usr/local/bin/claude

LABEL org.opencontainers.image.description="Claude Code CLI tool for DevWorkspace injection" \
      org.opencontainers.image.source="https://github.com/olexii4/che-ai-tool-images"