---
title: Generare un URI di firma di accesso condiviso per un'immagine di macchina virtuale-Azure Marketplace
description: Generare un URI di firma di accesso condiviso (SAS) per un disco rigido virtuale (VHD) in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 10/19/2020
ms.openlocfilehash: ead367568762d4b76de7164feb56b7a31cd53e0d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129117"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Come generare un URI di firma di accesso condiviso per un'immagine di macchina virtuale

Durante il processo di pubblicazione, è necessario specificare un URI di firma di accesso condiviso (SAS) per ogni disco rigido virtuale associato ai piani, chiamati in precedenza SKU. Microsoft deve accedere a tali VHD durante il processo di certificazione. Questo URI verrà immesso nella scheda **piani** del centro per i partner.

I requisiti per la generazione di URI SAS per i VHD sono i seguenti:

- Supportano solo dischi rigidi virtuali non gestiti.
- Sono necessarie solo le autorizzazioni di lettura e di elenco. Non fornire l'accesso in scrittura o eliminazione.
- La durata dell'accesso (data di scadenza) deve essere di almeno tre settimane a partire dal momento in cui l'URI di firma di accesso condiviso è stato creato.
- Per tener conto di variazioni di ora UTC, impostare la data di inizio su un giorno precedente alla data corrente. Ad esempio, se la data corrente è il 16 giugno 2020, selezionare 6/15/2020.

## <a name="generate-the-sas-address"></a>Generare l'indirizzo SAS

Per creare un indirizzo SAS (URL) sono disponibili due strumenti comuni:

1. **Azure Storage Explorer** : disponibile nel portale di Azure.
2. **Interfaccia** della riga di comando di Azure: consigliata per sistemi operativi non Windows e ambienti di integrazione automatizzati o continui.

### <a name="using-tool-1-azure-storage-explorer"></a>Uso dello strumento 1: Azure Storage Explorer

1. Passare all' **account di archiviazione** .
1. Aprire **Storage Explorer** .

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Finestra dell'account di archiviazione.":::

3. Nel **contenitore** , fare clic con il pulsante destro del mouse sul file VHD e selezionare **Ottieni firma di accesso alla condivisione** .
4. Nella finestra di dialogo **firma di accesso condiviso** completare i campi seguenti:

    1. Ora di inizio: data e ora di inizio autorizzazione per l'accesso al disco rigido virtuale. Specificare una data precedente di un giorno rispetto alla data corrente.
    2. Ora di scadenza: data e ora di scadenza per l'accesso al disco rigido virtuale. Specificare una data successiva di almeno tre settimane alla data corrente.
    3. Autorizzazioni: selezionare le autorizzazioni lettura ed elenco.
    4. A livello di contenitore: selezionare la casella di controllo Genera l'URI di firma di accesso condiviso a livello di contenitore.

    ![Finestra di dialogo firma di accesso condiviso.](media/vm/create-sas-uri-storage-explorer.png)

5. Selezionare **Crea** per creare l'URI di firma di accesso condiviso associato al disco rigido virtuale.
6. Copiare l'URL e salvarlo in un file di testo in un percorso sicuro. L'URI di firma di accesso condiviso è stato generato per l'accesso a livello di contenitore. Per renderla specifica, modificare il file di testo per aggiungere il nome del disco rigido virtuale.
7. Inserire il nome del disco rigido virtuale dopo la stringa vhds nell'URI di firma di accesso condiviso (includere una barra). L'URI di firma di accesso condiviso finale dovrebbe essere simile al seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Ripetere questi passaggi per ogni disco rigido virtuale nei piani che si pubblicheranno.

### <a name="using-tool-2-azure-cli"></a>Uso dello strumento 2: interfaccia della riga di comando di Azure

1. Scaricare e installare [Microsoft Azure CL](/cli/azure/install-azure-cli)I. Le versioni sono disponibili per Windows, macOS e diverse distribuzioni di Linux.
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

    - Eseguire lo script dalla console. In Windows fare clic con il pulsante destro del mouse sullo script e selezionare **Esegui come amministratore** .
    - Eseguire lo script da un editor di script di PowerShell, ad esempio [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Questa schermata mostra la creazione di una stringa di connessione di firma di accesso condiviso nell'editor:

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

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi, vedere la pagina relativa ai [messaggi di errore SAS della macchina virtuale](azure-vm-sas-failure-messages.md).
- [Accedi al centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Creare un'offerta di macchina virtuale in Azure Marketplace](azure-vm-create.md)