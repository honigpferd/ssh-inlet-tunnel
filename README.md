# chart-repo-template

a template for a chart repo

## usage

* clone this repo (include branches)
* double check gh-pages [deployment is configured](../../settings/pages)
* replace `README.md`
* add chart files to `charts/<mychart>/`
  * `Charts.yaml`
  * `values.yaml`
  * `templates/...yaml`
  * `ci/values.yaml` (if ci test needs proper values)
* await autorelease of chart
* use it ...
