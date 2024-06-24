build:
	docker build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.1 .

build_dev:
	docker build --build-arg ENVIRONMENT=development -t edk24/docker-php:8.1-dev .

build_and_push:
	docker buildx build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.1 --platform linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64/v8,linux/386 --push .	

build_and_push_dev:
	docker buildx build --build-arg ENVIRONMENT=development -t edk24/docker-php:8.1-dev --platform linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64/v8,linux/386 --push .	
