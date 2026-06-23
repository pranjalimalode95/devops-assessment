# -------- BUILD STAGE --------
    FROM python:3.14-slim AS builder

    WORKDIR /app
    COPY requirements.txt .
    
    RUN pip install --upgrade pip && \
        pip wheel --no-cache-dir --no-deps --wheel-dir /wheels -r requirements.txt
    
    # -------- RUN STAGE --------
    FROM python:3.14-slim
    
    RUN adduser --disabled-password appuser
    
    WORKDIR /app
    
    # Install packages as root
    COPY --from=builder /wheels /wheels
    RUN pip install --no-cache /wheels/*
    
    # Copy application files
    COPY . .
    
    # Set ownership
    RUN chown -R appuser:appuser /app
    
    # Switch to non-root user
    USER appuser
    
    EXPOSE 8000
    
    # Run migrations, create superuser, and start server
    CMD sh -c "python manage.py migrate --noinput && python create_superuser.py && python manage.py runserver 0.0.0.0:8000"
    