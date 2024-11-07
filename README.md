# AI Analysis API Doc

---

## Base URL
`https://`

## Authentication
All endpoints require API key-based authentication. Include your API key in the header of each request:

```http
Authorization: Bearer {API_KEY}
```

---

## Endpoints Overview

| Endpoint                             | Method | Description                                    |
|--------------------------------------|--------|------------------------------------------------|
| `/upload`                            | `POST` | Uploads retina imgs and patient data         |
| `/analyze/{patient_id}`              | `GET`  | Triggers analysis and generates a medical report|
| `/analysis/result/{patient_id}`      | `POST` | Returns the analysis result and medical report |
| `/patient/{patient_id}`              | `GET`  | Retrieves patient data and analysis summary    |
| `/patient/{patient_id}`              | `DELETE`| Deletes patient data and related images       |

---

## API Endpoints

### 1. Upload Retina Image and Patient Data

**Endpoint**: `/upload`  
**Method**: `POST`

**Description**: Uploads retina images and patient data to the server for further analysis.

**Request Parameters**:

- **Headers**:
  - `Authorization`: `Bearer {API_KEY}`
  - `Content-Type`: `multipart/form-data`

- **Body**:
  - `patient_id` (string, required): Unique ID for the patient.
  - `image` (file, required): Retina image file in JPEG/PNG format.
  - `timestamp` (ISO 8601, optional): Date and time the image was captured.
  - `patient_data` (JSON, required): JSON object containing patient information, e.g., `{ "name": "Lukas Zigmund", "age": 28, "gender": "M", "notes": "Patient exhibits symptoms of blurred vision" }`

**Sample Request**:

```http
POST /upload HTTP/1.1
Authorization: Bearer {API_KEY}
Content-Type: multipart/form-data

{
  "patient_id": "12345",
  "image": <file>,
  "timestamp": "2023-10-01T12:30:00Z",
  "patient_data": {
    "name": "Lukas Zigmund",
    "age": 28,
    "gender": "M",
    "notes": "Patient exhibits symptoms of blurred vision"
  }
}
```

**Response**:

- **201 Created**: Successfully uploaded imgs and data.
- **400 Bad Request**: Missing or invalid data.

**Response Example**:

```json
{
  "status": "success",
  "message": "Image and patient data uploaded successfully",
  "patient_id": "12345"
}
```

---

### 2. Request Analysis of Patient Data

**Endpoint**: `/analyze/{patient_id}`  
**Method**: `GET`

**Description**: Initiates analysis of uploaded retina image and patient data, generating an associated medical report.

**Request Parameters**:

- **Headers**:
  - `Authorization`: `Bearer {API_KEY}`

**URL Path Parameters**:

- `patient_id` (string, required): Unique ID of the patient whose data should be analyzed.

**Response**:

- **202 Accepted**: Analysis has been initiated.
- **404 Not Found**: Patient data not found.

**Response Example**:

```json
{
  "status": "success",
  "message": "Analysis initiated",
  "patient_id": "12345"
}
```

---

### 3. Retrieve Analysis Result and Medical Report

**Endpoint**: `/analysis/result/{patient_id}`  
**Method**: `POST`

**Description**: Retrieves the analysis result and medical report for a patient and sends it back to the camera.

**Request Parameters**:

- **Headers**:
  - `Authorization`: `Bearer {API_KEY}`
  - `Content-Type`: `application/json`

**URL Path Parameters**:

- `patient_id` (string, required): Unique ID of the patient.

**Response**:

- **200 OK**: Analysis result and medical report retrieved and sent.
- **404 Not Found**: Patient data or analysis result not found.

**Response Example**:

```json
{
  "status": "success",
  "message": "Analysis result and medical report sent back to camera",
  "patient_id": "12345",
  "result": {
    "diagnosis": "Signs of diabetic retinopathy",
    "confidence": 0.92,
    "recommendation": "Schedule follow-up with an ophthalmologist"
  },
  "medical_report": {
    "patient_name": "Lukas Zigmund",
    "patient_age": 28,
    "date_of_analysis": "2023-10-01",
    "diagnosis_summary": "The analysis indicates signs of diabetic retinopathy with a confidence score of 0.92.",
    "clinical_notes": "Patient shows evidence of microaneurysms and slight retinal swelling. Further evaluation by an ophthalmologist is recommended.",
    "recommendations": [
      "Immediate follow-up with an ophthalmologist",
      "Monitor blood glucose levels",
      "Consider lifestyle adjustments to manage diabetes"
    ]
  }
}
```

