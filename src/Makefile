TEXMFHOME = $(shell kpsewhich -var-value=TEXMFHOME)
INSTALL_DIR = $(TEXMFHOME)/tex/latex/mtheme
DOC_DIR = $(TEXMFHOME)/doc/latex/mtheme
TEMP_DIR = .temptex

INS = mtheme.ins
PRES_SRC_PT = intro-cuda-pt.tex
PRES_PDF_PT = intro-cuda-pt.pdf
PRES_SRC_EN = intro-cuda-en.tex
PRES_PDF_EN = intro-cuda-en.pdf
SAMP_SRC_EN = intro-cuda-sample-en.tex
SAMP_PDF_EN = intro-cuda-sample-en.pdf
DOC_SRC = mtheme.dtx
DOC_PDF = mtheme.pdf
DTX = $(wildcard *.dtx)
STY = $(patsubst %.dtx,%.sty,$(wildcard beamer*.dtx pgfplotsthemetol.dtx))
CTAN_CONTENT = $(INS) $(DTX) $(DOC_PDF)

TEXC := latexmk -xelatex -output-directory=$(TEMP_DIR)

DOCKER_IMAGE = latex-image
DOCKER_CONTAINER = latex-container


.PHONY: sty doc autotuning-cloud ctan clean install uninstall docker-run docker-build docker-rm

all: sty doc autotuning-cloud

$(STY): $(DTX) $(INS)
	@latex $(INS)

$(PRES_PDF_PT): $(STY) $(PRES_SRC_PT)
	$(TEXC) $(PRES_SRC_PT)
	@cp $(TEMP_DIR)/$(PRES_PDF_PT) .

$(PRES_PDF_EN): $(STY) $(PRES_SRC_EN)
	$(TEXC) $(PRES_SRC_EN)
	@cp $(TEMP_DIR)/$(PRES_PDF_EN) .

$(SAMP_PDF_EN): $(STY) $(SAMP_SRC_EN)
	$(TEXC) $(SAMP_SRC_EN)
	@cp $(TEMP_DIR)/$(SAMP_PDF_EN) .

$(DOC_PDF): $(DOC_SRC) $(DTX)
	@$(TEXC) $(DOC_SRC)
	@cp $(TEMP_DIR)/$(DOC_PDF) .

sty: $(STY)

doc: $(DOC_PDF)

slides-pt: $(PRES_PDF_PT)

slides-en: $(PRES_PDF_EN)

sample-slides-en: $(SAMP_PDF_EN)

ctan: $(CTAN_CONTENT)
	@mkdir -p mtheme
	@cp $(CTAN_CONTENT) mtheme/
	@zip -q mtheme-$(shell grep -A1 ProvidesPackage < beamerthemem.dtx | grep -P -o '\d\.\d\.\d').zip mtheme/*
	@rm -rf mtheme

clean:
	@git clean -xfd

install: $(STY) $(DOC_PDF)
	@mkdir -p $(INSTALL_DIR)
	@cp $(STY) $(INSTALL_DIR)
	@mkdir -p $(DOC_DIR)
	@cp $(DOC_PDF) $(DOC_DIR)

uninstall:
	@rm -f $(addprefix $(INSTALL_DIR)/, $(STY))
	@rm -f $(DOC_DIR)/$(DOC_PDF)
	@rmdir $(INSTALL_DIR)
	@rmdir $(DOC_DIR)

docker-run: docker-build
	docker run --rm=true --name $(DOCKER_CONTAINER) -i -t -v `pwd`:/data $(DOCKER_IMAGE) make

docker-build:
	docker build -t $(DOCKER_IMAGE) .

docker-rm:
	docker rm $(DOCKER_CONTAINER)
