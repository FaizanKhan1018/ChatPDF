Chat with PDF using Google Generative AI and FAISS
Introduction
The Chat with PDF app enables users to upload PDF documents and interact with them via a chat interface. Using Google Generative AI Embeddings for embedding the content and FAISS (Facebook AI Similarity Search) as the vector store, this application allows efficient querying of document contents. This is ideal for use cases like summarizing reports, answering questions from contracts, or exploring academic papers.

Features
Upload Multiple PDFs: Users can upload multiple PDF files and process them simultaneously.
Text Extraction: Extracts and processes the text from each PDF.
Text Chunking: Breaks the extracted text into manageable chunks for embedding.
Vector Store: Uses FAISS to store the embedded chunks for fast similarity searches.
Question Answering: Allows users to ask questions about the PDF documents and get detailed answers based on the content.
Conversational Chain: Uses a conversational AI model to provide context-aware and accurate answers.
Tech Stack
Streamlit: For the user interface.
Google Generative AI Embeddings: For converting text into embeddings.
FAISS: For similarity search and vector database.
LangChain: For conversational AI and chain management.
PyPDF2: For extracting text from PDFs.
Python: Programming language.
Installation
Clone the repository:

bash
Copy code
git clone https://github.com/your-repo/chat-pdf-ai.git
Navigate to the project directory:

bash
Copy code
cd chat-pdf-ai
Create a virtual environment (optional but recommended):

bash
Copy code
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
Install the dependencies:

bash
Copy code
pip install -r requirements.txt
Set up your Google API Key:

Create a .env file in the project directory.

Add the following line to the .env file:

makefile
Copy code
GOOGLE_API_KEY=your_google_api_key
Make sure to replace your_google_api_key with your actual Google API key.

Usage
Run the Streamlit application:

bash
Copy code
streamlit run app.py
Upload PDF Documents:

In the sidebar, click on "Upload your PDF Files".
Choose multiple PDF files to upload.
Process PDFs:

After uploading the PDFs, click on the "Submit & Process" button to extract and process the text from the PDFs.
The app will chunk the text and store it in a FAISS vector store.
Ask Questions:

After processing the PDFs, type a question related to the uploaded PDFs in the chat box.
The app will use Google’s Generative AI to find relevant chunks and answer your question based on the PDF contents.
Example Workflow
Upload PDF: Upload one or more PDF files from the sidebar.
Process PDF: Click "Submit & Process" to extract and chunk the text.
Ask Questions: Type questions related to the PDFs and get responses.
How It Works
PDF Text Extraction
The app uses PyPDF2 to extract text from each PDF file. This raw text is then processed into manageable chunks.

python
Copy code
def get_pdf_text(pdf_docs):
    text = ""
    for pdf in pdf_docs:
        pdf_reader = PdfReader(pdf)
        for page in pdf_reader.pages:
            text += page.extract_text()
    return text
Text Chunking
Text is divided into smaller chunks using RecursiveCharacterTextSplitter to ensure manageable inputs for embeddings.

python
Copy code
def get_text_chunks(text):
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=10000, chunk_overlap=1000)
    chunks = text_splitter.split_text(text)
    return chunks
FAISS Vector Store
The text chunks are converted into embeddings using Google Generative AI Embeddings, and stored in a FAISS vector store for fast similarity search.

python
Copy code
def get_vector_store(text_chunks):
    embeddings = GoogleGenerativeAIEmbeddings(model="models/embedding-001")
    vector_store = FAISS.from_texts(text_chunks, embedding=embeddings)
    vector_store.save_local("faiss_index")
Question Answering
The app retrieves the most relevant chunks from the vector store using FAISS and then uses Google Generative AI to provide a detailed, context-aware response.

python
Copy code
def user_input(user_question):
    embeddings = GoogleGenerativeAIEmbeddings(model="models/embedding-001")
    new_db = FAISS.load_local("faiss_index", embeddings, allow_dangerous_deserialization=True)
    docs = new_db.similarity_search(user_question)

    chain = get_conversational_chain()
    response = chain({"input_documents": docs, "question": user_question}, return_only_outputs=True)

    st.write("Reply: ", response["output_text"])
