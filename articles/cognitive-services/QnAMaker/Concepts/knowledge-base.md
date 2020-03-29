---
title: Importazione da origini dati - QnA MakerImporting from data sources - QnA Maker
description: Una Knowledge Base di QnA Maker è costituita da un set di domande e risposte (QnA) e metadati facoltativi associati a ogni coppia QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7772b2c76dfb92ced4d2c0d4b8dae97a59d1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457822"
---
# <a name="importing-from-data-sources"></a>Importazione da origini dati

Una Knowledge Base è costituita da set di domande e risposte inseriti da URL e file pubblici.

## <a name="data-source-locations"></a>Percorsi di origine dati

Il contenuto viene inserito in una Knowledge Base da un'origine dati. I percorsi delle origini dati sono **URL o file pubblici,** che non richiedono l'autenticazione.

I file di [SharePoint,](../how-to/add-sharepoint-datasources.md)protetti con autenticazione, sono l'eccezione. Le risorse di SharePoint devono essere file, non pagine Web. Se l'URL termina con un'estensione Web, ad esempio . ASPX, non verrà importato in QnA Maker da SharePoint.

## <a name="chit-chat-content"></a>Contenuto della chat Chit

Il set di contenuti Chit chat QnA è offerto come origine dati di contenuto completa in diverse lingue e stili di conversazione. Può trattarsi di un punto di partenza per la personalità del bot e consentirà di risparmiare tempi e costi di scrittura da zero. [Scopri come aggiungere](../how-to/chit-chat-knowledge-base.md) questo set di contenuti alla tua Knowledge Base.

## <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione

L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione `.tsv` richiede un file strutturato contenente domande e risposte. Queste informazioni consentono a QnA Maker di raggruppare i set di risposte domande e di attribuirli a una determinata origine dati.

| Domanda  | Risposta  | Source (Sorgente)| Metadati (1 chiave: 1 valore) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato multiturno strutturato tramite importazione

È possibile creare conversazioni a `.tsv` più turni in un formato di file. Il formato ti offre la possibilità di creare le conversazioni a più turni analizzando i log di `.tsv` chat precedenti (con altri processi, non utilizzando QnA Maker), quindi creare il file tramite l'automazione. Importare il file per sostituire la Knowledge Base esistente.

> [!div class="mx-imgBorder"]
> ![Modello concettuale di 3 livelli di domanda multi-turn](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

La colonna per un'opzione a più turni, `.tsv`specifica per più **turni,** è Prompts . Un `.tsv`esempio, illustrato in Excel, mostra le informazioni da includere per definire gli elementi figlio a più turni:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

Il **displayOrder** è numerico e **displayText** è il testo che non deve includere markdown.

> [!div class="mx-imgBorder"]
> ![Esempio di domande a più turni come mostrato in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Esporta come esempio

Se non si è certi di come rappresentare il set QnA nel `.tsv` file:
* Usare questo [esempio scaricabile da GitHubUse this downloadable example from GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* In alternativa, creare il set nel portale QnA Maker, salvare, quindi esportare la Knowledge Base per un esempio di come rappresentare il set.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita di sviluppo di una knowledge](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Vedere anche

Utilizzare il riferimento QnA Maker [Markdown](../reference-markdown-format.md) per formattare le risposte.

[Panoramica di QnA Maker](../Overview/overview.md)

Creare e modificare una Knowledge Base con:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Genera una risposta con:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
