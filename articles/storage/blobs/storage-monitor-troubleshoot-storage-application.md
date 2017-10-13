---
title: Monitorare e risolvere i problemi relativi a un'applicazione di archiviazione cloud in Azure | Microsoft Docs
description: Usare gli strumenti di diagnostica, le metriche e gli avvisi per risolvere i problemi e monitorare un'applicazione cloud.
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorare e risolvere i problemi relativi a un'applicazione di archiviazione cloud

Questa esercitazione è la quarta e ultima parte di una serie di esercitazioni. Descrive come monitorare e risolvere i problemi relativi a un'applicazione di archiviazione cloud.

Nella quarta parte della serie si apprenderà come:

> [!div class="checklist"]
> * attivare le metriche e la registrazione
> * attivare gli avvisi per gli errori di autorizzazione
> * eseguire il traffico di test con token SAS non corretti
> * scaricare e analizzare i log

[Analisi archiviazione di Azure](../common/storage-analytics.md) offre la registrazione e i dati metrica per un account di archiviazione. Questi dati offrono informazioni dettagliate sull'integrità dell'account di archiviazione. Prima di ottenere la visibilità nell'account di archiviazione, è necessario configurare la raccolta dati. Questo processo implica l'attivazione della registrazione, la configurazione delle metriche e l'attivazione degli avvisi.

La registrazione e le metriche degli account di archiviazione vengono abilitate dalla scheda **Diagnostica** nel portale di Azure. Esistono due tipi di metriche. Le metriche **aggregate** raccolgono dati in ingresso/uscita, di disponibilità, latenza e percentuale di operazioni riuscite, che vengono aggregate per i servizi BLOB, di accodamento, tabelle e file. **Per API** raccolgono lo stesso set di metriche per ogni operazione di archiviazione nell'API del servizio Archiviazione di Azure. La registrazione di archiviazione consente all'utente di registrare i dettagli delle richieste, riuscite e non riuscite, nel proprio account di archiviazione. Questi log consentono di visualizzare i dettagli delle operazioni di lettura, scrittura ed eliminazione a fronte delle tabelle, code e BLOB di Azure. Consentono anche di visualizzare i motivi di fallimento delle richieste, ad esempio errori di timeout, limitazione e autorizzazione.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>attivare le metriche e la registrazione

Nel menu a sinistra selezionare **Gruppi di risorse**, scegliere **myResourceGroup**, quindi selezionare l'account di archiviazione nell'elenco di risorse.

In **Diagnostica** impostare **Stato** su **On** (Attivo). Verificare che **Metriche aggregate dei BLOB**, **Metriche dei BLOB per API** e **Log dei BLOB** siano abilitati.

Al termine, fare clic su **Salva**

![Riquadro Diagnostica](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>Attivare gli avvisi

Gli avvisi sono un metodo per inviare messaggi di posta elettronica agli amministratori o attivare un webhook in base a una metrica che supera una soglia. In questo esempio, si attiva un avviso per la metrica `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Passare all'account di archiviazione nel portale di Azure

Nel menu a sinistra selezionare **Gruppi di risorse**, scegliere **myResourceGroup**, quindi selezionare l'account di archiviazione nell'elenco di risorse.

Selezionare **Regole di avviso** nella sezione **Monitoraggio**.

Selezionare **+ Aggiungi avviso** in **Aggiungi una regola di avviso** e inserire le informazioni necessarie. Scegliere `SASClientOtherError` dall'elenco a discesa **Metrica**.

![Riquadro Diagnostica](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simulare un errore

Per simulare un avviso valido, si può provare a richiedere un BLOB inesistente dall'account di archiviazione. A tale scopo, sostituire il valore `<incorrect-blob-name>` con un valore che non esiste. Eseguire il codice di esempio seguente più volte per simulare richieste BLOB non riuscite.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

L'immagine seguente è un avviso di esempio che si basa sull'errore simulato eseguito con l'esempio precedente.

 ![Avviso di esempio](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Scaricare e visualizzare i log

I log di archiviazione archiviano i dati in un set di BLOB in un contenitore BLOB denominato **$logs** nell'account di archiviazione. Questo contenitore non mostra se si elencano tutti i contenitori BLOB nell'account, ma è possibile visualizzarne il contenuto se vi si accede direttamente.

In questo scenario usare [Analizzatore messaggi di Microsoft](http://technet.microsoft.com/library/jj649776.aspx) per interagire con l'account di archiviazione di Azure.

### <a name="download-microsoft-message-analyzer"></a>Scaricare Analizzatore messaggi di Microsoft

Scaricare [Analizzatore messaggi di Microsoft](https://www.microsoft.com/download/details.aspx?id=44226) e installare l'applicazione.

Avviare l'applicazione e scegliere **File** > **Apri** > **From Other File Sources** (Da altre origini file).

Nella finestra di dialogo **File Selector** (Selettore file) selezionare **+ Add Azure Connection** (+ Aggiungi connessione di Azure). Immettere il **nome dell'account di archiviazione** e **la chiave dell'account** e fare clic su **OK**.

![Analizzatore messaggi di Microsoft: finestra di dialogo Aggiungi connessione di Archiviazione di Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Dopo aver effettuato la connessione, espandere i contenitori nella vista della struttura di archiviazione per visualizzare i BLOB dei log. Selezionare il log più recente e fare clic su **OK**.

![Analizzatore messaggi di Microsoft: finestra di dialogo Aggiungi connessione di Archiviazione di Azure](media/storage-monitor-troubleshoot-storage-application/figure4.png)

Nella finestra di dialogo **Nuova sessione** fare clic su **Avvia** per visualizzare il log.

Quando il log si apre, è possibile visualizzare gli eventi di archiviazione. Come mostrato nell'immagine seguente, `SASClientOtherError` è stato attivato nell'account di archiviazione. Per altre informazioni sulla registrazione archiviazione, visitare [Analisi archiviazione](../common/storage-analytics.md).

![Analizzatore messaggi di Microsoft: visualizzazione degli eventi](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un altro strumento che può essere usato per interagire con gli account di archiviazione, includendo il contenitore **$logs** e i registri in esso contenuti.

## <a name="next-steps"></a>Passaggi successivi

Nella quarta e ultima parte della serie è stato descritto come monitorare e risolvere i problemi relativi all'account di archiviazione, ad esempio come:

> [!div class="checklist"]
> * attivare le metriche e la registrazione
> * attivare gli avvisi per gli errori di autorizzazione
> * eseguire il traffico di test con token SAS non corretti
> * scaricare e analizzare i log

Fare clic sul collegamento per visualizzare esempi di archiviazione predefiniti.

> [!div class="nextstepaction"]
> [Esempi di script di archiviazione di Azure](storage-samples-blobs-cli.md)