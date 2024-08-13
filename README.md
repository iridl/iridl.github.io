# IRI DL partner-facing documentation

This repo contains the documentation served at <http://dldocs.iri.columbia.edu>.

## One-time setup
use python 3.9
```
mkdir ~/venvs
python3 -m venv ~/venvs/sphinx
. ~/venvs/sphinx/bin/activate
pip install -r requirements-frozen.txt
```

## Making changes
Edit markdown files, then
```
. ~/venvs/sphinx/bin/activate
make github
```
then commit changes (to both markdown source files and compiled files) and push. dldocs.iri.columbia.edu will be updated automatically within a few minutes. If your changes don't appear, check <https://github.com/iridl/iridl.github.io/actions> for a failed build job. If you find one, you can look for error messages in its log.
