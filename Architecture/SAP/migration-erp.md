# Prompt

## Description
Act as a senior technical writer and enterprise integration architect. Create a practical manual of best practices and recommendations for migrating applications that are currently integrated with Oracle ERP through a database connection to SAP, with the target integration implemented via RESTful API.  

## Audience and style: 
- Use clear, simple, and professional language. 
- Make the content accessible to developers at different experience levels. 
- Give special attention to Indian developers by keeping explanations concrete, practical, and easy to follow. 
- Avoid unnecessary jargon; define technical terms briefly when needed.   

## Required content:  
1. Identify complex queries involving more than one table. - Explain how to recognize these queries. - Describe how the API design should address them. - Include recommendations for mapping query logic to API endpoints, payloads, and responses. 
2. Best practices for RESTful API implementation. - Cover resource modeling, HTTP methods, versioning, idempotency, pagination, filtering, sorting, and documentation. - Include guidance for maintainability, scalability, and backward compatibility. 
3. Best practices for error handling in RESTful APIs. - Explain standardized error responses, status codes, validation errors, business errors, and logging. - Include recommendations for making errors actionable for developers and operations teams. 
4. Best practices for RESTful API retries. - Cover retry strategies, exponential backoff, jitter, retryable vs non-retryable errors, timeouts, and circuit breaker considerations. - Explain how to avoid duplicate processing and ensure safe retry behavior. 
5. Best practices for RESTful API authentication. - Cover common authentication approaches such as OAuth 2.0, API keys, tokens, and secure credential management. - Include recommendations for authorization, token rotation, secret storage, and transport security. 

## Structure: 
- Start with a short introduction explaining the migration context and goals. 
- Present the manual in clearly labeled sections matching the five topics above. 
- For each section, include practical recommendations, common pitfalls, and implementation guidance. 
- Add a final section with a concise checklist for migration readiness.  

## Quality requirements: 
- Focus on actionable guidance, not theory. 
- Include examples where helpful, but keep them concise. 
- Make recommendations specific enough to be directly useful in real migration projects. 
- Ensure the manual is coherent, well-organized, and consistent across all sections.

