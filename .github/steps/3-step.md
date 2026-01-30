## Passo 3: Criar fluxo de trabalho para testar sua ação

Pause por um momento! Precisamos de uma forma de testar suas ações em um ambiente real do GitHub.

Vamos criar um fluxo de trabalho que acionará sua ação sempre que um novo comentário for adicionado a uma issue. Sua ação analisará a piada no comentário e usaremos esse resultado para atualizar o comentário com a avaliação gerada por IA.

### 📖 Teoria: Concedendo acesso ao GitHub Models

O token interno `{% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}` usado em fluxos de trabalho do GitHub Actions não tem acesso aos modelos de IA do GitHub por padrão.

Para habilitar seu fluxo de trabalho a usar esses modelos, você precisa conceder explicitamente a permissão `models: read` no seu arquivo de fluxo de trabalho.

> [!NOTE]
> Para uma lista completa das permissões disponíveis do `GITHUB_TOKEN` e orientação sobre como configurá-las, veja a [referência de permissões de fluxo de trabalho](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#permissions).

### ⌨️ Atividade: Criar o fluxo de trabalho

Vamos ver sua ação Rate Jokes em ação criando um fluxo de trabalho que a usa!

1. Crie um novo arquivo de fluxo de trabalho do GitHub Actions com o seguinte nome

   ```txt
   .github/workflows/rate-joke.yml
   ```

   > 🪧 Nota: Para fins de aprendizado, criamos o fluxo de trabalho no mesmo repositório que sua ação. No entanto, você normalmente consumiria sua ação publicada de outro projeto/repositório.

1. Vamos definir o fluxo de trabalho para acionar em novos comentários de issue e definir as permissões necessárias:

   ```yaml
   name: Rate Joke
   run-name: {% raw %}Avaliar Piada por ${{ github.event.comment.user.login }}{% endraw %}

   on:
     issue_comment:
       types: [created]

   permissions:
     issues: write
     contents: read
     models: read


   ```

1. Agora vamos adicionar um job para executar sua ação e atualizar o comentário com a análise de piada da IA.

   Adicione o seguinte passo ao seu fluxo de trabalho:

    ```yaml

    jobs:
      joke:
        name: Avaliar Piada
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v6
          - name: Avaliar Piada
            id: rate-joke
            uses: ./
            with:
              joke: {% raw %}${{ github.event.comment.body }}{% endraw %}
              token: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
          - name: Atualizar comentário
            uses: peter-evans/create-or-update-comment@v5
            with:
              comment-id: {% raw %}${{ github.event.comment.id }}{% endraw %}
              reactions: laugh
              edit-mode: replace
              body: |
                ## 🤖 Resultados da Avaliação de Piada por IA

                **Sua piada:**
                > {% raw %}${{ github.event.comment.body }}{% endraw %}

                **Análise da IA:**
                {% raw %}${{ steps.rate-joke.outputs.result }}{% endraw %}
    ```

    Primeiro, chamamos sua ação com o corpo do comentário como entrada; então atualizamos o comentário original da issue no lugar usando `peter-evans/create-or-update-comment`, inserindo a avaliação de IA gerada pela sua ação.

    > ❗️ **Importante:** Certifique-se de que a indentação está correta no seu arquivo YAML.

1. Faça commit e push do arquivo de fluxo de trabalho para o branch `main`:

   ```sh
   git add .github/workflows/rate-joke.yml
   git commit -m "Add workflow to test rate joke action"
   git push
   ```

### ⌨️ Atividade: Testar o fluxo de trabalho com comentários reais

Vamos tentar testar o fluxo de trabalho comentando aqui mesmo, na issue!

1. Poste um comentário contendo uma piada para acionar o fluxo de trabalho.

   Exemplo:

   ```md
   Por que o livro de matemática estava triste? Porque tinha muitos problemas!
   ```

   Após um momento, você deverá ver o comentário que adicionou ser atualizado.

   Você também pode monitorar a aba Actions do seu repositório para ver a execução do fluxo de trabalho e verificar se há erros.

1. (opcional) Poste um comentário sem uma piada para testar como sua ação lidará com comentários que não são piadas.

   Exemplo:

   ```md
   Eu amo aprender sobre GitHub Actions!
   ```

1. Com o comentário adicionado, Mona deve compartilhar os próximos passos!

<details>
<summary>Tendo problemas? 🤷</summary><br/>

Se o fluxo de trabalho não acionar ou completar com sucesso, por favor verifique o seguinte:

- Veja se há erros na aba Actions do seu repositório.
- Certifique-se de que você executou `npm run build` nas últimas alterações de código.
- Certifique-se de que o arquivo de fluxo de trabalho está formatado corretamente
- Se você estiver encontrando problemas de limitação de taxa, por favor aguarde alguns minutos e tente novamente.
  - Se você atingiu os limites diários, pode ter que voltar a este exercício amanhã.

</details>
