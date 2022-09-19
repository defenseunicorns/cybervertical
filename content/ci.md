# Modular Pipelines

Issue: [8!](https://github.com/defenseunicorns/cybervertical/issues/8)

The creation of modular CI/CD pipelines will involve a few steps:

1. Creation of central templates / baseline pipelines --> `pipeline-templates` repo
2. Evaluation of build tools required (`make`, `poetry`, `pip`, etc...)
3. Creation of pipeline naming conventions, inheritance, etc...
4. Creation of continuation documentation + training + record of changes

---

## CI Templates Repo

In a newly created repo `pipeline-templates`, the team will create a baseline
CI configuration for _most_ projects.

The goal here is to create modular, unopinionated pipeline jobs that can be stiched
together in countless ways for varying use cases.

There will be a few pre-made, opinionated pipelines that should cover simple, and general use cases.
These, will be placed in a `base/premade/<lang>` dir.

Otherwise, jobs/steps will be created and placed in `base/jobs/<lang>`

## Dependency Images

All CI pipelines will use images either directly from Repo1, or custom images from hardened baselines.

To accomplish this, a review of development dependencies will be carried out.

Dependency's will be identified, pinned, and packaged into Docker containers for CI usage, stored
in `base/images/...`.

## Pipeline Steps

Following Gitlab CI pipeline naming conventions, a series of `.yaml` 
files will be created under `base/jobs/<lang>`, each named after a pipeline stage.

ex.

```terminal
base
└── jobs
    └── <lang>
        ├── build.yaml
        ├── package.yaml
        ├── pre.yaml
        ├── release.yaml
        ├── test.yaml
        └── ...
```

A central import: `base/jobs/<lang>/all.yaml` will import all the jobs from this dir w/ [`includes`](https://docs.gitlab.com/ee/ci/yaml/#include).  
This allows for the import of specific jobs by including the specific file, and for the import of all jobs via the `all.yaml` file.

## Job Usage Convention

Any baseline shell scripts / tooling will be used during the `before_script` step within each stage. 
To disable, upstream custom CI simply sets `before_script: null`.

This allows for a clean baseline, while still allowing for full overrides, any combination of inheritance, etc...

eg.

```yaml
# base/jobs/<lang>/pre.yaml

base-check-env:
  pre_script:
    - echo "Hello There!"
    - env | grep SHELL
```

```yaml
# upstream/.gitlab-ci.yaml

include:
  - <path>/base/jobs/<lang>/pre.yaml

check-env:
  extends: base-check-env
  pre-script: null
  script:
    - echo "General Kenobi!"
```

## Documentation

Documentation will be housed in the `pipeline-templates/docs` dir.

Potential exports:

- `mdbook`
- `mkdocs` static site w/ Gitlab pages
- Viewing in Gitlab (no export)
