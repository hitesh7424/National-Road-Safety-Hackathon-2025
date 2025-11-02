# Project 3: Road Safety Intervention GPT

This plan is designed for a solo developer to complete a functional and impressive prototype within the hackathon timeline. We will use a popular and powerful technique called **Retrieval-Augmented Generation (RAG)**.

## **The Core Idea: What is RAG?**

You can't just ask a normal chatbot (like ChatGPT or Gemini) a question and expect it to know the contents of your specific road safety database. RAG solves this. The process is:

1. A user asks a question (e.g., "What is the best intervention for a sharp curve on a rural highway with poor visibility?").
2. Your system **retrieves** the most relevant documents from your specialized database.
3. Your system then passes these documents to a powerful Large Language Model (LLM) along with the original question and says, "Using *only* this information I've given you, answer the user's question."
4. The LLM **generates** a high-quality answer based *only* on the provided context, making it accurate and relevant to your database.

---

### **Phase 1: Setup and Foundation (First 1-2 Hours)**

This is the most critical phase. Get this right, and everything else will be smoother.

#### Step 1: Understand Your Database

- Download the database provided via the QR code in the rulebook.
- **Analyze it:** What format is it in? (CSV, JSON, PDF, plain text?). What is the structure? Are there columns like `Intervention_Name`, `Description`, `Road_Type`, `Safety_Issue_Addressed`? Understanding this structure is key.

#### Step 2: Choose Your Tech Stack (Keep it Simple!)

- **Programming Language:** **Python**.
- **LLM API:** Get an API key from **Google AI Studio for the Gemini API**. It has a generous free tier perfect for a hackathon.
- **Core Framework:** **LangChain**. This library is the glue that will connect everything. It makes building RAG systems much easier.
- **Vector Database:** **ChromaDB**. It's an open-source vector database that runs on your machine. It's incredibly easy to set up and perfect for a hackathon project.
- **UI Framework:** **Streamlit**. This is the fastest way to build a simple, good-looking web app for your demo, all within Python.

#### Step 3: Set Up Your Environment

1. Create a new project folder.

2. Open a terminal and create a Python virtual environment:

    ```bash
    python -m venv venv
    # On Windows, use the following command to activate the virtual environment:
    venv\Scripts\activate
    ```

3. Install all the necessary libraries:

    ```bash
    pip install langchain google-generativeai langchain-google-genai chromadb-client pypdf streamlit python-dotenv
    ```

4. Create a file named `.env` and store your Google API key in it:

    ```env
    GOOGLE_API_KEY="YOUR_API_KEY_HERE"
    ```

---

### **Phase 2: The Backend - Building the RAG Pipeline (Main Coding Block: 4-6 Hours)**

This is where you'll write the core logic in a Python script (e.g., `app.py`).

#### Step 4: Load the Database

- Use LangChain's **Document Loaders** to load the data from your file. If it's a PDF, use `PyPDFLoader`. If it's a CSV, use `CSVLoader`.

#### Step 5: Split the Documents into Chunks

- The loaded documents might be very long. You need to break them into smaller, meaningful chunks.
- Use LangChain's `RecursiveCharacterTextSplitter` to do this. This ensures you don't cut sentences in half.

#### Step 6: Create and Store Embeddings (The "Indexing" Step)

- This is a one-time process you'll run to "teach" your system about your database.
- **Embeddings** are numerical representations of your text chunks.
- Use `GoogleGenerativeAIEmbeddings` from LangChain to convert your text chunks into vectors.
- Use `Chroma.from_documents()` to store these vectors in your local ChromaDB database. Your database is now indexed and ready for searching.

#### Step 7: Build the Core Q&A Chain

- When a user asks a question, your app will perform the following steps:

    1. **Create a Retriever:** Set up your ChromaDB database as a "retriever" (`db.as_retriever()`). This component's job is to find the most relevant chunks for a given question.

    2. **Define a Prompt Template:** This is crucial for getting good answers. Your prompt should look something like this:

        ```python
        template = """
        You are an expert assistant for road safety.
        Use the following pieces of context to answer the user's question.
        If you don't know the answer, just say that you don't know, don't try to make one up.
        Provide a detailed answer and also reference the source of the information from the context.

        Context: {context}
        Question: {question}
        Helpful Answer:
        """
        ```

    3. **Create the RAG Chain:** Use LangChain to create a chain that:
        - Takes the user's question.
        - Passes it to the retriever to get the relevant context.
        - Puts the context and question into your prompt.
        - Sends the final prompt to the Gemini LLM.
        - Returns the LLM's generated answer.

---

### **Phase 3: The Frontend - Building the User Interface (1-2 Hours)**

Now, let's wrap your RAG logic in a user-friendly Streamlit app.

#### Step 8: Design the Streamlit App

- In your `app.py` script:

  - Add `import streamlit as st`.
  - Use `st.title("Road Safety Intervention GPT")` to set the page title.
  - Use `user_input = st.text_input("Ask a question about a road safety problem:")` to get input from the user.
  - Add a button: `if st.button("Get Recommendation"):`.
  - Inside the `if` block, call your RAG chain with the `user_input`.
  - Display a loading spinner while the model is thinking: `with st.spinner("Finding the best intervention..."):`.
  - Display the final answer using `st.markdown(response["result"])`.

---

### **Phase 4: Final Touches and Presentation Prep (1-2 Hours)**

This is how you meet the evaluation criteria and prepare to win.

#### Step 9: Address the Evaluation Criteria Directly

- **Relevance:** Your RAG architecture is built for this. It will always provide relevant answers.
- **Comprehensiveness & Explainability (The Winning Move):** Modify your RAG chain to also return the source documents that the retriever found. In your Streamlit app, after displaying the answer, add a section like this:

    ```python
    st.subheader("Sources Used:")
    for doc in response['source_documents']:
        st.write(doc.page_content)  # Or a snippet of it
    ```

    This shows the judges *why* the AI gave the answer it did. It's a massive feature for explainability.

#### Step 10: Prepare Your 7-Slide Presentation

- **Slide 1: Welcome:** Title, Your Name.
- **Slide 2: The Problem:** "Choosing the right road safety intervention is complex and requires expert knowledge. We need a tool to make this knowledge accessible."
- **Slide 3: Our Solution:** "Introducing the Road Safety Intervention GPT, an AI-powered expert that provides evidence-based recommendations from a curated database."
- **Slide 4: How It Works:** A simple diagram showing your RAG workflow (User Input -> Retriever -> LLM -> Answer + Sources). This shows your technical understanding.
- **Slide 5: Live Demo:** Be ready to share your screen and show your Streamlit app in action. Have 2-3 good example questions ready.
- **Slide 6: Meeting the Criteria:** Explicitly state: "Our tool ensures **Relevance** by using a RAG architecture and **Comprehensiveness** by providing detailed answers with full source citations for explainability."
- **Slide 7: Thank You:** Your contact information.
