# Variables
NAME      = ciscotestautomation/pyats
TAG       = latest-alpine
SRC       ?=

.PHONY: help image

help:
	@echo "Please use 'make <target> TAG=<string>' where <target> is one of"
	@echo ""
	@echo " image                build builder image"
	@echo " help                 display this help"
	@echo ""

image:
	@echo ""
	@echo "--------------------------------------------------------------------"
	@echo "Building the builder $(NAME):$(TAG)"
	@echo ""
	@test -n "$(SRC)" || (echo "SRC variable not set. Use make image SRC=/path/to/src" ; exit 1)
	@cp -r $(SRC) ./src
	@docker build -t $(NAME):$(TAG) .
	@rm -rf src/
	@echo ""
	@echo "Done."
	@echo ""

clean:
	@echo ""
	@echo "--------------------------------------------------------------------"
	@echo "Deleting src filder"
	@rm -rf src/
