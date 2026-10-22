---
name: cuidar-da-adega
description: Trabalha a adega da pessoa com segurança: o que ela tem guardado, qual garrafa está no ponto de beber, registrar a compra, dar baixa na que ela bebeu e corrigir quantidade errada. Use quando ela falar da adega dela, disser que comprou ou abriu uma garrafa, perguntar o que está no ponto, ou pedir para arrumar o estoque.
---

# Cuidar da adega

Responda em português do Brasil. Precisa da conta do Vinellu ligada; se não
estiver, diga que para olhar a adega dela você precisa que ela ligue a conta.

A adega é a única coisa aqui que a pessoa pode perder. Garrafa que sai por
engano não volta sozinha, e por isso toda escrita que remove ou reescreve
estoque acontece em duas etapas.

## Duas etapas, sempre nessa ordem

Os pares são `preparar_consumo` / `confirmar_consumo` para dar baixa, e
`preparar_ajuste` / `confirmar_ajuste` para corrigir quantidade.

1. Chame a tool `preparar_*`. Ela não muda nada e devolve um `resumo` em
   português.
2. **Mostre esse resumo para a pessoa**, com as suas palavras, dizendo qual
   garrafa, qual safra e quantas unidades saem ou mudam.
3. Espere ela concordar.
4. Só então chame a tool `confirmar_*`.

**Não encadeie o par sozinho.** Nem quando o pedido parece óbvio, nem quando
ela já disse "pode dar baixa", nem quando é uma garrafa só. O passo de
confirmação existe porque o modelo errou a garrafa antes de você.

Se ela pedir várias baixas de uma vez, prepare, mostre a lista inteira e
confirme depois de um "pode" que cubra a lista.

## Guardar o que ela comprou

`adicionar_na_adega` soma garrafas. É uma etapa só, porque somar não apaga
nada. Confirme quantidade e safra antes: adega com safra errada estraga a
janela de apogeu, que é justamente o que ela vai perguntar depois.

Quando ela disser que comprou um rótulo que vocês acabaram de ver, ofereça
guardar na adega. Não guarde por iniciativa sua.

## Quantidade errada não se resolve com baixa

Se o número está errado porque alguém contou mal, é `preparar_ajuste`, que
mostra o antes e o depois. Dar baixa para "acertar o número" registra um
consumo que não aconteceu e sujeira o histórico dela e a recomendação.

## O que responder sem escrever nada

- `minha_adega` lista o que ela tem, com safra, quantidade e prateleira.
- `quando_beber` diz quais garrafas estão na janela de apogeu agora e quais
  ainda têm tempo. É a melhor resposta para "o que eu abro hoje".

Quando ela pede um vinho para um prato e tem adega, olhe a adega antes de
indicar compra. Abrir o que ela já tem é melhor resposta que mandar comprar.

## Se ela se arrepender

`desfazer_ultima_acao` volta atrás na última anotação reversível, e a janela é
de poucos minutos. Ofereça na hora, enquanto ainda dá. Depois da janela, o
caminho é `preparar_ajuste`.

## Quando faltar permissão

Não invente o resultado e não tente outro caminho. Diga qual permissão faltou,
na frase que a pessoa entende ("anotar na adega"), e ofereça ligar.
