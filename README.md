# 🔍 Vulnerability Extractor

Uma ferramenta CLI para extrair vulnerabilidades de relatórios PDF de segurança usando LLMs (Large Language Models).

## 📋 Descrição

Ferramenta para automatizar a extração, estruturação e conversão de vulnerabilidades de relatórios PDF dos scanners OpenVAS e Tenable WAS, utilizando PLN e LLMs como GPT-4.1. Permite criar datasets padronizados para gestão de riscos e aplicações em aprendizado de máquina.

## ✨ Funcionalidades

- Extração automática de vulnerabilidades de PDFs
- Remoção de duplicatas baseada no nome da vulnerabilidade
- Suporte a múltiplos provedores de LLM (OpenAI, Groq, etc.)
- Configuração via arquivo JSON
- Interface de linha de comando (CLI)
- Processamento em chunks para documentos grandes
- Conversão para CSV/XLSX/TSV
- Tratamento robusto de erros

## 🚀 Instalação

1. Clone ou baixe os arquivos
```bash
git clone <repositório>
cd pdf-vulnerability-extractor
```

2. Crie um ambiente virtual (recomendado)
```bash
python -m venv venv
venv\Scripts\activate  # Windows
# ou
source venv/bin/activate  # Linux/Mac
```

3. Instale as dependências
```bash
pip install -r requirements.txt
```

### Dependências principais
- `langchain` - Framework principal para LLM
- `langchain-openai` - Interface para APIs OpenAI/Groq
- `langchain-community` - Loaders e utilitários
- `unstructured[pdf]` - Processamento de PDFs
- `PyPDF2`, `pdfplumber` - Extração de texto
- `pandas` - Manipulação de dados

## ⚙️ Configuração

A arquitetura é modular e extensível, permitindo personalização de modelos LLM, perfis de processamento e templates de prompt. Veja exemplos e instruções nos diretórios `src/configs/llms`, `src/configs/profile` e `src/configs/templates`.

## 📖 Uso

Sintaxe:
```bash
python main.py <pdf_path> [opções]
```

Principais opções:
- `--profile` Perfil de configuração (ex: openvas, tenable)
- `--LLM` Modelo LLM a usar (ex: gpt4, llama3)
- `--convert` Formato de saída (`csv`, `xlsx`, `tsv`, `all`)
- `--output` Caminho do arquivo convertido
- `--output-dir` Diretório para arquivos convertidos
- `--csv-delimiter` Delimitador CSV
- `--csv-encoding` Codificação CSV

Exemplos:
```bash
python main.py relatorio.pdf --profile openvas --convert csv
python main.py relatorio.pdf --LLM deepseek --convert all --output-dir ./resultados
```

## 📄 Formato de saída

Gera JSON estruturado com campos como `Name`, `description`, `cvss`, `severity`, `solution`, `port`, `protocol`, `references`, além de campos específicos para cada scanner. Permite conversão para CSV/XLSX/TSV.

## 🔧 Resolução de problemas

- Erro de modelo: atualize o modelo nas configurações
- Arquivo não encontrado: verifique o caminho do PDF
- API key inválida: revise a chave nas configurações
- Limite de quota: aguarde ou troque de provedor

## 📁 Estrutura do projeto

```
pdf-vulnerability-extractor/
├── main.py              # Script principal
├── requirements.txt     # Dependências
├── README.md            # Este arquivo
├── src/                 # Código fonte modular
│   ├── configs/         # Configurações (LLMs, perfis, templates)
│   ├── converters/      # Conversores de saída
│   └── utils/           # Utilitários de processamento
└── data/                # Dados de entrada e saída
```

## Objetivo

O objetivo geral deste trabalho é desenvolver um método automatizado, baseado em Processamento de Linguagem Natural (PLN) e Large Language Models (LLMs), para construir datasets de vulnerabilidades. Especificamente, propõe-se extrair e estruturar informações a partir de relatórios heterogêneos dos scanners OpenVAS e Tenable WAS, convertendo seus dados não estruturados em um formato padronizado que facilite a gestão de riscos. Com isso, busca-se assegurar consistência entre diferentes ferramentas e reduzir substancialmente o esforço manual. O uso de GPT-4.1 demonstrou-se viável para gerar datasets utilizáveis em modelos de aprendizado de máquina. Além disso, o método contempla futura integração de módulos de anonimização, automação de rotulagem e atualização contínua, visando tornar os datasets seguros, reutilizáveis e representativos.

## Metodologia

