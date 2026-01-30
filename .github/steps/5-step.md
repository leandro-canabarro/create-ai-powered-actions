## Passo 5: Acionar e validar

Incrível! :rocket: Você criou a GitHub Action Rate Jokes, aproveitou saídas estruturadas e criou um fluxo de trabalho para usá-la.

A única coisa que resta fazer é testá-la!

### ⌨️ Atividade: Experimente sua ação

Se tudo funcionar corretamente, seu fluxo de trabalho deve atualizar os comentários de piadas e deixar os comentários genéricos inalterados.

Vamos experimentar isso!

1. Aqui mesmo nesta issue, comente com uma piada que você gostaria que a IA avaliasse. Aqui estão alguns exemplos

    ```md
    Por que o livro de matemática estava triste? Porque tinha muitos problemas!
    ```

    ```md
    Qual é o doce preferido do átomo? Pé-de-molécula!
    ```

    ```md
    Por que o programador foi preso? Porque ele tinha muitos bugs no código!
    ```

    > 🪧 **Nota:** Você pode encontrar mais piadas (em inglês) no site [icanhazdadjoke](https://icanhazdadjoke.com/).

1. Vamos tentar comentar com um comentário que não seja piada e monitorar a aba Actions para ver se são classificados corretamente. Aqui estão alguns exemplos.

    ```md
    Eu amo aprender sobre GitHub Actions!
    ```

    ```md
    Como está o clima hoje?
    ```

    ```md
    Alguém pode me ajudar com meu código?
    ```

    Para todos os comentários que não são piadas, o fluxo de trabalho deve deixá-los inalterados.

    Nos logs do fluxo de trabalho você deve ver o passo `Update comment` sendo pulado.

   
    <details>
    <summary>📸 Mostrar screenshot</summary><br/>

    <img width="350" alt="aba de portas" src="https://github.com/user-attachments/assets/e7db9def-d6ac-4a4c-800a-43ee82be0b22" />

    </details>

1. Mona postará a revisão do exercício assim que seu novo fluxo de trabalho Rate Joke for completado **com sucesso**!

   <details>
   <summary>Tendo problemas? 🤷</summary><br/>

   Se o fluxo de trabalho não acionar ou falhar:
   - Verifique a aba Actions para mensagens de erro
   - Verifique se seu arquivo `dist/index.js` existe e foi commitado
   - Se você fez alguma atualização no seu código fonte, certifique-se de ter re-empacotado com `npm run build` e enviado as alterações
   - Certifique-se de que seu arquivo de fluxo de trabalho está formatado corretamente

    Se você não está recebendo revisões da Mona:
    - Certifique-se de que seu fluxo de trabalho é nomeado `Rate Joke` e completa com sucesso
   </details>

> [!NOTE]
> Mesmo depois que o exercício terminar, você pode continuar comentando com piadas ou não-piadas!
