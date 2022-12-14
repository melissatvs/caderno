# Como mudar a "branch origem" da minha "branch" atual?

Encontrei uma boa resposta no StackOverflow, só que a explicação não usa comandos e imagens, só texto.

> **StackOverflow pt-br**
>
>  * Pergunta: https://pt.stackoverflow.com/q/375082/111325
>  * Resposta: https://pt.stackoverflow.com/a/375104/111325

Sou muito visual e embora use somente linhas de comando para git, também gosto de exemplos visuais.

Se houvesse uma sequência de comandos, já me ajudaria muito mais rápido.

Resolvi então anotar em um caderno (de papel mesmo), a solução perfeita pra que eu consultasse mais rápido, e estou transferindo pra cá.

## Resposta

Vamos nomear as branchs para evitar confusão:

   * :red_circle:*Branch-A*: originou a sua branch
   * :green_circle:*Branch-B*: branch que será a nova origem
   * :purple_circle:*Branch-C*: sua branch, criada a partir da :red_circle:*Branch-A*

![](./imgs/situacao.PNG)

Siga os passos:

1. Crie uma branch a partir da :green_circle:*Branch-B* (chamaremos essa branch de :large_blue_circle:*Branch-D*)
  ```
  git checkout Branch-B
  git checkout -b Branch-D
  ```

![](./imgs/passo1.PNG)

2. Na :purple_circle:*Branch-C*, volte para o último ponto (commit) sem suas alterações:
  ```
  git checkout Branch-C
  git reset –-soft [commit-Id]
  ```

![](./imgs/passo2.PNG)

3. Agora salve temporariamente suas alterações:
  ```
  git stash
  ```

![](./imgs/passo3.PNG)

4. As alterações que estão salvas, mova para a :large_blue_circle:*Branch-D*. Alguns conflitos podem aparecer, resolva esses conflitos e salve suas alterações.
  ```
  git checkout Branch-D
  git stash apply
  ```
  Se necessário, resolva conflitos
  ```
  git add .
  git commit -m "feat: minhas alterações"
  ```

![](./imgs/passo4.PNG)

5. :purple_circle:*Branch-C* pode ser excluída:
  ```
  git branch –D Branch-C
  ```


![](./imgs/passo5.PNG)
