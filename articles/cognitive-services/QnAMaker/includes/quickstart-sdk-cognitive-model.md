---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114539"
---

## <a name="using-this-example-knowledge-base"></a>Uso della knowledge base di esempio

La knowledge base di questo argomento di avvio rapido inizia con due coppie di domande e risposte di conversazione per semplificare l'esempio e per avere ID altamente prevedibili da usare nel metodo Update, associando le richieste di completamento a domande in nuove coppie. Questa operazione è stata pianificata e implementata in un ordine specifico per questo argomento di avvio rapido.

Se si prevede di sviluppare una knowledge base nel tempo con richieste di completamento che dipendono da coppie di domande e risposte esistenti, è possibile scegliere di:
* Gestire la knowledge base in un editor di testo o in uno strumento TSV che supporti l'automazione e quindi di sostituire completamente la knowledge base con un aggiornamento (per le knowledge base di grandi dimensioni).
* Gestire le richieste di completamento interamente nel portale di QnA Maker (per le knowledge base di piccole dimensioni).

Dettagli sulle coppie di domande e risposte usate in questo argomento di avvio rapido:
* Tipi di coppie di domande e risposte: in questa knowledge base sono disponibili due tipi di coppie di domande e risposte, dopo l'aggiornamento, ovvero chitchat e informazioni specifiche del dominio. Questo scenario è tipico se la knowledge base è collegata a un'applicazione per conversazioni, ad esempio un chatbot.
* Anche se è possibile filtrare le risposte della knowledge base in base a metadati o usare le richieste di completamento, queste operazioni non vengono illustrate in questo argomento di avvio rapido. Per questi esempi di generateAnswer indipendenti dal linguaggio, vedere [qui](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).
* Il testo della risposta può contenere un'[ampia varietà di markdown](../reference-markdown-format.md), come immagini (immagini Internet disponibili pubblicamente), collegamenti (a URL disponibili pubblicamente) e punti elenco. In questo argomento di avvio rapido non viene usata questa varietà.
