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
