---
title: Trasformazione Espandi JSON con Azure Machine Learning Workbench
description: Documento di riferimento per la trasformazione "Espandi JSON"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 57f461f9a39e8069654985d86bfe3ae23dd0855a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="expand-json-transformation"></a>Trasformazione Espandi JSON
La trasformazione **Espandi JSON** consente agli utenti di espandere una colonna esistente che contiene il testo JSON valido in più colonne.

## <a name="how-to-perform-this-transformation"></a>Come eseguire questa trasformazione

Per eseguire questa trasformazione, adottare la procedura seguente:
1. Selezionare la colonna di origine che contiene il testo JSON.
2. Scegliere **Expand JSON** ("Espandi JSON) dal menu **Trasformazioni**. In alternativa fare clic con il pulsante destro del mouse sull'intestazione della colonna di origine e scegliere **Expand JSON** (Espandi JSON) dal menu di scelta rapida. 
3. Fare clic su **OK**. 
 
Accanto alla colonna di origine vengono aggiunte le nuove colonne. Queste colonne contengono le proprietà del livello successivo della gerarchia nel testo JSON. La proprietà Key, se presente, viene usata per creare il nome della colonna corrispondente. Le proprietà annidate vengono mantenute come testo JSON che l'utente può espandere o eliminare in modo iterativo in base alle esigenze.

## <a name="examples"></a>esempi

La colonna di origine *Cliente* si espande in due colonne *Customer.Name* e *Customer.Phone*.

| Customer                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Nome": "Maria Dodson", "Telefono": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Nome": "Leonard Robledo", "Telefono": "123 456-7890"} | Leonard Robledo | 456-7890-123   |