---

### 4. Retrieve Patient Data and Analysis Summary

**Endpoint**: `/patient/{patient_id}`  
**Method**: `GET`

**Description**: Retrieves patient information and a summary of analysis results, including the medical report if available.

**Request Parameters**:

- **Headers**:
  - `Authorization`: `Bearer {API_KEY}`

**URL Path Parameters**:

- `patient_id` (string, required): Unique ID for the patient.

**Response**:

- **200 OK**: Patient data retrieved.
- **404 Not Found**: Patient data not found.

**Response Example**:

```json
{
  "patient_id": "12345",
  "patient_data": {
    "name": "Lukas Zigmund",
    "age": 28,
    "gender": "M",
    "notes": "Patient exhibits symptoms of blurred vision"
  },
  "analysis_summary": {
    "diagnosis": "Signs of diabetic retinopathy",
    "confidence": 0.92,
    "recommendation": "Schedule follow-up with an ophthalmologist"
  },
  "medical_report": {
    "patient_name": "Lukas Zigmund",
    "patient_age": 28,
    "date_of_analysis": "2023-10-01",
    "diagnosis_summary": "The analysis indicates signs of diabetic retinopathy with a confidence score of 0.92.",
    "clinical_notes": "Patient shows evidence of microaneurysms and slight retinal swelling. Further evaluation by an ophthalmologist is recommended.",
    "recommendations": [
      "Immediate follow-up with an ophthalmologist",
      "Monitor blood glucose levels",
      "Consider lifestyle adjustments to manage diabetes"
    ]
  }
}
```

---

### 5. Delete Patient Data

**Endpoint**: `/patient/{patient_id}`  
**Method**: `DELETE`

**Description**: Deletes all data related to a specific patient, including uploaded images, analysis results, and medical reports.

**Request Parameters**:

- **Headers**:
  - `Authorization`: `Bearer {API_KEY}`

**URL Path Parameters**:

- `patient_id` (string, required): Unique ID of the patient.

**Response**:

- **200 OK**: Patient data, images, and medical report successfully deleted.
- **404 Not Found**: Patient data not found.

**Response Example**:

```json
{
  "status": "success",
  "message": "Patient data and associated files deleted",
  "patient_id": "12345"
}
```

---

## Error Codes

| Status Code | Description                         |
|-------------|-------------------------------------|
| 400         | Bad Request                         |
| 401         | Unauthorized                        |
| 404         | Not Found                           |
| 500         | Internal Server Error               |

## Notes
- **Image Quality**: Retina images should be of high resolution to ensure accurate analysis.
- **Processing Time**: The analysis process duration may vary depending on the number of images submitted and their quality. More images or higher quality may take longer.
- **Left and Right Eye Requirements**: For a thorough analysis, the system requires images of both the left and right eye. This helps ensure comprehensive detection of any potential abnormalities.
- **Positive Diagnosis**: If the analysis returns a positive diagnosis, the patient should be promptly referred to an ophthalmologist for a detailed examination and follow-up.
- **Image Format**: Accepted image formats are JPEG and PNG. Ensure images are well-lit and focused for better results.
- **Data Retention**: Patient data and images are retained for 30 days by default, unless manually deleted using the `/patient/{patient_id}` `DELETE` endpoint.
- **Error Reporting**: In cases where the analysis cannot proceed (e.g., missing required images), the API will return an appropriate error message detailing the missing requirements.
- **API Rate Limits**: To maintain server performance, API requests are rate-limited. Consult the documentation on specific limits and consider batching multiple images into a single upload request.

## Additional Considerations
- **Patient Privacy and Compliance**: Ensure that all data uploads comply with relevant privacy laws (e.g., HIPAA, GDPR), especially when handling sensitive medical information.
- **Version Updates**: Periodically check for updates in the API documentation, as new parameters, improved analysis capabilities, and additional endpoints may be added to enhance functionality.
- **Testing**: Before using the API in a production environment, test the endpoints with sample data to familiarize yourself with response structures and error handling.


