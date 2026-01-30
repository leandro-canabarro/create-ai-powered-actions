## Passo 2: Implementar lógica de avaliação de piadas com IA

Certo! Agora que você configurou os metadados da ação e instalou o OpenAI SDK, é hora de implementar a lógica principal da sua GitHub Action alimentada por IA.

Vamos primeiro entender como interagiremos com o GitHub Models usando o OpenAI SDK.

### 📖 Teoria: Tour rápido pelo GitHub Models

O [GitHub Models](https://github.com/features/models) traz IA para seu fluxo de trabalho de desenvolvedor com uma única chave de API que desbloqueia múltiplos modelos líderes.

O GitHub Models usa diferentes [limites de taxa](https://docs.github.com/en/github-models/use-github-models/prototyping-with-ai-models#rate-limits), e os limites variam por modelo. Fique especialmente atento a isso se você não tem um plano pago do Copilot.

Todos os modelos também são acessíveis programaticamente, que é exatamente o que precisamos para nossa ação! Você pode usar requisições HTTP regulares ou aproveitar SDKs existentes para interagir com o GitHub Models - como você já deve ter adivinhado, usaremos o OpenAI SDK para este exercício.

> [!NOTE]
> Explore todos os modelos disponíveis no [GitHub Marketplace](https://github.com/marketplace/models).
>
> Aprenda mais sobre [limites de taxa](https://docs.github.com/en/github-models/use-github-models/prototyping-with-ai-models#rate-limits) do GitHub Models.

### ⌨️ Atividade: Implementar a ação

Vamos criar os arquivos fonte e implementar a lógica para sua ação.

1. Crie o diretório `src/` para guardar seus arquivos JavaScript:

1. Crie o arquivo `src/rateJoke.js` para conter a lógica de comunicação com o GitHub Models e avaliação de piadas:

   ```js
   const OpenAI = require("openai");

   async function rateJoke(joke, token) {
     const endpoint = "https://models.github.ai/inference";

     // Inicializa o cliente OpenAI com o endpoint do GitHub Models
     const client = new OpenAI({ baseURL: endpoint, apiKey: token });

     const response = await client.chat.completions.create({
       messages: [
         {
           role: "system",
           content:
             "Você é um assistente prestativo que avalia piadas. Avalie se a entrada é realmente uma piada e, se for, classifique sua qualidade de humor, criatividade e entrega. Responda brevemente e inclua uma avaliação numérica geral de 0 a 10.",
         },
         {
           role: "user",
           content: `Por favor, avalie esta piada: "${joke}"`,
         },
       ],
       model: "openai/gpt-4.1-mini",
     });

     // Retorna a resposta em texto simples
     return response.choices[0].message.content;
   }

   module.exports = { rateJoke };
   ```

   A função `rateJoke` inicializa um cliente OpenAI configurado para o endpoint do GitHub Models e envia a piada para um modelo de IA para avaliação.

   A resposta do modelo é retornada como texto simples.

1. Crie `src/main.js` que será a lógica principal da ação:

   ```js
   const { rateJoke } = require("./rateJoke");
   const core = require("@actions/core");

   async function run() {
     // Obtém as entradas
     const joke = core.getInput("joke", { required: true });
     const token = core.getInput("token", { required: true });

     // Avalia a piada usando GitHub Models
     const rating = await rateJoke(joke, token);

     // Define a saída
     core.setOutput("result", rating);
   }

   module.exports = { run };
   ```

   A função `run` recupera as entradas da ação, chama a função `rateJoke` para obter a avaliação gerada por IA e define a saída da ação.

1. Crie `src/index.js` que será o ponto de entrada principal da ação:

   ```js
   const { run } = require("./main");

   run();
   ```

### ⌨️ Atividade: Testar a ação localmente

Para testar a ação localmente, precisamos configurar um arquivo `.env` com variáveis de ambiente formatadas adequadamente para simular as entradas do GitHub Actions.

1. Crie uma cópia do arquivo `.env.example` e nomeie-o como `.env`

   ```sh
   cp .env.example .env
   ```

1. Execute este comando dentro do seu terminal para obter seu token do GitHub:

   ```sh
   echo $GITHUB_TOKEN
   ```

   E copie-o para sua área de transferência

1. Abra o arquivo `.env` e substitua o valor placeholder de `INPUT_TOKEN` pelo seu token do GitHub.

   > ✨ **Bônus:** Experimente mudar a entrada da piada para testar diferentes piadas! Mas fique atento aos limites de taxa!

1. Abra a seção `Run and Debug` do VSCode e execute a ação.

   > ✨ **Bônus:** Se você está familiarizado com os recursos de depuração do VSCode, defina breakpoints no seu código para percorrer a execução e inspecionar variáveis.

   <img width="400" alt="imagem mostrando a seção run and debug do vscode e um botão para executar a ação" src="https://github.com/user-attachments/assets/8be1b62f-8c11-4af0-9e0a-255b9d33f83e" />

   > 🪧 **Nota:** A configuração de execução está armazenada em `.vscode/launch.json` e usa `@github/local-action` junto com seu arquivo `.env`

1. Se tudo funcionar corretamente, você deverá ver a avaliação da piada gerada por IA no console de depuração!

   <details>
   <summary>📸 Mostrar screenshot</summary><br/>

   <img width="800" alt="imagem mostrando logs de depuração do github/local-action no console do vscode" src="https://github.com/user-attachments/assets/3eec5e80-21a7-4f55-b7ff-25f6a710874f" />

   </details>

### ⌨️ Atividade: Fazer build e empacotar a ação

Agora que você verificou que sua ação funciona corretamente quando testada localmente - vamos fazer o build e empacotar a ação para distribuição.

1. Vamos fazer o build da sua ação executando:

   ```sh
   npm run build
   ```

1. Faça commit e push de todas as alterações para o branch `main`.
1. Com as alterações enviadas para o GitHub, Mona verificará seu trabalho e compartilhará os próximos passos.
