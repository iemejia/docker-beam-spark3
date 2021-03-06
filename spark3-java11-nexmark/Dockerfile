FROM openjdk:8 AS beam-java8-build
WORKDIR /root

ARG BEAM_REPO=https://github.com/iemejia/beam
ARG BRANCH_NAME=BEAM-7092-spark3

RUN git clone --branch=$BRANCH_NAME --depth=1 $BEAM_REPO
RUN cd beam && \
    ./gradlew :runners:spark:jar && \
    ./gradlew :runners:spark:testJar && \
    ./gradlew :sdks:java:testing:nexmark:jar && \
    ./gradlew :sdks:java:testing:nexmark:testJar;

FROM openjdk:11
WORKDIR /root
ENV BUILD_ID=idonthaveabuildidbutgradleishappynow
COPY --from=beam-java8-build /root/beam beam
RUN cd beam && \
    ./gradlew \
        --console=plain \
        -x shadowJar \
        -x shadowTestJar \
        -x compileJava \
        -x compileTestJava \
        -x jar \
        -x testJar \
        -x classes \
        -x testClasses \
        :sdks:java:testing:nexmark:run \
          -Pnexmark.runner=":runners:spark" \
          -Pnexmark.args=" \
          --runner=SparkRunner \
          --streaming=false \
          --suite=SMOKE \
          --manageResources=false \
          --monitorJobs=true \
          --enforceEncodability=true \
          --enforceImmutability=true";
