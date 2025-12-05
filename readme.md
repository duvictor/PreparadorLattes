Aqui está uma proposta de **README.md** completa, profissional e formatada, pronta para ser usada no repositório do seu projeto (GitHub, GitLab, etc.).

Ele inclui seções de instalação, arquitetura, uso com Docker e explicação da lógica utilizada.

-----

# 📄 Lattes Auditor AI: Verificador Automático de Conformidade

> **Validação inteligente entre Currículo Lattes e Comprovantes utilizando IA Generativa e Busca Vetorial.**

O **Lattes Auditor AI** é uma ferramenta desenvolvida para automatizar a auditoria de currículos acadêmicos (Plataforma Lattes). O sistema analisa o PDF do currículo e cruza as informações com uma pasta de certificados digitais, identificando inconsistências, itens sem comprovação e certificados não utilizados.

-----

## 🚀 Funcionalidades Principais

  * **Leitura Automatizada:** Extração de texto de PDFs (Lattes e Certificados) utilizando `pdfminer.six`.
  * **Busca Semântica (RAG):** Utiliza **Embeddings (OpenAI)** e **FAISS** (Vector Database) para encontrar o certificado mais provável para cada item do currículo, mesmo que os títulos não sejam idênticos.
  * **Juiz com Inteligência Artificial:** Integração com **GPT-4o** para validar logicamente se o conteúdo do certificado realmente comprova o item citado.
  * **Auditoria Bidirecional:**
    1.  **Lattes $\to$ Certificados:** Identifica itens no currículo sem comprovante correspondente.
    2.  **Certificados $\to$ Lattes:** Identifica certificados presentes na pasta que não foram citados no currículo (documentos órfãos).
  * **Relatório de Não Conformidade:** Geração automática de um relatório em PDF apontando as falhas e sucessos.

-----

## 🛠️ Tecnologias Utilizadas

  * [**Python 3.9+**](https://www.python.org/)
  * [**OpenAI API**](https://openai.com/) (Modelos `gpt-4o` e `text-embedding-3-small`)
  * [**FAISS**](https://github.com/facebookresearch/faiss) (Facebook AI Similarity Search) - Para indexação e busca vetorial eficiente.
  * [**PDFMiner.six**](https://pdfminersix.readthedocs.io/) - Para extração de dados de arquivos PDF.
  * [**Pandas**](https://pandas.pydata.org/) - Manipulação de dados estruturados.
  * [**ReportLab**](https://www.reportlab.com/) - Geração do relatório final em PDF.

-----

## 📂 Estrutura do Projeto

```bash
Lattes-Auditor/
├── certificados/          # PASTA OBRIGATÓRIA: Coloque seus PDFs comprobatórios aqui
│   ├── certificado_curso_python.pdf
│   ├── diploma_doutorado.pdf
│   └── ...
├── src/
│   └── lattes_auditor.ipynb  # O Notebook com o código fonte
├── meu_curriculo.pdf      # ARQUIVO OBRIGATÓRIO: Seu Lattes exportado em PDF
├── Relatorio_Conformidade.pdf # ARQUIVO GERADO: O resultado da análise
├── requirements.txt       # Dependências do projeto
├── README.md              # Documentação
└── .env                   # Arquivo de variáveis de ambiente (Opcional)
```

-----

## ⚙️ Instalação e Configuração

### Pré-requisitos

  * Python instalado.
  * Uma chave de API da OpenAI (`OPENAI_API_KEY`).

### Passo a Passo

1.  **Clone o repositório:**

    ```bash
    git clone https://github.com/seu-usuario/lattes-auditor.git
    cd lattes-auditor
    ```

2.  **Crie um ambiente virtual (Recomendado):**

    ```bash
    python -m venv venv
    # No Windows:
    venv\Scripts\activate
    # No Linux/Mac:
    source venv/bin/activate
    ```

3.  **Instale as dependências:**

    ```bash
    pip install pdfminer.six openai faiss-cpu pandas reportlab numpy jupyter
    ```

4.  **Configure a Chave de API:**
    Defina sua chave no ambiente ou edite o notebook diretamente:

    ```bash
    export OPENAI_API_KEY="sk-..."
    ```

-----

## 🖥️ Como Usar

1.  **Prepare os Arquivos:**

      * Baixe seu currículo da Plataforma Lattes em formato PDF e renomeie para `meu_curriculo.pdf` na raiz do projeto.
      * Coloque todos os seus comprovantes (PDFs) dentro da pasta `certificados/`.

2.  **Execute o Notebook:**

    ```bash
    jupyter notebook src/lattes_auditor.ipynb
    ```

3.  **Acompanhe o Processo:**

      * Execute as células sequencialmente.
      * O sistema exibirá o progresso de indexação e validação item a item.

4.  **Verifique o Resultado:**

      * Ao final, será gerado o arquivo `Relatorio_Conformidade.pdf` na raiz do projeto contendo a auditoria completa.

-----

## 🐳 Executando com Docker (Opcional)

Se preferir não instalar o Python localmente, utilize o Docker.

1.  **Crie o `Dockerfile` na raiz:**

    ```dockerfile
    FROM python:3.9-slim

    WORKDIR /app

    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt

    COPY . .

    # Variável de ambiente (Pode ser passada no docker run)
    ENV OPENAI_API_KEY=""

    CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--allow-root", "--no-browser"]
    ```

2.  **Construa e Rode:**

    ```bash
    docker build -t lattes-auditor .
    docker run -p 8888:8888 -v $(pwd)/certificados:/app/certificados -v $(pwd)/meu_curriculo.pdf:/app/meu_curriculo.pdf -e OPENAI_API_KEY="sua-chave" lattes-auditor
    ```

-----

## 🧠 Como Funciona a Lógica de Verificação?

O sistema opera em um fluxo de **RAG (Retrieval-Augmented Generation)** simplificado para auditoria:

1.  **Segmentação:** O Lattes é "fatiado" em blocos de texto (ex: cada produção bibliográfica vira um bloco).
2.  **Vetorização:** Cada certificado na pasta é convertido em um vetor numérico (embedding) que representa seu significado semântico.
3.  **Busca (Retrieval):** Quando o código analisa um item do Lattes (ex: "Artigo sobre IA publicado em 2024"), ele busca no banco vetorial (FAISS) qual certificado é matematicamente mais próximo desse texto.
4.  **Validação (LLM):** O sistema recupera o texto do certificado encontrado e envia junto com o item do Lattes para o GPT-4 com o seguinte prompt: *"Este documento X comprova o item Y?"*.
5.  **Veredito:** O modelo retorna `true` ou `false` com uma justificativa, que é gravada no relatório.

-----

## ⚠️ Limitações Conhecidas

  * **PDFs Scaneados (Imagens):** O sistema utiliza `pdfminer`, que extrai texto selecionável. Certificados que são apenas fotos ou digitalizações sem OCR prévio não terão seu texto lido e aparecerão como não encontrados.
  * **Formatação do Lattes:** A quebra dos itens do Lattes é feita por heurística de espaçamento. Currículos com formatação muito atípica podem ter itens aglutinados incorretamente.

-----

## 🔮 Melhorias Futuras

  * [ ] Implementar OCR (Tesseract) para ler certificados digitalizados como imagem.
  * [ ] Criar uma interface web (Streamlit) para upload de arquivos.
  * [ ] Melhorar o chunking (separação de itens) do Lattes usando LLM para estruturar em JSON antes da validação.

-----

## 📄 Licença

Este projeto está sob a licença MIT. Sinta-se livre para utilizar e modificar para fins acadêmicos e profissionais.