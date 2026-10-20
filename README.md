# Customer AI Agent

An intelligent automation framework leveraging **EasyOCR** and the **Laya Router** to extract visual and textual data, classify customer requests, process return forms, and analyze product feedback seamlessly.

---

## 🚀 Features

* **OCR Integration**: Extracts text automatically from images (such as product labels, documents, or badges) using EasyOCR.
* **Intent & Classification Routing**: Utilizes Laya's `Router` to route extracted text against custom classification criteria.
* **Customer Support Automation**: Parses customer return/refund requests, identifies primary issues (e.g., quality issues, damaged packaging), and checks form validity (Order ID, customer details).

---

## 🛠️ Prerequisites & Installation

To run this project, make sure you have Python installed along with the required libraries:

```bash
pip install easyocr laya
```

---

## 📖 Usage Examples

### 1. Extracting Text & Classifying Cues from Images
This script reads an image (`Car.jpg`), extracts text cues via EasyOCR, and classifies the target category using the Laya router.

```python
import easyocr
from laya import Router

# Extract text from the image using OCR
reader = easyocr.Reader(['en'])
ocr_results = reader.readtext('Car.jpg', detail=0)
extracted_text = " ".join(ocr_results)

# Pass the extracted text into Laya's router
router = Router(preload=True)
questions = {
    "car_brand": {
        "type": "choice",
        "instructions": "Identify the car brand from the extracted text cues",
        "criteria": {
            "toyota": "Toyota, Corolla, Camry badges",
            "ford": "Ford, Mustang, F-150 badges",
            "bmw": "BMW, M-series badges",
            "other": "Any other brand"
        }
    }
}

result = router.predict(extracted_text, questions)
print("Predicted Brand:", result["answers"]["car_brand"]["choice"])
```

### 2. Processing Customer Return & Refund Requests
This script analyzes customer feedback text to determine request types (return vs. replacement), underlying reasons, and form validity.

```python
from laya import Router

extracted_text = (
    "COFFEE RETURN REQUEST FORM"
    "Order Number: #ORD-55410"
    "Customer Name: Robert Smith"
    "Issue Description: I bought the Ethiopian Yirgacheffe coffee seeds, but upon opening, they smell completely stale and taste rancid. There's no seal freshness date either. I would like a full refund."
)

router = Router(preload=True)
questions = {
    "request_type": {
        "type": "choice",
        "instructions": "Determine whether the customer is requesting a return (refund) or a replacement",
        "criteria": {
            "return": "Refund, money back, returning item",
            "replacement": "Exchange, replace, send new item"
        }
    },
    "reason": {
        "type": "choice",
        "instructions": "Identify the primary reason for the return or replacement request",
        "criteria": {
            "damaged_packaging": "Torn bag, leaking package, crushed box, damaged",
            "quality_issue": "Stale beans, mold, poor roast, bad taste",
            "wrong_item": "Incorrect blend, wrong roast level, incorrect weight",
            "other": "Any other reason"
        }
    },
    "form_validity": {
        "type": "choice",
        "instructions": "Check if the form includes essential validation details like Order ID and Customer Name",
        "criteria": {
            "valid": "Contains Order ID, customer name, and clear request details",
            "invalid": "Missing order number, name, or key information"
        }
    }
}

result = router.predict(extracted_text, questions)

print("Predicted Request Type:", result["answers"]["request_type"]["choice"])
print("Predicted Reason:", result["answers"]["reason"]["choice"])
print("Form Validity:", result["answers"]["form_validity"]["choice"])
```

---

## 📁 Project Structure

* `lavya_vision.py`: Main python script containing OCR integration and Laya router classification logic.