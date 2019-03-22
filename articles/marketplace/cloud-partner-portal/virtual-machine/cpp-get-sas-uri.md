---
title: Ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale basata su Microsoft Azure | Microsoft Docs
description: L'articolo illustra come ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c21fa3cf819f48dcda46f2d444ed52bc2eb9ae3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113521"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale

Durante il processo di pubblicazione è necessario specificare un URI (Uniform Resource Identifier) per ogni disco rigido virtuale associato agli SKU. Microsoft deve accedere a tali VHD durante il processo di certificazione. Questo articolo illustra come generare un URI di firma di accesso condiviso per ogni disco rigido virtuale. L'URI viene immesso nella scheda **SKU** nel portale Cloud Partner. 

Quando si generano gli URI di firma di accesso condiviso per i dischi rigidi virtuali in uso, rispettare i requisiti seguenti:

- Sono supportati solo i dischi rigidi virtuali non gestiti.
- Sono sufficienti le autorizzazioni `List` e `Read`­. *Non* concedere l'accesso per l'operazione `Write` o `Delete`.
- La durata dell'accesso (*data di scadenza*) deve essere di almeno tre settimane a partire dal momento in cui l'URI di firma di accesso condiviso è stato creato.
- Per tener conto di variazioni di ora UTC, impostare la data di inizio su un giorno precedente alla data corrente. Ad esempio, se la data corrente è il 6 ottobre 2014 selezionare 5/10/2014.

## <a name="generate-the-sas-url"></a>Generare l'URL SAS

L'URL di firma di accesso condiviso può essere generato in due modi comuni con gli strumenti seguenti:

-   Microsoft Azure Storage Explorer: strumento grafico disponibile per Windows, macOS e Linux
-   Interfaccia della riga di comando di Microsoft Azure: consigliato per sistemi operativi non Windows e per ambienti di integrazione automatizzata o continua


### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per generare un URI di firma di accesso condiviso con l'interfaccia della riga di comando di Azure, seguire questa procedura.

1. Scaricare e installare l'[interfaccia della riga di comando di Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Le versioni sono disponibili per Windows, macOS e diverse distribuzioni di Linux. 
2. Creare un file PowerShell (con estensione `.ps1`), copiarlo nel codice seguente e quindi salvarlo in locale.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. Modificare il file per specificare i valori dei parametri seguenti.  Le date devono essere specificate nel formato di data/ora UTC, ad esempio `10-25-2016T00:00:00Z`.
   - `<account-name>`: nome dell'account di archiviazione di Azure
   - `<account-key>`: chiave dell'account di archiviazione di Azure
   - `<vhd-name>`: nome del disco rigido virtuale
   - `<start-date>`: data e ora di inizio dell'autorizzazione per l'accesso al disco rigido virtuale. Specificare la data del giorno precedente rispetto alla data corrente. 
   - `<expiry-date>`: data e ora di scadenza dell'autorizzazione per l'accesso al disco rigido virtuale.  Specificare una data successiva di almeno tre settimane alla data corrente. 
 
   L'esempio seguente mostra i valori dei parametri appropriati (al momento della stesura di questo articolo).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. Salvare le modifiche a questo script di PowerShell.
5. Eseguire lo script con privilegi amministrativi per generare una *stringa di connessione della firma di accesso condiviso* per l'accesso a livello di contenitore.  È possibile usare due approcci di base:
   - Eseguire lo script dalla console.  In Windows, ad esempio, fare clic con il pulsante destro del mouse sullo script e quindi scegliere **Esegui come amministratore**.
   - Eseguire lo script in un editor di PowerShell, ad esempio [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), con privilegi amministrativi. 
     Di seguito viene illustrata una stringa di connessione della firma di accesso condiviso generata nell'editor. 

     ![Generazione di URI di firma di accesso condiviso in PowerShell ISE](./media/publishvm_032.png)

6. Copiare la stringa di connessione della firma di accesso condiviso risultante e salvarla in un file di testo in un percorso sicuro.  La stringa verrà modificata in seguito per aggiungere le informazioni sul percorso del disco rigido virtuale associato per creare l'URI di firma di accesso condiviso finale. 
7. Nel portale di Azure passare all'archiviazione BLOB che contiene il disco rigido virtuale associato all'URI generato.
8. Copiare il valore dell'URL di **Endpoint di servizio BLOB**, come illustrato di seguito.

    ![Endpoint del servizio BLOB nel portale di Azure](./media/publishvm_033.png)

