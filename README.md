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

## 📚 Recursos Adicionais

- [Documentação do UV](https://docs.astral.sh/uv/)
- [MySQL MCP Server no PyPI](https://pypi.org/project/mysql-mcp-server/)
- [Model Context Protocol Documentation](https://modelcontextprotocol.io/docs/getting-started/intro)

---

*Documento criado para facilitar a configuração do MySQL MCP Server em projetos de desenvolvimento.*
