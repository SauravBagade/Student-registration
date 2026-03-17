# Multi-stage build for React application
FROM node:18-alpine AS build

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies (including dev dependencies for build)
RUN npm ci

# Copy source code
COPY . .

# Build arguments for backend IP detection
ARG BACKEND_HOST
ARG BACKEND_PORT=8080

# Install curl for IP detection
RUN apk add --no-cache curl

# Detect backend IP and update .env file
RUN if [ -n "$BACKEND_HOST" ]; then \
        # If BACKEND_HOST is provided, use it directly
        BACKEND_IP="$BACKEND_HOST"; \
    else \
        # Try to detect the backend IP automatically
        BACKEND_IP=$(curl -s ifconfig.me 2>/dev/null || echo "localhost"); \
    fi && \
    echo "Detected backend IP: $BACKEND_IP" && \
    if [ -f .env ]; then \
        sed -i "s/\$BACKEND/$BACKEND_IP/g" .env; \
        echo "Updated .env file with backend IP: $BACKEND_IP"; \
        cat .env; \
    else \
        echo "VITE_API_URL=http://$BACKEND_IP:$BACKEND_PORT/api" > .env; \
        echo "VITE_API_BASE_URL=http://$BACKEND_IP:$BACKEND_PORT" >> .env; \
        echo "VITE_APP_TITLE=EasyCRUD Student Registration" >> .env; \
        echo "Created .env file with backend IP: $BACKEND_IP"; \
    fi

# Build the application
RUN npm run build

# Production stage with nginx
FROM nginx:alpine

# Install curl for health checks
RUN apk add --no-cache curl

# Copy built application from build stage
COPY --from=build /app/dist /usr/share/nginx/html

# Copy custom nginx configuration
COPY nginx-simple.conf /etc/nginx/conf.d/default.conf

# Use existing nginx user (already exists in nginx:alpine)

# Change ownership of nginx directories
RUN chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    chown -R nginx:nginx /etc/nginx/conf.d && \
    chown -R nginx:nginx /usr/share/nginx/html

# Run as root for proper nginx startup
USER root

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD curl -f http://localhost:3000 || exit 1

# Start nginx
CMD ["nginx", "-g", "daemon off;"] 