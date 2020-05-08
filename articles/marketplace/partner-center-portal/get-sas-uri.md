---
title: URI di firma di accesso condiviso per immagini di VM-Azure Marketplace
description: Generare un URI di firma di accesso condiviso (SAS) per i dischi rigidi virtuali (VHD) in Azure Marketplace.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: b521a3a035044e2f0c1b625df19d265cfa35b49a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857937"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte di macchine virtuali di Azure dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni in [ottenere l'URI della firma di accesso condiviso per l'immagine di macchina virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) per portale cloud partner per gestire le offerte.

Questo articolo descrive come generare un URI (Uniform Resource Identifier) di firma di accesso condiviso per ogni disco rigido virtuale (VHD).

Durante il processo di pubblicazione, è necessario specificare un URI per ogni disco rigido virtuale associato ai piani. Questi piani erano indicati in precedenza come SKU o unità di mantenimento delle scorte. Microsoft deve accedere a tali VHD durante il processo di certificazione. Questo URI verrà immesso nella scheda **piani** del centro per i partner.

Quando si generano URI SAS per i dischi rigidi virtuali, attenersi ai requisiti seguenti:

* Sono supportati solo i dischi rigidi virtuali non gestiti.
* Sono `List` necessarie `Read` solo le autorizzazioni e. Non fornire l'accesso in scrittura o eliminazione.
* La durata dell'accesso (data di scadenza) deve essere di almeno tre settimane a partire dal momento in cui l'URI di firma di accesso condiviso è stato creato.
* Per la protezione da modifiche all'ora UTC, impostare la data di inizio su un giorno prima della data corrente. Ad esempio, se la data corrente è il 6 ottobre 2019, selezionare 10/5/2019.

## <a name="generate-the-sas-address"></a>Generare l'indirizzo SAS

Per creare un indirizzo SAS (URL) sono disponibili due strumenti comuni:

* **Microsoft Storage Explorer** : strumento grafico disponibile per Windows, MacOS e Linux.
* **Interfaccia** della riga di comando Microsoft Azure: consigliata per i sistemi operativi non Windows e gli ambienti di integrazione automatica o continua.

### <a name="use-microsoft-storage-explorer"></a>Usare Microsoft Storage Explorer

