ARG BASE_REGISTRY=registry.access.redhat.com
ARG BASE_IMAGE=ubi8/ubi
ARG BASE_TAG=8.5
ARG REGISTRY=containers.istio.tetratelabs.com
ARG IMAGE=pilot
ARG TAG=1.11.4-tetratefips-v0

FROM ${REGISTRY}/${IMAGE}:${TAG} AS base

FROM ${BASE_REGISTRY}/${BASE_IMAGE}:${BASE_TAG}

RUN dnf -y update && \
    dnf update ca-certificates && \
    dnf -y upgrade && \
    dnf clean all && \
    rm -rf  /var/log/*log /var/lib/apt/lists/* /var/log/apt/* /var/lib/dpkg/*-old /var/cache/debconf/*-old

ARG TARGETARCH

COPY --from=base /usr/local/bin/pilot-discovery /usr/local/bin/
COPY --from=base /var/lib/istio/envoy/envoy_bootstrap_tmpl.json /var/lib/istio/envoy/
COPY --from=base /var/lib/istio/envoy/gcp_envoy_bootstrap_tmpl.json /var/lib/istio/envoy/

USER 1337:1337

HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 CMD [ "executable" ]

ENTRYPOINT [ "/usr/local/bin/pilot-discovery" ]
