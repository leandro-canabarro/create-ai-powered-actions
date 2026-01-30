## Passo 4: Adicionar saídas estruturadas

Bom trabalho — pronto para ir um passo além?

Agora mesmo, o fluxo de trabalho atualiza ansiosamente cada novo comentário de issue, mesmo quando o comentário não é uma piada. O que realmente queremos é reagir apenas a piadas reais. A parte complicada é que nossa ação atualmente retorna texto simples, o que torna difícil escrever lógica condicional limpa no fluxo de trabalho.

Vamos mudar isso usando saídas estruturadas!

### 📖 Teoria: Assumindo o controle com saídas estruturadas

[Structured outputs](https://platform.openai.com/docs/guides/structured-outputs) permitem que você peça aos modelos dados bem-formados (JSON) em vez de texto livre, tornando a automação e integrações downstream confiáveis.

Structured Outputs garantem que o modelo sempre gere respostas que aderem ao seu JSON Schema fornecido.

[Zod](https://zod.dev/) é uma biblioteca popular de declaração e validação de schemas que pode ser usada no lugar de escrever [JSON Schemas](https://json-schema.org/) puros.

Uma vez que o schema é definido, você pode incluí-lo em suas requisições ao GitHub Models, e as respostas serão automaticamente validadas e parseadas de acordo com esse schema, em vez de texto de formato livre.



### ⌨️ Atividade: Instalar Zod

Vamos começar instalando [Zod](https://zod.dev/) no seu projeto de ação.

1. Execute o seguinte comando para instalar Zod:

   ```sh
   npm install zod
   ```

1. Verifique a instalação conferindo o arquivo `package.json` para a dependência `zod`.

### ⌨️ Atividade: Implementar saídas estruturadas

1. Substitua o conteúdo do seu arquivo `src/rateJoke.js`.

   ```js
   const OpenAI = require("openai");
   const { zodResponseFormat } = require("openai/helpers/zod");
   const { z } = require("zod");

   // Define o formato de saída estruturada usando schema Zod
   const JokeRatingSchema = z.object({
     is_joke: z.boolean().describe("Se a entrada é realmente uma piada ou tentativa de humor"),
     score: z.number().min(1).max(10).nullable().describe("Avaliação de 1 a 10, onde 10 é o mais engraçado."),
     humor_type: z.string().nullable().describe("O tipo de humor (ex: trocadilho, jogo de palavras, piada de pai, humor negro, etc)"),
     feedback: z.string().nullable().describe("Feedback curto sobre os pontos fortes e fracos da piada."),
   });

   async function rateJoke(joke, token) {
     const endpoint = "https://models.github.ai/inference";

     // Inicializa o cliente OpenAI com o endpoint do GitHub Models
     const client = new OpenAI({ baseURL: endpoint, apiKey: token });

     // Cria chat completion com formato de resposta Zod
     const completion = await client.chat.completions.parse({
       messages: [
         {
           role: "system",
           content:
             "Você é um assistente prestativo que avalia piadas. Avalie se a entrada é realmente uma piada e, se for, classifique sua qualidade de humor, criatividade e entrega.",
         },
         {
           role: "user",
           content: `Por favor, avalie esta piada: "${joke}"`,
         },
       ],
       model: "openai/gpt-4.1-mini",

       // Usa schema Zod para resposta estruturada
       response_format: zodResponseFormat(JokeRatingSchema, "joke_rating"),
     });

     // Retorna a resposta parseada (automaticamente validada pelo Zod)
     return completion.choices[0]?.message?.parsed;
   }

   module.exports = { rateJoke };
   ```

   This looks very similar to your previous implementation, but now includes the Zod schema definition and uses it for structured outputs from the AI model.

   Additionally, this function now returns a JSON object matching the defined schema instead of plain text.

### ⌨️ Atividade: Testar localmente e atualizar build

1. Na seção `Run and Debug` do VSCode, execute a ação.
1. Você deverá ver a saída `result` contendo dados JSON estruturados correspondentes ao schema definido.
1. Com nossas alterações funcionando, vamos agora fazer o build da versão de distribuição

   ```sh
   npm run build
   ```

   Isso deve atualizar seu arquivo `dist/index.js` com as últimas alterações de código.

> [!IMPORTANT]
> Atualizar seu build é crucial sempre que você faz alterações no código fonte. Deixar de fazer isso resultará na ação executando código desatualizado quando executada em fluxos de trabalho do GitHub.

### ⌨️ Atividade: Atualizar fluxo de trabalho com lógica condicional

Certo! Agora que a ação retorna dados estruturados, podemos atualizar o fluxo de trabalho para atualizar comentários apenas quando a entrada for realmente uma piada.

1. Abra seu arquivo de fluxo de trabalho em `.github/workflows/rate-joke.yml`.
1. Atualize o passo `Update Comment` para acionar apenas se a entrada for uma piada:

   ```yaml
   - name: Atualizar comentário
     if: fromJSON(steps.rate-joke.outputs.result).is_joke == true
     uses: peter-evans/create-or-update-comment@v5
   ```

1. Atualize o corpo do comentário para usar os campos estruturados:

   ```yaml
   body: |
     ## 🤖 Resultados da Avaliação de Piada por IA

     **Sua piada:**
     > {% raw %}${{ github.event.comment.body }}{% endraw %}

     **Análise da IA:**
     - **Pontuação:** {% raw %}${{ fromJSON(steps.rate-joke.outputs.result).score }}{% endraw %}/10
     - **Tipo de Humor:** {% raw %}${{ fromJSON(steps.rate-joke.outputs.result).humor_type }}{% endraw %}
     - **Feedback:** {% raw %}${{ fromJSON(steps.rate-joke.outputs.result).feedback }}{% endraw %}
   ```

1. Faça commit e push de todas as suas alterações para o branch `main`.
1. Com as alterações enviadas para o GitHub, Mona verificará seu trabalho e compartilhará os próximos passos.

> [!TIP]
> Embora nesta ação usemos uma única saída JSON `result` e a parseemos no fluxo de trabalho - pode ser uma boa ideia expor os campos estruturados como saídas individuais na ação
>
> Mais tarde, uma vez que você tenha terminado o exercício, sinta-se à vontade para revisitar o código da ação para adicionar saídas individuais para `is_joke`, `score`, `humor_type` e `feedback` para consumo mais fácil em fluxos de trabalho!
