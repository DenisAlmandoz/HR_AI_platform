# Document Ingestion

HR Knowledge Service accepts PDF, DOCX, and Markdown policy documents. It owns ingestion state, document versions, extraction metadata, chunks, embedding references, and source citations.

Ingestion validates file type and size, records failures explicitly, and preserves document provenance. It must not ingest real employee data during the synthetic-data prototype.
