---
title: Filtro per le espressioni volgari - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Usare il filtro per le espressioni volgari nell'API Traduzione testuale.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: bd7a05f2f597d1882293387e5aac8e4d7367d051
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916632"
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
> [Applicare il filtro con la chiamata all'API Microsoft Translator per le espressioni volgari](reference/v3-0-translate.md)
