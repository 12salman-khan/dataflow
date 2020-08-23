1) If beam code is changed then it is checked into Azure DevOps repo. An Azure DevOps Build pipeline gets triggered either dev or master branch. 

2) AzureDevOps build pipeline create the Google Dataflow templates in Cloud Storage bucket. Build pipeline in this use case is created using YAML format. Refer to the link Azure Build Pipelines

3) Build pipeline contains the following tasks to create the templates
i) Script task calls the python utility to upload the metadata file of a Dataflow pipeline to cloud storage. For example 
- script: | python copyMetaDataFile.py $(dtaflowTemplate)_metadata

ii) Gradle build task that accepts the goal & parameters. Goal should be clean run and parameters can be like numWorkers, maxWorkers, project, region.

Refer the link for more details Gradle build task

4) AzureDevops build pipeline saves the build details in Deployment store.

Build pipeline contains the following tasks to save the build details

i) Script task under steps calls the python utility to save the build details in Deployment store. For example - script: | python saveBuildDetails.py $(buildNumber) $(dataflowTemplateLocation) $(runtimeParameters)

ii) Deployment tool that is running on Cloud Run retrieves the build details from Deployment Store when a deployment user accesses it.

5) User selects the specific build and clicks on deployment button in tool. Then deployment tool makes a call to Dataflow API by passing the template & runtime arguments

Uses the API uri POST /v1b3/projects/{projectId}/templates. Refer the link for more details API documentation

6) Dataflow API gets the template from cloud storage 

7) Dataflow API submits the job to Dataflow Engine.
