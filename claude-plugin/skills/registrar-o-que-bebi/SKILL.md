---
name: registrar-o-que-bebi
description: Registra a avaliação de um rótulo e o check-in de que a pessoa está bebendo agora, avisando antes que o check-in vira post no feed dela. Use quando ela contar o que bebeu e o que achou, pedir para anotar a nota, quiser corrigir uma review dela, pedir para marcar que está bebendo, ou perguntar o que ela tomou num dia.
---

# Registrar o que ela bebeu

Responda em português do Brasil. Precisa da conta do Vinellu ligada.

Duas coisas diferentes moram aqui, e confundir as duas é o erro caro:

- **Review** é a avaliação dela sobre um rótulo. Nota de 1 a 5 e texto.
- **Check-in** é "estou bebendo esta agora", e **vira post no feed**, visível
  para os amigos dela.

## Review

`registrar_review` grava a nota e o texto. Antes de chamar, tenha as três
coisas: qual rótulo, que nota de 1 a 5, e o que ela achou. Se ela só disse
"gostei muito", pergunte a nota em vez de arbitrar uma.

O texto é dela. Pode arrumar a pontuação, pode transcrever o que ela falou,
mas não escreva uma review bonita no lugar dela e não acrescente descritor
sensorial que ela não disse. "Taninos sedosos com final longo de especiarias"
numa review de quem falou "desce fácil" é mentira com a assinatura dela.

`editar_review` muda nota ou texto de uma review dela. Review de outra pessoa
fica de fora, e não existe caminho alternativo para isso.

`minhas_reviews` lista o que ela escreveu, da mais recente para a mais antiga.
Use antes de registrar quando houver chance de já existir uma review daquele
rótulo: aí é correção, não review nova.

## Check-in: o aviso vem antes

O par é `preparar_checkin` / `confirmar_checkin`.

1. `preparar_checkin` não publica nada e devolve um `resumo`.
2. **Diga, com essas palavras, que isso vai aparecer no feed dela.** Quem pede
   "anota que estou tomando esse" muitas vezes não espera post.
3. Só chame `confirmar_checkin` depois do sim.

Não encadeie o par sozinho. Post publicado não é anotação privada, e desfazer
depois de alguém ver não desfaz.

Se ela quiser registrar sem aparecer para ninguém, o caminho é review, não
check-in. Diga isso em uma frase e siga.

## O histórico dela

`o_que_tomei` mostra os rótulos de um dia e separa o que foi check-in do que
foi só review escrita. É a resposta para "o que eu bebi no sábado".

Quando ela registra algo, feche citando o endereço no site (`url`) e o atalho
do aplicativo (`deep_link`) do rótulo. Não repita o JSON da resposta.

## Quando faltar permissão

Diga qual faltou na frase que ela entende ("escrever review") e ofereça ligar.
Não invente o resultado e não tente outro caminho.
