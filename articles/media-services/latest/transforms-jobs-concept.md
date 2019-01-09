---
title: Trasformazioni e processi in Servizi multimediali di Azure | Microsoft Docs
description: Quando si usa Servizi multimediali, è necessario creare un oggetto Transform per descrivere le regole o le specifiche per l'elaborazione dei video. Questo articolo offre una panoramica dell'oggetto Transform e del relativo utilizzo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742780"
---
# <a name="transforms-and-jobs"></a>Trasformazioni e processi
 
Usare [Trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. 

Un [processo](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare la **trasformazione** a determinati contenuti audio o video di input. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare il percorso del video di input mediante: URL HTTP, URL SAS oppure [asset di Servizi multimediali di Microsoft Azure](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Flusso di lavoro tipico

1. Creare un oggetto Transform 
2. Inviare i processi a tale oggetto Transform 
3. Elencare gli oggetti Transform 
4. Eliminare una trasformazione se non si intende usarla in futuro. 

Si supponga di voler estrarre il primo frame di tutti i video come immagine di anteprima, i passaggi da eseguire sono: 

1. Definire il recipe o la regola per l'elaborazione dei video, ad esempio "usare il primo fotogramma del video come anteprima". 
2. Per ogni video è possibile indicare al servizio: 
    1. dove trovare il video,  
    2. dove scrivere l'immagine di anteprima dell'output. 

Una **trasformazione** consente di creare il file recipe una sola volta (passaggio 1) e inviare i processi che usano quel recipe (passaggio 2).

## <a name="transforms"></a>Trasformazioni

È possibile creare trasformazioni nell'account di Servizi multimediali usando direttamente l'API v3 o uno degli SDK pubblicati. L'API di Servizi multimediali v3 è determinata da Azure Resource Manager, quindi è possibile usare modelli di Resource Manager per creare e distribuire trasformazioni nell'account di Servizi multimediali. Si può usare il controllo degli accessi in base al ruolo per bloccare l'accesso alle trasformazioni.

### <a name="transform-definition"></a>Definizione di Transform

Nella tabella seguente vengono illustrate le proprietà delle trasformazioni e le relative definizioni.

|NOME|DESCRIZIONE|
|---|---|
|ID|ID di risorsa completo per la risorsa.|
|name|Nome della risorsa.|
|properties.created |Data e ora UTC in cui l'oggetto Transform è stato creato, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.description |Descrizione dettagliata facoltativa dell'oggetto Transform.|
|properties.lastModified |Data e ora UTC in cui l'oggetto Transform è stato aggiornato, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.outputs |Matrice di una o più proprietà TransformOutputs che l'oggetto Transform deve generare.|
|type|Tipo di risorsa.|

Per la definizione completa, vedere [Transforms](https://docs.microsoft.com/rest/api/media/transforms) (Oggetti Transform).

Come descritto in precedenza, una trasformazione consente di creare un file recipe o una regola per l'elaborazione dei video. Una singola trasformazione può applicare più di una regola. Ad esempio, una trasformazione può specificare che ogni video venga codificato in un file MP4 a un dato bitrate e che venga generata un'immagine di anteprima dal primo fotogramma del video. È possibile aggiungere una voce TransformOutput per ogni regola che si prevede di includere nella trasformazione.

> [!NOTE]
> Benché la definizione delle trasformazioni supporti un'operazione di aggiornamento, è necessario prestare attenzione e assicurarsi che tutti i processi in corso vengano completati prima di apportare una modifica. In genere si aggiorna una trasformazione già esistente per modificare la descrizione o le priorità dell'oggetto TransformOutputs sottostante. Per riscrivere il file recipe, è opportuno creare una nuova trasformazione.

## <a name="jobs"></a>Processi

Dopo aver creato una trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. È possibile ottenere l'avanzamento e lo stato dei processi monitorando gli eventi con Griglia di eventi. Per altre informazioni, vedere [Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e l'interfaccia della riga di comando di Azure](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Definizione dei processi

Nella tabella seguente vengono illustrate le proprietà dei processi e le relative definizioni.

|NOME|DESCRIZIONE|
|---|---|
|id|ID di risorsa completo per la risorsa.|
|name   |Nome della risorsa.|
|properties.correlationData |Dati di correlazione specificati dal cliente (non modificabili) e restituiti come parte delle notifiche di modifica dello stato di processi e output dei processi. Per altre informazioni, vedere [Schemi degli eventi](media-services-event-schemas.md).<br/><br/>Questa proprietà può essere usata anche per l'utilizzo multi-tenant di Servizi multimediali. È possibile inserire ID tenant nei processi. |
|properties.created |Data e ora UTC in cui è stato creato il processo, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.description |Descrizione facoltativa del processo aggiunta dal cliente.|
|properties.input|Gli input per il processo.|
|properties.lastModified    |Data e ora UTC in cui è stato aggiornato il processo, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.outputs|Output di Job.|
|properties.priority    |Priorità con cui deve essere elaborato l'oggetto Job. Gli oggetti Job con priorità più alta vengono elaborati prima di quelli con priorità inferiore. Se questa proprietà non è impostata, il valore predefinito è quello normale.|
|properties.state   |Stato corrente dell'oggetto Job.|
|type   |Tipo di risorsa.|

Per la definizione completa, vedere [Jobs](https://docs.microsoft.com/rest/api/media/jobs) (Oggetti Job).

> [!NOTE]
> Benché la definizione dei processi supporti un'operazione di aggiornamento, le uniche proprietà che possono essere modificate dopo l'invio del processo sono la descrizione e la priorità. Inoltre, una modifica alla priorità ha effetto solo se il processo è ancora in uno stato di accodamento. Se il processo ha iniziato l'elaborazione o è terminato, la modifica della priorità non ha alcun effetto.

### <a name="pagination"></a>Paginazione

La paginazione dei processi è supportata in Servizi multimediali v3.

> [!TIP]
> Usare sempre il collegamento seguente per enumerare la raccolta e non dipendere da una determinata dimensione di pagina.

Se la risposta di una query contiene molti elementi, il servizio restituisce una proprietà "\@odata.nextLink" per ottenere la pagina di risultati successiva. Questa proprietà può essere usata per scorrere l'intero set di risultati. Non è possibile configurare la dimensione della pagina. 

Se i processi vengono creati o eliminati durante il paging della raccolta, le modifiche vengono riflesse nei risultati restituiti (se tali modifiche si trovano nella parte della raccolta che non è stata scaricata). 

Il seguente esempio in C# illustra come enumerare i processi nell'account.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Per esempi di REST, vedere [Jobs - List](https://docs.microsoft.com/rest/api/media/jobs/list) (Processi - Elenco)


## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di file video](stream-files-dotnet-quickstart.md)
