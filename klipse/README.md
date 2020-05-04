# Klipse CLJS Analysis Cache

This Dockerfile generates a container environment that is suitable for building the CLJS analysis cache files for
[Klipse][1] deployments. Notably, [re-frame uses it][2] in a GitHub Actions workflow for building the documentation site.

[1]: https://github.com/viebel/klipse
[2]: https://github.com/day8/re-frame/blob/master/.github/workflows/docs-workflow.yml