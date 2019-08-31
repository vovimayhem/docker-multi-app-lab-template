# Stage 1: Runtime =============================================================
# Step 1: Start off from the ruby official image, alpine version:
FROM ruby:2.5.1-alpine AS runtime

# Step 2: Set the home to the code path, and add the code executables to $PATH:
ENV HOME=/usr/src PATH=/usr/src/bin:$PATH

# Step 3: Set the main working directory:
WORKDIR /usr/src

# Step 4: Add the runtime packages (needed for the app to run) using Alpine's
# package manager:
RUN apk add --no-cache ca-certificates less libpq nodejs openssl tzdata

# Stage 2: Development =========================================================
FROM runtime AS development

# Step 6: Add the development packages (compilers, dev headers, etc):
RUN apk add --no-cache \
  build-base \
  chromium \
  chromium-chromedriver \
  git \
  postgresql-dev

RUN export DOCKERIZE_VERSION=0.6.1 \
 && wget https://github.com/jwilder/dockerize/releases/download/v0.6.1/dockerize-linux-amd64-v${DOCKERIZE_VERSION}.tar.gz \
 && tar -C /usr/local/bin -xzvf dockerize-linux-amd64-v${DOCKERIZE_VERSION}.tar.gz \
 && rm dockerize-linux-amd64-v${DOCKERIZE_VERSION}.tar.gz

# Step 7: Copy the project's Gemfile:
COPY Gemfile* /usr/src/

# Step 8: Download & Install the project's ruby libraries:
RUN bundle install --jobs=4 --retry=3

# Step 9: Set the default command for development images:
CMD ["rails", "server", "-b", "0.0.0.0", "-p", "3000"]

# Stage 3: Testing =============================================================
FROM development AS testing

COPY . /usr/src/

# Stage 4: Builder =============================================================
FROM testing AS builder

RUN DATABASE_URL=postgres://postgres@example.com:5432/fake_db \
  SECRET_KEY_BASE=234567898765432345678987654345678 \
  RAILS_ENV=production \
  rails assets:precompile

RUN bundle config without development:test && bundle clean

# Stage 5: Release =============================================================
FROM runtime AS release

COPY --from=builder /usr/local/bundle /usr/local/bundle

COPY --from=builder /usr/src /usr/src

ENV RAILS_ENV=production RACK_ENV=production PORT=3000

CMD [ "puma" ]