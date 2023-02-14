# Perform Foundational Infrastructure Tasks in Google Cloud - Challenge Lab
You can find the Perform Foundational Infrastructure Tasks in Google Cloud Qwiklab [here](https://www.cloudskillsboost.google/quests/118)

## Task 1: Create a bucket

1. Navigation menu > **Cloud Storage** > Browser > Create Bucket
2. Name your bucket > Enter **GCP Project ID** > Continue
3. Choose where to store your data > **Region:** us-east1 > Continue
4. Use default for the remaining 
5. Create

## Task 2: Create a Pub/Sub topic

1. Navigation menu > **Pub/Sub** > Topics
2. Create Topic > **Name:** Jooli > Create Topic

## Task 3: Create the thumbnail Cloud Function

1. Navigation menu > **Cloud Functions** > Create Function

2. Use the following config:

   **Name:** CFJooli
   **Region:** us-east1
   **Trigger:** Cloud Storage
   **Event type:** Finalize/Create
   **Bucket:** BROWSE > Select the qwiklabs bucket

3. Remaining default > Next

4. **Runtime:** Node.js 14
   **Entry point:** thumbnail
5. Add the code appropiately
6. Download the image from URL
7. Navigation menu > **Cloud Storage** > Browser > Select your bucket > Upload files
8. Refresh bucket

## Task 4: Remove the previous cloud engineer

1. Navigation menu > **IAM & Admin** > IAM
2. Search for the "**Username 2**" > Edit > Delete Role