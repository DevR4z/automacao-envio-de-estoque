# Automação de Estoque – Olist/Tiny

Automação em Python para lançamento em massa de estoque no Olist/Tiny, zerando o balanço em múltiplas janelas e registrando o estoque real a partir de arquivos CSV de contagem.

---

## Índice

- [Visão geral](#visão-geral)
- [Funcionalidades](#funcionalidades)
- [Arquivos do projeto](#arquivos-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Como usar](#como-usar)
  - [1. Preparação inicial](#1-preparação-inicial)
  - [2. Encontrar coordenadas (modo teste)](#2-encontrar-coordenadas-modo-teste)
  - [3. Atualizar coordenadas no script](#3-atualizar-coordenadas-no-script)
  - [4. Executar automação](#4-executar-automação)
  - [5. Conferir resultados](#5-conferir-resultados)
- [Estrutura de dados (CSV)](#estrutura-de-dados-csv)
- [Configurações principais](#configurações-principais)
- [Fluxo da automação](#fluxo-da-automação)
- [Troubleshooting rápido](#troubleshooting-rápido)
- [Checklist pré-execução](#checklist-pré-execução)
- [Conceitos importantes](#conceitos-importantes)
- [Licença](#licença)

---

## Visão geral

Este projeto resolve o problema de lançar manualmente o estoque de dezenas ou centenas de SKUs em múltiplas janelas do Olist/Tiny, tarefa repetitiva e propensa a erros humanos.  
A automação lê uma lista de SKUs e suas quantidades de estoque a partir de arquivos CSV, zera o balanço em 3 janelas (balanço = 0) e lança o estoque real em uma 4ª janela dedicada ao estoque real.

O foco é acelerar o processo de contagem de estoque e garantir consistência entre matriz e filiais, reduzindo o esforço operacional em rotinas de inventário.

---

## Funcionalidades

- Leitura de SKUs a partir de `contagem/sku.csv`.
- Leitura das quantidades de estoque a partir de `contagem/estoque.csv`.
- Validação básica: mesma quantidade de linhas em ambos os arquivos.
- Automação por cliques (PyAutoGUI) em 4 janelas do Olist/Tiny:
  - 3 janelas com lançamento por balanço (quantidade igual a 0).
  - 1 janela com lançamento de estoque real.
- Dois modos de execução:
  - Modo 1: teste para encontrar coordenadas dos cliques.
  - Modo 2: automação completa de todos os SKUs.
- Script auxiliar para copiar a data de lançamento para o clipboard.
- Versão alternativa com Selenium como backup para cenários com elementos dinâmicos.

---

## Arquivos do projeto

### Scripts principais

- `lançamento_clicks.py`  
  Script principal de automação via cliques e coordenadas na tela.  
  - Modo 1: captura de coordenadas (modo teste).  
  - Modo 2: processamento completo da lista de SKUs em até 4 janelas do navegador.  
  - Recomendado para uso no dia a dia.

- `lançamento.py`  
  Versão alternativa usando Selenium.  
  - Funciona como backup mais robusto para interações com elementos dinâmicos.  
  - Mesma lógica de processamento, porém controlando o navegador via WebDriver.

- `copiar_data.py`  
  Script auxiliar para copiar a data de lançamento (ex.: `20/05/2026`) para a área de transferência, facilitando colagens com `Ctrl+V` durante a automação.

### Documentação

- `INSTRUÇÕES.md`  
  Guia completo de uso do projeto, configuração inicial e resolução de problemas.

- `RESUMO.md`  
  Visão geral rápida da automação, fluxo do processo e checklist final.

- `COORDENADAS.md`  
  Passo a passo para encontrar, testar e atualizar as coordenadas de clique no `lançamento_clicks.py`.

- `COMANDOS.md`  
  Comandos prontos para execução em Windows (PowerShell / Prompt), atalhos e quick start.

- `README.md`  
  Visão geral do projeto, objetivos e instruções principais (este arquivo).

---

## Pré-requisitos

- Sistema operacional: Windows (projeto pensado para uso em ambiente Windows).
- Python: versão 3.11 ou superior instalada e disponível no PATH.
- Navegador: Google Chrome (com acesso ao Olist/Tiny).
- Bibliotecas Python:
  - `pyautogui`
  - `pynput`
  - `selenium` (necessário apenas para `lançamento.py`)

Instale as dependências com:

```bash
pip install pyautogui pynput selenium
```

---

## Instalação

1. Clone este repositório:

   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio
   ```

2. Crie (ou confirme) a pasta de contagem de estoque:

   ```text
   contagem/
     ├─ sku.csv
     └─ estoque.csv
   ```

3. Preencha:
   - `sku.csv` com um SKU por linha.
   - `estoque.csv` com uma quantidade por linha, na mesma ordem do `sku.csv`.

---

## Como usar

### 1. Preparação inicial

1. Abra o Chrome em 4 janelas (ou 4 perfis / abas destacadas em janelas separadas).
2. Faça login no Olist/Tiny em todas as janelas.
3. Organize as janelas lado a lado na tela.
4. Identifique cada janela (sugestão):
   - Matriz  
   - Filial 3  
   - Filial 4  
   - Conta 6 (estoque real)

### 2. Encontrar coordenadas (modo teste)

1. Execute o script principal em modo teste:

   ```powershell
   python lançamento_clicks.py
   ```

2. No menu exibido, escolha a opção `1` (modo teste).
3. Clique nos elementos solicitados pelo script (menus, campos de busca, botões etc.).
4. Anote as coordenadas X e Y que forem exibidas ou siga as instruções do `COORDENADAS.md`.

### 3. Atualizar coordenadas no script

Abra o arquivo `lançamento_clicks.py` em um editor de texto (VS Code, por exemplo) e atualize as constantes de coordenadas:

```python
COORD_MENU_CONTROLE_ESTOQUE = (X, Y)
COORD_CAMPO_BUSCA_ESTOQUE   = (X, Y)
# ...
```

Use o `COORDENADAS.md` como referência para cada campo que precisa de ajuste.

### 4. Executar automação

1. Certifique-se de que:
   - `sku.csv` e `estoque.csv` estão preenchidos e com a mesma quantidade de linhas.
   - A data desejada está configurada (ou use `copiar_data.py` para copiá-la para o clipboard).

2. Execute:

   ```powershell
   python lançamento_clicks.py
   ```

3. Escolha a opção `2` (automação completa).
4. Aguarde o tempo inicial (por exemplo, 5 segundos) e não mexa no mouse/teclado enquanto a automação estiver rodando.

O tempo total vai depender da quantidade de SKUs, mas um cenário típico fica entre 30 e 60 minutos de execução contínua.

### 5. Conferir resultados

Ao final:

- Verifique no Olist/Tiny se todos os SKUs foram lançados.
- Confirme se:
  - Nas janelas de balanço, o estoque foi zerado (quantidade igual a 0).
  - Na janela de estoque real (Conta 6), as quantidades refletem os valores de `estoque.csv`.

---

## Estrutura de dados (CSV)

### `contagem/sku.csv`

- Um SKU por linha.
- Exemplo:

  ```text
  LB-00323
  LB-00325
  LB-00329
  ```

### `contagem/estoque.csv`

- Uma quantidade inteira por linha, na mesma ordem de `sku.csv`.
- Exemplo:

  ```text
  26
  33
  1
  ```

---

## Configurações principais

No topo de `lançamento_clicks.py` você encontra as principais constantes:

```python
# Data do lançamento
DATA_LANCAMENTO = '20/05/2026'

# Tempos de espera (em segundos)
TEMPO_PADRAO       = 1   # Intervalo entre ações
TEMPO_CARREGAMENTO = 2   # Carregamento de páginas

# Caminhos dos arquivos de contagem
CAMINHO_SKUS    = r'c:\Users\seu_usuario\Documents\Contagem\contagem\sku.csv'
CAMINHO_ESTOQUE = r'c:\Users\seu_usuario\Documents\Contagem\contagem\estoque.csv'
```

Ajuste os caminhos para a estrutura de pastas do seu ambiente antes de rodar a automação.

---

## Fluxo da automação

Em alto nível, a automação segue este fluxo:

1. Carrega e valida os dados:
   - Lê os SKUs de `sku.csv`.
   - Lê as quantidades de `estoque.csv`.
   - Valida se a quantidade de linhas é igual.

2. Para cada SKU:
   - Zera o estoque (balanço = 0) nas janelas de matriz e filiais.
   - Lança o estoque real na janela de Conta 6.

3. Ao final, exibe um resumo da execução no console (por exemplo, total de SKUs processados).

---

## Troubleshooting rápido

| Problema                                | Possível solução                                                   |
|-----------------------------------------|---------------------------------------------------------------------|
| Script clica no lugar errado            | Refazer o modo teste (opção 1) e atualizar as coordenadas          |
| Campos não carregam a tempo             | Aumentar `TEMPO_CARREGAMENTO` para 3 ou mais segundos              |
| Script trava no meio                    | Pressionar `Ctrl + C` no terminal e executar novamente             |
| Data não é colada corretamente          | Rodar `copiar_data.py` e confirmar se a data está no clipboard     |
| Erro de arquivo não encontrado          | Verificar `CAMINHO_SKUS` e `CAMINHO_ESTOQUE` no script             |
| Quantidades inconsistentes entre janelas| Conferir se `sku.csv` e `estoque.csv` têm o mesmo número de linhas |

Para detalhes adicionais, consulte `INSTRUÇÕES.md` e `COORDENADAS.md`.

---

## Checklist pré-execução

Antes de rodar a automação completa, confirme:

- [ ] Python 3.11+ instalado.
- [ ] Bibliotecas `pyautogui`, `pynput` e `selenium` instaladas.
- [ ] 4 janelas do Chrome abertas com Olist/Tiny logado.
- [ ] Coordenadas testadas e atualizadas em `lançamento_clicks.py`.
- [ ] `sku.csv` preenchido com todos os SKUs.
- [ ] `estoque.csv` preenchido com todas as quantidades.
- [ ] Mesma quantidade de linhas em `sku.csv` e `estoque.csv`.
- [ ] Data correta configurada (ou disponível no clipboard).
- [ ] Tempo de 30 a 60 minutos livre para deixar a automação rodar sem interrupções.

---

## Conceitos importantes

- **Coordenadas (X, Y)**  
  Representam a posição do mouse na tela em pixels.  
  - X: eixo horizontal (0 no extremo esquerdo).  
  - Y: eixo vertical (0 no topo da tela).

- **Balanço**  
  Tipo de lançamento em que a quantidade é zerada (0), utilizado para resetar o estoque nas janelas de matriz e filiais.

- **Estoque**  
  Lançamento com a quantidade real do produto, usado na janela específica de estoque real (por exemplo, Conta 6).

- **Clipboard (área de transferência)**  
  O script utiliza o clipboard do sistema para copiar e colar SKUs e a data usando `Ctrl + C` / `Ctrl + V`.

---

## Licença

Defina aqui a licença do projeto (por exemplo, MIT).  
Exemplo:

```text
Este projeto está licenciado sob os termos da licença MIT.
Consulte o arquivo LICENSE para mais detalhes.
```
