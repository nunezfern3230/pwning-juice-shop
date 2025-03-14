# Integration

OWASP Juice Shop follows strict conventions for describing challenges.
These allow you to easily integrate Juice Shop tutorials, hints and
solutions into your own security guides, knowledge bases, testing labs
etc.

## Challenges API

The endpoint `/api/Challenges` on your local Juice Shop instance (i.e.
<http://localhost:3000/api/Challenges>) returns information on the
configured challenges along with their current state in an easily
consumable JSON format:

```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "key": "restfulXssChallenge",
      "name": "API-only XSS",
      "category": "XSS",
      "tags": "Danger Zone",
      "description": "Perform a <i>persisted</i> XSS attack with <code>&lt;iframe src=\"javascript:alert(`xss`)\"&gt;</code> without using the frontend application at all. <em>(This challenge is <strong>not available</strong> on Heroku!)</em>",
      "difficulty": 3,
      "hint": "You need to work with the server-side API directly. Try different HTTP verbs on different entities exposed through the API.",
      "hintUrl": "https://pwning.owasp-juice.shop/part2/xss.html#perform-a-persisted-xss-attack-without-using-the-frontend-application-at-all",
      "mitigationUrl": "https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html",
      "solved": false,
      "disabledEnv": "Heroku",
      "tutorialOrder": null,
      "codingChallengeStatus": 0,
      "createdAt": "2023-05-18T14:03:24.232Z",
      "updatedAt": "2023-05-18T14:03:24.232Z"
    },
    {
      "id": 2,
      "key": "accessLogDisclosureChallenge",
      "name": "Access Log",
      "category": "Sensitive Data Exposure",
      "tags": null,
      "description": "Gain access to any access log file of the server.",
      "difficulty": 4,
      "hint": "Who would want a server access log to be accessible through a web application?",
      "hintUrl": "https://pwning.owasp-juice.shop/part2/sensitive-data-exposure.html#gain-access-to-any-access-log-file-of-the-server",
      "mitigationUrl": "https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html",
      "solved": false,
      "disabledEnv": null,
      "tutorialOrder": null,
      "codingChallengeStatus": 0,
      "createdAt": "2023-05-18T14:03:24.232Z",
      "updatedAt": "2023-05-18T14:03:24.232Z"
    },
    {
      "...":"..."
    }
  ]
}
```

Instead of your own local instance you can also use the API of the
publicly available demo instances to pull challenge information:

* <http://demo.owasp-juice.shop/api/Challenges> for the latest released
  version (`master` branch)
* <http://preview.owasp-juice.shop/api/Challenges> for the next version
  to be released (`develop` branch)

🚨 _There is no uptime guarantee for either of these as they are both
only free Heroku dynos. Please refrain from making unnecessary amounts
of requests._

### API integration example

* The
  [Juice Shop CTF Extension](https://www.npmjs.com/package/juice-shop-ctf-cli)
  calls the API of a specified Juice Shop instance
  [to retrieve the challenges to later import into a CTF score server](../part1/ctf.md#generating-challenge-import-files-with-juice-shop-ctf-cli).

## Challenge declaration file

The single source of truth for challenges in OWASP Juice Shop is its
`data/static/challenges.yml` file. It is used during start-up of the
application to populate the `Challenges` table which is then exposed via
the [Challenges API](#challenges-api) endpoint.

Parsing this file directly for integration makes sense when you do not
rely on changed settings (e.g. hints being turned off) from an
individual [Customization](../part1/customization.md#customization)
and/or you do not have a running Juice Shop instance available in the
first place.

```yaml
-
  name: 'Some Name'
  category: 'Category of the challenge'
  tags: # (optional) for grouping by aspects beyond category-level
    - Brute Force
    - Code Analysis
    - Contraption
    - Danger Zone
    - Good Practice
    - Good for Demos
    - OSINT
    - Prerequisite
    - Shenanigans
    - Tutorial
  description: 'Here the actual task for the attacker is described.'
  difficulty: 1 # a number between 1 and 6
  hint: 'A text hint to display on the Score Board when hovering over the challenge'
  hintUrl: 'https://pwning.owasp-juice.shop/part2/<category>.html#<shortened description>'
  mitigationUrl: 'https://cheatsheetseries.owasp.org/cheatsheets/<corresponding cheat sheet>.html' # will be empty if n/a
  key: someNameChallenge
  disabledEnv: # (optional) to disable challenges dangerous or incompatible in certain environments
    - Docker
    - Heroku
    - Gitpod
    - Windows
  tutorial: # (optional) present only on challenges with a Hacking Instructor tutorial
    order: 1 # a unique number to specify the recommended order of tutorials
```

The latest versions of the `challenges.yml` file can be found here:

* <https://raw.githubusercontent.com/juice-shop/juice-shop/master/data/static/challenges.yml>
  for the latest released version (`master` branch)
* <https://raw.githubusercontent.com/juice-shop/juice-shop/develop/data/static/challenges.yml>
  for the next version to be released (`develop` branch)

### Generating links to Juice Shop

| Description                                                                                                        | Link composition                                                                 | Condition                                        | Examples                                                                                                                                                                                   |
|:-------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------|:-------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Scroll to a specific challenge on the Score Board _(unless hidden by a filter)_                                    | `/#/score-board/challenge=<name>`                                                |                                                  | <http://localhost:3000/#/score-board?challenge=Score%20Board>                                                                                                                              |
| Link to official hints for a specific challenge                                                                    | `<hintUrl>`                                                                      |                                                  | <https://pwning.owasp-juice.shop/part2/score-board.html#find-the-carefully-hidden-score-board-page> or <https://pwning.owasp-juice.shop/part2/xss.html#perform-a-dom-xss-attack>           |
| Link to official step-by-step solution for a specific challenge                                                    | `https://pwning.owasp-juice.shop/appendix/solutions.html#<hash part of hintUrl>` |                                                  | <https://pwning.owasp-juice.shop/appendix/solutions.html#find-the-carefully-hidden-score-board-page> or <https://pwning.owasp-juice.shop/appendix/solutions.html#perform-a-dom-xss-attack> |
| Direct link to a [Hacking Instructor](../part1/challenges.md#hacking-instructor) tutorial for a specific challenge | `/#/hacking-instructor?challenge=<name>`                                         | Only for challenges where `tutorial` is defined. | <http://localhost:3000/#/hacking-instructor?challenge=Score%20Board> or <http://preview.owasp-juice.shop/#/hacking-instructor?challenge=DOM%20XSS>                                         |

🖼️ _As the utilized GitBook version does not set the
`x-frame-options` header, it is possible to display content from
<https://pwning.owasp-juice.shop> in an `<iframe>`._

### YAML integration example

* The official project website <https://owasp-juice.shop> uses (a copy
  of) the `challenges.yml` to render
  [_Challenge Categories_](https://owasp.org/www-project-juice-shop/#div-challenges)
  and [_Hacking
  Instructor Tutorials_](https://owasp.org/www-project-juice-shop/#div-tutorials)
  tables with the help of
  [Liquid Filters](https://jekyllrb.com/docs/liquid/filters/).

## Challenge solution webhook

Any Juice Shop instance can be configured to call a webhook whenever one
of its {{book.juiceShopNumberOfChallenges}} hacking challenges is
solved. To use this feature the following environment variable needs to
be supplied to the Juice Shop server:

| Environment variable | Expected value                                                                    | Recommendations                                                                                                                                                                                                                                                     |
|:---------------------|:----------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `SOLUTIONS_WEBHOOK`  | URL of the webhook Juice Shop is supposed to call whenever a challenge is solved. | The webhook URL should be bound to the user who solved the challenge and allow its provider to verify the Juice Shop origin instance. In most cases **the webhook URL should be treated as sensitive information and not be published or transmitted unencrypted!** |

### Webhook payload

Juice Shop will send a `POST` request to the configured
`SOLUTIONS_WEBHOOK` with the following payload:

```json
{ "solution":
  { "challenge": "<'key' of the solved challenge from ./data/static/challenges.yml>",
    "cheatScore": "<probability of 0..1 that this solution has been cheated>",
    "totalCheatScore": "<average probability of 0..1 that solutions up until now have been cheated>",
    "issuedOn": "<yyyy-MM-ddThh:mm:ssZ>"
  },
  "ctfFlag": "<CTF flag code of the solved challenged based on the injected (or default) 'CTF_KEY'>",
  "issuer": {
    "hostName": "<server os hostname>",
    "os": "<server os type (and release)>",
    "appName": "<'application.name' from loaded YAML configuration in ./config folder>",
    "config": "<name of the loaded configuration>",
    "version": "<version from ./package.json>"
  }
}
```

#### Webhook payload example

```json
{
    "solution": {
        "challenge": "key",
        "cheatScore": 0,
        "totalCheatScore": 0,
        "issuedOn": "2020-12-15T18:24:33.027Z"
    },
    "ctfFlag": "b0d70dce6cadadb85882ea498fac6785dba2349b",
    "issuer": {
        "hostName": "fv-az116-673",
        "os": "Linux (5.4.0-1031-azure)",
        "appName": "OWASP Juice Shop",
        "config": "default",
        "version": "12.3.0-SNAPSHOT"
    }
}
```

## Vulnerable code snippets API

Any running Juice Shop instance since `v12.7.0` provides two REST
endpoints `/snippets` and `/snippets/<challengeKey>` which can be used
to retrieve the actual vulnerable code snippets for many of the
challenges. These are described in the
[REST endpoints](code-snippets.md#rest-endpoints) section of the
[Vulnerable code snippets](code-snippets.md) appendix.

### API integration example

* The Score Board itself is using these endpoints to decide for which
  challenges to show a code snippet button, and then to subsequently
  retrieve a code snippet when such a button in clicked.
* The
  [Juice Shop CTF Extension](https://www.npmjs.com/package/juice-shop-ctf-cli)
  calls the API of a specified Juice Shop instance
  [to retrieve the snippets to later offer them as a hint on a CTF score server](../part1/ctf.md#generating-challenge-import-files-with-juice-shop-ctf-cli).

