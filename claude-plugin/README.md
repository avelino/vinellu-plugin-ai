# Vinellu

O Vinellu é a rede social do vinho. Este plugin traz para dentro do Claude o
acervo de 174 mil rótulos, com ficha completa de cada um, e a adega, as
reviews e as recomendações de quem liga a conta.

## O que dá para perguntar

Sem ligar conta nenhuma, você já pode:

- achar um rótulo por nome, produtor, uva, região ou faixa de preço
- ver a ficha: país, região, uva, tipo, corpo, acidez, tanino, doçura
- pedir o vinho que combina com o prato que você vai fazer
- descobrir quem vende aquele rótulo e entrega na sua cidade

Ligando a sua conta do Vinellu, o Claude passa a trabalhar com o seu acervo:
a adega e qual garrafa está no ponto de beber, as suas reviews, a lista de
quero tomar, as curtidas, o perfil de gosto que afina a recomendação, o
check-in do que você está bebendo e os seus amigos.

Exemplos que funcionam sem conta:

> Me acha um Malbec argentino até 150 reais.

> Que uva é a base do Chianti, e que rótulo vale provar?

> Quem vende esse rótulo e entrega em Minas?

E com a conta ligada:

> Que vinho da minha adega combina com cordeiro?

> Qual garrafa minha está no ponto de beber?

> Anota esse aí que eu quero provar.

## Como instalar

Na claude.ai e no Cowork, pelo diretório de plugins. No Claude Code:

```
/plugin install vinellu --marketplace avelino/vinellu-plugin-ai
```

## Como ligar a conta

A conta aparece na aba de conectores deste plugin. Você precisa ter conta no
Vinellu, criada no aplicativo de iOS ou Android. Ao entrar, abre a página do
Vinellu com um código curto na tela, e você aprova esse código dentro do
aplicativo, no seu celular. A sua senha nunca passa pelo Claude.

Na hora de aprovar, o aplicativo mostra o que você está liberando, em cinco
permissões separadas: só consultar, anotar na adega, escrever review, mexer na
sua lista e achar amigos. Você aprova uma a uma.

Dar baixa numa garrafa, corrigir o estoque, publicar check-in e mandar pedido
de amizade acontecem em duas etapas: o Claude mostra em português o que vai
fazer e só executa depois que você concorda.

Para desligar, a qualquer momento: `vinellu.com/mcp/conectados`.

## Que dados saem daqui

As suas perguntas sobre vinho chegam ao servidor do Vinellu em
`vinellu.com/mcp`, que é o único endereço que este plugin usa. Com a conta
ligada, o que sai é o que a tool da vez precisa: o rótulo que você citou, a
nota e o texto da review que você ditou, a quantidade que entra ou sai da
adega. Com a conta desligada, só o texto da busca.

Na busca de contatos, o aplicativo manda uma versão embaralhada do contato,
nunca o e-mail. O plugin em si não guarda nada: ele é um conjunto de
instruções em texto mais o endereço do servidor.

Privacidade: `vinellu.com/privacidade`. Termos: `vinellu.com/termos`.
Suporte: `vinellu.com/suporte`.
