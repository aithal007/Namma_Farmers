NammaFarmers

Crop recommendation web app and model training files.

Overview
--------
This project predicts the best crop recommendation based on features such as soil nutrient values (N, P, K), temperature, humidity, pH, and rainfall. The repository contains model artifacts, training scripts, and a simple web app to serve predictions.

Files
-----
- `app.py` - Flask app to serve crop recommendation predictions.
- `train.py` - Script used to train the model and create `stacked_model.pkl`.
- `Crop_recommendation.csv` - Dataset used to train the model.
- `NammaFarmers.ipynb` - Notebook with exploratory data analysis and model experiments.
- `stacked_model.pkl` - Serialized trained model (stacked model).
- `scaler.pkl` - Feature scaler used during preprocessing.
- `label_encoder.pkl` - Label encoder for target crop names.
- `requirements.txt` - Python dependencies.
- `render.yaml` - Configuration for deploying to Render (optional).
 
Model details
-------------
This project uses a stacked (stacking) ensemble as the final crop recommendation model. The stacked model is built from multiple base learners whose predictions are combined by a meta-learner. Below is a high-level description of the training and inference pipeline used to create `stacked_model.pkl`.

1. Preprocessing
	- Numerical features (N, P, K, temperature, humidity, pH, rainfall) are scaled using a `StandardScaler` (stored as `scaler.pkl`).
	- The target crop labels are encoded with a `LabelEncoder` and saved as `label_encoder.pkl`.

2. Base learners
	- Multiple base models (for example: Decision Tree, Random Forest, Gradient Boosting) were trained on the preprocessed features. Each base learner learns to predict the crop label probabilities.
	- During training, out-of-fold predictions from each base learner were collected to form a new training set for the meta-learner. This prevents leakage and keeps the meta-learner honest.

3. Meta-learner (stacking)
	- A final model (meta-learner), e.g., Logistic Regression or another tree-based model, was trained on the stacked out-of-fold predictions from the base learners.
	- The meta-learner learns to combine base learner predictions into a final probability distribution over crops.

4. Cross-validation and evaluation
	- k-fold cross-validation (commonly k=5) was used to generate out-of-fold predictions for stacking and to estimate generalization performance.
	- Standard metrics (accuracy, F1-score, confusion matrix) were used to evaluate model performance on held-out folds.

5. Artifacts saved
	- `scaler.pkl` — feature scaler used at training and inference time.
	- `label_encoder.pkl` — encoder to map predicted class indices back to crop names.
	- `stacked_model.pkl` — serialized stacking pipeline that accepts raw (or scaled) features and outputs crop predictions. The pipeline can be a single sklearn `Pipeline` that performs scaling, passes data to base learners and meta-learner, or a custom wrapper that loads each artifact and runs the prediction flow.

6. Inference flow (how `app.py` uses the artifacts)
	- `app.py` loads `scaler.pkl`, `label_encoder.pkl`, and `stacked_model.pkl` at startup (or on demand).
	- Incoming JSON features are scaled using the loaded `scaler`.
	- The scaled features are fed into the `stacked_model` which returns class probabilities or class indices.
	- If class indices are returned, `label_encoder.inverse_transform` converts them into human-readable crop names. Probabilities can be returned as additional information to the client.

Notes and reproducibility
------------------------
- If you re-train models, ensure you save reproducible artifacts (random seeds, training logs, and the exact versions of libraries used). Consider adding a requirements file with pinned versions (e.g., `pandas==1.4.0`) if reproducibility is important.
- You may want to move large binary artifacts (`*.pkl`) to a release or external storage (S3, Google Drive) and add a `.gitignore` to keep the repository lightweight.

Setup
-----
1. Create a Python virtual environment and activate it.

2. Install dependencies:

pip install -r requirements.txt

Running the app
---------------
1. Ensure the model artifacts (`stacked_model.pkl`, `scaler.pkl`, `label_encoder.pkl`) are present in the project root.
2. Run:

python app.py

3. The app will start on the configured port (default 5000). Send POST requests with JSON payload to the prediction endpoint.

Development
-----------
- Use `train.py` to retrain the models and regenerate `stacked_model.pkl`.
- Use the notebook for exploratory data analysis and model tuning.

License
-------
Specify your license here.
