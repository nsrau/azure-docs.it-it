---
title: Trasformazione Espandi JSON con Azure Machine Learning Workbench
description: Documento di riferimento per la trasformazione "Espandi JSON"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="expand-json-transformation"></a>Trasformazione Espandi JSON
La trasformazione **Espandi JSON** consente agli utenti di espandere una colonna esistente che contiene il testo JSON valido in più colonne.

## <a name="how-to-perform-this-transformation"></a>Come eseguire questa trasformazione

Per eseguire questa trasformazione, adottare la procedura seguente:
1. Selezionare la colonna di origine che contiene il testo JSON.
2. Scegliere **Expand JSON** ("Espandi JSON) dal menu **Trasformazioni**. In alternativa fare clic con il pulsante destro del mouse sull'intestazione della colonna di origine e scegliere **Expand JSON** (Espandi JSON) dal menu di scelta rapida. 
3. Fare clic su **OK**. 
 
Accanto alla colonna di origine vengono aggiunte le nuove colonne. Queste colonne contengono le proprietà del livello successivo della gerarchia nel testo JSON. La proprietà Key, se presente, viene usata per creare il nome della colonna corrispondente. Le proprietà annidate vengono mantenute come testo JSON che l'utente può espandere o eliminare in modo iterativo in base alle esigenze.

## <a name="examples"></a>Esempi

La colonna di origine *Cliente* si espande in due colonne *Customer.Name* e *Customer.Phone*.

| Customer                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Nome": "Maria Dodson", "Telefono": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Nome": "Leonard Robledo", "Telefono": "123 456-7890"} | Leonard Robledo | 456-7890-123   |

