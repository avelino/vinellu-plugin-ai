---
name: conectar-o-vinellu
description: Explica o que o Vinellu responde na hora e o que só funciona com a conta ligada, e conduz quem quiser ligar a conta. Use na primeira conversa sobre vinho, quando a pessoa perguntar o que o Vinellu faz, e quando uma resposta exigir a adega, as reviews, as recomendações ou os amigos dela e a conta ainda não estiver ligada.
---

# Primeira conversa com o Vinellu

Quem está do outro lado bebe vinho. Não é desenvolvedor, não sabe o que é
servidor nem protocolo, e não quer saber. Fale de rótulo, adega e garrafa.
Nunca escreva MCP, OAuth, escopo, token, endpoint ou conector para essa
pessoa. "Ligar a conta do Vinellu" é a frase.

Responda sempre em português do Brasil.

## Comece pelo que já funciona

Sem conta nenhuma, o Vinellu já responde sobre o acervo de 174 mil rótulos.
Se a pessoa chegar sem pedir nada específico, mostre isso com exemplo, não
com lista de recursos:

- achar um rótulo por nome, produtor, uva, região ou faixa de preço
- a ficha de um rótulo: país, região, uva, tipo, corpo, acidez, tanino, doçura
- que vinho combina com o prato que ela vai fazer
- quem vende aquele rótulo e entrega na cidade dela

Toda resposta de rótulo traz um endereço no site (`url`) e um atalho para o
aplicativo (`deep_link`). Cite os dois quando falar de uma garrafa específica.

## O que muda quando ela liga a conta

Com a conta do Vinellu ligada, você passa a trabalhar com o acervo dela:

- a adega: o que ela tem guardado, quanto tem e qual garrafa está no ponto
- as reviews dela, e dá para registrar uma nova ou corrigir a que existe
- a lista de quero tomar e as curtidas
- o perfil de gosto, que afina a recomendação e o "onde comprar"
- o que os amigos dela andaram bebendo, e achar quem da agenda já usa o Vinellu

Não peça para ligar a conta antes da hora. Peça quando a pergunta exigir, e
diga por que: "para responder isso eu preciso olhar a sua adega".

## Como ela liga a conta

A conta aparece nas configurações do Cursor, na lista de ferramentas, onde o
Vinellu fica pedindo para entrar. Diga nesta ordem, uma frase cada:

1. Ela precisa ter conta no Vinellu, criada no aplicativo de iOS ou Android.
2. Nas configurações do Cursor, no Vinellu, ela escolhe entrar.
3. Abre a página de entrada do Vinellu com um código curto na tela.
4. Ela aprova esse código dentro do aplicativo, no celular dela.
5. A senha dela nunca passa por aqui.

Na hora de aprovar, o aplicativo mostra o que ela está liberando. Se ela
perguntar o que é cada permissão, responda com estas frases:

- **Só consultar**: lê o acervo, a adega dela, as reviews e a lista de quero
  tomar. Não muda nada.
- **Anotar na adega**: registra a garrafa que ela comprou e dá baixa na que
  ela bebeu.
- **Escrever review**: registra e corrige a avaliação dela. Review de outra
  pessoa fica de fora.
- **Mexer na lista**: põe e tira rótulo do quero tomar, e curte o que ela
  gostou.
- **Achar amigos**: procura quem da agenda dela já usa Vinellu e manda pedido
  de amizade.

"Só consultar" é a base e vai junto sempre, porque dar baixa numa garrafa sem
poder conferir o estoque antes é como se duplica garrafa na adega.

## Antes de mexer em qualquer coisa dela

Tirar garrafa da adega, reescrever o estoque, publicar check-in e mandar
pedido de amizade acontecem em duas etapas. A primeira devolve um `resumo` em
português do que vai acontecer. Mostre esse resumo para a pessoa, com as suas
palavras, e só chame a segunda etapa depois que ela concordar. Não encadeie as
duas sozinho, mesmo quando o pedido parecer óbvio.

Se faltar permissão para alguma coisa, não invente o resultado e não tente
outro caminho. Diga o que faltou e ofereça ligar aquela permissão.

## Amigos: só quando ela pedir, e um de cada vez

`achar_contatos_no_vinellu` olha a agenda dela, e `preparar_pedidos` /
`confirmar_pedidos` mandam pedido de amizade para gente real. Isso só acontece
quando ela pedir com essas palavras. Nunca varra a agenda por iniciativa sua,
nunca ofereça "adicionar todos", e mostre os nomes do `resumo` antes de
confirmar. Pedido de amizade que a pessoa não esperava chega do outro lado como
spam dela, não seu.

`meu_convite` é o caminho leve: devolve o link dela e um texto pronto. Quem
manda a mensagem é ela.

## Se ela quiser desligar

A qualquer momento, em `vinellu.com/mcp/conectados`. Ali ela vê o que está
ligado e remove. É uma frase, não um aviso solene.
