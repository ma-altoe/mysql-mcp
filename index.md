---
layout: default
---

# Configurando MySQL MCP Server

Este guia explica como configurar um servidor MCP (Model Context Protocol) para MySQL, permitindo que assistentes de IA interajam de forma segura diretamente com bancos de dados MySQL.

## 📋 Pré-requisitos

- Python 3.11 ou superior
- Acesso a um banco de dados MySQL
- VS Code (recomendado)

## 🚀 Instalação

### 1. Instalar o UV (Python Package Manager)

O UV é uma ferramenta moderna e rápida para gerenciamento de pacotes Python. Instale usando o instalador standalone:

```bash
# Linux/macOS
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows PowerShell
powershell -c "irm https://astral.sh/uv/install.sh | iex"
```

Para mais opções de instalação, consulte a [documentação oficial do UV](https://docs.astral.sh/uv/getting-started/installation/#standalone-installer).

### 2. Instalar o MySQL MCP Server

Com o UV instalado, instale o servidor MCP para MySQL:

```bash
pip install mysql-mcp-server
```

> 📦 **Referência oficial**: [mysql-mcp-server no PyPI](https://pypi.org/project/mysql-mcp-server/)

## ⚙️ Configuração

### 1. Ativar o MCP no VS Code

Primeiro, ative o suporte ao MCP no VS Code:

1. Abra **VS Code Settings** (`Ctrl+,` ou `Cmd+,`)
2. Pesquise por **"Chat: Enable MCP"**
3. **Marque a opção** para ativar o Agent Mode
4. Reinicie o VS Code para aplicar as mudanças

### 2. Criar arquivo de configuração MCP

No seu projeto VS Code, crie o arquivo `.vscode/mcp.json` com o seguinte conteúdo:

```json
{
  "servers": {
    "mysql": {
      "type": "stdio",
      "command": "uvx",
      "args": ["--from", "mysql-mcp-server", "mysql_mcp_server"],
      "env": {
        "MYSQL_HOST": "${env:MYSQL_HOST}",
        "MYSQL_PORT": "${env:MYSQL_PORT}",
        "MYSQL_USER": "${env:MYSQL_USER}",
        "MYSQL_PASSWORD": "${env:MYSQL_PASSWORD}",
        "MYSQL_DATABASE": "${env:MYSQL_DATABASE}"
      }
    }
  }
}
```

### 3. Configurar variáveis de ambiente

Defina as seguintes variáveis de ambiente no seu sistema ou arquivo `.env`:

```bash
export MYSQL_HOST="localhost"
export MYSQL_PORT="3306"
export MYSQL_USER="seu_usuario"
export MYSQL_PASSWORD="sua_senha"
export MYSQL_DATABASE="seu_banco"
```

#### No Windows:
```cmd
set MYSQL_HOST=localhost
set MYSQL_PORT=3306
set MYSQL_USER=seu_usuario
set MYSQL_PASSWORD=sua_senha
set MYSQL_DATABASE=seu_banco
```

#### No VS Code (.env file):
Crie um arquivo `.env` na raiz do projeto:

```env
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=seu_usuario
MYSQL_PASSWORD=sua_senha
MYSQL_DATABASE=seu_banco
```

## 🔧 Estrutura de Arquivos

Após a configuração, sua estrutura de projeto deve incluir:

```
projeto/
├── .vscode/
│   └── mcp.json
├── .env (opcional)
└── ... outros arquivos
```

## ✅ Verificação da Instalação

Para verificar se tudo está funcionando corretamente:

1. **Teste a instalação do UV:**
   ```bash
   uv --version
   ```

2. **Teste a instalação do mysql-mcp-server:**
   ```bash
   uvx --from mysql-mcp-server mysql_mcp_server --help
   ```

3. **Verifique as variáveis de ambiente:**
   ```bash
   echo $MYSQL_HOST
   echo $MYSQL_USER
   # (não recomendado imprimir a senha por segurança)
   ```

## 🎯 Uso

Com tudo configurado, seu assistente de IA poderá:

- Executar consultas SQL diretamente no banco
- Analisar estruturas de tabelas
- Gerar relatórios baseados nos dados
- Ajudar com otimização de queries

### Exemplo de comandos que funcionarão:

- "Mostre-me todas as tabelas do banco"
- "Execute esta query SQL: SELECT * FROM usuarios LIMIT 10"
- "Analise a estrutura da tabela produtos"
- "Gere um relatório de vendas do último mês"

## 🤖 Exemplos Práticos com GitHub Copilot

### 1. Análise de Estrutura de Banco

**Você pergunta:**
```
"Analise a estrutura do meu banco de dados e me mostre todas as tabelas disponíveis"
```

**GitHub Copilot responde:**
```sql
-- Consulta executada automaticamente
SHOW TABLES;

-- Resultado: lista de todas as tabelas
+------------------+
| Tables_in_loja   |
+------------------+
| usuarios         |
| produtos         |
| pedidos          |
| categorias       |
+------------------+
```

### 2. Consultas Inteligentes

**Você pergunta:**
```
"Me mostre os 10 produtos mais vendidos do último mês"
```

**GitHub Copilot gera e executa:**
```sql
SELECT 
    p.nome,
    p.preco,
    SUM(pp.quantidade) as total_vendido,
    COUNT(pp.pedido_id) as num_pedidos
FROM produtos p
JOIN pedido_produto pp ON p.id = pp.produto_id
JOIN pedidos pd ON pp.pedido_id = pd.id
WHERE pd.data_pedido >= DATE_SUB(NOW(), INTERVAL 1 MONTH)
GROUP BY p.id, p.nome, p.preco
ORDER BY total_vendido DESC
LIMIT 10;
```

### 3. Relatórios Automatizados

**Você pergunta:**
```
"Gere um relatório de vendas por categoria dos últimos 3 meses"
```

**GitHub Copilot executa:**
```sql
SELECT 
    c.nome as categoria,
    COUNT(DISTINCT pd.id) as total_pedidos,
    SUM(pp.quantidade * p.preco) as receita_total,
    AVG(pp.quantidade * p.preco) as ticket_medio
FROM categorias c
JOIN produtos p ON c.id = p.categoria_id
JOIN pedido_produto pp ON p.id = pp.produto_id
JOIN pedidos pd ON pp.pedido_id = pd.id
WHERE pd.data_pedido >= DATE_SUB(NOW(), INTERVAL 3 MONTH)
GROUP BY c.id, c.nome
ORDER BY receita_total DESC;
```

### 4. Otimização de Performance

**Você pergunta:**
```
"Esta query está lenta, pode me ajudar a otimizá-la?"
```

**GitHub Copilot analisa e sugere:**
```sql
-- Query original (lenta)
SELECT * FROM pedidos WHERE YEAR(data_pedido) = 2024;

-- Sugestão otimizada
SELECT * FROM pedidos 
WHERE data_pedido >= '2024-01-01' 
  AND data_pedido < '2025-01-01';

-- Sugestão de índice
CREATE INDEX idx_pedidos_data ON pedidos(data_pedido);
```

### 5. Análise de Dados Complexa

**Você pergunta:**
```
"Identifique padrões de compra dos usuários e segmente os clientes"
```

**GitHub Copilot gera:**
```sql
WITH customer_stats AS (
    SELECT 
        u.id,
        u.nome,
        COUNT(DISTINCT pd.id) as total_pedidos,
        SUM(pp.quantidade * p.preco) as valor_total,
        AVG(pp.quantidade * p.preco) as ticket_medio,
        DATEDIFF(NOW(), MAX(pd.data_pedido)) as dias_ultima_compra
    FROM usuarios u
    JOIN pedidos pd ON u.id = pd.usuario_id
    JOIN pedido_produto pp ON pd.id = pp.pedido_id
    JOIN produtos p ON pp.produto_id = p.id
    GROUP BY u.id, u.nome
)
SELECT 
    *,
    CASE 
        WHEN total_pedidos >= 10 AND valor_total >= 1000 THEN 'VIP'
        WHEN total_pedidos >= 5 AND valor_total >= 500 THEN 'Frequente'
        WHEN dias_ultima_compra <= 30 THEN 'Ativo'
        ELSE 'Casual'
    END as segmento_cliente
FROM customer_stats
ORDER BY valor_total DESC;
```

### 6. Monitoramento em Tempo Real

**Você pergunta:**
```
"Monitore vendas em tempo real e me alerte sobre anomalias"
```

**GitHub Copilot cria:**
```sql
-- Vendas da última hora
SELECT 
    DATE_FORMAT(data_pedido, '%Y-%m-%d %H:00') as hora,
    COUNT(*) as pedidos,
    SUM(pp.quantidade * p.preco) as receita
FROM pedidos pd
JOIN pedido_produto pp ON pd.id = pp.pedido_id
JOIN produtos p ON pp.produto_id = p.id
WHERE data_pedido >= DATE_SUB(NOW(), INTERVAL 1 HOUR)
GROUP BY DATE_FORMAT(data_pedido, '%Y-%m-%d %H:00')
ORDER BY hora DESC;

-- Alerta para queda nas vendas (menos de 5 pedidos na última hora)
SELECT 
    CASE 
        WHEN COUNT(*) < 5 THEN 'ALERTA: Vendas baixas!'
        ELSE 'Vendas normais'
    END as status
FROM pedidos 
WHERE data_pedido >= DATE_SUB(NOW(), INTERVAL 1 HOUR);
```

## 🔒 Segurança

### ⚠️ Importante:

- **Nunca** commite credenciais de banco no arquivo `mcp.json`
- Use sempre variáveis de ambiente para dados sensíveis
- Configure usuários de banco com privilégios mínimos necessários
- Para produção, considere usar conexões SSL/TLS

### Exemplo de usuário com privilégios limitados:

```sql
-- Criar usuário apenas para leitura
CREATE USER 'mcp_readonly'@'localhost' IDENTIFIED BY 'senha_segura';
GRANT SELECT ON database_name.* TO 'mcp_readonly'@'localhost';
FLUSH PRIVILEGES;
```

## 🛠️ Troubleshooting

### Problemas Comuns:

1. **Erro de conexão:**
   - Verifique se o MySQL está rodando
   - Confirme host, porta e credenciais
   - Teste conectividade: `mysql -h localhost -u usuario -p`

2. **Comando uvx não encontrado:**
   - Reinstale o UV seguindo a documentação oficial
   - Verifique se o PATH está configurado corretamente

3. **Permissões negadas:**
   - Verifique privilégios do usuário MySQL
   - Confirme se o usuário tem acesso ao banco especificado

4. **VS Code não reconhece o MCP:**
   - Verifique se "Chat: Enable MCP" está ativado nas configurações
   - Reinicie o VS Code após criar/modificar `mcp.json`
   - Verifique se as extensões necessárias estão instaladas

## ❓ FAQ - Perguntas Frequentes

<details>
<summary><strong>Q: O MCP funciona com outras extensões do VS Code?</strong></summary>
<br>
<strong>A:</strong> Sim! O MCP é compatível com a maioria das extensões. Ele funciona especialmente bem com extensões de banco de dados, Git, e ferramentas de desenvolvimento.
</details>

<details>
<summary><strong>Q: Posso usar MCP com PostgreSQL, SQLite ou outros bancos?</strong></summary>
<br>
<strong>A:</strong> Sim! Existem servidores MCP para diversos bancos:
<ul>
<li>PostgreSQL: <code>pip install postgres-mcp-server</code></li>
<li>SQLite: <code>pip install sqlite-mcp-server</code></li>
<li>MongoDB: Verifique repositórios da comunidade</li>
</ul>
</details>

<details>
<summary><strong>Q: O GitHub Copilot vai executar comandos destrutivos (DELETE, DROP)?</strong></summary>
<br>
<strong>A:</strong> Por segurança, recomendamos criar usuários de banco somente com privilégios de leitura (SELECT). O Copilot sempre mostra a query antes de executar, permitindo revisão.
</details>

<details>
<summary><strong>Q: Posso usar MCP em projetos de equipe?</strong></summary>
<br>
<strong>A:</strong> Sim! Cada desenvolvedor pode ter seu próprio arquivo <code>.vscode/mcp.json</code> com suas credenciais. Use <code>.gitignore</code> para não versionar credenciais.
</details>

<details>
<summary><strong>Q: O MCP consome muitos recursos do sistema?</strong></summary>
<br>
<strong>A:</strong> Não. O MCP é leve e só ativa quando solicitado. O processo <code>mysql_mcp_server</code> consome poucos MB de RAM.
</details>

<details>
<summary><strong>Q: Como funciona a segurança das credenciais?</strong></summary>
<br>
<strong>A:</strong> As credenciais ficam em variáveis de ambiente locais, nunca são enviadas para serviços externos. O MCP conecta diretamente ao seu banco local.
</details>

<details>
<summary><strong>Q: Posso personalizar as respostas do GitHub Copilot?</strong></summary>
<br>
<strong>A:</strong> Sim! Você pode fazer perguntas específicas como "explique esta query", "otimize para performance", ou "adicione comentários explicativos".
</details>

<details>
<summary><strong>Q: O MCP funciona offline?</strong></summary>
<br>
<strong>A:</strong> O servidor MCP funciona offline, mas o GitHub Copilot precisa de internet para processar suas solicitações.
</details>

<details>
<summary><strong>Q: Como limitar o acesso a tabelas específicas?</strong></summary>
<br>
<strong>A:</strong> Configure um usuário MySQL com privilégios granulares:

```sql
GRANT SELECT ON database.tabela_especifica TO 'mcp_user'@'localhost';
```
</details>

<details>
<summary><strong>Q: O que fazer se o MCP parar de funcionar após atualização?</strong></summary>
<br>
<strong>A:</strong> 
<ol>
<li>Atualize o mysql-mcp-server: <code>pip install --upgrade mysql-mcp-server</code></li>
<li>Reinicie o VS Code</li>
<li>Verifique se "Chat: Enable MCP" ainda está ativado</li>
</ol>
</details>

<details>
<summary><strong>Q: Posso usar MCP em produção?</strong></summary>
<br>
<strong>A:</strong> Para produção, use conexões SSL, usuários com privilégios mínimos, e considere um servidor de banco dedicado para análises.
</details>

<details>
<summary><strong>Q: Como fazer backup antes de usar MCP?</strong></summary>
<br>
<strong>A:</strong> Sempre recomendado! Use:

```bash
mysqldump -u usuario -p nome_banco > backup.sql
```
</details>

## 📚 Recursos Adicionais

- [Documentação do UV](https://docs.astral.sh/uv/)
- [MySQL MCP Server no PyPI](https://pypi.org/project/mysql-mcp-server/)
- [Model Context Protocol Documentation](https://modelcontextprotocol.io/docs/getting-started/intro)

---

*Documento criado para facilitar a configuração do MySQL MCP Server em projetos de desenvolvimento.*
