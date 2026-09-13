# Jeff Portfolio

This repository contains the static portfolio website for Jeffrey Buskirk.

## Publish / deploy status

The site currently lives in the outside-OneDrive workspace copy at:
`C:\Users\jgbus\Projects\Jeff_Portfolio`

The repo was already connected to a GitHub remote:
`https://github.com/JgBuskirk/jeffreybuskirkaero.git`

The Netlify CLI was authenticated and linked to the project using the existing GitHub remote.

Attempted production deployment command:
`netlify deploy --prod --dir .`

Result:
The command failed with `JSONHTTPError: Forbidden` due to Netlify account/credit availability, not due to a source or HTML error.

Repository state:
- Git status is clean.
- The GitHub remote is already set and in sync with the local branch.
- The Netlify project is linked for the repo and ready to redeploy when credits are available.

Expected next action when credits reset:
- Run `netlify deploy --prod --dir .` from `C:\Users\jgbus\Projects\Jeff_Portfolio`.
- Alternatively open Netlify and trigger a deployment from the linked project.
