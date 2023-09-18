Roteiro Passo a Passo
Exercício 1: Fazer uma Requisição para Duas APIs
Crie um novo projeto Node.js.
Instale as dependências necessárias, como express para criar o servidor, node-fetch para fazer requisições HTTP e sqlite3 para trabalhar com o banco de dados local.

    bash
    Copy code
    npm install express node-fetch sqlite3
Configure um arquivo index.js para iniciar o servidor e configurar as rotas.

Crie um endpoint para receber o CEP como parâmetro na URL.

      javascript
      Copy code
      // index.js
 
      const express = require('express');
      const fetch = require('node-fetch');
      const app = express();
      const port = 3000;

      app.use(express.json());

      app.get('/buscarCEP/:cep', async (req, res) => {
      const { cep } = req.params;

  // Fazer requisição para as duas APIs
  
    const API1 = await fetch(`https://cep.awesomeapi.com.br/json/${cep}`);
    const API2 = await fetch(`https://brasilapi.com.br/api/cep/v2/${cep}`);
  
    // Verificar qual API respondeu primeiro
 
    const resposta = await Promise.any([API1.json(), API2.json()]);

    // Registrar no banco de dados local
  
    // (Esta etapa será abordada posteriormente)
  
    // Enviar resposta ao cliente

    res.json(resposta);
    });

    app.listen(port, () => {
      console.log(`Servidor rodando na porta ${port}`);
    });
    
Exercício 2: Criar o Banco de Dados Local
Crie um banco de dados local usando SQLite para armazenar os resultados das requisições.

    javascript
    Copy code
    // index.js
    const sqlite3 = require('sqlite3').verbose();
    const db = new sqlite3.Database('localDB.db');

    // Crie uma tabela para armazenar os resultados
    db.serialize(() => {
      db.run('CREATE TABLE IF NOT EXISTS resultados (cep TEXT PRIMARY KEY, data TEXT)');
    });
    
    // Função para inserir resultados no banco de dados
    const inserirResultadoNoDB = (cep, data) => {
      const stmt = db.prepare('INSERT OR REPLACE INTO resultados (cep, data) VALUES (?, ?)');
      stmt.run(cep, JSON.stringify(data));
      stmt.finalize();
    };
    
Modifique o código do endpoint para registrar o resultado no banco de dados local.
    
    javascript
    Copy code
    // index.js
    app.get('/buscarCEP/:cep', async (req, res) => {
      const { cep } = req.params;
    
      // Fazer requisição para as duas APIs
      const API1 = await fetch(`https://cep.awesomeapi.com.br/json/${cep}`);
      const API2 = await fetch(`https://brasilapi.com.br/api/cep/v2/${cep}`);
      
      
      // Verificar qual API respondeu primeiro
      const resposta = await Promise.race([API1.json(), API2.json()]);
    
    
      // Registrar no banco de dados local
      inserirResultadoNoDB(cep, resposta);
    
      
      // Enviar resposta ao cliente
      res.json(resposta);
    });
Exercício 3: Mostrar Resultados no Console
Adicione código para recuperar e mostrar os resultados no console.
    
    javascript
    Copy code
    // index.js
    app.get('/mostrarResultados', (req, res) => {
      // Consultar todos os resultados no banco de dados
      db.all('SELECT * FROM resultados', (err, rows) => {
        if (err) {
          console.error(err.message);
          res.status(500).json({ error: 'Erro interno do servidor' });
          return;
        }
    
        // Mostrar os resultados no console
        console.log('Resultados armazenados no banco de dados:');
        console.log(rows);
    
        // Enviar os resultados como resposta
        res.json(rows);
      });
    });
README
Aqui está um esboço do README para o seu projeto:

Projeto de Requisição de CEP com Banco de Dados Local
Este projeto é uma atividade que envolve a criação de um servidor Node.js que faz requisições para duas APIs de CEP e armazena os resultados em um banco de dados local (SQLite). Além disso, ele permite visualizar os resultados no console.

Pré-requisitos
Node.js instalado
npm (gerenciador de pacotes do Node.js) instalado
Instalação
Clone este repositório para o seu computador.
Navegue até o diretório do projeto no terminal.
Execute o comando npm install para instalar as dependências necessárias.
Execução
Para iniciar o servidor, execute node index.js.
Acesse http://localhost:3000/buscarCEP/{CEP} no seu navegador para fazer uma requisição de CEP, onde {CEP} é o CEP que você deseja buscar.
Use http://localhost:3000/mostrarResultados para visualizar os resultados armazenados no banco de dados local.
Estrutura do Projeto
index.js: O arquivo principal que configura o servidor e as rotas.
localDB.db: O banco de dados local SQLite onde os resultados são armazenados.
Exercícios
Exercício 1: Fazer uma requisição para duas APIs.
Exercício 2: Criar o banco de dados local.
Exercício 3: Mostrar resultados no console.
Dicas e Observações
Certifique-se de que as dependências necessárias estão instaladas usando npm install.
Os resultados das requisições são armazenados no banco de dados local para consultas posteriores.
Use os endpoints mencionados acima para testar o projeto e visualizar os resultados.
Você pode personalizar o projeto adicionando mais funcionalidades, como autenticação de usuários ou consultas mais avançadas ao banco de dados.
Este é um projeto básico que pode ser expandido e personalizado conforme necessário. Boa sorte!


Example of package.json with good meta data:

