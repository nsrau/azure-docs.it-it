---
title: Ottenere un URI di firma di accesso condiviso per l'immagine di macchina virtuale-Azure Marketplace
description: Generare un URI di firma di accesso condiviso (SAS) per i dischi rigidi virtuali (VHD) in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144515"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>Ottenere un URI di firma di accesso condiviso per l'immagine di macchina virtuale

Durante il processo di pubblicazione, è necessario specificare un URI di firma di accesso condiviso (SAS) per ogni disco rigido virtuale associato ai piani, chiamati in precedenza SKU. Microsoft deve accedere a tali VHD durante il processo di certificazione. Questo URI verrà immesso nella scheda **piani** del centro per i partner.

I requisiti per la generazione di URI SAS per i VHD sono i seguenti:

- Supportano solo dischi rigidi virtuali non gestiti.
- Sono necessarie solo le autorizzazioni di lettura e di elenco. Non fornire l'accesso in scrittura o eliminazione.
- La durata dell'accesso (data di scadenza) deve essere di almeno tre settimane a partire dal momento in cui l'URI di firma di accesso condiviso è stato creato.
- Per tener conto di variazioni di ora UTC, impostare la data di inizio su un giorno precedente alla data corrente. Ad esempio, se la data corrente è il 16 giugno 2020, selezionare 6/15/2020.

## <a name="generate-the-sas-address"></a>Generare l'indirizzo SAS

Per creare un indirizzo SAS (URL) sono disponibili due strumenti comuni:

1. **Microsoft Azure Storage Explorer**: strumento grafico disponibile per Windows, macOS e Linux.
2. **Interfaccia della riga di comando di Microsoft Azure**: consigliato per sistemi operativi non Windows e per ambienti di integrazione automatizzata o continua.

### <a name="using-tool-1-microsoft-storage-explorer"></a>Utilizzo dello strumento 1: Microsoft Storage Explorer

1. Scaricare e installare [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Aprire Esplora risorse e scegliere **Aggiungi account**dal menu a sinistra.
3. Nella finestra di dialogo **Connetti ad archiviazione di Azure** selezionare **Aggiungi un account Azure** e accedere all'account Azure.
4. Nel riquadro di esplorazione a sinistra espandere il nodo **account di archiviazione** .
5. Fare clic con il pulsante destro del mouse sul VHD e selezionare **Ottieni firma di accesso alla condivisione**.
6. Nella finestra di dialogo **firma di accesso condiviso** completare i campi seguenti:

    1. Ora di inizio: data e ora di inizio autorizzazione per l'accesso al disco rigido virtuale. Specificare una data precedente di un giorno rispetto alla data corrente.
    2. Ora di scadenza: data e ora di scadenza per l'accesso al disco rigido virtuale. Specificare una data successiva di almeno tre settimane alla data corrente.
    3. Autorizzazioni: selezionare le autorizzazioni lettura ed elenco.
    4. A livello di contenitore: selezionare la casella di controllo Genera l'URI di firma di accesso condiviso a livello di contenitore.

    ![Finestra di dialogo firma di accesso condiviso.](media/vm/create-sas-uri-storage-explorer.png)

7. Selezionare **Crea** per creare l'URI di firma di accesso condiviso associato al disco rigido virtuale. La finestra di dialogo viene aggiornata e mostra i dettagli relativi a questa operazione.

8. Copiare l'URL e salvarlo in un file di testo in un percorso sicuro.

    ![Copia dell'URI.](media/vm/create-sas-uri-shared-access-signature-details.png)

    L'URI di firma di accesso condiviso è stato generato per l'accesso a livello di contenitore. Per renderla specifica, modificare il file di testo per aggiungere il nome del disco rigido virtuale.

9. Inserire il nome del disco rigido virtuale dopo la stringa vhds nell'URI di firma di accesso condiviso (includere una barra). L'URI di firma di accesso condiviso finale dovrebbe essere simile al seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Ripetere questi passaggi per ogni disco rigido virtuale nei piani che si pubblicheranno.

### <a name="using-tool-2-azure-cli"></a>Uso dello strumento 2: interfaccia della riga di comando di Azure

1. Scaricare e installare [Microsoft Azure CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I. Le versioni sono disponibili per Windows, macOS e diverse distribuzioni di Linux.
2. Creare un file PowerShell (con estensione .ps1), copiarlo nel codice seguente e quindi salvarlo in locale.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Modificare il file per usare i valori dei parametri seguenti. Fornire le date nel formato DateTime UTC, ad esempio 2020-04-01T00:00:00Z.

    - nome account: nome dell'account di archiviazione di Azure.
    - chiave account: chiave dell'account di archiviazione di Azure.
    - VHD-nome: nome del disco rigido virtuale.
    - Data inizio: data di inizio dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare la data del giorno precedente rispetto alla data corrente.
    - Data di scadenza: data di scadenza dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare una data successiva di almeno tre settimane alla data corrente.

    Di seguito è riportato un esempio di valori di parametri appropriati (al momento della stesura di questo articolo):

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Salvare le modifiche.
2. Usando uno dei metodi seguenti, eseguire questo script con privilegi amministrativi per creare una stringa di connessione della firma di accesso condiviso per l'accesso a livello di contenitore:

    - Eseguire lo script dalla console. In Windows fare clic con il pulsante destro del mouse sullo script e selezionare **Esegui come amministratore**.
    - Eseguire lo script da un editor di script di PowerShell, ad esempio [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Questa schermata mostra la creazione di una stringa di connessione di firma di accesso condiviso nell'editor:

    [![creazione di una stringa di connessione SAS nell'editor di PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Copiare la stringa di connessione della firma di accesso condiviso e salvarla in un file di testo in un percorso sicuro. Modificare la stringa per aggiungere le informazioni sul percorso del disco rigido virtuale per creare l'URI di firma di accesso condiviso finale.
7. Nella portale di Azure passare all'archivio BLOB che include il disco rigido virtuale associato al nuovo URI.
8. Copiare l'URL dell'endpoint del servizio thebBlob:

    ![Copia dell'URL dell'endpoint del servizio BLOB.](media/vm/create-sas-uri-blob-endpoint.png)

9. Modificare il file di testo con la stringa di connessione della firma di accesso condiviso del passaggio 6. Creare l'URI di firma di accesso condiviso completo usando il formato seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Verificare l'URI di firma di accesso condiviso

Controllare l'URI di firma di accesso condiviso prima di pubblicarlo nel centro per i partner, in modo da evitare eventuali problemi correlati all'invio della richiesta da parte dell'URI SAS. Questo processo è facoltativo ma consigliato.

- L'URI include il nome file dell'immagine VHD, inclusa l'estensione del nome file `.vhd` .
- `Sp=rl` viene visualizzato in prossimità del centro dell'URI. Questa stringa Mostra l'accesso in lettura ed elenco è specificato.
- Quando viene visualizzato `sr=c`, significa che l'accesso è specificato a livello di contenitore.
- Copiare e incollare l'URI in un browser per eseguire il test e scaricare il BLOB (è possibile annullare l'operazione prima del completamento del download).

## <a name="next-step"></a>Passaggio successivo

- Leggere [creare un'offerta di macchina virtuale di Azure](azure-vm-create-offer.md).
