---
title: Filtro volgare-Translator
titleSuffix: Azure Cognitive Services
description: Usare il filtro volgare per determinare il livello di volgarità tradotto nel testo nel traduttore di servizi cognitivi di Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7ebfe766e6362a3f62e70db8bf2dcae370aceee3
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996160"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Aggiungere filtri per la volgarità con il traduttore

In genere il servizio Translator mantiene il contenuto volgare presente nell'origine della traduzione. Il grado di volgarità e il contesto che rendono le parole volgari differiscono tra le culture. Di conseguenza, il grado di volgarità nella lingua di destinazione può essere amplificato o ridotto.

Se si vuole evitare la presenza di espressioni volgari nella traduzione, indipendentemente dalla presenza o meno di contenuto volgare nel testo di origine, è possibile usare l'opzione di filtro per le espressioni volgari disponibile nel metodo Translate(). Questa opzione consente di scegliere se eliminare il contenuto volgare, contrassegnarlo con tag appropriati oppure non eseguire alcuna operazione.

Il metodo Translate () accetta il parametro "Options", che contiene il nuovo elemento "ProfanityAction". I valori accettati di ProfanityAction sono "NoAction", "contrassegnato" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valori accettati di ProfanityAction ed esempi
|Valore ProfanityAction | Azione | Esempio: origine - giapponese | Esempio: destinazione - inglese|
| :---|:---|:---|:---|
| NoAction | Valore predefinito. Equivale a non impostare l'opzione. Il contenuto volgare passa dall'origine alla destinazione. | 彼は変態です。 | È un cretino. |
| Marked | Le parole profane sono racchiuse tra tag XML \<profanity> .. \</profanity> . | 彼は変態です。 | Si tratta di un \<profanity> coglione \</profanity> . |
| Eliminata | Le parole volgari vengono rimosse dall'output senza sostituzione. | 彼は。 | È un. |

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Applicare il filtro volgare alla chiamata di traduzione](reference/v3-0-translate.md)
