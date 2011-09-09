# Usually you don't need to change this file at all.
# However, read the following comments to know when
# you do have to tweak it. Use with GNU make.

# By default, this Makefile will remake the most recent
# target in the current directory. Run 'make thebook.pdf'
# at the first compilation, and simply 'make' after that.
# Uncomment this line if you do 'make clean' often.
TARGET = tff1spec.pdf

# Uncomment this line if your document is spread across
# several subdirectories. This Makefile doesn't dare to
# descend there by itself.
#SUBDIRS = preface part1 part2 part3 epilogue

# Uncomment the following lines if this Makefile guesses
# wrong what source files your document depends on.
#TEXFILES =
#BIBFILES =
#FIGFILES =

# Uncomment this line if your document depends on any
# sources other than .tex, .bib, or .fig files.
#COMFILES =

# Edit the following lines if you don't like them.
BIBTEX = bibtex
MINDEX = makeindex -s gind.ist
MGLOSS = makeindex -s gglo.ist -o $*.gls -t $*.glg

PAPER = a4

LATEXOPTION = -halt-on-error

FIGURES =

#################################
# PRIVATE INTELLECTUAL PROPERTY #
#        NO TRESPASSING         #
#################################

TARGET   ?= $(if $(OUTFILES),$(firstword $(shell ls -t $(OUTFILES))),help)

TEXFILES ?= $(wildcard *.tex $(addsuffix /*.tex,$(SUBDIRS)))
BIBFILES ?= $(wildcard *.bib $(addsuffix /*.bib,$(SUBDIRS)))
FIGFILES ?= $(wildcard *.fig $(addsuffix /*.fig,$(SUBDIRS)))

SRCFILES = $(TEXFILES) $(COMFILES) $(INDFILES) $(GLSFILES) $(IMGFILES)
SECFILES = $(LOGFILES) $(BBLFILES) $(INDFILES) $(GLSFILES) $(IMGFILES)

IMGFILES = $(foreach ex,eps  pdf  tex_t, $(FIGFILES:.fig=.$(ex)))
DOCFILES = $(foreach ex,dvi ps 2.ps pdf, $(TEXFILES:.tex=.$(ex)))
OUTFILES = $(wildcard $(DOCFILES))

IDXFILES = $(wildcard *.idx)
GLOFILES = $(wildcard *.glo)
INDFILES = $(IDXFILES:.idx=.ind)
GLSFILES = $(GLOFILES:.glo=.gls)
LOGFILES = $(TEXFILES:.tex=.log)
BBLFILES = $(TEXFILES:.tex=.bbl)

cancel = rm -f $*.log
exists = test -f $(1)
recent = ! test $@ -ot $*.log
unroll = touch -r $(1).bak $(1)
commit = (cp $(1) $(1).bak && $(cancel))
compar = (cmp -s $(1) $(1).bak && $(unroll))
verify = ! $(exists) || $(compar) || $(commit)
remake = $(MAKE) -f $(MAKEFILE_LIST) .$*.mk

.PHONY: .%.mk help export clean view
.SECONDARY: $(SECFILES)
.DELETE_ON_ERROR:

$(TARGET):

view: $(TARGET)
	xpdf -remote _$(TARGET) -exec reload

%.dvi: %.tex
%.dvi: DVI = dvi
%.pdf: PDF = pdf
export DVI PDF

OUT = $(DVI)$(PDF)

%.dvi %.pdf : $(if $(BIBFILES),%.bbl) .%.mk
	@$(recent) || $(cancel) $(IMGFILES)
	@$(call exists,$*.log) || $(remake)
	@touch -c $*.bbl $@

.%.mk : %.log
	@! grep -q 'cross-references right' $*.log || $(cancel)
	@$(foreach ex,bbl toc lot lof,$(call verify,$*.$(ex));)
	@for ix in *.idx *.glo ; do $(call verify,$$ix); done;
	@$(call exists,$*.log) || $(remake)

%.log : %.tex $(SRCFILES)
	-$(PDF)latex $(LATEXOPTION) $*

%.bbl : %.log $(BIBFILES)
	$(BIBTEX) $* $(if $(OUT),|| (rm -f $*.$(OUT) && false))

%.ind : %.idx
	$(MINDEX) $< $(if $(OUT),|| (rm -f $*.$(OUT) && false))

%.gls : %.glo
	$(MGLOSS) $< $(if $(OUT),|| (rm -f $*.$(OUT) && false))

#################
# JANITOR STUFF #
#################

AUXFILES = $(wildcard $(ATXFILES) $(AIXFILES) $(AGLFILES) $(IMGFILES) *.bak)
ATXFILES = $(foreach ex,aux log toc lof lot bbl blg, $(TEXFILES:.tex=.$(ex)))
AIXFILES = $(foreach ex,ind ilg, $(IDXFILES:.idx=.$(ex))) $(IDXFILES)
AGLFILES = $(foreach ex,gls glg, $(GLOFILES:.glo=.$(ex))) $(GLOFILES)

export:
	$(if $(AUXFILES), rm -f $(AUXFILES))

clean:	export
	$(if $(OUTFILES), rm -f $(OUTFILES))

help:
	@echo "Usage: make x.(dvi|ps|2.ps|pdf)"
	@echo "       make [export|clean|help]"

###############
# CONVERSIONS #
###############

%.tex_t : %.fig %.eps %.pdf
	$(if $(DVI),fig2dev -L pstex_t  -p $*.eps $< $@)
	$(if $(PDF),fig2dev -L pdftex_t -p $*.pdf $< $@)

%.eps : %.fig
	$(if $(PDF),,fig2dev -L pstex  $< $@)

%.pdf : %.fig
	$(if $(DVI),,fig2dev -L pdftex $< $@)

%.ps : %.dvi
	dvips -t $(PAPER) -Pcmz -Pamz -o $@ $<

%.2.ps : %.ps
	psnup -p$(PAPER) -P$(PAPER) -n 2 $< $@

