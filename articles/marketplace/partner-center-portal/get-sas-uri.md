---
title: URI di firma di accesso condiviso per le immagini di macchina virtuale - Azure Marketplace
description: Generare un URI di firma di accesso condiviso (SAS) per i dischi rigidi virtuali (VHD) in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/14/2020
ms.openlocfilehash: f3589fb9ae176e04f727f516cca7c18c87dad9e0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317502"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale

Questo articolo descrive come generare un URI (Uniform Resource Identifier) di firma di accesso condiviso per ogni disco rigido virtuale (VHD).

Durante il processo di pubblicazione, è necessario specificare un URI per ogni disco rigido virtuale associato ai piani (in precedenza denominati SKU). Microsoft deve accedere a tali VHD durante il processo di certificazione. Questo URI verrà immesso nella scheda **Piani** del centro per i partner.

Quando si generano gli URI di firma di accesso condiviso per i dischi rigidi virtuali in uso, rispettare i requisiti seguenti:

* Sono supportati solo i dischi rigidi virtuali non gestiti.
* Sono necessarie solo le autorizzazioni `List` e `Read`. Evitare di fornire accesso con autorizzazioni di scrittura o eliminazione.
* La durata dell'accesso (data di scadenza) deve essere di almeno tre settimane a partire dal momento in cui l'URI di firma di accesso condiviso è stato creato.
* Per tener conto di variazioni di ora UTC, impostare la data di inizio su un giorno precedente alla data corrente. Ad esempio, se la data corrente è il 6 ottobre 2019 selezionare 5/10/2019.

## <a name="generate-the-sas-address"></a>Generare l'indirizzo SAS

Per creare un indirizzo SAS (URL) sono disponibili due strumenti comuni:

* **Microsoft Azure Storage Explorer**: strumento grafico disponibile per Windows, macOS e Linux.
* **Interfaccia della riga di comando di Microsoft Azure**: consigliato per sistemi operativi non Windows e per ambienti di integrazione automatizzata o continua.

### <a name="use-microsoft-storage-explorer"></a>Usare Microsoft Storage Explorer

