---
title: "Ricerca di Azure disponibile in più lingue | Documentazione Microsoft"
description: Ricerca di Azure supporta 56 lingue, grazie ai vantaggi offerti dagli analizzatori delle lingue della tecnologia Lucene e di elaborazione del linguaggio naturale di Microsoft.
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Creare un indice per i documenti in più lingue in Ricerca di Azure
> [!div class="op_single_selector"]
>
> * [Portale](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Consentire l'espressione della potenza degli analizzatori delle lingue è semplice come l'impostazione di una proprietà in un campo di ricerca nella definizione dell'indice. Questo passaggio può ora essere eseguito nel portale.

Di seguito sono riportate schermate dei pannelli del portale di Azure relativi a Ricerca di Azure che consentono agli utenti di definire uno schema di indice. Da questi pannelli, gli utenti possono creare tutti i campi e impostare la proprietà analyzer per ognuno di essi.

> [!IMPORTANT]
> È possibile impostare solo un analizzatore della lingua durante la definizione del campo, come quando si crea un nuovo indice da zero o si aggiunge un nuovo campo a un indice esistente. Assicurarsi di specificare completamente tutti gli attributi, incluso l'analizzatore, durante la creazione del campo. Non sarà possibile modificare gli attributi o cambiare il tipo di analizzatore dopo avere salvato le modifiche.
>
>

## <a name="define-a-new-field-definition"></a>Definire una nuova definizione di campo
1. Accedere al [portale di Azure](https://portal.azure.com) e aprire il pannello del servizio di ricerca.
2. Fare clic su **Aggiungi un indice** nella barra di comando del dashboard del servizio per avviare un nuovo indice oppure aprire un indice esistente per impostare un analizzatore nei nuovi campi aggiunti a un indice esistente.
3. Viene visualizzato il pannello Campi, con opzioni per la definizione dello schema dell'indice, inclusa la scheda Analizzatore usata per la scelta di un analizzatore della lingua.
4. In Campi avviare una definizione di campo fornendo un nome, scegliendo il tipo di dati e impostando gli attributi per contrassegnare il campo come disponibile per la ricerca full-text, recuperabile nei risultati della ricerca, utilizzabile nelle strutture di navigazione facet, ordinabile e così via.
5. Prima di passare al campo successivo, aprire la scheda **Analizzatore** .

![][1]
*Per selezionare un analizzatore, fare clic sulla scheda Analizzatore nel pannello Campi*

## <a name="choose-an-analyzer"></a>Scegliere un analizzatore
1. Scorrere per trovare il campo che si sta definendo.
2. Se il campo non è stato contrassegnato come ricercabile, fare clic sulla casella di controllo per contrassegnarlo come **Ricercabile**.
3. Fare clic sull'area dell'analizzatore per visualizzare l'elenco degli analizzatori disponibili.
4. Scegliere l'analizzatore che si desidera usare.

![][2]
*Selezionare uno degli analizzatori supportati per ogni campo*

Per impostazione predefinita, tutti i campi di ricerca usano l' [analizzatore Standard Lucene](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) che è indipendente dalla lingua. Per visualizzare l'elenco completo degli analizzatori supportati, vedere il post di blog relativo alle [lingue supportate in Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Dopo aver selezionato l'analizzatore della lingua per un campo, verrà usato per ogni richiesta di ricerca e indicizzazione relativa a quel campo. Quando viene eseguita una query su più campi usando analizzatori diversi, la query verrà elaborata in modo indipendente dagli analizzatori specifici di ciascun campo.

Molte applicazioni Web e per dispositivi mobili vengono sfruttate dagli utenti in tutto il mondo in diverse lingue. È possibile definire un indice per uno scenario simile al seguente mediante la creazione di un campo per ogni lingua supportata.

![][3]
*Definizione di indice con un campo di descrizione per ogni lingua supportata*

Se la lingua dell'agente che esegue una query è nota, è possibile definire per una richiesta di ricerca un ambito relativo a un campo specifico usando il parametro di query **searchFields** . La query seguente verrà generata solo per la descrizione in polacco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

È possibile eseguire query sull'indice dal portale, usando **Esplora ricerche** per incollare una query simile a quello illustrata in precedenza. Esplora ricerche è disponibile nella barra dei comandi nel pannello del servizio. Per informazioni dettagliate, vedere [Eseguire query su un indice di Ricerca di Azure](search-explorer.md) .

È possibile che la lingua dell'agente che esegue una query non sia nota, in tal caso la query può essere inviata a tutti i campi contemporaneamente. Se necessario, è possibile definire delle preferenze per i risultati in una determinata lingua usando i [profili di punteggio](https://msdn.microsoft.com/library/azure/dn798928.aspx). Nell'esempio seguente, alle corrispondenze trovate nella descrizione in inglese viene assegnato un punteggio più elevato rispetto alle corrispondenze in polacco e francese:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Per gli sviluppatori .NET, si noti che è possibile configurare analizzatori delle lingue mediante l' [SDK .NET di Ricerca di Azure](http://www.nuget.org/packages/Microsoft.Azure.Search). La versione più recente supporta anche gli analizzatori delle lingue di Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
