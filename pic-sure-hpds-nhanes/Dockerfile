FROM alpine:3.8 as encryption_key

RUN apk add gnupg
RUN apk add openssl

RUN openssl rand -hex 16 > encryption_key
COPY gpg_pub_key.asc gpg_pub_key.asc

RUN gpg --import gpg_pub_key.asc
RUN gpg --list-keys

RUN gpg --always-trust --batch --no-tty -e -r "jason_stedman@hms.harvard.edu" encryption_key 

RUN base64 encryption_key.gpg > encryption_key_base64.gpg

RUN echo "-----BEGIN PGP MESSAGE-----"
RUN cat encryption_key_base64.gpg
RUN echo "-----END PGP MESSAGE-----"

FROM dbmi/pic-sure-hpds-loader:Oct_26_2018 as loader

COPY --from=encryption_key encryption_key /opt/local/hpds/encryption_key

RUN java -Xmx2048m -jar pic-sure-hpds-SQLLoader-jar-with-dependencies.jar 

FROM dbmi/pic-sure-hpds:Oct_26_2018 as nhanes-hpds

COPY --from=loader /tmp/allObservationsStore.javabin /opt/local/phenocube/
COPY --from=loader /tmp/columnMeta.javabin /opt/local/phenocube/
COPY --from=encryption_key encryption_key_base64.gpg /opt/local/hpds/encryption_key

ENTRYPOINT java -XX:+UseParallelGC -XX:SurvivorRatio=${SURVIVOR_RATIO} -Xms${HEAPSIZE}m -Xmx${HEAPSIZE}m -server -jar pic-sure-hpds-1.0-SNAPSHOT-war-exec.jar -httpPort 8080 -DCACHE_SIZE=${CACHESIZE} -DSMALL_TASK_THREADS=${SMALL_TASK_THREADS} -DLARGE_TASK_THREADS=${LARGE_TASK_THREADS} -DSMALL_JOB_LIMIT=${SMALL_JOB_LIMIT} -DID_BATCH_SIZE=${ID_BATCH_SIZE}


