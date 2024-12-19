# Test regression in uv 0.5.10

Ticket in UV Repo: https://github.com/astral-sh/uv/issues/10039

The regression happens when installing a package from a subdirectory ([`projects/subproject`](projects/subproject) in this case) that has a correct [`pyproject.toml`](projects/subproject/pyproject.toml) file (`projects/subproject/pyproject.toml` in this case). However the repository root directory has a [`pyproject.toml`](pyproject.toml) file that does not have a `project.version` field.


## Failure case on latest uv (0.5.10)

```bash
mamba deactivate && mamba remove --name uv_test --all --yes
mamba create --name uv_test --yes python=3.12
mamba activate uv_test
mamba install --channel conda-forge --yes "uv=0.5.10"

uv pip install "test-uv-pyproject-toml @ git+ssh://git@github.com/peterroelants/test_uv_pyproject_toml.git#subdirectory=projects/subproject"
```

results in:
```
Using Python 3.12.8 environment at: /home/peter/miniforge3/envs/uv_test
 Updated ssh://git@github.com/peterroelants/test_uv_pyproject_toml.git (60f1e62)
  × Failed to download and build `test-uv-pyproject-toml @ git+ssh://git@github.com/peterroelants/test_uv_pyproject_toml.git#subdirectory=projects/subproject`
  ├─▶ Failed to parse: `/home/peter/.cache/uv/git-v0/checkouts/01cb0307aefbc565/60f1e62/pyproject.toml`
  ╰─▶ TOML parse error at line 1, column 1
        |
      1 | [project]
        | ^^^^^^^^^
      `pyproject.toml` is using the `[project]` table, but the required `project.version` field is neither set nor present in the `project.dynamic` list
```

## uv 0.5.9 works
```bash
mamba deactivate && mamba remove --name uv_test --all --yes
mamba create --name uv_test --yes python=3.12
mamba activate uv_test
mamba install --channel conda-forge --yes "uv=0.5.9"

uv pip install "test-uv-pyproject-toml @ git+ssh://git@github.com/peterroelants/test_uv_pyproject_toml.git#subdirectory=projects/subproject"
```


## pip works
```bash
mamba deactivate && mamba remove --name uv_test --all --yes
mamba create --name uv_test --yes python=3.12
mamba activate uv_test
mamba install --channel conda-forge --yes "pip=24.3.1"

pip install "test-uv-pyproject-toml @ git+ssh://git@github.com/peterroelants/test_uv_pyproject_toml.git#subdirectory=projects/subproject"
```


## What also works with uv 0.5.10
- Adding a version field to the root project
- Removing the `[project]` table from the root project
