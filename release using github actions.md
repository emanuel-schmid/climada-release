# Release and publish climada_python using GitHub actions

## Docs

- check the docs

- (semi) auto update rst files

  ```bash
  python arbitrary\script\make_rst_files.py
  ```

  look at the changes and the untracked files, discard or accept

- create docs locally and listen to the warnings

  ```bash
  cd doc
  rm _build -r
  make html
  ```

## Dependencies

- for climada_petals: update required climada version in setup.py

- update requirements/env_climada.yml

  `python -m cdm.main --suggest-updates`

  ```sql
  SELECT package, `current`, suggested
  FROM suggestions
  WHERE env_name = 'climada_env' AND `date` = (SELECT CURRENT_DATE);
  ```

  &rArr; requirements/env_climada.yaml

- update environment _on Jenkins_ and run
  
  `python -m cdm.main climada_env climada`

  (afterwards, repeating above step should result in an empty table)

- update CHANGELOG.md

  find `$BACKTHEN` and `$LATEST` with

  ```sql
  SELECT id, * FROM environments -- id is what we are looking for
  WHERE env_name = 'climada_env'
    AND `date` IN ('202x-xx-xx', (SELECT CURRENT_DATE));
  ```

  `python -m cdm.main --spread-news $BACKTHEN,$LATEST`
  
  &rArr; CHANGELOG.md

## Merge

**_develop_ &rArr; _main_**

#### expected conflicts

- setup.py
- climada/_version.py
- CHANGELOG.md

Resolve all conflicts "using theirs" (_develop_).\
_main_ and _develop_ are supposed to be exactly the same after merging.

> [!WARNING]
> Make sure the top CHANGELOG subsection is `## Unreleased`!

## API Tokens

- PYPI_API_TOKEN: https://pypi.org/manage/account/
- PERSONAL_ACCESS_TOKEN: https://github.com/settings/tokens

&rArr; https://github.com/CLIMADA-project/climada_python/settings/secrets/actions

## GitHub Actions

https://github.com/CLIMADA-project/climada_python/actions

&rarr; Create a new release &rarr; Run workflow &rarr; select Release level &rarr; push the button

## After the Release

- fetch from origin, updating all tags
- check correct version numbers and changelogs in _main_ and _develop_
- use pip install in vanilla environment and run installation test

## Conda-Forge Release

- create new branch on https://github.com/CLIMADA-project/climada-feedstock
- `python -m cdm.main --spread-news $BACKTHEN,$LATEST` \
  &rArr; recipe/meta.yaml \
  (watch out for trailing spaces)
- make a pull request targeting main on https://github.com/conda-forge/climada-feedstock
