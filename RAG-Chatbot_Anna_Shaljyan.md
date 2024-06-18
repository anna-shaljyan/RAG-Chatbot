### RAG Based Chatbot for PDF

#### Company: Service Titan
#### Applicant: Anna Shaljyan

For answering the questions for the task, I found a RAG chatbot implementation that processes PDF files and answers user questions. I cloned the github repository to my local machine and run the streamlit app locally to test 10 complex questions, 5 of which will fail and 5 of which the app will succeed.

Reference for github repository used: https://github.com/ArmaanSeth/ChatPDF

### RAG System components
1) PDF Processor:
- Tool Used: PyPDF2
- Reason for Choice: PyPDF2 is chosen for its ability to extract text from PDFs effectively, which is essential for converting PDF manuals into readable text chunks.

2) Text Splitter:
- Tool Used: CharacterTextSplitter from langchain
- Reason for Choice: This tool segments the extracted text into manageable chunks suitable for embedding and retrieval. It uses character-level splitting with overlap, ensuring coherence across chunks.

3) Embedding Model (Embedder):
- Tool Used: HuggingFace Transformers (MiniLM)
- Reason for Choice: MiniLM is selected for its balance between computational efficiency and embedding quality, suitable for handling various equipment manuals without overwhelming computational resources.
- Tool NOT Used: Sentence-BERT (SBERT) Embeddings
- Reason why NOT: SBERT embeddings are excellent for semantic textual similarity tasks but might be overkill for the specific task of understanding and responding to operational queries in equipment manuals. They may also require more computational resources compared to MiniLM, impacting the responsiveness of the chatbot.

4) Vector Database (Vector Store):
- Tool Used: FAISS (Facebook AI Similarity Search)
- Reason for Choice: FAISS efficiently indexes embeddings from MiniLM, enabling fast similarity search required for retrieving relevant chunks of text based on user queries.
- Tool NOT Used: Elasticsearch
- Reason why NOT: While Elasticsearch is robust for full-text search, FAISS is chosen for its specialized capabilities in vector similarity search, which is more suitable for the chatbot's retrieval needs. Elasticsearch, though versatile, might introduce unnecessary complexity and overhead for this specific use case.

5) Language Model (LLM - Large Language Model):
- Tool Used: OpenAI's ChatGPT
- Reason for Choice: ChatGPT is employed for its conversational capabilities, utilizing a retrieval-augmented generation (RAG) approach. It leverages both MiniLM embeddings and FAISS retrievals to generate responses based on user queries.

### Challenges and Mitigations
1) Challenge: Vector Size and Memory Consumption
- Mitigation: Limiting the chunk size and overlap during text splitting reduces the size of embeddings and memory footprint. Additionally, optimizing FAISS indexing parameters can enhance memory efficiency.

2) Challenge: Handling Diverse Equipment Manuals
- Mitigation: Fine-tuning MiniLM embeddings on a corpus of equipment manuals can improve domain-specific understanding, enhancing the relevance and accuracy of responses.

3) Challenge: Unstructured Data in the Manuals
- Mitigation: In scenarios where text is embedded within images or presented in non-bullet or non-list formats in PDF manuals, Optical Character Recognition (OCR) technology can be employed. OCR tools like Tesseract can extract textual content from images, enabling integration with the RAG chatbot's text processing pipeline. This allows the chatbot to effectively handle unstructured data by converting image-based text into readable format for analysis and response generation.


### 10 Complex Questions (5 failed, 5 succeeded)
- Tip: I included a folder, where the questions and respective response images are stored.

#### Questions that succeeded:
1) What are the safety precautions for installing a water heater? 
- Reason why it succeeded: It is specified in bullet points in the given document
2) Can you explain how to perform pump-down procedure for an air-conditioner?
- Reason why it succeeded: It considers "Safety Precautions" for pump-down procedures following the context under both Warning and Caution subheaders.
3) How should I choose good indoor unit for installation?
- Reason why it succeeded: It considers header "Choosing an Installation Site" with listed points under "Indoor Unit" subheader, that should be followed to ensure good location for installation.
4) What are the recommended mounting plate retention spots and dimensions?
- Reason why it succeeded: The unstructed information was above and below an image in an understandable format not merged with image, so the chatbot was able to answer the question using RAG.
5) How to set drain plug?
- Reason why it succeeded: Even though the answer was mentioned using both textual information and images, the text was in text box and next to image with guiding arrows, which helped the chatbot to extract the answer.

#### Questions that failed:
1) What is the best air conditioner for my living room? 
- Reason why it failed: The document didn't have recommendations specified for which air conditioner is the best, which made the RAG system to fail at answering it. 
2) What accessories does the DAIKIN room air conditioner have? List all of them
- Reason why it failed: The information was presented in table with multiple rows and columns, but the RAG system was able to partially answer the question and upper portion of accessories mentioned were not listed.
3) How many air filters does DAIKIN ROOM AIR CONDITIONER  have?
- Reason why it failed: The information was in textual form under image, but the RAG system was unable to tie the text with the image, so it answered that only 1 filter is used, while the right answer is 2 filters are present (Titanium apatite photocatalytic
air purifying filter (2 pcs)). Additionally the image clearly guided towards two seperate filters, but still the chatbot failed.
4) How the refrigerant piping works and what precautions I should follow for its handling?
- Reason why it failed: It only answered the second portion of the question and missed the part about how refrigerant piping works. Additionally, as required text format was presented with differnt means: tables, images, bullet point lists and etc., it was unable to compose a full logical response. 
5) What are the right heat insulation dimensions?
- Reason why it failed: The information was in table format, but table was in complex form, not usual several rows and columns, but columns and rows were in places splitted the system extracted partial answer to the question.
