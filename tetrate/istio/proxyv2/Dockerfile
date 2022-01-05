ARG BASE_REGISTRY=registry.access.redhat.com
ARG BASE_IMAGE=ubi8/ubi
ARG BASE_TAG=8.5
ARG REGISTRY=containers.istio.tetratelabs.com
ARG IMAGE=proxyv2
ARG TAG=1.11.4-tetratefips-v0

# Updated by Renovate
ARG version="1.11.4"

FROM ${REGISTRY}/${IMAGE}:${TAG} AS base

FROM ${BASE_REGISTRY}/${BASE_IMAGE}:${BASE_TAG}

# Need to add `ARG version` after FROM. No need to specify the version again. Declaring a variable is enough.
ARG version

# Environment variable indicating the exact build, for debugging
ENV ISTIO_META_ISTIO_VERSION=${version}

COPY --from=base /sbin/xtables-nft-multi /sbin/iptables* /sbin/ip6tables* /sbin/ip /sbin/
COPY --from=base /usr/lib/x86_64-linux-gnu/xtables/ /usr/lib/x86_64-linux-gnu/xtables
COPY --from=base /usr/lib/x86_64-linux-gnu/ /usr/lib/x86_64-linux-gnu
COPY --from=base /etc/iproute2 /etc/iproute2

# Copy Envoy bootstrap templates used by pilot-agent
COPY --from=base /var/lib/istio/envoy/envoy_bootstrap_tmpl.json /var/lib/istio/envoy/envoy_bootstrap_tmpl.json
COPY --from=base /var/lib/istio/envoy/gcp_envoy_bootstrap_tmpl.json /var/lib/istio/envoy/gcp_envoy_bootstrap_tmpl.json

# Install Envoy.
COPY --from=base /usr/local/bin/envoy /usr/local/bin/envoy
COPY --from=base /usr/local/bin/pilot-agent /usr/local/bin/pilot-agent
COPY --from=base /etc/istio/extensions /etc/istio/extensions

RUN useradd -u 1337 istio-proxy && \
    chown -R 1337 /var/lib/istio && \
    chown -R 1337 /etc/istio && \
    dnf update -y && \
    dnf clean all && \
    rm -rf /var/cache/dnf/

USER 1337

HEALTHCHECK --start-period=10s \
   CMD bash -c '</dev/tcp/127.0.0.1/8001 &>/dev/null' || exit 1

# The pilot-agent will bootstrap Envoy.
ENTRYPOINT ["/usr/local/bin/pilot-agent"]
