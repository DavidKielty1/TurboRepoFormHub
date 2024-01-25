# Builder ------------------------------  ------------------------------
FROM node:lts AS builder
WORKDIR /app
RUN npm i -g pnpm
RUN npm -g add turbo
COPY . .
RUN turbo prune --scope=backend --docker

# Installer ------------------------------  ------------------------------
FROM node:lts AS installer
WORKDIR /app
RUN npm i -g pnpm
COPY .gitignore .gitignore
COPY --from=builder /app/out/json/ .
COPY --from=builder /app/out/pnpm-lock.yaml ./pnpm-lock.yaml
RUN pnpm install
COPY --from=builder /app/out/full/ .  
COPY turbo.json turbo.json
RUN pnpm turbo run build --filter=backend...

# Runner ------------------------------ ------------------------------
FROM node:lts AS runner
WORKDIR /app
RUN npm i -g pnpm
COPY --from=installer /app .
WORKDIR /app/apps/backend
RUN pnpx prisma generate

EXPOSE 8080
CMD [ "pnpm", "start" ]