1. Scaricare e installare [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Aprire la visualizzazione di esplorazione e nel menu a sinistra selezionare **Aggiungi account**. Viene visualizzata la finestra di dialogo **Connetti ad Archiviazione di Azure**.
3. Selezionare **Aggiungi un account Azure** e quindi **Accedi**. Completare la procedura necessaria per accedere all'account di Azure.
4. Nella parte sinistra del riquadro **Esplora** spostarsi su **Account di archiviazione** ed espandere questo nodo.
5. Fare clic con il pulsante destro del mouse sul disco rigido virtuale e quindi selezionare **Ottieni firma di accesso condiviso**.
6. Viene visualizzata la finestra di dialogo **Firma di accesso condiviso**. Completare i campi seguenti:

    * **Ora di inizio**: data e ora di inizio autorizzazione per l'accesso al disco rigido virtuale. Specificare una data precedente di un giorno rispetto alla data corrente.
    * **Ora di scadenza**: data e ora di scadenza per l'accesso al disco rigido virtuale. Specificare una data successiva di almeno tre settimane alla data corrente.
    * **Autorizzazioni**: selezionare le autorizzazioni lettura ed elenco.
    * **A livello di contenitore**: selezionare la casella di controllo **Genera l'URI di firma di accesso condiviso a livello di contenitore**.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustra la finestra di dialogo Firma di accesso condiviso":::

7. Selezionare **Crea** per creare l'URI di firma di accesso condiviso associato al disco rigido virtuale. La finestra di dialogo viene aggiornata e mostra i dettagli relativi a questa operazione.
8. Copiare l'**URL** e salvarlo in un file di testo in un percorso sicuro.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Illustra la casella Dettagli Firma di accesso condiviso":::

    L'URI di firma di accesso condiviso è stato generato per l'accesso a livello di contenitore. Per renderlo specifico, modificare il file di testo per aggiungere il nome del disco rigido virtuale (passaggio successivo).

9. Inserire il nome del disco rigido virtuale dopo la stringa vhds nell'URI di firma di accesso condiviso (includere una barra). L'URI di firma di accesso condiviso finale dovrebbe essere simile al seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>` Se il nome del disco rigido virtuale è `TestRGVM2.vhd`, l'URI di firma di accesso condiviso risultante è il seguente:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Ripetere questi passaggi per ogni disco rigido virtuale nei piani che si pubblicheranno.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

1. Scaricare e installare l'[interfaccia della riga di comando di Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Le versioni sono disponibili per Windows, macOS e diverse distribuzioni di Linux.
2. Creare un file PowerShell (con estensione .ps1), copiarlo nel codice seguente e quindi salvarlo in locale.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Modificare il file per usare i valori dei parametri seguenti. Specificare le date in formato di data/ora UTC, ad esempio `2020-04-01T00:00:00Z`.

    * `<account-name>`: nome dell'account di archiviazione di Azure
    * `<account-key>`: chiave dell'account di archiviazione di Azure
    * `<vhd-name>`: nome del disco rigido virtuale
    * `<start-date>`: data e ora di inizio dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare la data del giorno precedente rispetto alla data corrente.
    * `<expiry-date>`: data e ora di scadenza dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare una data successiva di almeno tre settimane alla data corrente.

    L'esempio seguente mostra i valori dei parametri appropriati (al momento della stesura di questo articolo):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Salvare le modifiche.
5. Usando uno dei metodi seguenti, eseguire questo script con privilegi amministrativi per creare una **stringa di connessione della firma di accesso condiviso** per l'accesso a livello di contenitore:

    * Eseguire lo script dalla console. In Windows fare clic con il pulsante destro del mouse sullo script e selezionare **Esegui come amministratore**.
    * Eseguire lo script da un editor di script di PowerShell, ad esempio [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Questa schermata mostra la creazione di una stringa di connessione di firma di accesso condiviso nell'editor:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Viene illustrata la creazione di una stringa di connessione di firma di accesso condiviso con Windows PowerShell ISE":::

6. Copiare la stringa di connessione della firma di accesso condiviso e salvarla in un file di testo in un percorso sicuro. Modificare la stringa per aggiungere le informazioni sul percorso del disco rigido virtuale per creare l'URI di firma di accesso condiviso finale.
7. Nella portale di Azure passare all'archivio BLOB che include il disco rigido virtuale associato al nuovo URI.
8. Copiare l'URL dell'**endpoint di servizio BLOB**, come illustrato nella schermata seguente

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Illustra l'endpoint di servizio BLOB":::

9. Modificare il file di testo con la stringa di connessione della firma di accesso condiviso del passaggio 6. Creare l'URI di firma di accesso condiviso completo usando il formato seguente:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Se, ad esempio, il nome del disco rigido virtuale è `TestRGVM2.vhd`, l'URI di firma di accesso condiviso sarà:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ripetere questi passaggi per ogni disco rigido virtuale nei piani che si pubblicheranno.

## <a name="verify-the-sas-uri"></a>Verificare l'URI di firma di accesso condiviso

Esaminare ogni URI di firma di accesso condiviso creato usando l'elenco di controllo seguente per verificare che:

* l'URI sia simile al seguente: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* l'URI contenga il nome di file dell'immagine del disco rigido virtuale, inclusa l'estensione del nome di file ".vhd".
* `sp=rl` viene visualizzato in prossimità del centro dell'URI. Questa stringa indica che è specificato l'accesso `Read` e `List`.
* Quando viene visualizzato `sr=c`, significa che l'accesso è specificato a livello di contenitore.
* Copiare e incollare l'URI in un browser per eseguire il test e scaricare il BLOB (è possibile annullare l'operazione prima del completamento del download).

## <a name="next-step"></a>Passaggio successivo

In caso di difficoltà nella creazione di un URI di firma di accesso condiviso, vedere [Problemi comuni degli URL di firma di accesso condiviso](common-sas-uri-issues.md). In caso contrario, salvare gli URI di firma di accesso condiviso in un percorso sicuro Sarà necessario per pubblicare l'offerta per le macchine virtuali nel centro per i partner.

* [Creare un'offerta per macchine virtuali di Azure](azure-vm-create-offer.md)
