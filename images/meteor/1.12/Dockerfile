# Some Meteor commands fail if we use one of the slimmer Node images.
# Make sure we use the exact version of node shipped with this exact
# version of meteor.
# For example, check this file for your meteor release tag of interest:
# https://github.com/meteor/meteor/blob/release/METEOR%401.11.1/meteor#L3
FROM node:12.18.4

SHELL ["/bin/bash", "-o", "pipefail", "-o", "errexit", "-u", "-c"]

ENV METEOR_VERSION 1.12
ENV PATH $PATH:/home/node/.meteor


ENV REACTION_DOCKER_BUILD true
ENV APP_SOURCE_DIR /opt/reaction/src
ENV APP_BUNDLE_DIR /opt/reaction/dist
ENV PATH $PATH:/home/node/.meteor

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
  build-essential \
  bsdtar \
  bzip2 \
  ca-certificates \
  git \
  python \
  wget \
  librdkafka-dev \
  libsasl2-dev \
 && rm -rf /var/lib/apt/lists/*

RUN mkdir -p "$APP_SOURCE_DIR" \
 && mkdir -p "$APP_BUNDLE_DIR" \
 && chown -R node "$APP_SOURCE_DIR" \
 && chown -R node "$APP_BUNDLE_DIR"


USER node

# Install Meteor. Copy the install script to a temp file first, so that we can
# replace the hard-coded `RELEASE` var in the script with the release we want installed.
RUN wget -O /tmp/install_meteor.sh https://install.meteor.com \
 && sed -i.bak "s/RELEASE=.*/RELEASE=\"$METEOR_VERSION\"/g" /tmp/install_meteor.sh \
 && printf "\\n[-] Installing Meteor %s...\\n" "$METEOR_VERSION" \
 && sh /tmp/install_meteor.sh \
 && rm /tmp/install_meteor.sh


WORKDIR $APP_SOURCE_DIR

# Node flags for the Meteor build tool
ONBUILD ARG TOOL_NODE_FLAGS
ONBUILD ENV TOOL_NODE_FLAGS $TOOL_NODE_FLAGS

LABEL maintainer="Reaction Commerce <engineering@reactioncommerce.com>"
