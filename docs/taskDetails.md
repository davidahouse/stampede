## task details

The task details JSON is the internal data structure that is passed between
the server and worker components of stampede. It both contains the details
to execute the task along with the results of the task execution.

### JSON structure

| key    | type    | description  |
|--------|---------|--------------|
| owner  | string | The owner/organization for the code repository |
| repository | string | The name of the repository |
| buildKey | string | The key identifies which part of the repository is for. For example the pull pullRequest, branch or release |
| buildNumber | integer | The build number that the task is a part of |
| buildID | string | The complete build ID that contains owner, repository, buildKey, buildNumber |
| status  | string | The status of the task: queued, in_progress or completed |
| task | object | Details about the task that is to be executed |
| config | object | The config parameters to pass to the task command |
| worker | object | Details about the worker that handles the task |
| scm | object | Details coming from the scm system |
| stats | object | Timestamps captured for the lifecycles of the task execution |
| result | object | The result of the task execution |

### task object

| key    | type    | description  |
|--------|---------|--------------|
| id  | string | The task ID that uniquely identifies the task in the stampede system |
| number | integer | The sequence number of this task in a list of tasks in the build. Because we can have more than one execution of the task id in a single build, we use the position in the list as a further identifier |

### config object

| key    | type    | description  |
|--------|---------|--------------|
| :name  | string | A name value pair that represents a parameter that will be passed to the worker task command via environment variable |

### worker object

| key    | type    | description  |
|--------|---------|--------------|
| node  | string | The name of the node the worker is running on |
| version | string | The `stampede-worker` version |


### scm object

| key    | type    | description  |
|--------|---------|--------------|
| id | string | An identifier of the scm system being used (github, gitlab, bitbucket) |
| externalID | string | An external ID used by the scm system to refer to this task |
| checkRunID | integer | A check run ID for pull request tasks |
| cloneURL | string | The URL to use when cloning the repo |
| sshURL | string | The SSH url to use when cloning using SSH |
| pullRequest | object | For a task related to a pull request, this contains the details of the pull request |
| branch | object | For a task related to a branch, this contains the branch details |
| release | object | For a task related to a release, this contains the release details |


### pullRequest object

| key    | type    | description  |
|--------|---------|--------------|
| url  | string | The URL for the Pull Request in the SCM system |
| id | integer | A unique identifier for the pull request |
| number | integer | The pull request number |
| head | object | The head git information for the pull request |
| base | object | The base git information for the pull request target |

### branch object

| key    | type    | description  |
|--------|---------|--------------|
| name  | string | The name of the branch |
| sha | string | The sha for the branch used in cloning it |

### release object

| key    | type    | description  |
|--------|---------|--------------|
| name  | string | The name of the release |
| tag | string | The tag for the release |
| sha | string | The sha for the release |

### stats object

| key    | type    | description  |
|--------|---------|--------------|
| queuedAt  | date | The time when the task was first queued |
| startedAt | date | The time when the task was started by the worker |
| finishedAt | date | The time when the task was finished |
| duration | integer | The duration of the task execution in ms |

### result object

| key    | type    | description  |
|--------|---------|--------------|
| conclusion | string | success of failure |
| title | string | The title to use when updating the SCM |
| summary | string | A markdown string containing a summary of the task |
| text| string | A markdown string containing detailed text from the task |
