---
title: Verificare le metriche di velocità effettiva e latenza per un account di archiviazione nel portale di Azure | Microsoft Docs
description: Informazioni su come verificare le metriche di velocità effettiva e latenza per un account di archiviazione nel portale.
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: e498e44fcda6877aa69ec763e46e7ae7879e5aa9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Verificare le metriche di velocità effettiva e latenza per un account di archiviazione

Questa esercitazione è la quarta e ultima parte di una serie di esercitazioni. Nelle esercitazioni precedenti si è appreso come caricare e scaricare grandi quantità di dati casuali in un account di archiviazione di Azure. Questa esercitazione illustra come è possibile usare le metriche per visualizzare la velocità effettiva e la latenza nel portale di Azure.

Nella quarta parte della serie si apprenderà come:

> [!div class="checklist"]
> * Configurare grafici nel portale di Azure
> * Verificare metriche di velocità effettiva e latenza

Le [metriche di Archiviazione di Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) usano Monitoraggio di Azure per offrire una visualizzazione unificata delle prestazioni e della disponibilità dell'account di archiviazione.

## <a name="configure-metrics"></a>Configurare le metriche

Nell'account di archiviazione passare a **Metriche (anteprima)** in **IMPOSTAZIONI**.

Scegliere BLOB nell'elenco a discesa **SUB SERVICE** (SOTTOSERVIZIO).

In **METRICA** selezionare una delle metriche riportate nella tabella seguente.

Le metriche elencate di seguito consentono di conoscere la latenza e la velocità effettiva dell'applicazione. Le metriche configurate nel portale sono espresse in medie di 1 minuto. Se una transazione è stata completata a metà di un minuto, i dati di tale minuto sono dimezzati per la media. Nell'applicazione è stata calcolata la durata delle operazioni di caricamento e download ottenendo come output l'effettiva quantità di tempo necessaria per caricare e scaricare i file. Queste informazioni possono essere usate insieme alle metriche del portale per una conoscenza completa della velocità effettiva.

|Metrica|Definizione|
|---|---|
|**Latenza end-to-end richieste riuscite**|Latenza end-to-end media di richieste con esito positivo effettuate in un servizio di archiviazione o nell'operazione API specificata. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|
|**Latenza server operazioni riuscite**|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency. |
|**Transazioni**|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Nell'esempio, la dimensione dei blocchi è stata impostata su 100 MB. In questo caso, ogni blocco di 100 MB viene considerato una transazione.|
|**Dati in ingresso**|Quantità di dati in ingresso. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. |
|**Dati in uscita**|Quantità di dati in uscita. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. |

Selezionare **Last 24 hours (Automatic)** (Ultime 24 ore - Automatico) accanto a **Tempo**. Scegliere **Ultima ora** e **Minuto** per **Time granularity** (Granularità temporale) e quindi fare clic su **Applica**.

![Metriche relative all'account di archiviazione](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Ai grafici possono essere assegnate più metriche, ma in tal caso non è possibile eseguire il raggruppamento per dimensioni.

## <a name="dimensions"></a>Dimensioni

Le [dimensioni](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) vengono usate per esaminare in modo più approfondito i grafici e ottenere informazioni più dettagliate. Metriche diverse hanno dimensioni diverse. Una dimensione disponibile è **Nome API**, che suddivide il grafico per singola chiamata API separata. La prima immagine di seguito mostra un grafico di esempio delle transazioni totali per un account di archiviazione. La seconda immagine mostra lo stesso grafico con la dimensione Nome API selezionata. Come è possibile osservare, ogni transazione viene riportata con una maggiore quantità di dettagli sul numero di chiamate effettuate per nome API.

![Metriche relative all'account di archiviazione: transazioni senza dimensioni](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Metriche relative all'account di archiviazione: transazioni](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la VM e fare clic su Elimina.

## <a name="next-steps"></a>Passaggi successivi

Nella quarta parte della serie si è appreso come visualizzare le metriche per la soluzione di esempio, ad esempio come:

> [!div class="checklist"]
> * Configurare grafici nel portale di Azure
> * Verificare metriche di velocità effettiva e latenza

Fare clic sul collegamento per visualizzare esempi di archiviazione predefiniti.

> [!div class="nextstepaction"]
> [Esempi di script di archiviazione di Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md