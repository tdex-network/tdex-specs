# Contributing to TDEX Specifications

This file define the guidelines and workflow to follow for correctly contributing on this project. 

## Writing specs

The specification is supposed to be readable both in [Markdown](https://en.wikipedia.org/wiki/Markdown) format and plain text.

Some requirements are obvious, some are subtle. They're designed to walk an implementer through the code they have to write, so write them as YOU develop YOUR implementation. Stick with MUST/SHOULD/MAY and NOT: see [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt)

## Extend messages

All message payloads are serialized with Protcol Buffers, in order to add new fields or deprecate older ones you MUST increment the number to maintain backward compatibility.


## Creating Test Vectors

For new low-level protocol constructions, test vectors are necessary. These have traditionally been lines within the spec itself, but the modern trend is to use JSON and separate files. The intent is that they be machine-readable by implementations.

## Workflow

To contribute a patch, the workflow is as follows:

1. Fork repository
2. Create topic branch
3. Commit patches
4. Push changes to your fork
5. Submit a pull request

Guidelines:

- Commits should be atomic and easy to read, messages should be describe in detail to helpful the readers
- If the commit references another issue, add the reference into message
- Write (if as a possible) unit tests and/or functional tests to verify patches
- Existing tests must be work


## Squashing

Committer should squash or rebase commits before it will be merged, following Git [doc](https://git-scm.com/docs/git-rebase).
