

|![](Aspose.Words.f93e8f5f-06be-4496-93fb-f9455fc36cc9.001.png)||<p></p><p>MediaAgility Inc.	</p><p>360 Wall St, Princeton</p><p>New Jersey, 08540</p><p></p><p></p>|![](Aspose.Words.f93e8f5f-06be-4496-93fb-f9455fc36cc9.002.png)|
| :- | :- | :- | :- |
\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

# **Vertex AI Endpoint Deployment Instructions**


-----

# **Files Required:**

1. Dockerfile.
1. Custom\_handler.py
1. Model Artifacts (.pt/.bin)
# **Overall Procedure:**

1. Save the Model Artifacts.
1. Create Custom handler using the Torch Model Archiver.
1. Build a custom container (Docker) with TorchServe.
1. Upload the model with the custom container image as a Vertex Model resource.
1. Deploy the model on the Endpoint in Vertex AI
1. # **Save the Model Artifacts from GCS Bucket:**
1. Once the model training is complete in Vertex AI, Download the model artifacts from GCS bucket using the command:

gsutil cp gs://{BUCKET\_NAME}/Model/model.pt .

1. # **Create Custom handler using the Torch Model Archiver.:**
1. The custom handler file should contain 3 main functions within the main class:
1. Initialize(self, …) – should contain initialization code.
1. preprocess (self, …) – should contain the pre-processing steps on input data.   

`       `(embedding/OHEnc, etc)

1. inference(self, …) – should contain the prediction code.
1. postprocess(self, …) – should contain the postprocessing steps (if readability req.)

1. # **Build a custom container (Docker) with TorchServe:**
1. Create a Dockerfile that comprises of Torchserve that is built with the help of Torch Model Archiever (an example file attached).

1. # **Build a custom container (Docker) with TorchServe:**
1. Create the custom container image with the command:

CUSTOM\_PREDICTOR\_IMAGE\_URI = f"gcr.io/{PROJECT\_ID}/pytorch\_predict\_{APP\_NAME}" docker build --tag=$CUSTOM\_PREDICTOR\_IMAGE\_URI ./predictor

1. Run the docker image to check if it gives predictions using this command:

docker run -t -d --rm -p 7080:7080 --name=local\_bert\_classifier $CUSTOM\_PREDICTOR\_IMAGE\_URI

1. Test server’s health check using the command:

cat > ./predictor/instances.json <<END { "instances": [{"data": {"b64": "$(echo 'Take away the CGI and the A-list cast and you end up with a film with less punch.' | base64 --wrap=0)"}}]}END 

curl -s -X POST \   -H "Content-Type: application/json; charset=utf-8" \   -d @./predictor/instances.json \   http://localhost:7080/predictions/$APP\_NAME/

1. Stop the docker Container with:

docker stop local\_bert\_classifier 

1. Now push the docker container to Google Container Registry:

docker push $CUSTOM\_PREDICTOR\_IMAGE\_URI

1. **Deploy the model on the Endpoint in Vertex AI**:
1. The Deployment on Vertex AI is 3 step process –
1. Import the Docker Image in the final model in Vertex AI:

aiplatform.Model.upload(…)

1. Create an Endpoint:

aiplatform.Endpoint.create(…)

1. Deploy the Model on the endpoint:

model.deploy(…)

1. Finally get predictions from the Endpoint using: 

endpoint.predict(instances=…)

\*Detailed Code for all Deployment Steps included along with this Readme file.


1-866-633-4281 								[www.mediaagility.com](http://www.mediaagility.com)

