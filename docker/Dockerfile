# This image provides a postgres installation from which to run backups 
FROM docker-registry.default.svc:5000/8ecbmv-test/invasivesbc-api-db:test-1.0.0

# Change timezone to PST for convenience
ENV TZ=PST8PDT

# Set the workdir to be root
WORKDIR /

# Load the backup scripts into the container (must be executable).
COPY backup.* /

COPY webhook-template.json /

# ========================================================================================================
# Install go-crond (from https://github.com/BCDevOps/go-crond)
#  - Adds some additional logging enhancements on top of the upstream project; 
#    https://github.com/webdevops/go-crond
#
# CRON Jobs in OpenShift:
#  - https://blog.danman.eu/cron-jobs-in-openshift/
# --------------------------------------------------------------------------------------------------------
ARG SOURCE_REPO=BCDevOps
ARG GOCROND_VERSION=0.6.3
ADD https://github.com/$SOURCE_REPO/go-crond/releases/download/$GOCROND_VERSION/go-crond-64-linux /usr/bin/go-crond

USER root

RUN chmod ug+x /usr/bin/go-crond
# ========================================================================================================

# ========================================================================================================
# Perform operations that require root privilages here ...
# --------------------------------------------------------------------------------------------------------
RUN echo $TZ > /etc/timezone
# ========================================================================================================
ENV PATH /opt/app-root/src/bin:/opt/app-root/bin:${PATH}
ENV APP_DATA /opt/app-root
ENV APP_ROOT /opt/app-root
# Important - Reset to the base image's user account.
USER 26

ENTRYPOINT ["container-entrypoint"]
CMD ["run-postgresql"]

# Set the default CMD.
CMD sh /backup.sh
