# -------- BUILD STAGE --------
    FROM node:20-alpine AS builder

    WORKDIR /app
    COPY package*.json ./
    RUN npm install
    COPY . .
    RUN npm run build
    
    # -------- RUN STAGE --------
    FROM nginx:alpine
    
    # Copy built files
    COPY --from=builder /app/dist /usr/share/nginx/html
    
    # Create nginx cache directories and set permissions
    # Note: nginx needs to run as root to bind to port 80
    # The base nginx:alpine image handles user switching internally
    RUN mkdir -p /var/cache/nginx/client_temp \
        /var/cache/nginx/proxy_temp \
        /var/cache/nginx/fastcgi_temp \
        /var/cache/nginx/uwsgi_temp \
        /var/cache/nginx/scgi_temp && \
        chmod -R 755 /var/cache/nginx
    
    EXPOSE 80
    CMD ["nginx", "-g", "daemon off;"]
    