# Analytical Export and Microsoft Fabric

Microsoft Fabric is a future analytical platform, not a direct operational API client. It receives curated data through controlled ingestion/export pipelines rather than the API Gateway as its primary integration path.

Each service exports approved, versioned datasets with documented schemas, lineage, access controls, and retention rules. The pipeline has no write access to operational databases. It consumes service-owned exports and produces Bronze, Silver, and Gold analytical layers. Synthetic data remains mandatory until approved enterprise data governance is in place.
