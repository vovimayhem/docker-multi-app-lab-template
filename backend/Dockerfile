# Step 1: Start off from the ruby official image, alpine version:
FROM ruby:2.5.1-alpine

# Step 2: Set the home to the code path, and add the code executables to $PATH:
ENV HOME=/usr/src PATH=/usr/src/bin:$PATH

# Step 3: Set the main working directory:
WORKDIR /usr/src

# Step 4: Add the runtime packages (needed for the app to run) using Alpine's
# package manager:
RUN apk add --no-cache ca-certificates less libpq nodejs openssl tzdata

# Step 6: Add the development packages (compilers, dev headers, etc):
RUN apk add --no-cache \
  build-base \
  chromium \
  chromium-chromedriver \
  git \
  postgresql-dev

# Step 7: Copy the project's Gemfile:
COPY Gemfile* /usr/src/

# Step 8: Download & Install the project's ruby libraries:
RUN bundle install --jobs=4 --retry=3

# Step 9: Set the default command for development images:
CMD ["rails", "server", "-b", "0.0.0.0", "-p", "3000"]
