# CHARRA: CHAllenge-Response based Remote Attestation with TPM 2.0

This is a proof-of-concept implementation of the [IETF RATS](https://datatracker.ietf.org/wg/rats/about/) [Reference Interaction Model for Challenge-Response-based Remote Attestation](https://datatracker.ietf.org/doc/draft-birkholz-rats-reference-interaction-model/) using TPM 2.0. The [IETF Remote ATtestation ProcedureS (RATS)](https://datatracker.ietf.org/wg/rats/about/) working group standardizes formats for describing assertions/claims about system components and associated evidence; and procedures and protocols to convey these assertions/claims to relying parties. Given the security and privacy sensitive nature of these assertions/claims, the working group specifies approaches to protect this exchanged data.

This proof-of-concept implementation realizes the Attesting Computing Environment—a Computing Environment capable of monitoring and attesting a target Computing Environment—as well as the target Computing Environment itself, as described in the [RATS Architecture](https://datatracker.ietf.org/doc/draft-birkholz-rats-architecture/).

Next steps:

* Block-wise CoAP data transfers
* Extended verification of claims with known-good values

## Build and Run in Docker

1. Install Docker.

2. Build Docker image:

        ./docker/build.sh

3. Run Docker image:

        ./docker/run.sh

4. Compile CHARRA (inside container):

        cd charra/
        make -j

5. Run CHARRA (inside container):

        (bin/attester &); sleep .2 ; bin/verifier ; sleep 1 ; pkill bin/attester

If you see "ATTESTATION SUCCESSFUL" you're done. Congratz :-D



## Build

1. Install all dependencies that are needed for the [TPM2-TSS](https://github.com/tpm2-software/tpm2-tss/blob/master/INSTALL.md).

2. Compile libraries:

   Either dynamically linked (default):

        make -j libs

3. Install libraries:

        sudo make libs.install

4. Compile programs:

    Either dynamically linked (default):

        make -j



## Further Preparation

1. Download and install [IBM's TPM 2.0 Simulator](https://sourceforge.net/projects/ibmswtpm2/).

2. Download and install the [TPM2 Tools](https://github.com/tpm2-software/tpm2-tools).



## Run

1. Start the TPM Simulator (and remove the state file `NVChip`):

        cd /tmp ; pkill tpm_server ; rm -f NVChip
        (/usr/local/bin/tpm_server > /dev/null &)

2. Send TPM startup command:

        /usr/bin/env TPM2TOOLS_TCTI_NAME=socket \
                TPM2TOOLS_SOCKET_ADDRESS=127.0.0.1 \
                TPM2TOOLS_SOCKET_PORT=2321 \
                /usr/local/bin/tpm2_startup --clear

3. Run Attester and Verifier:

         (bin/attester &); sleep .2 ; bin/verifier ; sleep 1 ; pkill bin/attester

## Debug

* Clang `scan-build`:

        make clean ; scan-build make

* Valgrind:

        (valgrind --leak-check=full \
            --show-leak-kinds=all -v \
            bin/attester \
            2> attester-valgrind-stderr.log &); \
        sleep .2 ; \
        (valgrind --leak-check=full \
            --show-leak-kinds=all -v \
            bin/verifier\
            2> verifier-valgrind-stderr.log) ;\
        sleep 1 ; \
        pkill bin/attester


