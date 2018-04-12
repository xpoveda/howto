Issuer API specifications
=========================
`VTS_Issuer_API_Specifications_2 2_JSON.pdf`, v2.2, 26 de Julio de 2017

```
VISA nos dice como ha de ser el interface con TSH
REST con JSON
```

Index
-----
1. Getting Started With Rest APIs
	- Backward Compatibility	
	- Endpoints	
		- Outbound From Visa
	- HTTP Headers
		- HTTP Request Headers
		- HTTP Response Headers
		- Error Handling
		- Error Response Structure 
		- Error Response	
		- Error Details
		- Sample Error Response
		- Standard Errors

2. Onboarding
	- Key Management
	- Client Configuration

3. Data Encryption and Decryption
	- Data Encryption/Signature
		- JWE Using RSA
			- JWE Composition 
			- JWE Header
			- JWE Header Sample
			- JWE Encrypted Key
			- JWE Initialization Vector	
			- JWE Ciphertext
			- JWE Authentication Tag	
	- General Steps for Data Encryption
	- JWS Using RSA
		- JWS Composition
		- JWS Header	
		- JWS Header
		- JWS Payload
		- JWS Signature
		- General Steps for Creating Signature 
	- Signature Validation/Data Decryption

4. API Specifications
	- Datos
		- Common Structures
			- Cardholder Information
			- Expiration Date
			- Address
			- Token Information
			- Device Information
			- Risk Information 
			- Terms and Conditions
	- Servicios
		- header, request(method, url params, body), response(ok, nok), examples
			- Approve Provisioning 
			- Approve Provisioning Stand-In Notification
			- Token Create Notification
			- Token Notification

5. Performance Targets
