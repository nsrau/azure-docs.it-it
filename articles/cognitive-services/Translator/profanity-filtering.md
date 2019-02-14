---
title: Filtro per le espressioni volgari - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Usare il filtro per le espressioni volgari nell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 82bb77d261c428171fec1f54fbbc42559fe5c637
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884835"
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
