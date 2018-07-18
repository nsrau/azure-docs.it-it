---
title: Filtro per il contenuto volgare con l'API Traduzione testuale Microsoft | Microsoft Docs
description: Usare il filtro per il contenuto volgare nell'API Traduzione testuale Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374348"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Come aggiungere il filtro per il contenuto volgare nell'API Traduzione testuale Microsoft

In genere il servizio Translator mantiene il contenuto volgare presente nell'origine della traduzione. Il grado di volgarità e il contesto che rendono le parole volgari differiscono tra le culture. Di conseguenza il grado di volgarità nella lingua di destinazione può essere amplificato o ridotto.

Se si vuole evitare la presenza di contenuto volgare nella traduzione (indipendentemente dalla presenza di contenuto volgare nel testo di origine), è possibile usare l'opzione di filtro per il contenuto volgare nel metodo Translate(). L'opzione consente di scegliere se si intende cancellare il contenuto volgare eliminato o contrassegnarlo con tag appropriati o non eseguire alcuna operazione.

Il metodo Translate() usa un parametro "options", che contiene il nuovo elemento "ProfanityAction". I valori consentiti di ProfanityAction sono "NoAction", "Marked" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valori accettati di ProfanityAction ed esempi
|Valore ProfanityAction | Azione | Esempio: origine - giapponese | Esempio: destinazione - inglese|
| :---|:---|:---|:---|
| NoAction | Default. Equivale a non impostare l'opzione. Il contenuto volgare passa dall'origine alla destinazione. | 彼は変態です。 | È un cretino. |
| Marked | Le parole volgari sono racchiuse tra XML \<profanity> … \</profanity>. | 彼は変態です。 | È un \<profanity>cretino\</profanity >. |
| Deleted | Le parole volgari vengono rimosse dall'output senza sostituzione. | 彼は。 | È un. |

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Applicare il filtro per il contenuto volgare alla chiamata API di Traduzione testuale](reference/v3-0-translate.md)

