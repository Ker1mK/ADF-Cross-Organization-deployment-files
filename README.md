# ADF Cross Organization deployment

This repository contains the necessary files to deploy cross organization with ADF

By default ADF does not allow you to deploy outside the current organization. In order to make this possible we have to make a couple of changes to the CDK of ADF. 

First of all we have to make sure that the account that we are going to deploy to is bootstrapped. We have to do this manually because this account is located outside the organization. You can find the default bootstrapping file in this repo. Make sure to provide the parameters in this template with the correct values.

Next on, we have to add the organization of the target account outside the organization to the S3 bucket en KMS policy. By default only the organization where ADF is deployed is allowed. You can do this manually by navigation via the AWS Console or you can make changes to the file that bootstraps the deployment account. Add the organization id of the target account to the KMS and PipelineBucketPolicy. This file is located in ‘adf-bootstrap’ → ‘deployment’ → ‘global.yml’.

<p align="center">
	<img src="https://i.gyazo.com/b37c01c82c8b61735c2313a0a1d4dd77.png" width="350">
</p>

Next we have to choose how we would like to define a cross organization account in the template which deploys the deployment pipeline. You can choose how you would like to distinguish a normal account (inside the organization) and an external account. In this example we are going to use ‘//’ to define an external account.

<p align="center">
	<img src="https://i.gyazo.com/dd7ec5aac57039f0e54c35ce6cb7a427.png" width="350">
</p>

Next we have to write the code for the external accounts so it can be used in the deployment pipeline. We will be adding the code to the ‘target.py’ python file that is being used for generating the targets that will be added to the pipeline. The location of this file is ‘adf-build’ → ‘shared’ → ‘ target.py’’ In this file navigate to the function called ‘fetch_accounts_for_target’. In this function we have to add the following code:

<p align="center">
	<img src="https://i.gyazo.com/1f5071b6402d817da5b2c09a1611dc6c.png" width="350">
</p>

First it will check if the account id starts with a ‘//’. Next when a target is added to the pipeline template with ‘//’ it will remove the ‘//’ from the account id and when it's a valid account id it will return the value to the function called ‘_target_is_account_id_extern’ that we have to create as well.

Next, we will be adding the function called ‘_target_is_account_id_extern’. This is the part where the response is manipulated (because the account is not in the same organization) so it can be added to the pipeline. We will be giving the Id and the name (both id of the target account). Following the status ‘active’ will be given in the response.

<p align="center">
	<img src="https://i.gyazo.com/aea0b1586a7a94946bd5ba115401cfe5.png" width="350">
</p>

	

