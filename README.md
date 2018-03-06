## Mediafly Extensions Documentation

This repository is meant to be a changelog for the [Extensions documentation](http://devdocs.mediafly.com/extensions).

### Publishing ###
`aws s3 sync . s3://devdocs.mediafly.com/extensions/ --exclude ".DS_Store" --exclude "*.git/*" --exclude "*.md" --acl public-read`
