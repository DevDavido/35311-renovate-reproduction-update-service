# Renovate Minimal Reproducible Example

## Current behavior

Originally Azure Platform is used. Created reproducible example on GitHub to demonstrate the issue.

We try to integrate Renovate to update a couple of repositories. This renovate repo [renovate-reproduction-update-service](https://github.com/DevDavido/35311-renovate-reproduction-update-service) contains a global config file ("renovate-global-config.json").
The repository [renovate-reproduction-updatable-dependencies-project](https://github.com/DevDavido/35311-renovate-reproduction-updatable-dependencies-project) to update also contains a local config file ("renovate.json."). It looks like follows:
```
{
     "$schema": "https://docs.renovatebot.com/renovate-schema.json",
     "extends": [
        // Original Azure reference: 
        // "local>Products/dev-renovate-update-service:renovate-global-config#0.1.0"
        
        "github>DevDavido/35311-renovate-reproduction-update-service:renovate-global-config#0.1.0"
     ]
}
```

But when executing the Azure pipeline or [GitHub workflow](https://github.com/DevDavido/35311-renovate-reproduction-update-service/actions/runs/15185965684/job/42706317055#step:3:385) to run the renovate update we get the following error:

```
Preset fetch error (repository=Products/example-project)
       "preset": "local>Products/dev-renovate-update-service:renovate-global-config#0.1.0",
       "err": {
         "message": "dep not found",
         "stack": "Error: dep not found\n    at fetchJSONFile (/usr/local/renovate/lib/config/presets/local/common.ts:29:11)\n    at processTicksAndRejections (node:internal/process/task_queues:105:5)\n    at fetchPreset (/usr/local/renovate/lib/config/presets/util.ts:60:19)\n    at getPreset (/usr/local/renovate/lib/config/presets/index.ts:132:20)\n    at fetchPreset (/usr/local/renovate/lib/config/presets/index.ts:269:12)\n    at Object.resolveConfigPresets (/usr/local/renovate/lib/config/presets/index.ts:202:31)\n    at mergeRenovateConfig (/usr/local/renovate/lib/workers/repository/init/merge.ts:235:5)\n    at getRepoConfig (/usr/local/renovate/lib/workers/repository/init/config.ts:14:12)\n    at initRepo (/usr/local/renovate/lib/workers/repository/init/index.ts:58:12)\n    at Object.renovateRepository (/usr/local/renovate/lib/workers/repository/index.ts:65:14)\n    at attributes.repository (/usr/local/renovate/lib/workers/global/index.ts:206:11)\n    at start (/usr/local/renovate/lib/workers/global/index.ts:191:7)\n    at /usr/local/renovate/lib/renovate.ts:19:22"
       }
INFO: Throwing preset error (repository=Products/example-project)
       "validationError": "Cannot find preset's package (local>Products/dev-renovate-update-service:renovate-global-config#0.1.0)"
INFO: Repository has invalid config (repository=Products/example-project)
       "error": {
         "validationError": "Cannot find preset's package (local>Products/dev-renovate-update-service:renovate-global-config#0.1.0)",
         "message": "config-validation",
         "stack": "Error: config-validation\n    at fetchPreset (/usr/local/renovate/lib/config/presets/index.ts:278:19)\n    at processTicksAndRejections (node:internal/process/task_queues:105:5)\n    at Object.resolveConfigPresets (/usr/local/renovate/lib/config/presets/index.ts:202:31)\n    at mergeRenovateConfig (/usr/local/renovate/lib/workers/repository/init/merge.ts:235:5)\n    at getRepoConfig (/usr/local/renovate/lib/workers/repository/init/config.ts:14:12)\n    at initRepo (/usr/local/renovate/lib/workers/repository/init/index.ts:58:12)\n    at Object.renovateRepository (/usr/local/renovate/lib/workers/repository/index.ts:65:14)\n    at attributes.repository (/usr/local/renovate/lib/workers/global/index.ts:206:11)\n    at start (/usr/local/renovate/lib/workers/global/index.ts:191:7)\n    at /usr/local/renovate/lib/renovate.ts:19:22"
       }
```

But we don't get the error in case the extends in the local repo config is changed to:
```
{
    "$schema": "https://docs.renovatebot.com/renovate-schema.json",
    "extends": [
      // Original Azure reference: 
      "local>Products/dev-renovate-update-service:renovate-global-config.json"
    ]
}
```

All tries dealing with a tag version produces a similar error. The only way to get it working was removing the tag version.

We used the docker image "[39-full](https://hub.docker.com/layers/renovate/renovate/39-full/images/sha256-452e38e819078aa02a36915e4189d81979ab460896280462a472fd6e14d60575)"

## Expected behavior

Renovate on Azure platform should be able to use the tag version in the extends property of the local config file.
For my needs it is imperative that the used configuration is based on tags because we plan to extend the update for multiple repositories.

## Link to the Renovate issue or Discussion

[35311](https://github.com/renovatebot/renovate/discussions/35311)