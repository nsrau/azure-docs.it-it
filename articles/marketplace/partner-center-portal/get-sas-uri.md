---
title: Ottenere l'URI della firma di accesso condiviso per l'immagine della macchina virtuale. Azure Marketplace
description: In questo articolo viene illustrato come ottenere l'URI della firma di accesso condiviso (SAS) per ogni disco rigido virtuale (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732647"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di Macchine virtuali di Azure dal portale Cloud Partner al Centro per i partner. Fino alla migrazione delle offerte, seguire le istruzioni in Ottenere l'URI della firma di [accesso condiviso per l'immagine della macchina virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) per il portale Cloud Partner per gestire le offerte.

In questo articolo viene descritto come generare un URI (Uniform Resource Identifier) della firma di accesso condiviso per ogni disco rigido virtuale.

Durante il processo di pubblicazione, è necessario fornire un URI per ogni disco rigido virtuale associato ai piani. Questi piani sono stati precedentemente indicati come SKU, o unità di conservazione delle scorte. Microsoft deve accedere a tali VHD durante il processo di certificazione. Questo URI verrà immesso nella scheda **Piani** del Centro per i partner.

Quando si generano URI di sas per i dischi rigidi virtuali, attenersi ai seguenti requisiti:

* Sono supportati solo i dischi rigidi virtuali non gestiti.
* Sono `List` `Read` necessari solo e le autorizzazioni. Non fornire l'accesso in scrittura o eliminazione.
* La durata dell'accesso (data di scadenza) deve essere di almeno tre settimane a partire dal momento in cui l'URI di firma di accesso condiviso è stato creato.
* Per proteggersi dalle modifiche dell'ora UTC, impostare la data di inizio su un giorno prima della data corrente. Ad esempio, se la data corrente è il 6 ottobre 2019, selezionare 5/10/2019.

## <a name="generate-the-sas-address"></a>Generare l'indirizzo di gestione rete

Esistono due strumenti comuni usati per creare un indirizzo sAS (URL):There are two common tools used to create an SAS address (URL):

* **Microsoft Storage Explorer** – Strumento grafico disponibile per Windows, macOS e Linux.
* Interfaccia di comando di **Microsoft Azure:** consigliata per sistemi operativi non Windows e ambienti di integrazione automatizzati o continui.

### <a name="use-microsoft-storage-explorer"></a>Usare Esplora archivi MicrosoftUse Microsoft Storage Explorer

1. Scaricare e installare [Esplora archivi di Microsoft Azure.](https://azure.microsoft.com/features/storage-explorer/)
2. Aprire esplora risorse e, nel menu a sinistra, selezionare **Aggiungi account**. Viene visualizzata la finestra di dialogo **Connetti ad Archiviazione di Azure.The Connect to Azure Storage** dialog box appears.
3. Selezionare **Aggiungi un account Azure** e quindi **Accedi**. Completare i passaggi necessari per accedere all'account Azure.Complete the required steps to sign into your Azure account.
4. Nel riquadro sinistro di**Explorer** passare agli **account di archiviazione** ed espandere questo nodo.
5. Fare clic con il pulsante destro del mouse sul disco rigido virtuale e quindi scegliere Ottieni firma di accesso alla **condivisione**.
6. Viene visualizzata la finestra di dialogo **Firma di accesso condiviso.** Completare i campi seguenti:

    * **Ora di inizio:** data di inizio dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare una data precedente di un giorno rispetto alla data corrente.
    * **Ora di scadenza:** data di scadenza dell'autorizzazione per l'accesso al disco rigido virtuale. Fornire una data che si validità almeno tre settimane dopo la data corrente.
    * **Autorizzazioni:** selezionare le autorizzazioni Lettura ed Elenco.
    * **Livello contenitore:** **selezionare** la casella di controllo Genera URI firma di accesso condiviso a livello di contenitore.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustra la finestra di dialogo Firma di accesso condiviso":::

7. Per creare l'URI di accesso condiviso associato per il disco rigido virtuale, selezionare **Crea**. La finestra di dialogo viene aggiornata e mostra i dettagli relativi a questa operazione.
8. Copiare **l'URI** e salvarlo in un file di testo in un percorso sicuro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Illustra la casella Dettagli firma di accesso condiviso":::

    Questo URI di accesso condiviso generato è per l'accesso a livello di contenitore. Per renderlo specifico, modificare il file di testo per aggiungere il nome del disco rigido virtuale (passaggio successivo).

9. Inserire il nome del disco rigido virtuale dopo la stringa vhds nell'URI di accesso condiviso (includere una barra). L'URI di chiamata finale dovrebbe essere simile al seguente:The final SAS URI should look like this:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Ad esempio, se il nome del `TestRGVM2.vhd`VDH è , l'URI di chiamata risultante sarà:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Ripetere questi passaggi per ogni disco rigido virtuale nei piani che verranno pubblicati.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

1. Scaricare e installare [l'interfaccia della riga di comando](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)di Microsoft Azure . Le versioni sono disponibili per Windows, macOS e diverse distribuzioni di Linux.
2. Creare un file di PowerShell (estensione di file con estensione ps1), copiare nel codice seguente, quindi salvarlo in locale.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Modificare il file per utilizzare i seguenti valori di parametro. Specificare le date in formato `2020-04-01T00:00:00Z`datetime UTC, ad esempio .

    * `<account-name>`– Nome dell'account di archiviazione di Azure
    * `<account-key>`– La chiave dell'account di archiviazione di Azure
    * `<vhd-name>`– Il tuo nome VHD
    * `<start-date>`– Data di inizio dell'autorizzazione per l'accesso Al disco rigido virtuale. Specificare la data del giorno precedente rispetto alla data corrente.
    * `<expiry-date>`– Data di scadenza delle autorizzazioni per l'accesso al disco rigido virtuale. Fornire una data almeno tre settimane dopo la data corrente.

    Questo esempio mostra i valori dei parametri appropriati (al momento della stesura di questo articolo):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Salvare le modifiche.
5. Utilizzando uno dei metodi seguenti, eseguire questo script con privilegi amministrativi per creare una stringa di connessione SAS per l'accesso a livello di contenitore:Using one of the following methods, run this script with administrative privileges to create a **SAS connection string** for container-level access:

    * Eseguire lo script dalla console. In Windows fare clic con il pulsante destro del mouse sullo script e **scegliere Esegui come amministratore**.
    * Eseguire lo script da un editor di script di PowerShell, ad esempio [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Questa schermata mostra la creazione di una stringa di connessione SAS all'interno di questo editor:This screen shows the creation of a SAS connection string within this editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Illustra la creazione di una stringa di connessione SAS con Windows PowerShell ISE":::

6. Copiare la stringa di connessione SAS e salvarla in un file di testo in un percorso sicuro. Modificare questa stringa per aggiungere le informazioni sul percorso del disco rigido virtuale per creare l'URI di accesso condiviso finale.
7. Nel portale di Azure passare all'archiviazione BLOB che include il disco rigido virtuale associato al nuovo URI.
8. Copiare l'URL dell'endpoint del **servizio BLOB,** come illustrato nella schermata seguente

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Illustra l'endpoint del servizio BLOB":::

9. Modificare il file di testo con la stringa di connessione della firma di accesso condiviso del passaggio 6. Creare l'URI di accesso condiviso completo usando questo formato:Create the complete SAS URI using this format:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Ad esempio, se il nome `TestRGVM2.vhd`del disco rigido virtuale è , l'URI di chiamata sarà:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ripetere questi passaggi per ogni disco rigido virtuale negli SKU che si prevede di pubblicare.

## <a name="verify-the-sas-uri"></a>Verificare l'URI di firma di accesso condiviso

Esaminare ogni URI di accesso condiviso creato usando l'elenco di controllo seguente per verificare che:Review each created SAS URI by using the following checklist to verify that:

* L'URI è simile al seguente:The URI looks like this:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* L'URI include il nome file dell'immagine VHD, inclusa l'estensione ".vhd".
* `sp=rl`viene visualizzato vicino al centro dell'URI. Questa stringa `Read` indica `List` che e l'accesso è specificato.
* Quando `sr=c` viene visualizzato, ciò significa che è specificato l'accesso a livello di contenitore.
* Copiare e incollare l'URI in un browser per eseguire il test del BLOB (è possibile annullare l'operazione prima del completamento del download).

## <a name="next-step"></a>Passaggio successivo

In caso di difficoltà nella creazione di un URI di accesso condiviso, vedere Problemi comuni relativi [all'URL di accesso condiviso](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). In caso contrario, salvare gli URI di firma di accesso condiviso in un percorso sicuro Sarà necessario per pubblicare l'offerta di macchine virtuali nel Centro per i partner.

* [Creare un'offerta di macchina virtuale di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
