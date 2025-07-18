<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "7a11a5dcf2f9fdf6392f5a4545cf005e",
  "translation_date": "2025-06-11T16:18:30+00:00",
  "source_file": "05-AdvancedTopics/web-search-mcp/README.md",
  "language_code": "cs"
}
-->
# Lesson: Construindo um Servidor MCP de Busca Web

Este capítulo demonstra como construir um agente de IA real que integra APIs externas, lida com diversos tipos de dados, gerencia erros e orquestra múltiplas ferramentas — tudo em um formato pronto para produção. Você verá:

- **Integração com APIs externas que exigem autenticação**
- **Manipulação de diversos tipos de dados de múltiplos endpoints**
- **Estratégias robustas de tratamento de erros e logging**
- **Orquestração de múltiplas ferramentas em um único servidor**

Ao final, você terá experiência prática com padrões e melhores práticas essenciais para aplicações avançadas de IA e LLM.

## Introdução

Nesta lição, você aprenderá a construir um servidor MCP avançado e um cliente que estendem as capacidades do LLM com dados da web em tempo real usando SerpAPI. Esta é uma habilidade crítica para desenvolver agentes de IA dinâmicos que podem acessar informações atualizadas da web.

## Objetivos de Aprendizagem

Ao final desta lição, você será capaz de:

- Integrar APIs externas (como SerpAPI) de forma segura em um servidor MCP
- Implementar múltiplas ferramentas para busca na web, notícias, produtos e Q&A
- Analisar e formatar dados estruturados para consumo do LLM
- Tratar erros e gerenciar limites de requisições das APIs de forma eficaz
- Construir e testar clientes MCP automatizados e interativos

## Servidor MCP de Busca Web

Esta seção apresenta a arquitetura e os recursos do Servidor MCP de Busca Web. Você verá como FastMCP e SerpAPI são usados juntos para estender as capacidades do LLM com dados da web em tempo real.

### Visão Geral

Esta implementação conta com quatro ferramentas que demonstram a capacidade do MCP de lidar com tarefas diversas, dirigidas por APIs externas, de forma segura e eficiente:

- **general_search**: Para resultados amplos na web
- **news_search**: Para manchetes recentes
- **product_search**: Para dados de comércio eletrônico
- **qna**: Para trechos de perguntas e respostas

### Recursos
- **Exemplos de Código**: Inclui blocos de código específicos para Python (e facilmente extensíveis para outras linguagens) usando seções recolhíveis para clareza

<details>  
<summary>Python</summary>  

```python
# Example usage of the general_search tool
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("general_search", arguments={"query": "open source LLMs"})
            print(result)
```
</details>

Antes de executar o cliente, é útil entender o que o servidor faz. O arquivo [`server.py`](../../../../05-AdvancedTopics/web-search-mcp/server.py) file implements the MCP server, exposing tools for web, news, product search, and Q&A by integrating with SerpAPI. It handles incoming requests, manages API calls, parses responses, and returns structured results to the client.

You can review the full implementation in [`server.py`](../../../../05-AdvancedTopics/web-search-mcp/server.py).

Aqui está um breve exemplo de como o servidor define e registra uma ferramenta:

<details>  
<summary>Python Server</summary> 

```python
# server.py (excerpt)
from mcp.server import MCPServer, Tool

async def general_search(query: str):
    # ...implementation...

server = MCPServer()
server.add_tool(Tool("general_search", general_search))

if __name__ == "__main__":
    server.run()
```
</details>

- **Integração com API Externa**: Demonstra o manuseio seguro de chaves de API e requisições externas
- **Análise de Dados Estruturados**: Mostra como transformar respostas de API em formatos amigáveis para o LLM
- **Tratamento de Erros**: Tratamento robusto de erros com logging apropriado
- **Cliente Interativo**: Inclui testes automatizados e um modo interativo para testes
- **Gerenciamento de Contexto**: Usa MCP Context para logging e rastreamento de requisições

## Pré-requisitos

Antes de começar, certifique-se de que seu ambiente está configurado corretamente seguindo estes passos. Isso garantirá que todas as dependências estejam instaladas e suas chaves de API configuradas corretamente para um desenvolvimento e testes sem problemas.

