cbor := $(patsubst %.edn,%.cbor,$(wildcard *.edn))
pretty := $(patsubst %.edn,%.pretty,$(wildcard *.edn))
targets := $(cbor) $(pretty)
current_dir := $(shell pwd)
.SECONDARY: $(%.diag)
content := $(filter-out report.cbor implied-original.cbor,$(cbor))

all: $(targets)

%.diag: %.edn
	gem exec --conservative edn-abnf -t diag $< > $@

%.cbor: %.diag
	gem exec --conservative -g cbor-diag diag2cbor.rb $< > $@

%.pretty: %.diag
	gem exec --conservative -g cbor-diag diag2pretty.rb $< > $@

validate-all: validate-content validate-report validate-implied

validate-content: $(content)
	cddl compile-cddl --cddl ../mimi-content.cddl
	for c in $(content); do gem exec --conservative cddl ../mimi-content.cddl validate $${c}; done

validate-report: report.cbor
	cddl compile-cddl --cddl ../delivery-report.cddl
	gem exec --conservative cddl ../delivery-report.cddl validate report.cbor
	#cddl-rs validate -d ../delivery-report.cddl -c report.cbor

validate-implied: implied-original.cbor
	cddl compile-cddl --cddl ../implied.cddl
	gem exec --conservative cddl ../implied.cddl validate implied-original.cbor
	#cddl-rs validate -d ../implied.cddl -c implied-original.cbor

help:
	@echo '1. Install these things:'
	@echo '  (sudo) gem install cbor-diag'
	@echo '  (sudo) gem install edn-abnf'
	@echo '  (sudo) gem install cddl'
	@echo '  cargo install cddl'
	@echo '2. Fix your paths if needed (especially on a Mac)'
	@echo '3. Profit'

.SUFFIXES:
