FROM node as build
COPY index.js /src/
COPY package* /src/
RUN cd /src && \
    npm install
FROM node
COPY --from=build /src/node_modules /dist/node_modules/
COPY index.js /dist/
WORKDIR /dist
EXPOSE 80
CMD ["node", "index.js"]
