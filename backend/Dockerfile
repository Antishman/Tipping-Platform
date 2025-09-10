# Use official PHP 8.2 + FPM image
FROM php:8.2-fpm

# Set working directory
WORKDIR /var/www/html

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    libzip-dev \
    default-mysql-client \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Composer
COPY --from=composer:2.6 /usr/bin/composer /usr/bin/composer

# Copy existing application
COPY . .

# Set permissions for storage and cache
RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache \
    && chmod -R 775 /var/www/html/storage /var/www/html/bootstrap/cache

# Install PHP dependencies
RUN composer install --no-dev --optimize-autoloader

# Set environment variables (Render uses its ENV)
ENV APP_ENV=production

# Cache config, routes, and views
RUN php artisan config:cache \
    && php artisan route:cache \
    && php artisan view:cache

# Link storage for public access
RUN php artisan storage:link

# Expose port (Render will inject $PORT)
EXPOSE 10000

# Run migrations and start PHP server on container start
CMD php artisan migrate --force && php artisan serve --host=0.0.0.0 --port=$PORT