9. Modificare il file di testo con la stringa di connessione della firma di accesso condiviso del passaggio 6.  L'URI di firma di accesso condiviso completo viene creato usando il formato seguente:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Se il nome del disco rigido virtuale è `TestRGVM2.vhd`, l'URI di firma di accesso condiviso risultante è il seguente:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ripetere questi passaggi per ogni disco rigido virtuale negli SKU che si prevede di pubblicare.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Usare la procedura seguente per generare un URI di firma di accesso condiviso con Microsoft Azure Storage Explorer.

1. Scaricare e installare [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Aprire lo strumento e nella barra dei menu a sinistra fare clic sull'icona **Aggiungi account**.  Viene visualizzata la finestra di dialogo **Connect to Azure Storage** (Connetti ad Archiviazione di Azure).
3. Selezionare **Add an Azure Account** (Aggiungi un account Azure) e fare clic su **Accedi**.  Continuare con la procedura necessaria per accedere all'account di Azure.
4. Nella parte sinistra del riquadro **Esplora** spostarsi su **Account di archiviazione** ed espandere questo nodo.
5. Fare clic con il pulsante destro del mouse sul disco rigido virtuale e quindi scegliere **Get Share Access Signature** (Ottieni firma di accesso condiviso) nel menu di scelta rapida. 

    ![Recupero della firma di accesso condiviso in Microsoft Azure Storage Explorer](./media/publishvm_034.png)

6. Viene visualizzata la finestra di dialogo **Firma di accesso condiviso**. Immettere i valori per i campi seguenti:
   - **Ora di inizio**: data e ora di inizio autorizzazione per l'accesso al disco rigido virtuale. Specificare una data precedente di un giorno rispetto alla data corrente.
   - **Ora di scadenza**: data e ora di scadenza per l'accesso al disco rigido virtuale.  Specificare una data successiva di almeno tre settimane alla data corrente.
   - **Autorizzazioni**: selezionare le autorizzazioni `Read` e `List`. 

     ![Finestra di dialogo per la firma di accesso condiviso in Microsoft Azure Storage Explorer](./media/publishvm_035.png)

7. Fare clic su **Crea** per creare l'URI di firma di accesso condiviso associato al disco rigido virtuale.  La finestra di dialogo visualizza i dettagli sull'operazione. 
8. Copiare il valore del campo **URL** e salvarlo in un file di testo in un percorso sicuro. 

    ![URI di firma di accesso condiviso creato in Microsoft Azure Storage Explorer](./media/publishvm_036.png)

    L'URL di firma di accesso condiviso è stato generato per l'accesso a livello di contenitore.  Per renderlo più specifico, è necessario aggiungervi il nome del disco rigido virtuale associato.

9. Modificare il file di testo. Inserire il nome del disco rigido virtuale dopo la stringa `vhds` nell'URL di firma di accesso condiviso (includere una barra iniziale).  Il formato dell'URI di firma di accesso condiviso deve essere il seguente:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Se il nome del disco rigido virtuale è `TestRGVM2.vhd`, l'URI di firma di accesso condiviso risultante è il seguente:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ripetere questi passaggi per ogni disco rigido virtuale negli SKU che si prevede di pubblicare.


## <a name="verify-the-sas-uri"></a>Verificare l'URI di firma di accesso condiviso

Esaminare e verificare ogni URI generato tramite  l'elenco di controllo seguente.
- L'URI deve essere nel formato:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- L'URI deve contenere il nome di file dell'immagine del disco rigido virtuale, inclusa l'estensione del nome di file "vhd".
- Al centro dell'URI è presente la stringa `sp=rl` che indica che è specificato l'accesso `Read` e `List`.
- Dopo questo punto, è presente la stringa `sr=c` che indica che l'accesso è specificato a livello di contenitore.
- Copiare e incollare l'URI in un browser per iniziare a scaricare il BLOB associato.  È possibile annullare l'operazione prima che il download venga completato.


## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi nella generazione di un URI di firma di accesso condiviso, vedere [Common SAS URL issues](./cpp-common-sas-url-issues.md) (Problemi comuni relativi agli URL di firma di accesso condiviso).  In caso contrario, salvare gli URI di firma di accesso condiviso in un percorso sicuro perché sono necessari in seguito per [pubblicare l'offerta di macchina virtuale](./cpp-publish-offer.md) nel portale Cloud Partner.
