# =========================================================================
# Includes R and prod dependencies
FROM --platform=linux/amd64 ghcr.io/r-lib/rig/ubuntu-22.04-release AS deps
WORKDIR /app
COPY ./DESCRIPTION .
RUN : -- DEPENDENCIES ------------------------------------------------ && \
    R -q -e 'pak::pkg_install("deps::.", lib = .Library); pak::pak_cleanup(force = TRUE)' && \
    apt-get clean && rm -rf /tmp/* && \
    : ---------------------------------------------------------------------

# =========================================================================
# deps stage + test dependencies
FROM deps AS test-deps
RUN : -- TEST DEPENDENCIES ------------------------------------------- && \
    R -q -e 'pak::pkg_install("deps::.", lib = .Library, dependencies = TRUE)' && \
    apt-get clean && rm -rf /tmp/* && \
    : ---------------------------------------------------------------------

# =========================================================================
# Use the deps stage to run the tests
FROM test-deps AS test
COPY . /app
RUN : -- TESTS ------------------------------------------------------- && \
    if [ -d tests ]; then \
      R -q -e 'testthat::test_local()'; \
      R -q -e 'covr::package_coverage()'; \
    fi && \
    : ---------------------------------------------------------------------

# =========================================================================
# Run the prod script
FROM deps AS prod
# introduce a dependency on the test stage
COPY --from=test /tmp/dumm[y] /tmp/

# copy everything (could exclude the tests)
COPY . /app

# =========================================================================
# For development we also include devtools & co
FROM test-deps AS dev

RUN R -q -e 'pak::pkg_install(c("devtools", "usethis", "profvis"))'
RUN R -q -e 'pak::pkg_install("languageserver")'

RUN mkdir -p /workspaces

# =========================================================================
# SSH server for running the dev container in Positron
RUN apt-get update && \
    apt-get install -y openssh-server && \
    apt-get clean

RUN echo PermitRootLogin yes >> /etc/ssh/sshd_config && \
    echo PermitEmptyPasswords yes >> /etc/ssh/sshd_config && \
    echo Port 2222 >> /etc/ssh/sshd_config && \
    passwd -d root

RUN git config --global --add safe.directory '/workspaces/*'
