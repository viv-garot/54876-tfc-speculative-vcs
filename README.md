# 54876-tfc-speculative-vcs
How to create  a speculative plan in TFC via a Pull Request in a VCS backed workspace


## Pre-requirements

* [TFC account](https://app.terraform.io/signup/account?utm_source=terraform_io&utm_content=terraform_cloud_hero)
* [GitHub account](https://github.com)

## How to use this repo

- Setup TFC
- Create a PR in GitHub
- Get speculative plan details via the [Terraform Cloud API](https://www.terraform.io/docs/cloud/api/index.html)

----

## Setup TFC

* If you're new to Terraform Cloud
   * Go to [app.terraform.io](https://app.terraform.io/). 
   * Create an [account and setup an organization](https://learn.hashicorp.com/collections/terraform/cloud-get-started)
   * [Setup a vcs provider](https://www.terraform.io/docs/cloud/vcs/index.html) for your organization, we'll be using GitHub for this repro but you can select any other supported vcs provider

* Create a new workspace, type must be **Version control workflow**, select a repository (this example uses viv-garot/tf-null GitHub repo) for which you should have setup a vcs provider in your TFC org's settings. Make sure in **advanced settings** you enable `Automatic speculative plans` as below :

![image](https://user-images.githubusercontent.com/85481359/131632154-226531e3-bde4-42b9-9666-8d1bea810714.png)

## Create a PR in GitHub

* Go to your VCS repo, create a new branch, make a code change and create a Pull Request to the default branch (this is the main branch most of the time), do not merge it. Observe how a check is created with the result of the speculative plan

![image](https://user-images.githubusercontent.com/85481359/131632543-39ead120-5e97-48ae-b78f-82492b162dfd.png)

* Click on Details. This will get you to speculative plan details in Terraform Cloud webpage. Observe the `Heads up` which confirms this is a speculative plan that cannot be applied. Note the run id in the url as you will need it later

![image](https://user-images.githubusercontent.com/85481359/131633089-d17adb85-9e09-451d-922b-ed0e0c943d8d.png)


## Get the Speculative plan details via the API

* As all requests to the Terraform Cloud API need to be authenticated, we need to create a user [token](https://www.terraform.io/docs/cloud/api/index.html#authentication).
In TFC, go to User Setting > Tokens > Create an API token

![image](https://user-images.githubusercontent.com/85481359/131634430-8985168b-1711-4c3d-9f40-67d0acecbfcc.png)

* In your terminal, export the generated token to a _token_ variable

_sample_

```
export token='YOUR-GENERATED-TOKEN'
```

* Show the run (update `<YOUR-RUN-ID>`)

```
curl  --header "Authorization: Bearer $token" --header "Content-Type: application/vnd.api+json" --request GET https://app.terraform.io/api/v2/runs/run-<YOUR-RUN-ID> | python -m json.tool
```

_sample_

```
curl  --header "Authorization: Bearer $token" --header "Content-Type: application/vnd.api+json" --request GET https://app.terraform.io/api/v2/runs/run-edRweCSQTgyWsPvA | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2294    0  2294    0     0   4480      0 --:--:-- --:--:-- --:--:--  4480
{
    "data": {
        "attributes": {
            "actions": {
                "is-cancelable": false,
                "is-confirmable": false,
                "is-discardable": false,
                "is-force-cancelable": false
            },
            "canceled-at": null,
            "created-at": "2021-08-31T15:21:55.266Z",
            "has-changes": true,
            "is-destroy": false,
            "message": "Update main.tf",
            "permissions": {
                "can-apply": false,
                "can-cancel": false,
                "can-comment": true,
                "can-discard": false,
                "can-force-cancel": false,
                "can-force-execute": false,
                "can-override-policy-check": false
            },
            "plan-only": true,
            "refresh": true,
            "refresh-only": false,
            "replace-addrs": null,
            "source": "tfe-configuration-version",
            "status": "planned_and_finished",
            "status-timestamps": {
                "cost-estimated-at": "2021-08-31T15:22:08+00:00",
                "cost-estimating-at": "2021-08-31T15:22:07+00:00",
                "plan-queueable-at": "2021-08-31T15:21:55+00:00",
                "plan-queued-at": "2021-08-31T15:21:55+00:00",
                "planned-and-finished-at": "2021-08-31T15:22:08+00:00",
                "planned-at": "2021-08-31T15:22:07+00:00",
                "planning-at": "2021-08-31T15:21:57+00:00"
            },
            "target-addrs": null,
            "trigger-reason": "disabled"
        },
        "id": "run-edRweCSQTgyWsPvA",
        "links": {
            "self": "/api/v2/runs/run-edRweCSQTgyWsPvA"
        },
        "relationships": {
            "apply": {
                "data": {
                    "id": "apply-SMyVweLudKt8xbFY",
                    "type": "applies"
                },
                "links": {
                    "related": "/api/v2/runs/run-edRweCSQTgyWsPvA/apply"
                }
            },
            "comments": {
                "data": [],
                "links": {
                    "related": "/api/v2/runs/run-edRweCSQTgyWsPvA/comments"
                }
            },
            "configuration-version": {
                "data": {
                    "id": "cv-WeJcrXexQ4CTqDja",
                    "type": "configuration-versions"
                },
                "links": {
                    "related": "/api/v2/runs/run-edRweCSQTgyWsPvA/configuration-version"
                }
            },
            "cost-estimate": {
                "data": {
                    "id": "ce-at59N5nySMgJkMei",
                    "type": "cost-estimates"
                },
                "links": {
                    "related": "/api/v2/cost-estimates/ce-at59N5nySMgJkMei"
                }
            },
            "plan": {
                "data": {
                    "id": "plan-M2g7mAV2K4o2diaR",
                    "type": "plans"
                },
                "links": {
                    "related": "/api/v2/runs/run-edRweCSQTgyWsPvA/plan"
                }
            },
            "policy-checks": {
                "data": [],
                "links": {
                    "related": "/api/v2/runs/run-edRweCSQTgyWsPvA/policy-checks"
                }
            },
            "run-events": {
                "data": [
                    {
                        "id": "re-xXmHoWCqdN6veDfC",
                        "type": "run-events"
                    },
                    {
                        "id": "re-Xy7tJN3HXGQ8RJpz",
                        "type": "run-events"
                    },
                    {
                        "id": "re-CcLKPECMbnFsvFdA",
                        "type": "run-events"
                    },
                    {
                        "id": "re-VKjMUSuH6fngq3oM",
                        "type": "run-events"
                    },
                    {
                        "id": "re-voAVBVam9TriKVPL",
                        "type": "run-events"
                    },
                    {
                        "id": "re-QgjdmJAiPy2mLhwB",
                        "type": "run-events"
                    }
                ],
                "links": {
                    "related": "/api/v2/runs/run-edRweCSQTgyWsPvA/run-events"
                }
            },
            "workspace": {
                "data": {
                    "id": "ws-88KFv4qKV9uMzYmZ",
                    "type": "workspaces"
                }
            }
        },
        "type": "runs"
    }
}
```

* Get the plan details. From the previous command output get the plan id and run (update `<YOUR-PLAN-ID>`) :

```
curl  --header "Authorization: Bearer $token" --header "Content-Type: application/vnd.api+json" --request GET https://app.terraform.io//api/v2/plans/plan-<YOUR-PLAN-ID>/json-output-redacted | python -m json.tool
```

_sample_

```
curl  --header "Authorization: Bearer $token" --header "Content-Type: application/vnd.api+json" --request GET https://app.terraform.io//api/v2/plans/plan-M2g7mAV2K4o2diaR/json-output-redacted | python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  3280    0  3280    0     0   5069      0 --:--:-- --:--:-- --:--:--  5061
{
    "output_changes": {},
    "provider_schemas": {
        "registry.terraform.io/hashicorp/null": {
            "data_source_schemas": {
                "null_data_source": {
                    "block": {
                        "attributes": {
                            "has_computed_default": {
                                "computed": true,
                                "description": "If set, its literal value will be stored and returned. If not, its value defaults to `\"default\"`. This argument exists primarily for testing and has little practical use.",
                                "description_kind": "markdown",
                                "optional": true,
                                "type": "string"
                            },
                            "id": {
                                "computed": true,
                                "deprecated": true,
                                "description": "This attribute is only present for some legacy compatibility issues and should not be used. It will be removed in a future version.",
                                "description_kind": "markdown",
                                "type": "string"
                            },
                            "inputs": {
                                "description": "A map of arbitrary strings that is copied into the `outputs` attribute, and accessible directly for interpolation.",
                                "description_kind": "markdown",
                                "optional": true,
                                "type": [
                                    "map",
                                    "string"
                                ]
                            },
                            "outputs": {
                                "computed": true,
                                "description": "After the data source is \"read\", a copy of the `inputs` map.",
                                "description_kind": "markdown",
                                "type": [
                                    "map",
                                    "string"
                                ]
                            },
                            "random": {
                                "computed": true,
                                "description": "A random value. This is primarily for testing and has little practical use; prefer the [hashicorp/random provider](https://registry.terraform.io/providers/hashicorp/random) for more practical random number use-cases.",
                                "description_kind": "markdown",
                                "type": "string"
                            }
                        },
                        "deprecated": true,
                        "description": "The `null_data_source` data source implements the standard data source lifecycle but does not\ninteract with any external APIs.\n\nHistorically, the `null_data_source` was typically used to construct intermediate values to re-use elsewhere in configuration. The\nsame can now be achieved using [locals](https://www.terraform.io/docs/language/values/locals.html).\n",
                        "description_kind": "markdown"
                    },
                    "version": 0
                }
            },
            "provider": {
                "block": {
                    "description_kind": "plain"
                },
                "version": 0
            },
            "resource_schemas": {
                "null_resource": {
                    "block": {
                        "attributes": {
                            "id": {
                                "computed": true,
                                "description": "This is set to a random value at create time.",
                                "description_kind": "markdown",
                                "type": "string"
                            },
                            "triggers": {
                                "description": "A map of arbitrary strings that, when changed, will force the null resource to be replaced, re-running any associated provisioners.",
                                "description_kind": "markdown",
                                "optional": true,
                                "type": [
                                    "map",
                                    "string"
                                ]
                            }
                        },
                        "description": "The `null_resource` resource implements the standard resource lifecycle but takes no further action.\n\nThe `triggers` argument allows specifying an arbitrary set of values that, when changed, will cause the resource to be replaced.",
                        "description_kind": "markdown"
                    },
                    "version": 0
                }
            }
        }
    },
    "resource_changes": [
        {
            "address": "null_resource.helloWorld",
            "change": {
                "actions": [
                    "delete"
                ],
                "after": null,
                "after_sensitive": false,
                "after_unknown": {},
                "before": {
                    "id": "3023385763558970267",
                    "triggers": null
                },
                "before_sensitive": {}
            },
            "mode": "managed",
            "name": "helloWorld",
            "provider_name": "registry.terraform.io/hashicorp/null",
            "type": "null_resource"
        },
        {
            "address": "null_resource.null",
            "change": {
                "actions": [
                    "no-op"
                ],
                "after": {
                    "id": "7343844911946942692",
                    "triggers": null
                },
                "after_sensitive": {},
                "after_unknown": {},
                "before": {
                    "id": "7343844911946942692",
                    "triggers": null
                },
                "before_sensitive": {}
            },
            "mode": "managed",
            "name": "null",
            "provider_name": "registry.terraform.io/hashicorp/null",
            "type": "null_resource"
        }
    ],
    "resource_drift": []
}
```
