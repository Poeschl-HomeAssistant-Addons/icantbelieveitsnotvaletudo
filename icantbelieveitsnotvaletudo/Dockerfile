ARG BUILD_FROM
FROM $BUILD_FROM AS BUILD

ENV LANG C.UTF-8

RUN apt-get update && apt-get -y --no-install-recommends install \
	git=1:2.47.3-0+deb13u1 \
	npm=9.2.0~ds1-3 \
	python3=3.13.5-1 \
	libpixman-1-dev=0.44.0-3 \
	libcairo2-dev=1.18.4-1+b1 \
	libpango1.0-dev=1.56.3-1 \
	build-essential=12.12 \
	libjpeg62-turbo-dev=1:2.1.5-4 \
	&& npm install -g n@10.2.0 && n 18.16.1

# Splited RUN for better layer caching
# hadolint ignore=DL3059
RUN git config --global advice.detachedHead false && \
    git clone https://github.com/Hypfer/ICantBelieveItsNotValetudo.git -b 2023.08.0 /app

WORKDIR /app
RUN npm install --verbose


FROM $BUILD_FROM AS SERVER

RUN apt-get update && apt-get -y --no-install-recommends install \
    npm=9.2.0~ds1-3 \
    && npm install -g n@10.2.0 && n 18.16.1

COPY root/server /server

WORKDIR /server
RUN npm install


FROM $BUILD_FROM AS RUNNING

RUN apt-get update && apt-get -y --no-install-recommends install \
    npm=9.2.0~ds1-3 \
    libpixman-1-0=0.44.0-3 \
		libcairo2=1.18.4-1+b1 \
		libpango-1.0-0=1.56.3-1 \
		libjpeg62-turbo=1:2.1.5-4 \
	  && npm cache clean -f && npm install -g n@10.2.0 && n 18.16.1 \
		&& apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY --from=BUILD /app /app
COPY --from=SERVER /server /server

ENTRYPOINT [ "/init" ]
CMD []
COPY root /
