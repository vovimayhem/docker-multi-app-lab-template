FROM node:dubnium-alpine

# Paso 2: Definimos a la locación del proyecto como directorio de trabajo:
WORKDIR /usr/src

# Paso 3: Definimos el directorio de trabajo como "Home", y también agregamos
# el directorio de los ejecutables del proyecto a la variable de entorno PATH:
ENV HOME=/usr/src PATH=/usr/src/bin:$PATH

# Paso 4: Instalamos 'socat', ya que aunque sólo nos es útil en la fase de
# desarrollo, debe estar presente en la imagen de 'release' para hacer staging
# local:
RUN apk add --no-cache socat

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