- Python 3.8 ou superior
- Chave da API SerpAPI (Cadastre-se em [SerpAPI](https://serpapi.com/) - plano gratuito disponível)

## Instalação

Para começar, siga estes passos para configurar seu ambiente:

1. Instale as dependências usando uv (recomendado) ou pip:

```bash
# Using uv (recommended)
uv pip install -r requirements.txt

# Using pip
pip install -r requirements.txt
```

2. Crie um arquivo `.env` na raiz do projeto com sua chave SerpAPI:

```
SERPAPI_KEY=your_serpapi_key_here
```

## Uso

O Servidor MCP de Busca Web é o componente central que expõe ferramentas para busca na web, notícias, produtos e Q&A integrando com SerpAPI. Ele trata requisições recebidas, gerencia chamadas de API, analisa respostas e retorna resultados estruturados para o cliente.

Você pode revisar a implementação completa em [`server.py`](../../../../05-AdvancedTopics/web-search-mcp/server.py).

### Executando o Servidor

Para iniciar o servidor MCP, use o seguinte comando:

```bash
python server.py
```

O servidor será executado como um servidor MCP baseado em stdio, ao qual o cliente pode se conectar diretamente.

### Modos do Cliente

O cliente (`client.py`) supports two modes for interacting with the MCP server:

- **Normal mode**: Runs automated tests that exercise all the tools and verify their responses. This is useful for quickly checking that the server and tools are working as expected.
- **Interactive mode**: Starts a menu-driven interface where you can manually select and call tools, enter custom queries, and see results in real time. This is ideal for exploring the server's capabilities and experimenting with different inputs.

You can review the full implementation in [`client.py`](../../../../05-AdvancedTopics/web-search-mcp/client.py).

### Executando o Cliente

Para rodar os testes automatizados (isso iniciará o servidor automaticamente):

```bash
python client.py
```

Ou rode no modo interativo:

```bash
python client.py --interactive
```

### Testando com Diferentes Métodos

Existem várias formas de testar e interagir com as ferramentas fornecidas pelo servidor, dependendo das suas necessidades e fluxo de trabalho.

#### Escrevendo Scripts de Teste Customizados com o MCP Python SDK
Você também pode criar seus próprios scripts de teste usando o MCP Python SDK:

<details>
<summary>Python</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def test_custom_query():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            # Call tools with your custom parameters
            result = await session.call_tool("general_search", 
                                           arguments={"query": "your custom query"})
            # Process the result
```
</details>

Neste contexto, um "script de teste" é um programa Python customizado que você escreve para atuar como cliente do servidor MCP. Em vez de ser um teste unitário formal, esse script permite que você conecte programaticamente ao servidor, chame qualquer uma das ferramentas com parâmetros escolhidos e inspecione os resultados. Essa abordagem é útil para:
- Prototipar e experimentar chamadas de ferramentas
- Validar como o servidor responde a diferentes entradas
- Automatizar invocações repetidas de ferramentas
- Construir seus próprios fluxos de trabalho ou integrações sobre o servidor MCP

Você pode usar scripts de teste para experimentar rapidamente novas consultas, depurar o comportamento das ferramentas ou até como ponto de partida para automações mais avançadas. Abaixo está um exemplo de como usar o MCP Python SDK para criar tal script:

## Descrições das Ferramentas

Você pode usar as ferramentas a seguir fornecidas pelo servidor para realizar diferentes tipos de buscas e consultas. Cada ferramenta é descrita abaixo com seus parâmetros e exemplos de uso.

Esta seção fornece detalhes sobre cada ferramenta disponível e seus parâmetros.

### general_search

Realiza uma busca geral na web e retorna resultados formatados.

**Como chamar esta ferramenta:**

Você pode chamar `general_search` a partir do seu próprio script usando o MCP Python SDK, ou interativamente usando o Inspector ou o modo interativo do cliente. Aqui está um exemplo de código usando o SDK:

<details>
<summary>Exemplo em Python</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_general_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("general_search", arguments={"query": "latest AI trends"})
            print(result)
```
</details>

Alternativamente, no modo interativo, selecione `general_search` from the menu and enter your query when prompted.

**Parameters:**
- `query` (string): A consulta de busca

**Exemplo de Requisição:**

```json
{
  "query": "latest AI trends"
}
```

### news_search

Busca por notícias recentes relacionadas a uma consulta.

**Como chamar esta ferramenta:**

Você pode chamar `news_search` a partir do seu próprio script usando o MCP Python SDK, ou interativamente usando o Inspector ou o modo interativo do cliente. Aqui está um exemplo de código usando o SDK:

<details>
<summary>Exemplo em Python</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_news_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("news_search", arguments={"query": "AI policy updates"})
            print(result)
```
</details>

Alternativamente, no modo interativo, selecione `news_search` from the menu and enter your query when prompted.

**Parameters:**
- `query` (string): A consulta de busca

**Exemplo de Requisição:**

```json
{
  "query": "AI policy updates"
}
```

### product_search

Busca por produtos que correspondam a uma consulta.

**Como chamar esta ferramenta:**

Você pode chamar `product_search` a partir do seu próprio script usando o MCP Python SDK, ou interativamente usando o Inspector ou o modo interativo do cliente. Aqui está um exemplo de código usando o SDK:

<details>
<summary>Exemplo em Python</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_product_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("product_search", arguments={"query": "best AI gadgets 2025"})
            print(result)
```
</details>

Alternativamente, no modo interativo, selecione `product_search` from the menu and enter your query when prompted.

**Parameters:**
- `query` (string): A consulta de busca de produto

**Exemplo de Requisição:**

```json
{
  "query": "best AI gadgets 2025"
}
```

### qna

Obtém respostas diretas para perguntas a partir de motores de busca.

**Como chamar esta ferramenta:**

Você pode chamar `qna` a partir do seu próprio script usando o MCP Python SDK, ou interativamente usando o Inspector ou o modo interativo do cliente. Aqui está um exemplo de código usando o SDK:

<details>
<summary>Exemplo em Python</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_qna():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("qna", arguments={"question": "what is artificial intelligence"})
            print(result)
```
</details>

Alternativamente, no modo interativo, selecione `qna` from the menu and enter your question when prompted.

**Parameters:**
- `question` (string): A pergunta para a qual buscar uma resposta

**Exemplo de Requisição:**

```json
{
  "question": "what is artificial intelligence"
}
```

## Detalhes do Código

Esta seção fornece trechos de código e referências para as implementações do servidor e cliente.

<details>
<summary>Python</summary>

Veja [`server.py`](../../../../05-AdvancedTopics/web-search-mcp/server.py) and [`client.py`](../../../../05-AdvancedTopics/web-search-mcp/client.py) para detalhes completos da implementação.

```python
# Example snippet from server.py:
import os
import httpx
# ...existing code...
```
</details>

## Conceitos Avançados Nesta Lição

Antes de começar a construir, aqui estão alguns conceitos avançados importantes que aparecerão ao longo deste capítulo. Compreender estes ajudará você a acompanhar, mesmo que sejam novos para você:

- **Orquestração Multi-ferramenta**: Isso significa executar várias ferramentas diferentes (como busca web, notícias, produtos e Q&A) dentro de um único servidor MCP. Permite que seu servidor lide com uma variedade de tarefas, não apenas uma.
- **Gerenciamento de Limite de Requisições da API**: Muitas APIs externas (como SerpAPI) limitam quantas requisições você pode fazer em um determinado período. Um código bem feito verifica esses limites e os trata com elegância, para que seu app não quebre se atingir o limite.
- **Análise de Dados Estruturados**: Respostas de API costumam ser complexas e aninhadas. Este conceito trata de transformar essas respostas em formatos limpos e fáceis de usar, amigáveis para LLMs ou outros programas.
- **Recuperação de Erros**: Às vezes algo dá errado — talvez a rede falhe ou a API não retorne o esperado. Recuperação de erros significa que seu código pode lidar com esses problemas e ainda fornecer feedback útil, ao invés de travar.
- **Validação de Parâmetros**: Trata-se de verificar se todas as entradas para suas ferramentas estão corretas e seguras para uso. Inclui definir valores padrão e garantir que os tipos estejam certos, ajudando a evitar bugs e confusões.

Esta seção ajudará você a diagnosticar e resolver problemas comuns que podem surgir ao trabalhar com o Servidor MCP de Busca Web. Se você encontrar erros ou comportamentos inesperados, esta seção de solução de problemas oferece soluções para os problemas mais comuns. Revise essas dicas antes de buscar ajuda adicional — elas frequentemente resolvem problemas rapidamente.

## Solução de Problemas

Ao trabalhar com o Servidor MCP de Busca Web, você pode ocasionalmente encontrar problemas — isso é normal ao desenvolver com APIs externas e novas ferramentas. Esta seção oferece soluções práticas para os problemas mais comuns, para que você possa retomar rapidamente. Se encontrar um erro, comece aqui: as dicas abaixo abordam as questões que a maioria dos usuários enfrenta e frequentemente resolvem seu problema sem ajuda extra.

### Problemas Comuns

Abaixo estão alguns dos problemas mais frequentes enfrentados pelos usuários, com explicações claras e passos para resolvê-los:

1. **Falta da variável SERPAPI_KEY no arquivo .env**
   - Se você vir o erro `SERPAPI_KEY environment variable not found`, it means your application can't find the API key needed to access SerpAPI. To fix this, create a file named `.env` in your project root (if it doesn't already exist) and add a line like `SERPAPI_KEY=your_serpapi_key_here`. Make sure to replace `your_serpapi_key_here` with your actual key from the SerpAPI website.

2. **Module not found errors**
   - Errors such as `ModuleNotFoundError: No module named 'httpx'` indicate that a required Python package is missing. This usually happens if you haven't installed all the dependencies. To resolve this, run `pip install -r requirements.txt` in your terminal to install everything your project needs.

3. **Connection issues**
   - If you get an error like `Error during client execution`, it often means the client can't connect to the server, or the server isn't running as expected. Double-check that both the client and server are compatible versions, and that `server.py` is present and running in the correct directory. Restarting both the server and client can also help.

4. **SerpAPI errors**
   - Seeing `Search API returned error status: 401` means your SerpAPI key is missing, incorrect, or expired. Go to your SerpAPI dashboard, verify your key, and update your `.env`, crie o arquivo `.env` se necessário. Se sua chave estiver correta, mas o erro persistir, verifique se sua cota do plano gratuito não foi esgotada.

### Modo Debug

Por padrão, o app registra apenas informações importantes. Se quiser ver mais detalhes sobre o que está acontecendo (por exemplo, para diagnosticar problemas difíceis), você pode ativar o modo DEBUG. Isso mostrará muito mais sobre cada etapa que o app está realizando.

**Exemplo: Saída Normal**
```plaintext
2025-06-01 10:15:23,456 - __main__ - INFO - Calling general_search with params: {'query': 'open source LLMs'}
2025-06-01 10:15:24,123 - __main__ - INFO - Successfully called general_search

GENERAL_SEARCH RESULTS:
... (search results here) ...
```

**Exemplo: Saída em DEBUG**
```plaintext
2025-06-01 10:15:23,456 - __main__ - INFO - Calling general_search with params: {'query': 'open source LLMs'}
2025-06-01 10:15:23,457 - httpx - DEBUG - HTTP Request: GET https://serpapi.com/search ...
2025-06-01 10:15:23,458 - httpx - DEBUG - HTTP Response: 200 OK ...
2025-06-01 10:15:24,123 - __main__ - INFO - Successfully called general_search

GENERAL_SEARCH RESULTS:
... (search results here) ...
```

Note como o modo DEBUG inclui linhas extras sobre requisições HTTP, respostas e outros detalhes internos. Isso pode ser muito útil para solução de problemas.

Para ativar o modo DEBUG, defina o nível de logging para DEBUG no topo do seu `client.py` or `server.py`:

<details>
<summary>Python</summary>

```python
# At the top of your client.py or server.py
import logging
logging.basicConfig(
    level=logging.DEBUG,  # Change from INFO to DEBUG
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
```
</details>

---

## Próximos passos

- [5.10 Real Time Streaming](../mcp-realtimestreaming/README.md)

**Prohlášení o vyloučení odpovědnosti**:  
Tento dokument byl přeložen pomocí AI překladatelské služby [Co-op Translator](https://github.com/Azure/co-op-translator). Přestože usilujeme o přesnost, mějte prosím na paměti, že automatické překlady mohou obsahovat chyby nebo nepřesnosti. Původní dokument v jeho mateřském jazyce by měl být považován za autoritativní zdroj. Pro kritické informace se doporučuje profesionální lidský překlad. Nejsme odpovědní za jakékoliv nedorozumění nebo nesprávné výklady vyplývající z použití tohoto překladu.