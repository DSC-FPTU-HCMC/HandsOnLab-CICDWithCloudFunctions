# HandsOnLab-ci-cd-with-cloud-functions

## Table of contents
- [Prerequisite](#prerequisite)
- [Open Cloud Build dashboard](#open-cloud-build-dashboard)
- [STEP 1: Connect to Github repository](#step-1-connect-to-github-repository)
- [STEP 2: Create pull trigger](#step-2-create-pull-trigger)
- [STEP 3: Build failed: Cloud Functions API has not been enabled](#step-3-build-failed-cloud-functions-api-has-not-been-enabled)
- [STEP 4: Enable Cloud Functions API](#step-4-enable-cloud-functions-api)
- [STEP 5: Manually build again. Build faild: User \[PROJECT_NUMBER@cloudbuild.gserviceaccount.com\] does not have permission to access project](#step-5-manually-build-again-build-faild-user-project-number-cloudbuildgserviceaccountcom-does-not-have-permission-to-access-project)
- [STEP 6: Enable Cloud Resource Manager API](#step-6-enable-cloud-resource-manager-api)
- [STEP 7: Build successfully](#step-7-build-successfully)
- [STEP 8: Test Cloud Functions](#step-8-test-cloud-functions)
- [STEP 9: Commit new code and test again](#step-9-commit-new-code-and-test-again)

## Prerequisite
- Basic knowledge of Cloud Functions, CI/CD

## Open Cloud Build dashboard
- In the Cloud Console page, open the left navigation menu
- Go to section TOOlS, select [Cloud Build](https://console.cloud.google.com/cloud-build/dashboard)

## STEP 1: Connect to Github repository
- In the Cloud Build dashboard, go to Triggers, and click CONNECT REPOSITORY
![](/assets/images/click-connect-to-github.png)

- Authorize Google Cloud Build with Github
![](/assets/images/authoriza-google-build-with-github.png)

- Click Install Google Cloud Build
![](/assets/images/click-install-google-cloud-build.png)
- Install Google Cloud Build in your repository
![](/assets/images/install-google-cloud-build-into-github-app.png)

- Select your repository, and click Connect repository
![](/assets/images/click-connect-repository.png)

## STEP 2: Create pull trigger
- Select your repository, and click Create pull trigger
![](/assets/images/click-create-trigger.png)

- Trigger has been created, then click on the trigger name to edit
![](/assets/images/click-edit-trigger.png)

- Update the trigger name as you want
- Update the description `Push to master`
- Select `Push to a branch`
- Edit the regular expression `^master$`
![](/assets/images/edit-trigger.png)

- Select `Cloud Build configuration file (yaml or json)`
![](/assets/images/edit-build-configuration.png)

## STEP 3: Build failed: Cloud Functions API has not been enabled
- Open Cloud Build history
![](/assets/images/open-cloud-build-history.png)

- Error message
```
ERROR: (gcloud.functions.deploy) PERMISSION_DENIED: Cloud Functions API has not been used in project PROJECT_NUMBER before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/cloudfunctions.googleapis.com/overview?project=PROJECT_NUMBER then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.
```

![](/assets/images/failed-cloud-functions-api.png)

## STEP 4: Enable Cloud Functions API
- Open search bar and type `cloud functions api`
![](/assets/images/search-cloud-functions-api.png)

- Go to APIs & Services to enable Cloud Functions API
![](/assets/images/click-enable-cloud-functions-api.png)

- Allow the Cloud Build service account to act as the Cloud Functions Runtime service account
```bash
gcloud iam service-accounts add-iam-policy-binding PROJECT_ID@appspot.gserviceaccount.com \
--member="serviceAccount:707493577884@cloudbuild.gserviceaccount.com" \
—-role="roles/iam.serviceAccountUser"
```

![](/assets/images/iam-add-role-iam-service-account-user.png)

## STEP 5: Manually build again. Build faild: User \[PROJECT_NUMBER@cloudbuild.gserviceaccount.com\] does not have permission to access project
- Error message
```
ERROR: (gcloud.functions.deploy) User [PROJECT_NUMBER@cloudbuild.gserviceaccount.com] does not have permission to access project [PROJECT_ID:testIamPermissions] (or it may not exist): Cloud Resource Manager API has not been used in project PROJECT_NUMBER before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/cloudresourcemanager.googleapis.com/overview?project=PROJECT_NUMBER then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.
```

![](/assets/images/failed-do-not-have-access-to-project.png)

## STEP 6: Enable Cloud Resource Manager API
- Go to APIs & Services to enable Cloud Resource Manager API

. . . do it yourself . . .

- Assign the Cloud Functions Developer role to the Cloud Build service account, which allows Cloud Build to deploy Cloud Functions
```bash
gcloud projects add-iam-policy-binding dsc-fptu-hcmc-orientation \
--member="serviceAccount:707493577884@cloudbuild.gserviceaccount.com" \
--role="roles/cloudfunctions.developer"
```

![](/assets/images/projects-add-role-cloudfunctions-developer.png)

## STEP 7: Build successfully
![](/assets/images/build-success.png)

## STEP 8: Test Cloud Functions
- Open left navigation menu and go to Cloud Functions
- Click the details tab, read some informations
![](/assets/images/go-to-cloud-functions.png)

- Test the function
![](/assets/images/test-cloud-functions.png)

## STEP 9: Commit new code and test again
- Edit the file `index.js`, update the response message
```js
/**
* HTTP Cloud Function.
*
* @param {Object} req Cloud Function request context.
* More info: https://expressjs.com/en/api.html#req
* @param {Object} res Cloud Function response context.
* More info: https://expressjs.com/en/api.html#res
*/
exports.helloCiCd = (req, res) => {
  res.send({ yourName: req.body.name });
};
```

- Commit the code and push to your repository

. . . do it yourself . . .

![](/assets/images/push-new-commit.png)

- Test the function again
![](/assets/images/test-cloud-functions-after-updated.png)

## Thank you!