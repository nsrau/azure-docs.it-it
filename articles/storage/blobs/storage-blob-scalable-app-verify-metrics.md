---
title: "Verificare le metriche di velocità effettiva e latenza per un account di archiviazione nel portale di Azure | Documenti Microsoft"
description: "Informazioni su come verificare le metriche di velocità effettiva e latenza per un account di archiviazione nel portale."
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: b3102bd4e40e10fe88c12295794da37e359c56f1
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Verificare le metriche di velocità effettiva e latenza per un account di archiviazione

Questa esercitazione è la quarta e ultima parte di una serie di esercitazioni. Nelle esercitazioni precedenti, è stato descritto come caricare e scaricare larges quantità di dati casuali a un account di archiviazione di Azure. Questa esercitazione viene illustrato come è possibile utilizzare le metriche per visualizzare la velocità effettiva e latenza nel portale di Azure.

Nella quarta parte della serie si apprenderà come:

> [!div class="checklist"]
> * Configurare i grafici nel portale di Azure
> * Verificare metriche di velocità effettiva e latenza

[Metriche di archiviazione Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) utilizza monitoraggio di Azure per offrire una vista unificata nelle prestazioni e la disponibilità dell'account di archiviazione.

## <a name="configure-metrics"></a>Configurare le metriche

Passare a **metriche (anteprima)** in **impostazioni** nell'account di archiviazione.

Scegliere i Blob dal **servizio SUB** elenco a discesa.

In **METRICA**, selezionare una delle metriche trovate nella tabella seguente:

Le metriche seguenti offrono un'idea della latenza e velocità effettiva dell'applicazione. Le metriche configurate nel portale sono in 1 minuto medie. Se la transazione è terminata all'interno di un minuto dati minuti sono halfed per la Media. Nell'applicazione, le operazioni di caricamento e download siano state replicate e fornito è l'output della quantità di tempo effettiva richiesto per caricare e scaricare i file. Queste informazioni è utilizzabile in combinazione con le metriche del portale per comprendere appieno la velocità effettiva.

|Metrica|Definizione|
|---|---|
|**Latenza E2E esito positivo**|Latenza end-to-end media di richieste con esito positivo effettuate in un servizio di archiviazione o nell'operazione API specificata. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|
|**Latenza Server esito positivo**|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency. |
|**Transazioni**|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Nell'esempio, la dimensione del blocco è stata impostata su 100 MB. In questo caso, ogni blocco di 100 MB viene considerato una transazione.|
|**In ingresso**|Quantità di dati in ingresso. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. |
|**In uscita**|Quantità di dati in uscita. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. |

Selezionare **ultime 24 ore (automatico)** accanto a **tempo**. Scegliere **ultima ora** e **minuto** per **granularità temporale**, quindi fare clic su **applica**.

![Metriche di account di archiviazione](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

I grafici possono disporre di più di una metrica assegnata, ma l'assegnazione di più di una metrica disabilita la possibilità di gruppo in base alle dimensioni.

## <a name="dimensions"></a>Dimensioni

[Dimensioni](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) consentono di esaminare più approfondita i grafici e ottenere informazioni più dettagliate. Metriche hanno dimensioni diverse. È una dimensione che è disponibile il **nome API** dimensione. Questa dimensione interrompe il piano in ogni chiamata API distinta. La prima immagine riportata di seguito viene illustrato un grafico delle transazioni totali per un account di archiviazione. La seconda immagine mostra lo stesso grafico, ma con l'API nome dimensione selezionata. Come si può notare, viene elencato ogni transazione fornendo ulteriori dettagli in quanti sono state effettuate in base al nome di API.

![Metriche di account di archiviazione - transazioni senza una dimensione](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Metriche di account di archiviazione - transazioni](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale e fare clic su Elimina.

## <a name="next-steps"></a>Passaggi successivi

Parte 4 della serie, illustrando la visualizzazione delle metriche per la soluzione di esempio, ad esempio come:

> [!div class="checklist"]
> * Configurare i grafici nel portale di Azure
> * Verificare metriche di velocità effettiva e latenza

Fare clic sul collegamento per visualizzare esempi di archiviazione predefiniti.

> [!div class="nextstepaction"]
> [Esempi di script di archiviazione di Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md