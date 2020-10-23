---
title: Esercitazione per copiare dati tramite SMB dal dispositivo Azure Data Box | Microsoft Docs
description: Informazioni su come copiare dati in Azure Data Box tramite SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fb1415874c42e3913d98d4a674732a4d9b98a0c5
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123909"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Esercitazione: Copiare i dati dal dispositivo Azure Data Box tramite SMB (anteprima)

Questa esercitazione descrive come connettersi al computer host e copiare i dati dal dispositivo Data Box in un server locale usando l'interfaccia utente Web locale. Il dispositivo Data Box contiene i dati esportati dall'account di archiviazione di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati da Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Sia stato inserito l'ordine per Azure Data Box.
    - Per un ordine di importazione, vedere [Esercitazione: Ordinare Azure Data Box](data-box-deploy-ordered.md).
    - Per un ordine di esportazione, vedere [Esercitazione: Ordinare Azure Data Box](data-box-deploy-export-ordered.md).
2. Aver ricevuto Data Box e che lo stato dell'ordine nel portale sia **Recapitati**.
3. Sia disponibile un computer host in cui copiare i dati dal dispositivo Data Box. Il computer host deve:
   * Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
   * Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a 10 GbE, è possibile usare un collegamento dati a 1 GbE, ma la velocità delle operazioni di copia ne risentirà.

## <a name="connect-to-data-box"></a>Connettersi al Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Se si usa un computer host Windows Server, eseguire le operazioni seguenti per connettersi a Data Box.

1. Innanzitutto è necessario autenticare e avviare una sessione. Selezionare **Connetti e copia**. Selezionare **Ottieni credenziali** per ottenere le credenziali di accesso per le condivisioni associate all'account di archiviazione. 

    ![Ottenere le credenziali delle condivisioni](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. Nella finestra di dialogo Accedi alla condivisione e copia i dati copiare il **nome utente** e la **password** corrispondenti alla condivisione. Selezionare **OK**.
    
    ![Ottenere le credenziali delle condivisioni 2](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Per accedere alle condivisioni associate all'account di archiviazione (*exportbvtdataset2* nell'esempio seguente) dal computer host, aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    A seconda del formato dei dati, i percorsi delle condivisioni sono i seguenti:
    - BLOB in blocchi di Azure: `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - BLOB di pagine di Azure: `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - File di Azure: `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Quando richiesto, immettere la password della condivisione. L'esempio seguente mostra la connessione a una condivisione tramite il comando precedente.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

4. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>`. Scegliere **OK** per aprire Esplora file.
    
    ![Connettersi a una condivisione tramite Esplora file](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Le condivisioni verranno ora visualizzate come cartelle.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Se si usa un client Linux, usare il comando seguente per montare la condivisione SMB. Il parametro "vers" di seguito è la versione di SMB supportata dall'host Linux. Collegare la versione appropriata nel comando seguente. Per le versioni di SMB supportate da Data Box,vedere [File system supportati per client Linux](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Copiare i dati da Data Box

Dopo aver stabilito la connessione alle condivisioni Data Box, il passaggio successivo consiste nel copiare i dati.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Dopo aver stabilito la connessione alla condivisione SMB, avviare la copia dei dati. È possibile usare qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy, per copiare i dati. È possibile avviare più processi di copia tramite Robocopy. 


Per altre informazioni sul comando Robocopy, vedere [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy e alcuni esempi).

Al termine della copia, passare a **Dashboard** e verificare lo spazio occupato e lo spazio disponibile nel dispositivo.

È ora possibile procedere con la spedizione del dispositivo Data Box a Microsoft.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati da Data Box

Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-export-picked-up.md)