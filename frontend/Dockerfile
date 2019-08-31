# Stage 1: Runtime =============================================================
FROM node:dubnium-alpine AS runtime

# Paso 2: Definimos a la locación del proyecto como directorio de trabajo:
WORKDIR /usr/src

# Paso 3: Definimos el directorio de trabajo como "Home", y también agregamos
# el directorio de los ejecutables del proyecto a la variable de entorno PATH:
ENV HOME=/usr/src PATH=/usr/src/bin:$PATH

# Paso 4: Instalamos 'socat', ya que aunque sólo nos es útil en la fase de
# desarrollo, debe estar presente en la imagen de 'release' para hacer staging
# local:
RUN apk add --no-cache socat

# Stage 2: Development =========================================================
FROM runtime AS development

# Paso 6: Instalamos dependencias de desarrollo con el package manager de
# Alpine:
RUN apk add --no-cache yarn openssl chromium

# Paso 7: Instalamos chromium usando el administrador de paquetes de Alpine:
RUN apk add --no-cache chromium harfbuzz nss

# Paso 8: Instalamos watchman copiando el ejecutable precompilado dentro de la
# imagen de icalialabs/watchman:
COPY --from=icalialabs/watchman:4-alpine3.8 /usr/local/bin/watchman* /usr/local/bin/

# Paso 9: Creamos el directorio de STATEDIR de watchman:
RUN mkdir -p /usr/local/var/run/watchman \
 && touch /usr/local/var/run/watchman/.not-empty

# Paso 10: Instalamos dependencias de desarrollo con el administrador de paquetes
# de node:
RUN npm install -g ember-cli -g check-dependencies

# Paso 11: Definimos el comando default del contenedor:
CMD ["ember", "server", "--live-reload-port", "35730"]

# Stage 3: Testing =============================================================
FROM development AS testing

COPY package.json yarn.lock /usr/src/

RUN yarn install

COPY . /usr/src

RUN ember build --environment=test

# Stage 4: Builder =============================================================
FROM testing AS builder

RUN ember build --environment=production

RUN yarn install --production

# Stage 5: Release =============================================================
FROM runtime AS release

COPY --from=builder /usr/src/node_modules /usr/src/node_modules

COPY --from=builder /usr/src/dist /usr/src/dist

COPY --from=builder /usr/src/fastboot-server.js /usr/src/reconfigure-app.js /usr/src/

CMD [ "node", "fastboot-server-js" ]
