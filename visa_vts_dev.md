Issuer web service interface development guide
===============================================
`[VISA] Visa_Token_Services_Issuer_Web_Service_Development_Guide_1_23.pdf`, v1.23, 1 de Abril de 2017

```
VISA nos dice como ha de ser el interface con TSH
REST con XML 
```

Index
-----
1. Visa Token Services Issuer Web Services

2. Visa Token Services Processes (prerequisites, flowchart and description)
	- No Step-Up Required Flow
	- Step-Up Required: Get Cardholder Verification Methods	
	- Step-Up Authentication Required: Issuer Sends Passcode to Consumer
	- OTP Configuration Options	
	- Step-Up Required: Consumer Uses Issuer Call Center
	- Step-Up Required: Consumer Uses Issuer Mobile Banking Application	
	- Submit Lifecycle Command 
	- Token Inquiry 
	- Update Card Metadata
	- General Messaging 
	- Ping Service

3. Connectivity
	- URL Scheme
		- Outbound From Visa
		- Inbound to Visa
	- Security	
		- Connecting to Visa-Exposed Web Service
			- Prerequisites
			- Security Requirements
			- Security Setup
		- Connecting to Issuer-Exposed Web Service
			- Security Requirements 
		- Data Encryption Method
			- Point of Decryption Requirements
			- Point of Encryption Requirements

4. Onboarding Requirements
	- Considerations
	- Source and Destination Connectivity Information Exchange
		- Information Exchange (for Non-Production Environment)
			- Visa Connecting to the Issuer Service (such as Check Eligibility API)
			- Issuer Connecting to Visa (such as Lifecycle Support API) 
	- Visa Online System Account With User ID and Non-Expiring Password


5. Card Art and Terms & Conditions
	- Delivery 
		- Visa Card Metadata Manager (VCMM) 
	- File Naming Convention 
	- Card Metadata Customization
		- Images
		- Mobile Banking Application Keys
		- Web Services Keys
		- Additional Keys
		- Card Metadata Profile ID Concept
			- Profile ID Example
	- HTML Tags in Terms & Conditions

6. Interface Definitions (request, path, method, body, response, example)
	- Backward Compatibility	
	- CheckEligibility
	- Get Cardholder Verification Methods
	- Send Passcode
	- Submit Lifecycle Command 
		- Lifecycle Actions at Token States
	- Token Inquiry
	- Update Card Metadata
	- Card Metadata Update Notification
	- Ping

7. Interface Definitions (SE DWP-Specific APIs)
	- General Messaging  (request, path, method, body, response, example)

8. Performance Targets

9. Relationship Between Key Elements Across APIs and ISO Messages

10. Schema Definition and XML Samples

Appendix A, Validating Connectivity to Visa-Exposed Web Services Using SOAP UI
	- Prerequisites
	- Configuration Steps
	
Appendix B,  Error Codes

Glossary
