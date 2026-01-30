## Passo 1: Configure a ação para avaliar suas piadas!

Você está convencido de que cada piada que você conta é uma obra-prima da comédia 😂 Hoje um robô sem emoções dará sua opinião 🤖.

Neste exercício você construirá uma GitHub Action que permite que a IA avalie o quão engraçadas suas piadas realmente são!

<img width="600" alt="dois octocats compartilham um trocadilho científico" src="https://github.com/user-attachments/assets/6ea16f47-5cf8-4448-9e4d-04283072822c" />

### ⌨️ Atividade: Configure seu ambiente de desenvolvimento

Vamos usar o **GitHub Codespaces** para configurar um ambiente de desenvolvimento baseado em nuvem e trabalhar nele durante o restante do exercício!

1. Clique com o botão direito no botão abaixo para abrir a página **Create Codespace** em uma nova aba. Use a configuração padrão.

   [![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/{{full_repo_name}}?quickstart=1)

1. Confirme que o campo **Repository** é sua cópia do exercício, não o original, então clique no botão verde **Create Codespace**.

   - ✅ Sua cópia: `/{{full_repo_name}}`
   - ❌ Original: `/dev-pods/create-ai-powered-actions`

1. Aguarde um momento para o Visual Studio Code carregar no seu navegador.

1. Verifique se o **Node.js** está disponível abrindo um terminal e executando:

   ```sh
   node --version
   npm --version
   ```

   <details>
   <summary>Tendo problemas? 🤷</summary><br/>

   - Certifique-se de que você selecionou sua cópia pessoal do repositório, não o template original.
   - Se o Codespace falhar ao iniciar, tente atualizar a página e criar um novo.
   - Node.js e npm devem estar pré-instalados no ambiente de desenvolvimento.

   </details>

### ⌨️ Atividade: Instalar OpenAI SDK

Agora que seu Codespace está pronto, vamos instalar o OpenAI SDK, que você usará para interagir com o GitHub Models.

1. Abra o terminal no seu Codespace.
1. Execute o seguinte comando para instalar o OpenAI SDK:

   ```sh
   npm install openai
   ```

1. Verifique a instalação conferindo o arquivo `package.json` para a dependência `openai`.

### ⌨️ Atividade: Criar arquivo de metadados

A ação que criaremos hoje aceitará uma piada como entrada e retornará uma avaliação gerada por IA dessa piada.

1. Crie o arquivo `action.yml` na raiz do repositório e defina os metadados da ação conforme mostrado abaixo.


   ```yml
   name: "Rate Joke Action"
   description: "Avalia uma piada usando GitHub Models"

   inputs:
     joke:
       description: "A piada a ser avaliada"
       required: true
     token:
       description: "Token de acesso pessoal a ser usado para chamada da API do GitHub Models"
       default: {% raw %}${{ github.token }}{% endraw %}

   outputs:
     result:
       description: "Avaliação da piada gerada por IA"

   runs:
     using: node24
     main: dist/index.js
   ```

   > ❕ **Importante:** Se você não está familiarizado com o que este arquivo é, pause e complete primeiro o **exercício [write-javascript-actions](https://github.com/dev-pods/write-javascript-actions)**.

1. Faça commit e push das alterações para o branch `main`.
1. Com as alterações enviadas para o GitHub, Mona verificará seu trabalho e compartilhará os próximos passos.
