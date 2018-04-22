```makefile
.PHONY: all debug clean

PANDOC = pandoc $(PANDOC_FLAGS) $(SOURCE)/header.md $< $(SOURCE)/footer.md
PANDOC_FLAGS = -s -t html+smart -f markdown+smart -M pagetitle=$(PAGETITLE)
PAGETITLE = "$(notdir $(basename $<))-robert's homepage"
# PANDOC_FLAGS = -s -S -H ./pandoc/github-markdown.css.html

SOURCE := md_src
OUT := .

# MDFILES = $(wildcard $(SOURCE)/pages/*.md)
rwildcard = $(foreach d,$(wildcard $1*),$(call rwildcard,$d/,$2) $(filter $(subst *,%,$2),$d))



MDFILES := $(call rwildcard,$(SOURCE)/pages/,*.md)

HTMLFILES = $(patsubst $(SOURCE)/pages/%.md,%.html,$(MDFILES))

all: $(HTMLFILES) makefile.html

debug:
	@echo $(MDFILES)
	@echo $(HTMLFILES)

define htmlrecipe

$1: $2
	$(PANDOC) -o $@

endef

$(call htmlrecipe,poop.html,poop.md)

%.html: $(SOURCE)/pages/%.md $(SOURCE)/header.md $(SOURCE)/footer.md
	$(PANDOC) -o $@

clean:
	rm $(HTMLFILES)

# ============================ #
# === Special page recipes === #
# ============================ #

$(SOURCE)/pages/makefile.md: Makefile
	@echo "\`\`\`makefile" > $@
	@cat Makefile >> $@
	@echo "\`\`\`" >> $@

$(SOURCE)/pages/index.md: $(filter-out $(SOURCE)/pages/index.md,$(MDFILES))
	@# Make a tempfile
	@# Create index of pages into tempfile
	@# Delete everything between [S and [E in index.md
	@# Insert contents of temp file in index.md
	@# Insert newline before [E in index.md
	@# Delete tempfile
	@TEMP=$$(mktemp); \
	for page in $(basename $(HTMLFILES)); do \
		echo "- [$$page]($$page.html)" >> $$TEMP; \
	done; \
	sort $$TEMP -o $$TEMP; \
	sed '/^\[S/,/^\[E/{/^\[/!d;}' -i $@; \
	sed "/^\[S/r $$TEMP" -i $@; \
	sed '/^\[E/i\ ' -i $@; \
	rm $$TEMP
	@echo Made $@

```
