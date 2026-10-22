---
name: ler-um-rotulo
description: Descobre que vinho é a partir do que está escrito na garrafa, na prateleira do mercado ou na carta do restaurante. Use quando a pessoa mandar foto de um rótulo, de uma prateleira ou de uma carta de vinhos, colar o texto de um rótulo, ou perguntar qual daqueles vale a pena.
---

# Ler um rótulo

Responda em português do Brasil. Precisa da conta do Vinellu ligada: o acervo
é público, mas essa leitura gasta a cota diária de leitura por IA da conta
dela. É a única coisa aqui que tem cota.

## Você lê a imagem, o Vinellu casa com o acervo

A foto **não** passa para o Vinellu. Quem lê a imagem é você. Transcreva o que
está escrito na garrafa, em texto cru, e mande esse texto:

- `que_vinho_e_esse` para uma garrafa.
- `lista_de_rotulos` para prateleira de mercado ou carta de restaurante, com
  vários rótulos de uma vez.

Transcreva tudo que você conseguir ler, mesmo bagunçado: nome, produtor,
safra, denominação, região, teor alcoólico, o que estiver no contra-rótulo.
Não "limpe" para o que parece certo e não corrija o nome do produtor pelo que
você acha que conhece. Nome mal lido é o que o Vinellu usa para dizer que não
casou; nome corrigido por você é o que faz ele casar com o rótulo errado.

Não descreva o vinho pela foto. Garrafa escura e rótulo bonito não dizem a
uva, e a ficha vem do acervo.

## O que fazer com a resposta

O Vinellu devolve qual rótulo do acervo casou, e pergunta quando mais de um se
parece. Quando ele perguntar, pergunte à pessoa: a safra ou a denominação
costumam resolver. Não escolha o primeiro por conta própria.

Na prateleira e na carta, a resposta vem **na ordem do gosto dela** e diz
abertamente o que não reconheceu. Mostre as duas partes. "Desses dez, o
Vinellu conhece sete, e na sua cara são esses três" é uma resposta honesta;
esconder os três que ele não conhece é fingir um acervo que não existe.

Para cada rótulo que casou, cite o endereço no site (`url`) e o atalho do
aplicativo (`deep_link`).

Depois de casar o rótulo, o que vem em seguida é a ficha
(`ficha_do_rotulo`), o preço nas lojas (`onde_comprar_para_mim`), guardar na
adega ou anotar no quero tomar. Ofereça o próximo passo que cabe na cena: na
prateleira do mercado é preço e ficha, no restaurante é qual pedir.

## Quando não der

A cota é diária e por conta. Se a resposta disser que acabou, que o serviço
está fora ou que demorou, **diga isso em uma frase em português** e ofereça o
caminho sem cota: a pessoa digita o nome do rótulo e você usa `buscar_vinho`.
Nunca mostre o erro cru da resposta e nunca tente de novo em sequência.
