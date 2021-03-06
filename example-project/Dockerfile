FROM node:boron
# Node Version v6.11., npm 2.15.11

ARG NPM_VERSION_1=2.15.11
ARG NPM_VERSION_2=4.2.0

RUN apt-get update && apt-get install vim -y

## Install NPM
WORKDIR /npm
COPY ./package.json ./package.json
RUN npm install npm@$NPM_VERSION_1

# Build 1 (Generate shrinkwrap)
WORKDIR /build1
COPY ./package.json ./package.json
RUN /npm/node_modules/.bin/npm -v > ./npm-version
RUN /npm/node_modules/.bin/npm install
RUN /npm/node_modules/.bin/npm shrinkwrap

## Install NPM
WORKDIR /npm
# Remove node_modules. They cause new npm install to fail
RUN rm -rf ./node_modules
COPY ./package.json ./package.json
RUN npm install npm@$NPM_VERSION_2

# Build 2 (Test shrinkwrap)
WORKDIR /build2
RUN cp /build1/npm-shrinkwrap.json ./
COPY ./package.json ./package.json
RUN /npm/node_modules/.bin/npm -v > ./npm-version
RUN /npm/node_modules/.bin/npm install
RUN /npm/node_modules/.bin/npm shrinkwrap

# Build 3 (Test shrinkwrap of the shrinkwrap result)
WORKDIR /build3
RUN cp /build2/npm-shrinkwrap.json ./
COPY ./package.json ./package.json
RUN /npm/node_modules/.bin/npm -v > ./npm-version
RUN /npm/node_modules/.bin/npm install
RUN /npm/node_modules/.bin/npm shrinkwrap

# Print sums
# Hypothesis: If npm shrinkwrap is not deterministic as the thing says, these would be diff
WORKDIR /
RUN npm install bluebird
COPY ./get-data.js /get-data.js

CMD node /get-data.js