1. Scaricare e installare [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Aprire Esplora risorse e scegliere **Aggiungi account**dal menu a sinistra. Viene visualizzata la finestra **di dialogo Connetti ad archiviazione di Azure** .
3. Selezionare **Aggiungi un account Azure** e quindi **Accedi**. Completare i passaggi necessari per accedere al proprio account Azure.
4. Nel riquadro di**esplorazione** a sinistra passare agli account di **archiviazione** ed espandere questo nodo.
5. Fare clic con il pulsante destro del mouse sul disco rigido virtuale e scegliere **Ottieni firma di accesso alla condivisione**.
6. Verrà visualizzata la finestra di dialogo **firma di accesso condiviso** . Completare i campi seguenti:

    * **Ora di inizio** : data di inizio dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare una data precedente di un giorno rispetto alla data corrente.
    * **Ora di scadenza** : data di scadenza dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare una data di almeno tre settimane oltre la data corrente.
    * **Autorizzazioni** : selezionare le autorizzazioni lettura ed elenco.
    * **A livello di contenitore** : selezionare la casella di controllo **genera l'URI di firma di accesso condiviso a livello di contenitore** .

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Viene illustrata la finestra di dialogo firma di accesso condiviso":::

7. Per creare l'URI SAS associato per questo disco rigido virtuale, selezionare **Crea**. La finestra di dialogo viene aggiornata e Mostra i dettagli relativi a questa operazione.
8. Copiare l' **URI** e salvarlo in un file di testo in una posizione sicura.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Viene illustrata la casella Dettagli firma di accesso condiviso":::

    Questo URI di firma di accesso condiviso viene generato per l'accesso a livello di contenitore. Per renderla specifica, modificare il file di testo per aggiungere il nome del disco rigido virtuale (passaggio successivo).

9. Inserire il nome VHD dopo la stringa VHD nell'URI SAS (includere una barra). L'URI di firma di accesso condiviso finale dovrebbe essere simile al seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Se, ad esempio, il nome di VDH è `TestRGVM2.vhd`, l'URI SAS risultante sarà:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Ripetere questi passaggi per ogni disco rigido virtuale nei piani che si pubblicheranno.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

1. Scaricare e installare l'interfaccia della riga di comando [Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Le versioni sono disponibili per Windows, macOS e diverse distribuzioni di Linux.
2. Creare un file di PowerShell (estensione di file ps1), copiare il codice seguente e quindi salvarlo in locale.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Modificare il file per usare i valori di parametro seguenti. Fornire date in formato DateTime UTC, ad esempio `2020-04-01T00:00:00Z`.

    * `<account-name>`: Nome dell'account di archiviazione di Azure
    * `<account-key>`: Chiave dell'account di archiviazione di Azure
    * `<vhd-name>`: Nome del disco rigido virtuale
    * `<start-date>`: Data di inizio dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare la data del giorno precedente rispetto alla data corrente.
    * `<expiry-date>`: Data di scadenza dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare una data di almeno tre settimane dopo la data corrente.

    Questo esempio mostra i valori dei parametri appropriati (al momento della stesura di questo documento):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Salvare le modifiche.
5. Usando uno dei metodi seguenti, eseguire questo script con privilegi amministrativi per creare una **stringa di connessione SAS** per l'accesso a livello di contenitore:

    * Eseguire lo script dalla console. In Windows fare clic con il pulsante destro del mouse sullo script e scegliere **Esegui come amministratore**.
    * Eseguire lo script da un editor di script di PowerShell, ad esempio [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Questa schermata mostra la creazione di una stringa di connessione SAS nell'Editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Viene illustrata la creazione di una stringa di connessione SAS con Windows PowerShell ISE":::

6. Copiare la stringa di connessione SAS e salvarla in un file di testo in una posizione sicura. Modificare questa stringa per aggiungere le informazioni sulla posizione del disco rigido virtuale per creare l'URI di firma di accesso condiviso finale.
7. Nella portale di Azure passare all'archivio BLOB che include il disco rigido virtuale associato al nuovo URI.
8. Copiare l'URL dell' **endpoint del servizio BLOB**, come illustrato nella schermata seguente.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Illustra l'endpoint del servizio BLOB":::

9. Modificare il file di testo con la stringa di connessione della firma di accesso condiviso del passaggio 6. Creare l'URI SAS completo usando il formato seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Se, ad esempio, il nome del disco rigido `TestRGVM2.vhd`virtuale è, l'URI di firma di accesso condiviso sarà:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ripetere questi passaggi per ogni disco rigido virtuale negli SKU che si prevede di pubblicare.

## <a name="verify-the-sas-uri"></a>Verificare l'URI di firma di accesso condiviso

Esaminare ogni URI SAS creato utilizzando l'elenco di controllo seguente per verificare che:

* L'URI ha un aspetto simile al seguente:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* L'URI include il nome file dell'immagine VHD, inclusa l'estensione del nome file ". VHD".
* `sp=rl`viene visualizzato in prossimità del centro dell'URI. Questa stringa indica che `Read` `List` l'accesso è specificato.
* Quando `sr=c` viene visualizzato, questo significa che viene specificato l'accesso a livello di contenitore.
* Copiare e incollare l'URI in un browser per eseguire il test: scaricare il BLOB. è possibile annullare l'operazione prima del completamento del download.

## <a name="next-step"></a>Passaggio successivo

In caso di difficoltà nella creazione di un URI di firma di accesso condiviso, vedere [problemi comuni relativi all'URL SAS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). In caso contrario, salvare gli URI di firma di accesso condiviso in un percorso sicuro Sarà necessario per pubblicare l'offerta di VM nel centro per i partner.

* [Creare un'offerta di macchina virtuale di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
