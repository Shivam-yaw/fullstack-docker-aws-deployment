FROM node:18-alpine AS build
WORKDIR /app

COPY package*.json ./
RUN npm ci || npm install

COPY . .

# Production build (Angular 15)
RUN npm run build -- --configuration production

FROM nginx:alpine
# Angular output usually: /app/dist/<project-name>
COPY --from=build /app/dist /usr/share/nginx/html

# If dist has single subfolder (e.g. dist/angular-15-crud), move it up to html root
RUN if [ "$(find /usr/share/nginx/html -mindepth 1 -maxdepth 1 -type d | wc -l)" -eq 1 ] && \
      [ "$(find /usr/share/nginx/html -mindepth 1 -maxdepth 1 -type f | wc -l)" -eq 0 ]; then \
      subdir="$(find /usr/share/nginx/html -mindepth 1 -maxdepth 1 -type d)"; \
      cp -a "$subdir"/. /usr/share/nginx/html/; \
      rm -rf "$subdir"; \
    fi

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
