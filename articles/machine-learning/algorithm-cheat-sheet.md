---
title: Roteiro de Machine Learning de algoritmos
titleSuffix: Azure Machine Learning
description: Uma folha de consulta de algoritmo de Machine Learning imprimível ajuda a escolher o algoritmo certo para seu modelo de previsão no designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 11/04/2019
ms.openlocfilehash: b43f2f351345f05c3eb56a84fb1a0eadb4826707
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771505"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Roteiro de Machine Learning de algoritmo para Azure Machine Learning designer

A **Folha de Referências de Algoritmo do Azure Machine Learning** ajuda a escolher o algoritmo certo para o modelo de análise preditiva.

Azure Machine Learning tem uma grande biblioteca de algoritmos das famílias ***classificação***, ***sistemas recomendados***, ***clustering***, ***detecção de anomalias***, ***regressão*** e ***análise de texto*** . Cada um foi projetado para atender a um tipo diferente de problema de aprendizado de máquina.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: roteiro de Machine Learning de algoritmos

**Baixe a folha de consulta daqui: [Folha de consulta de algoritmos do Machine Learning (11 x 17 pol.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Roteiro de Machine Learning de algoritmos: saiba como escolher um algoritmo de Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Baixar e imprimir a folha de consulta do algoritmo do Machine Learning em tamanho tabloide para mantê-lo à mão e obter ajuda para escolher um algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Como usar a folha de consulta do algoritmo de Machine Learning

As sugestões oferecidas nessa página de dicas úteis de algoritmo são aproximadas às regras de bolso. Algumas podem ser ajustadas e algumas podem ser flagrantemente violadas. Isso serve para sugerir um ponto de partida. Não tenha medo de comparar vários algoritmos em seus dados. Simplesmente não há nenhum substituto para entender os princípios de cada algoritmo e o sistema que gerou os dados.

Cada algoritmo de aprendizado de máquina tem seu próprio estilo ou tendência indutivo. Para um problema específico, vários algoritmos podem ser apropriados e um algoritmo pode ser um melhor ajuste do que outros. Mas nem sempre é possível saber com antecedência qual é a melhor opção. Em casos como esse, vários algoritmos estão listados juntos na folha de consulta. Uma estratégia apropriada seria um algoritmo e se os resultados ainda não estiverem satisfatórios, tentar os outros. 

Para saber mais sobre os algoritmos no Azure Machine Learning, acesse o [algoritmo e a referência do módulo](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Tipos de aprendizado de máquina

Há três categorias principais de aprendizado de máquina: *aprendizado supervisionado*, *aprendizado sem supervisão* e *aprendizado de reforço*.

### <a name="supervised-learning"></a>Aprendizado supervisionado

No aprendizado supervisionado, cada ponto de dados é rotulado ou associado a uma categoria ou valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como, por exemplo, um gato ou um cão. Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. O objetivo do aprendizado supervisionado é estudar vários exemplos rotulados como esses e, em seguida, conseguir fazer previsões sobre os pontos de dados futuros. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços de vendas precisos a outros carros usados. Este é um tipo popular e útil de aprendizado de máquina.

### <a name="unsupervised-learning"></a>Aprendizado não supervisionado

No aprendizado não supervisionado, os pontos de dados não têm rótulos associados a eles. Em vez disso, a meta de um algoritmo de aprendizado sem supervisão é organizar os dados de alguma forma ou descrever sua estrutura. Isso pode significar agrupá-los em clusters, como faz o K-means, ou encontrar diferentes maneiras de consultar dados complexos para que eles pareçam mais simples.

### <a name="reinforcement-learning"></a>Aprendizado de reforço

No aprendizado de reforço, o algoritmo escolhe uma ação em resposta a cada ponto de dados. É uma abordagem comum em robótica, em que o conjunto de leituras do sensor, em um ponto no tempo, é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é um ajuste natural para aplicativos da Internet das Coisas. O algoritmo de aprendizado também recebe um sinal de recompensa pouco tempo depois, indicando se a decisão foi boa. Com base nisso, o algoritmo modifica sua estratégia para alcançar a recompensa mais alta. 

## <a name="next-steps"></a>Próximos passos

* [Saiba mais sobre o estúdio em Azure Machine Learning e o portal do Azure](overview-what-is-azure-ml.md).

* Consulte uma lista de algoritmos e módulos no [algoritmo e referência de módulo](algorithm-module-reference/module-reference.md).

* [Tutorial: criar um modelo de previsão no designer de Azure Machine Learning](tutorial-designer-automobile-price-train-score.md).

* [Saiba mais sobre aprendizado profundo versus aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).
