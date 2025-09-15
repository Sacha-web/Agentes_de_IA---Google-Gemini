# Agente de Service Desk com Langchain e Langgraph

Este projeto demonstra a construção de um agente de Service Desk utilizando as bibliotecas Langchain e Langgraph para classificar e responder a perguntas dos usuários com base em políticas internas da empresa Carraro Desenvolvimento.

## Funcionalidades

- **Classificação de Intenções:** Utiliza o modelo Gemini 2.5 Flash para triar mensagens dos usuários em categorias como "AUTO_RESOLVER", "PEDIR_INFO" e "ABRIR_CHAMADO".
- **Processamento de Documentos (RAG):** Carrega e processa documentos PDF contendo políticas internas, divide o texto em chunks, cria embeddings e armazena em uma Vector Store (FAISS) para busca de informações relevantes.
- **Geração de Respostas Baseadas em Documentos:** Utiliza uma cadeia RAG para buscar o contexto relevante nos documentos e gerar respostas precisas para as perguntas dos usuários, incluindo citações das fontes consultadas.
- **Orquestração do Fluxo (Langgraph):** Define um fluxo de execução utilizando Langgraph para direcionar a solicitação do usuário para o nó apropriado (triagem, auto-resolver, pedir informação, abrir chamado) com base na intenção classificada e no sucesso da busca nos documentos.

## Tecnologias Utilizadas

- **Langchain:** Framework para desenvolvimento de aplicações com modelos de linguagem.
- **Langgraph:** Extensão do Langchain para construir fluxos de agentes com estados e transições.
- **Google Gemini 2.5 Flash:** Modelo de linguagem utilizado para classificação e geração de texto.
- **Langchain-Google-Genai:** Integração do Langchain com os modelos Google Generative AI.
- **FAISS:** Biblioteca para busca de similaridade eficiente em grandes conjuntos de dados.
- **PyMuPDFLoader:** Loader de documentos PDF para Langchain.
- **RecursiveCharacterTextSplitter:** Splitter de texto para dividir documentos em chunks.
- **Pydantic:** Biblioteca para validação de dados e modelagem de saídas estruturadas.

## Como Executar

1. Clone este repositório.
2. Instale as dependências: `pip install -q --upgrade langchain langchain-google-genai google-generativeai langchain_community faiss-cpu langchain-text-splitters pymupdf langgraph`
3. Configure sua API Key do Google Gemini nas secrets do Colab com o nome `GEMINI_API_KEY`.
4. Carregue seus arquivos PDF de políticas internas na pasta `/content/drive/MyDrive/OneDrive_1_9-10-2025`.
5. Execute as células do notebook em sequência.

## Fluxo do Agente

O agente segue o seguinte fluxo:

1. **Início:** Recebe a pergunta do usuário.
2. **Triagem:** Classifica a intenção da pergunta ("AUTO_RESOLVER", "PEDIR_INFO", "ABRIR_CHAMADO").
3. **Auto-Resolver (se aplicável):** Se a triagem for "AUTO_RESOLVER", busca a resposta nos documentos utilizando RAG.
    - Se encontrar contexto relevante, fornece a resposta com citações e finaliza.
    - Se não encontrar contexto relevante:
        - Se a pergunta contiver palavras-chave de abertura de chamado, direciona para "Abrir Chamado".
        - Caso contrário, direciona para "Pedir Informação".
4. **Pedir Informação (se aplicável):** Se a triagem for "PEDIR_INFO" ou se o RAG falhar sem keywords de chamado, solicita mais informações ao usuário e finaliza.
5. **Abrir Chamado (se aplicável):** Se a triagem for "ABRIR_CHAMADO" ou se o RAG falhar com keywords de chamado, simula a abertura de um chamado com base na urgência e descrição inicial e finaliza.

## Testes

O notebook inclui exemplos de testes para demonstrar o funcionamento do agente com diferentes tipos de perguntas.