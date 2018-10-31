---
title: Filtro per le espressioni volgari - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Usare il filtro per le espressioni volgari nell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 4154950cf8d8b6ec2e47a9f8100cb7983ac127bf
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648038"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Aggiungere il filtro per le espressioni volgari nell'API Traduzione testuale

In genere il servizio Translator mantiene il contenuto volgare presente nell'origine della traduzione. Il grado di volgarità e il contesto che rendono le parole volgari differiscono tra le culture. Di conseguenza, il grado di volgarità nella lingua di destinazione può essere amplificato o ridotto.

Se si vuole evitare la presenza di espressioni volgari nella traduzione, indipendentemente dalla presenza o meno di contenuto volgare nel testo di origine, è possibile usare l'opzione di filtro per le espressioni volgari disponibile nel metodo Translate(). Questa opzione consente di scegliere se eliminare il contenuto volgare, contrassegnarlo con tag appropriati oppure non eseguire alcuna operazione.

Il metodo Translate() accetta il parametro "options", che contiene il nuovo elemento "ProfanityAction". I valori accettati di ProfanityAction sono "NoAction", "Marked" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valori accettati di ProfanityAction ed esempi
|Valore ProfanityAction | Azione | Esempio: origine - giapponese | Esempio: destinazione - inglese|
| :---|:---|:---|:---|
| NoAction | Default. Equivale a non impostare l'opzione. Il contenuto volgare passa dall'origine alla destinazione. | 彼は変態です。 | È un cretino. |
| Marked | Le parole volgari sono racchiuse tra i tag XML \<profanity> ... \</profanity>. | 彼は変態です。 | È un \<profanity>cretino\</profanity >. |
| Deleted | Le parole volgari vengono rimosse dall'output senza sostituzione. | 彼は。 | È un. |

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Applicare il filtro per il contenuto volgare alla chiamata API di Traduzione testuale](reference/v3-0-translate.md)
