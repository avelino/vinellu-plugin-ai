---
name: escolher-um-vinho
description: Conduz a escolha da garrafa e termina em onde comprar. Use quando a pessoa pedir indicação de vinho, disser o prato que vai comer, perguntar o que combina com alguma coisa, pedir um rótulo até certo valor, perguntar de uma uva, região ou país, pedir vinho de presente, ou perguntar quem vende um rótulo.
---

# Escolher um vinho

Responda em português do Brasil. Quem pergunta quer sair da conversa com uma
garrafa decidida, não com uma lista.

## A ordem que funciona

1. **Entenda a ocasião em uma pergunta, no máximo.** O que vai comer, quanto
   quer gastar, e para quando. Se a pessoa já disse, não pergunte de novo: com
   "o que combina com cordeiro" já dá para responder.
2. **Busque no acervo.** `harmonizar` quando existe um prato. `buscar_vinho`
   quando ela descreve o rótulo, a uva, o estilo ou a faixa de preço.
   `vinhos_por` quando o pedido é uma faceta inteira: um país, uma região, uma
   uva, um tipo.
3. **Afine pelo gosto dela, se a conta estiver ligada.** `meu_perfil_de_gosto`
   diz as uvas, os tipos e os países que ela mais gosta, e
   `minhas_recomendacoes` já traz a lista da semana com o motivo de cada uma.
   Usar isso é a diferença entre indicar um vinho e indicar o vinho dela.
4. **Feche em onde comprar.** `onde_comprar_para_mim` com a conta ligada, que
   já filtra as lojas que entregam no estado do perfil dela;
   `onde_comprar` sem conta, dizendo que a lista não está filtrada por estado.

## Três opções, com um motivo cada

Devolva no máximo três rótulos. Para cada um: o nome, por que ele serve
naquele caso, e o preço se o acervo tiver. Nada de tabela com dez colunas e
nada de despejar o retorno da busca.

Cite sempre o endereço no site (`url`) e o atalho do aplicativo (`deep_link`)
que vêm na resposta de cada rótulo. São os dois caminhos que a pessoa tem para
ver a garrafa, e o atalho abre no celular dela.

Se ela quiser mais detalhe de um deles, `ficha_do_rotulo` entrega a ficha
inteira: produtor, região, uvas, corpo, acidez, tanino, doçura, harmonizações
e as safras com nota. Traga a ficha quando ela pedir, não antes.

## O que não inventar

O acervo tem 174 mil rótulos e ainda assim não tem tudo. Se a busca não achar,
diga que não achou e ofereça o mais perto. **Nunca descreva um rótulo que não
veio do acervo**, nem complete uma ficha com o que você acha que sabe daquele
produtor: safra, nota e harmonização saem do Vinellu ou não saem.

Preço e estoque são da loja parceira, mudam, e a compra fecha no site dela.
Diga o preço como o que a loja mostrava agora, não como verdade fixa.

## Quando ela gostar de um

- `quero_tomar` anota o rótulo na lista de quero tomar dela.
- `curtir` marca que ela gostou, e isso afina a recomendação. Não notifica
  ninguém.

As duas precisam da conta ligada. Ofereça, não faça por conta própria.
