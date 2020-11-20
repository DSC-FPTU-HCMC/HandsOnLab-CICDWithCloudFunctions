![DSC FPTU HCMC](/assets/images/dsc-fptu-hcmc/DSC_FPT_University_HCMC_Horizontal_Logo.png)

### Hands-On Lab
# CI/CD with Cloud Functions

## Table of contents
- [Prerequisite](#prerequisite)
- [Open Cloud Build dashboard](#open-cloud-build-dashboard)
- [STEP 1: Connect to Github repository](#step-1-connect-to-github-repository)
- [STEP 2: Create pull trigger](#step-2-create-pull-trigger)
- [STEP 3: Granting permissions to run builds and deployments](#step-3-granting-permissions-to-run-builds-and-deployments)
- [STEP 4: Build failed: Cloud Functions API has not been enabled](#step-4-build-failed-cloud-functions-api-has-not-been-enabled)
- [STEP 5: Enable Cloud Functions API](#step-5-enable-cloud-functions-api)
- [STEP 6: Manually build again. Build faild: User \[PROJECT_NUMBER@cloudbuild.gserviceaccount.com\] does not have permission to access project](#step-6-manually-build-again-build-faild-user-project-number-cloudbuildgserviceaccountcom-does-not-have-permission-to-access-project)
- [STEP 7: Enable Cloud Resource Manager API](#step-7-enable-cloud-resource-manager-api)
- [STEP 8: Build successfully](#step-8-build-successfully)
- [STEP 9: Test Cloud Functions](#step-9-test-cloud-functions)
- [STEP 10: Commit new code and test again](#step-10-commit-new-code-and-test-again)

## Prerequisite
- Basic knowledge of Cloud Functions, CI/CD

## Open Cloud Build dashboard
- In the Cloud Console page, open the left navigation menu
- Go to section TOOLS, select [Cloud Build](https://console.cloud.google.com/cloud-build/dashboard)

## STEP 1: Connect to Github repository
- In the Cloud Build dashboard, go to Triggers, and click `Connect repository`

![](/assets/images/guidelines/click-connect-to-github.png)

- Authorize Google Cloud Build with Github

![](/assets/images/guidelines/authoriza-google-build-with-github.png)

- Click Install Google Cloud Build

![](/assets/images/guidelines/click-install-google-cloud-build.png)
- Install Google Cloud Build in your repository

![](/assets/images/guidelines/install-google-cloud-build-into-github-app.png)

- Select your repository, and click Connect repository

![](/assets/images/guidelines/click-connect-repository.png)

## STEP 2: Create pull trigger
- Select your repository, and click Create pull trigger

![](/assets/images/guidelines/click-create-trigger.png)

- Trigger has been created, then click on the trigger name to edit

![](/assets/images/guidelines/click-edit-trigger.png)

- Update the trigger name as you want
- Update the description `Push to master`
- Select `Push to a branch`
- Edit the regular expression `^master$`

![](/assets/images/guidelines/edit-trigger.png)

- Select `Cloud Build configuration file (yaml or json)`

![](/assets/images/guidelines/edit-build-configuration.png)

## STEP 3: Granting permissions to run builds and deployments
**1. Act as the Cloud Functions Runtime service account**
- You will get this error message if you don't allow the Cloud Build service account to act as the Cloud Functions Runtime service account
```bash
ERROR: (gcloud.functions.deploy) ResponseError: status=[403], code=[Forbidden],
message=[Missing necessary permission iam.serviceAccounts.actAs for $MEMBER
on the service account dsc-fptu-hcmc-orientation@appspot.gserviceaccount.com.
```
- To solve this problem
```bash
gcloud iam service-accounts add-iam-policy-binding PROJECT_ID@appspot.gserviceaccount.com \
--member="serviceAccount:PROJECT_NUMBER@cloudbuild.gserviceaccount.com" \
--role="roles/iam.serviceAccountUser"
```

![](/assets/images/guidelines/iam-add-role-iam-service-account-user.png)

**2. The Cloud Functions Developer role**
- You will get this error message if you don't assign the Cloud Functions Developer role to the Cloud Build service account, which allows Cloud Build to deploy Cloud Functions
```bash
ERROR: (gcloud.functions.deploy) ResponseError: status=[403], code=[Forbidden],
message=[Permission 'cloudfunctions.functions.get' denied on resource
'projects/dsc-fptu-hcmc-orientation/locations/us-central1/functions/helloCiCd' (or resource may not exist).]
```

- To solve this problem
```bash
gcloud projects add-iam-policy-binding PROJECT_ID \
--member="serviceAccount:PROJECT_NUMBER@cloudbuild.gserviceaccount.com" \
--role="roles/cloudfunctions.developer"
```

![](/assets/images/guidelines/projects-add-role-cloudfunctions-developer.png)

## STEP 4: Build failed: Cloud Functions API has not been enabled
- Open Cloud Build history

![](/assets/images/guidelines/open-cloud-build-history.png)

- Error message
```
ERROR: (gcloud.functions.deploy) PERMISSION_DENIED: Cloud Functions API has not been used in
project PROJECT_NUMBER before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/cloudfunctions.googleapis.com/overview?project=PROJECT_NUMBER then retry.
If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.
```


![](/assets/images/guidelines/failed-cloud-functions-api.png)

## STEP 5: Enable Cloud Functions API
- Open search bar and type `cloud functions api`

![](/assets/images/guidelines/search-cloud-functions-api.png)

- Go to APIs & Services to enable Cloud Functions API

![](/assets/images/guidelines/click-enable-cloud-functions-api.png)

## STEP 6: Manually build again. Build faild: User \[PROJECT_NUMBER@cloudbuild.gserviceaccount.com\] does not have permission to access project
- Error message
```
ERROR: (gcloud.functions.deploy) User [PROJECT_NUMBER@cloudbuild.gserviceaccount.com] does not
have permission to access project [PROJECT_ID:testIamPermissions] (or it may not exist):
Cloud Resource Manager API has not been used in project PROJECT_NUMBER before or it is disabled.
Enable it by visiting https://console.developers.google.com/apis/api/cloudresourcemanager.googleapis.com/overview?project=PROJECT_NUMBER then retry.
If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.
```


![](/assets/images/guidelines/failed-do-not-have-access-to-project.png)

## STEP 7: Enable Cloud Resource Manager API
- Go to APIs & Services to enable Cloud Resource Manager API

. . . do it yourself . . .

- Assign the Cloud Functions Developer role to the Cloud Build service account, which allows Cloud Build to deploy Cloud Functions

## STEP 8: Build successfully

![](/assets/images/guidelines/build-success.png)

## STEP 9: Test Cloud Functions
- Open left navigation menu and go to Cloud Functions
- Click the details tab, read some informations

![](/assets/images/guidelines/go-to-cloud-functions.png)

- Test the function

![](/assets/images/guidelines/test-cloud-functions.png)

## STEP 10: Commit new code and test again
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

![](/assets/images/guidelines/push-new-commit.png)

- Test the function again

![](/assets/images/guidelines/test-cloud-functions-after-updated.png)

## Thank you!
![DSC FPTU HCMC](/assets/images/dsc-fptu-hcmc/HOME_PAGE_BANNERS.png)