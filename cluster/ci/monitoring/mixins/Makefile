#
SHELL=/usr/bin/env bash -o pipefail

dashboards = build_cop boskos canary deck dptp ghproxy hook jenkins_operator plank prow sinker tide

all: $(dashboards) prometheus_rule_prow.yaml

$(dashboards):
	@echo "Generating $@.json ..."
	jsonnet -J vendor/ grafana_dashboards/$@.jsonnet > ../$@.json

prometheus_rule_prow.yaml:
	@echo "Generating prometheus_rule_prow.yaml ..."
	jsonnet  ./prometheus/prometheus_rule_prow.jsonnet | gojsontoyaml > ../$@

clean:
	rm -rfv ./vendor

install:
	jb install

validate-latest: clean install all
	@echo "Generating sinker.json ..."
	git status -s ../
	test -z "$$(git status -s ../)"

.PHONY : generate-k8s-dashboards
generate-k8s-dashboards: clean install
	cd ./vendor/kubernetes-mixin; jb install
	make -C ./vendor/kubernetes-mixin dashboards_out
	rm -rfv ./vendor/kubernetes-mixin/vendor
	find ./vendor/kubernetes-mixin/dashboards_out -name "*.json"
