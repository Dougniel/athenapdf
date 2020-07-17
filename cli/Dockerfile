FROM node:14.5-alpine AS build

RUN mkdir -p /athenapdf/build/artifacts/
WORKDIR /athenapdf/

COPY package.json /athenapdf/
RUN npm install

COPY package.json /athenapdf/build/artifacts/
RUN cp -r /athenapdf/node_modules/ /athenapdf/build/artifacts/

COPY src /athenapdf/build/artifacts/
RUN npm run build:linux

FROM  ubuntu:20.04

RUN apt-get -yq update && apt-get -yq install --no-install-recommends \
    libglib2.0-0 \
    libgtk-3-0 \
    libasound2 \
    libnss3 \
    libxss1 \
    libgbm1 \
    libxtst6 \
    xvfb \
    && apt-get -yq autoremove \
    && apt-get -yq clean \
    && rm -rf /var/lib/apt/lists/* \
    && truncate -s 0 /var/log/*log

#COPY fonts.conf /etc/fonts/conf.d/100-athena.conf

COPY --from=build  /athenapdf/build/athenapdf-linux-x64/ /athenapdf/
WORKDIR /athenapdf/

ENV PATH /athenapdf/:$PATH

COPY entrypoint.sh /athenapdf/entrypoint.sh

RUN mkdir -p /converted/
WORKDIR /converted/

CMD ["athenapdf"]

ENTRYPOINT ["/athenapdf/entrypoint.sh"]
