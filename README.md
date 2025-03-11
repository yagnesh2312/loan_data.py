import streamlit as st
import pandas as pd
import pickle

# Load the trained model
model = pickle.load(open('loan_model.pkl', 'rb'))

# Set up Streamlit app
st.set_page_config(page_title="Loan Approval Prediction", layout="wide")
st.title("🏦 Loan Approval Prediction")

# User input fields
st.sidebar.header("Applicant Details")

# Collect user inputs
gender = st.sidebar.selectbox("Gender", ["Male", "Female"])
married = st.sidebar.selectbox("Married", ["No", "Yes"])
education = st.sidebar.selectbox("Education", ["Not Graduate", "Graduate"])
self_employed = st.sidebar.selectbox("Self Employed", ["No", "Yes"])
applicant_income = st.sidebar.number_input("Applicant Income", min_value=0)
coapplicant_income = st.sidebar.number_input("Coapplicant Income", min_value=0)
loan_amount = st.sidebar.number_input("Loan Amount (in thousands)", min_value=0)
loan_term = st.sidebar.selectbox("Loan Term (in months)", [12, 24, 36, 60, 120, 180, 240, 300, 360])
credit_history = st.sidebar.selectbox("Credit History", [0, 1])
property_area = st.sidebar.selectbox("Property Area", ["Urban", "Semiurban", "Rural"])

# Convert categorical inputs to numerical values
input_data = pd.DataFrame({
    'Gender': [1 if gender == "Male" else 0],
    'Married': [1 if married == "Yes" else 0],
    'Education': [1 if education == "Graduate" else 0],
    'Self_Employed': [1 if self_employed == "Yes" else 0],
    'ApplicantIncome': [applicant_income],
    'CoapplicantIncome': [coapplicant_income],
    'LoanAmount': [loan_amount],
    'Loan_Amount_Term': [loan_term],
    'Credit_History': [credit_history],
    'Property_Area': [0 if property_area == "Rural" else (1 if property_area == "Semiurban" else 2)]
})

# Predict loan approval
if st.button("Predict Loan Approval"):
    prediction = model.predict(input_data)
    result = "Approved" if prediction[0] == 1 else "Rejected"
    st.subheader(f"Loan Status: {result}")
