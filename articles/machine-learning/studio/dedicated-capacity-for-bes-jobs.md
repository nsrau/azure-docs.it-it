---
title: "Capacità dedicata per i processi del servizio di esecuzione Batch di Machine Learning | Documentazione Microsoft"
description: Panoramica dei servizi di Azure Batch per i processi di Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl
ms.openlocfilehash: 4a4c5e6bf44fb4774d9ba501479383d6c7d3b128
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Servizio Azure Batch per i processi di Machine Learning

L'elaborazione di pool di Batch in Machine Learning fornisce la scalabilità gestita dal cliente per il servizio di esecuzione Batch di Azure Machine Learning. L'elaborazione batch classica per l'apprendimento automatico avviene in un ambiente multi-tenant, limitando il numero di processi simultanei che è possibile inviare, e i processi vengono accodati in base alla modalità First-In-First-Out. Non è quindi possibile prevedere con precisione quando verrà eseguito il processo.

L'elaborazione di pool di Batch consente di creare pool a cui è possibile inviare i processi batch. Vengono controllati le dimensioni del pool e il pool specifico a cui viene inviato il processo. Il processo BES viene eseguito nel proprio spazio di elaborazione, assicurando prestazioni di elaborazione prevedibili e la possibilità di creare pool di risorse corrispondenti al carico di elaborazione inviato.

## <a name="how-to-use-batch-pool-processing"></a>Procedura: Usare l'elaborazione di pool Batch

La configurazione dell'elaborazione di pool di Batch non è attualmente disponibile tramite il portale di Azure. Per usare l'elaborazione di pool di Batch, è necessario:

-   Chiamare CSS per creare un account di pool di Batch e ottenere un URL del servizio pool e una chiave di autorizzazione
-   Creare un nuovo servizio Web e un nuovo piano di fatturazione basati su Resource Manager

Per creare il proprio account, contattare il Supporto tecnico Microsoft e fornire l'ID di sottoscrizione. Il personale è a disposizione per aiutare a determinare la capacità necessaria per lo scenario in uso. Configurerà quindi l'account con il numero massimo di pool che è possibile creare e il numero massimo di macchine virtuali (VM) che è possibile inserire in ogni pool. Al termine della configurazione dell'account viene fornito l'URL del servizio pool e una chiave di autorizzazione.

Dopo avere creato l'account, usare l'URL del servizio pool e la chiave di autorizzazione per eseguire le operazioni di gestione del pool sul pool Batch.

![Architettura del servizio pool Batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Creare i pool chiamando l'operazione di creazione dei pool per l'URL del servizio pool fornito dal Supporto tecnico. Quando si crea un pool, specificare il numero di VM e l'URL del file swagger.json di un nuovo servizio Web di Machine Learning basato su Resource Manager. Questo servizio Web viene fornito per definire l'associazione al piano di fatturazione. ll servizio pool Batch usa il file swagger.json per associare il pool a un piano di fatturazione. È possibile eseguire qualsiasi servizio Web BES scelto per il pool: quello nuovo basato su Resource Manager o quello classico.

È possibile usare qualsiasi nuovo servizio Web basato su Resource Manager, ma tenere presente che la fatturazione per i processi viene addebitata in base al piano di fatturazione associato al servizio scelto. È consigliabile creare un servizio Web e un nuovo piano di fatturazione specifici per l'esecuzione dei processi di pool Batch.

Per altre informazioni sulla creazione di servizi Web, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).

Dopo avere creato un pool, si invia un processo BES usando l'URL delle richieste Batch per il servizio Web. È possibile scegliere di inviarlo a un pool o usare l'elaborazione batch classica. Per inviare un processo a un pool Batch, aggiungere al corpo della richiesta di invio del processo il parametro seguente:

"AzureBatchPoolId":"&lt;ID pool&gt;"

Se non si aggiunge il parametro, il processo viene eseguito nell'ambiente di elaborazione classico. Se sono disponibili risorse per il pool, il processo viene avviato immediatamente. Se non sono disponibili risorse per il pool, il processo viene accodato fino a quando non diventa disponibile una risorsa.

Se è necessario aumentare la capacità perché si raggiunge spesso la capacità massima dei pool, contattare il Supporto tecnico Microsoft e concordare un aumento delle quote.

Richiesta di esempio:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Considerazioni sull'elaborazione di pool Batch

L'elaborazione di pool Batch è un servizio fatturabile sempre attivo che deve essere associato a un piano di fatturazione basato su Resource Manager. Verrà addebitato solo il numero di ore di calcolo in esecuzione nel pool, indipendentemente dal numero di processi eseguiti in questo periodo. Se si crea un pool, vengono addebitate le ore di calcolo di ogni macchina virtuale in essa contenuto fino all'eliminazione del pool, anche se non sono in esecuzione processi batch. La fatturazione per le macchine virtuali inizia con il completamento del provisioning e termina con l'eliminazione del batch. È possibile usare uno dei piani disponibili nella [pagina relativa ai prezzi di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Esempio di fatturazione:

Se si crea un pool Batch con 2 macchine virtuali e lo si elimina dopo 24 ore, al piano di fatturazione verranno addebitate 48 ore di calcolo, indipendentemente dal numero di processi eseguiti in questo periodo.

Se si crea un pool Batch con 4 macchine virtuali e lo si elimina dopo 12 ore, al piano di fatturazione verranno addebitate 48 ore di calcolo.

È consigliabile eseguire il polling dello stato del processo per determinare quando i processi sono stati completati. Al termine dell'esecuzione di tutti i processi, chiamare l'operazione di ridimensionamento del pool per impostare il numero di macchine virtuali del pool a zero. Se le risorse nel pool non sono sufficienti ed è necessario creare un nuovo pool, ad esempio per la fatturazione in base a un piano di fatturazione diverso, è possibile invece eliminare il pool al termine dell'elaborazione di tutti i processi.


| **Usare l'elaborazione di pool Batch quando**    | **Usare l'elaborazione di batch classica quando**  |
|---|---|
|È necessario eseguire un numero elevato di processi<br>Oppure<br/>È necessario che i processi vengano eseguiti immediatamente<br/>Oppure<br/>È necessario garantire la velocità effettiva. Ad esempio, è necessario eseguire alcuni processi in un determinato intervallo temporale e aumentare le risorse di calcolo in base alle esigenze.    | Si sta eseguendo un numero limitato di processi<br/>e<br/> non è necessario che i processi vengano eseguiti immediatamente |
