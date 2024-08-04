FROM node:lts-alpine AS build-stage

ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
ENV ENVMODE=local

ARG CACHEBUST=1

RUN corepack enable

WORKDIR /build

COPY . .

RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --frozen-lockfile

RUN pnpm run build


# production stage
FROM nginx:alpine AS production-stage

ARG user
ARG uid

COPY --from=build-stage /build/dist /usr/share/nginx/html/app

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx/nginx.conf /etc/nginx/conf.d

# Set up user
# RUN adduser -u $uid -D $user
# RUN mkdir -p /etc/sudoers.d \
#     && echo "$user ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/$user \
#     && chmod 0440 /etc/sudoers.d/$user

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]