O método automatiza a extração de vulnerabilidades a partir de relatórios em formato PDF gerados pelos scanners OpenVAS e Tenable WAS. O pipeline é organizado em fases modulares para assegurar a integridade e a consistência dos dados, lidando com a heterogeneidade estrutural e semântica entre as ferramentas:

1. **Extração Textual e Divisão (Chunking):**  
   O processo inicia com a leitura do relatório e a extração do conteúdo textual, preservando a fidelidade aos dados originais. O texto é dividido em blocos lógicos (chunks) para manter o contexto de cada vulnerabilidade dentro das limitações de tokens dos modelos de linguagem, sendo utilizados blocos médios de aproximadamente 9.000 caracteres nos experimentos.

2. **Processamento com Mapeamento Explícito:**  
   Cada bloco é processado por um prompt específico que orienta o LLM (como GPT-4.1) a identificar os campos relevantes (descrição, impacto, solução, referências). Para garantir a padronização, foi implementado um mapeamento explícito no prompt que associa os campos específicos de cada scanner (como Vulnerability Insight do OpenVAS e Risk Information do Tenable WAS) a um conjunto de rótulos generalizados. Campos inexistentes são preenchidos com `NULL` para evitar a geração de informações artificiais.

3. **Pós-processamento e Consolidação:**  
   Os dados extraídos são validados e consolidados. Esta fase inclui a remoção de duplicações, verificação da conformidade sintática e a reconstrução do conjunto completo de vulnerabilidades. Essa etapa permite a análise e contagem final das vulnerabilidades extraídas a partir de diferentes arquivos e fontes de relatórios (OpenVAS e Tenable WAS), resultando em um dataset unificado.

## Arquitetura Proposta

- Leitura e extração de texto de PDFs (OpenVAS/Tenable)
- Divisão em chunks para processamento eficiente por LLMs
- Prompting e mapeamento de campos para padronização dos dados
- Conversores para formatos CSV/XLSX
- Validação, deduplicação e consolidação dos dados extraídos

## Tecnologias Utilizadas

- Python 3.x
- GPT-4.1 (OpenAI API)
- PyPDF2, pdfplumber (extração de texto)
- Pandas (manipulação de dados)
- Ferramentas de automação e scripts customizados
- OpenVAS e Tenable WAS (fontes dos relatórios)

## Resultados Obtidos

O pipeline proposto foi executado sobre um conjunto de 478 vulnerabilidades originadas de aplicações vulneráveis propositalmente (DVWA, GRAV e OWASP Juice Shop), além de instâncias rodando localmente, incluindo servidores web, bancos de dados, serviços de rede e estações de trabalho, para garantir diversidade de contexto. A extração estruturada com GPT‑4.1, seguida pelos processos de normalização de severidade e deduplicação semântica, resultou nos dados consolidados utilizados nesta análise.

### Distribuição por Severidade Normalizada

![Distribuição das vulnerabilidades por nível de severidade padronizado](figuras/barras_severidade_padronizada.png)
*Figura: Distribuição das vulnerabilidades por nível de severidade padronizado (n = 478)*

- **High**: 136 (28,5%)
- **Medium**: 203 (42,5%)
- **Low**: 25 (5,2%)
- **Log**: 114 (23,8%)

As severidades Medium e High representam conjuntamente 71% dos achados, evidenciando exposição significativa a riscos críticos e de alta probabilidade de exploração.

### Análise de Recorrência e Duplicidade

![Vulnerabilidades mais recorrentes por nome original](figuras/duplicatas_por_nome2.png)
*Figura: Vulnerabilidades mais recorrentes por nome original (top 30 de 312 entradas únicas)*

As vulnerabilidades mais frequentes segundo o campo `Name` original do OpenVAS apresentaram alta concentração em problemas de configuração e obsolescência de protocolos SSL/TLS (certificados expirados, suítes criptográficas fracas, falta de PFS, suporte a SSLv3), exposição repetida de serviços legados em claro (FTP, Telnet, VNC, rexec/rlogin/rsh), múltiplas instâncias de dívida técnica em componentes específicos (phpMyAdmin, TWiki, PostgreSQL, Samba e versões obsoletas do PHP), além de achados consolidados de inventário (Services, OS Detection) com elevada cardinalidade.

Após a desduplicação semântica realizada com combinação de correspondência exata de CVE/CPE, o conjunto original foi reduzido de 478 para 294 entradas únicas, uma diminuição de 38,5%. A abordagem eliminou redundâncias por host, mantendo, entretanto, a representatividade global do risco.

##  Licença

Este projeto é fornecido como está, para fins educacionais e de pesquisa.
