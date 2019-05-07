---
title: Monitorare e risolvere i problemi relativi a un'applicazione di archiviazione cloud in Azure | Microsoft Docs
description: Usare gli strumenti di diagnostica, le metriche e gli avvisi per risolvere i problemi e monitorare un'applicazione cloud.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.custom: mvc
ms.openlocfilehash: 1383ccd570e23d80343ccdfd586d08ee25ebbc8d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148229"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorare e risolvere i problemi relativi a un'applicazione di archiviazione cloud

Questa esercitazione è la quarta e ultima parte di una serie di esercitazioni. Descrive come monitorare e risolvere i problemi relativi a un'applicazione di archiviazione cloud.

Nella quarta parte della serie si apprenderà come:

> [!div class="checklist"]
> * attivare le metriche e la registrazione
> * attivare gli avvisi per gli errori di autorizzazione
> * eseguire il traffico di test con token SAS non corretti
> * scaricare e analizzare i log

[Analisi archiviazione di Azure](../common/storage-analytics.md) offre la registrazione e i dati metrica per un account di archiviazione. Questi dati offrono informazioni dettagliate sull'integrità dell'account di archiviazione. Per raccogliere dati analitici dall'archiviazione di Azure, è possibile configurare la registrazione, le metriche e gli avvisi. Questo processo implica l'attivazione della registrazione, la configurazione delle metriche e l'attivazione degli avvisi.

La registrazione e le metriche degli account di archiviazione vengono abilitate dalla scheda **Diagnostica** nel portale di Azure. La registrazione di archiviazione consente all'utente di registrare i dettagli delle richieste, riuscite e non riuscite, nel proprio account di archiviazione. Questi log consentono di visualizzare i dettagli delle operazioni di lettura, scrittura ed eliminazione a fronte delle tabelle, code e BLOB di Azure. Consentono anche di visualizzare i motivi di fallimento delle richieste, ad esempio errori di timeout, limitazione e autorizzazione.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Attivare le metriche e la registrazione

Nel menu a sinistra selezionare **Gruppi di risorse**, scegliere **myResourceGroup**, quindi selezionare l'account di archiviazione nell'elenco di risorse.

In **Impostazioni di diagnostica (versione classica)** impostare **Stato** su **Attivo**. Assicurarsi che tutte le opzioni in **Proprietà BLOB** siano abilitate.

Al termine, fare clic su **Salva**

![Riquadro Diagnostica](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Attivare gli avvisi

Gli avvisi sono un metodo per inviare messaggi di posta elettronica agli amministratori o attivare un webhook in base a una metrica che supera una soglia. In questo esempio, si attiva un avviso per la metrica `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Passare all'account di archiviazione nel portale di Azure

Nella sezione **Monitoraggio** selezionare **Avvisi (versione classica)**.

Selezionare **Aggiungi avviso per la metrica (versione classica)** e completare il modulo **Aggiungi regola** inserendo le informazioni richieste. Nell'elenco a discesa **Metrica** selezionare `SASClientOtherError`. Per consentire l'attivazione dell'avviso al primo errore, nell'elenco a discesa **Condizione** selezionare **Maggiore o uguale a**.

![Riquadro Diagnostica](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Simulare un errore

Per simulare un avviso valido, si può provare a richiedere un BLOB inesistente dall'account di archiviazione. Il comando seguente richiede il nome di un contenitore di archiviazione. È possibile usare il nome di un contenitore esistente o crearne uno nuovo per gli scopi di questo esempio.

Sostituire i segnaposto con valori reali (verificare che `<INCORRECT_BLOB_NAME>` sia impostato su un valore che non esiste) ed eseguire il comando.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

L'immagine seguente è un avviso di esempio che si basa sull'errore simulato eseguito con l'esempio precedente.

 ![Avviso di esempio](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Scaricare e visualizzare i log

I log di archiviazione archiviano i dati in un set di BLOB in un contenitore BLOB denominato **$logs** nell'account di archiviazione. Questo contenitore non mostra se si elencano tutti i contenitori BLOB nell'account, ma è possibile visualizzarne il contenuto se vi si accede direttamente.

In questo scenario usare [Analizzatore messaggi di Microsoft](https://technet.microsoft.com/library/jj649776.aspx) per interagire con l'account di archiviazione di Azure.

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

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un altro strumento che può essere usato per interagire con gli account di archiviazione, includendo il contenitore **$logs** e i log in esso contenuti.

## <a name="next-steps"></a>Passaggi successivi

Nella quarta e ultima parte della serie è stato descritto come monitorare e risolvere i problemi relativi all'account di archiviazione, ad esempio come:

> [!div class="checklist"]
> * Attivare le metriche e la registrazione
> * attivare gli avvisi per gli errori di autorizzazione
> * eseguire il traffico di test con token SAS non corretti
> * scaricare e analizzare i log

Fare clic sul collegamento per visualizzare esempi di archiviazione predefiniti.

> [!div class="nextstepaction"]
> [Esempi di script di archiviazione di Azure](storage-samples-blobs-cli.md)