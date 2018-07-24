---
title: Copiare dati su Microsoft Azure Data Box Disk | Microsoft Docs
description: Usare questa esercitazione per informazioni su come copiare i dati su Azure Data Box Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b0769ba70f495728df5c38b43bae4059b27de88b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010821"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Esercitazione: Copiare i dati su Azure Data Box Disk ed eseguire la verifica

Questa esercitazione descrive come copiare dati dal computer host e quindi generare i checksum per la verifica dell'integrità.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Copiare i dati sul Data Box Disk.
> * Verificare l'integrità dei dati.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:
- Sia stata completata l'[esercitazione: Installare e configurare Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- I dischi siano stati disimballati e attivati.
- Sia disponibile un computer host per copiare i dati sui dischi. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-disk-system-requirements.md).
    - Avere [Windows PowerShell 4 installato ](https://www.microsoft.com/download/details.aspx?id=40855).
    - Avere [.NET Framework 4.5 installato](https://www.microsoft.com/download/details.aspx?id=30653).


## <a name="copy-data-to-disks"></a>Copiare i dati sui dischi

Eseguire la procedura seguente per connettersi e copiare i dati dal computer sul Data Box Disk.

1. Visualizzare il contenuto dell'unità sbloccata. 

    ![Visualizzare il contenuto dell'unità](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copiare i dati che devono essere importati come BLOB in blocchi nella cartella BlockBlob. Allo stesso modo copiare i dati come VHD/VHDX nella cartella PageBlob. 

    Viene creato un contenitore nell'account di archiviazione di Azure per ogni sottocartella nelle cartelle BlockBlob e PageBlob. Tutti i file nelle cartelle BlockBlob e PageBlob vengono copiati in un contenitore predefinito `$root` sotto l'account di Archiviazione di Azure. Tutti i file nel contenitore `$root` vengono sempre caricati come BLOB in blocchi.

    Se nella directory radice esistono file e cartelle, è necessario spostarli in un'altra cartella prima di iniziare la copia dei dati.

    Seguire i requisiti di denominazione di Azure per i nomi di container e BLOB.

    |Entità   |Convenzioni  |
    |---------|---------|
    |Nomi di contenitori BLOB in blocchi e BLOB di pagine     |Devono iniziare con una lettera o un numero e possono contenere solo lettere minuscole, numeri e il trattino (-). Ogni trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. I trattini consecutivi non sono consentiti nei nomi. <br>Deve essere un nome DNS valido, da 3 a 63 caratteri.          |
    |Nomi di BLOB per blob in blocchi e blob di pagine    |I nomi di BLOB fanno distinzione tra maiuscole e minuscole e possono contenere una qualsiasi combinazione di caratteri. <br>La lunghezza di un nome di BLOB deve essere compresa tra 1 e 1.024 caratteri.<br>I caratteri URL riservati devono essere correttamente preceduti da un carattere di escape.<br>Il numero di segmenti di linea che includono il nome BLOB non può essere superiore a 254. Un segmento di linea è la stringa tra caratteri di delimitatore consecutivi, ad esempio, la barra rovesciata '/', che corrisponde al nome di una directory virtuale.         |

    > [!IMPORTANT] 
    > Tutti i contenitori e i BLOB devono essere conformi alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Se queste regole non vengono rispettate, il caricamento di dati in Azure avrà esito negativo.

3. Quando si copiano file assicurarsi che la dimensione non superi ~4,7 TiB per i BLOB in blocchi e ~8 TiB per i BLOB di pagine. 
4. È possibile usare il trascinamento della selezione con Esplora file per copiare i dati. È anche possibile usare qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy per copiare i dati. È possibile avviare più processi di copia usando il seguente comando di Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    I parametri e le opzioni del comando sono riportati nella tabella seguente:
    
    |Parametri/opzioni  |DESCRIZIONE |
    |--------------------|------------|
    |<Source>            | Specifica il percorso della directory di origine.        |
    |<Destination>       | Specifica il percorso della directory di destinazione.        |
    |/E                  | Copia le sottodirectory, incluse le directory vuote. |
    |/MT[:N]             | Crea copie multi-thread con N thread, dove N è un numero intero compreso tra 1 e 128. <br>Il valore predefinito per N è 8.        |
    |/R: <N>             | Specifica il numero di tentativi per le copie non riuscite. Il valore predefinito di N è 1.000.000 (un milione di tentativi).        |
    |/W: <N>             | Specifica il tempo di attesa tra i tentativi, in secondi. Il valore predefinito di N è 30 (tempo di attesa di 30 secondi).        |
    |/NFL                | Specifica che i nomi dei file non devono essere inseriti nei log.        |
    |/NDL                | Specifica che i nomi di directory non devono essere inseriti nei log.        |
    |/FFT                | Presuppone i tempi dei file FAT (precisione di due secondi).        |
    |/Log:<Log File>     | Scrive l'output di stato nel file di log sovrascrivendo il file di log esistente.         |

    È possibile usare più dischi in parallelo con più processi in esecuzione su ogni disco. 

6. Controllare lo stato della copia quando il processo è in corso. L'esempio seguente mostra l'output del comando robocopy per copiare i file sul Data Box Disk.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. Aprire la cartella di destinazione per visualizzare e verificare i file copiati. In caso di errori durante il processo di copia, scaricare i file di log per la risoluzione dei problemi. I file di log si trovano nella posizione specificata nel comando robocopy.
 


> [!IMPORTANT]
> - È responsabilità dell'utente assicurarsi di copiare i dati nelle cartelle corrispondenti al formato dati appropriato. Ad esempio, copiare i dati del BLOB in blocchi nella cartella per i BLOB in blocchi. Se il formato dei dati non corrisponde alla cartella appropriata (tipo di archiviazione), il caricamento dei dati in Azure avrà negativo.
> -  Durante la copia dei dati assicurarsi che la dimensione dei dati sia conforme ai valori descritti nei [limiti delle risorse di archiviazione e del Data Box Disk in Azure](data-box-disk-limits.md). 
> - Se i dati caricati dal Data Box Disk vengono caricati contemporaneamente da altre applicazioni all'esterno del Data Box Disk, è possibile che si verifichino errori del processo di caricamento e il danneggiamento di dati.

## <a name="verify-data-integrity"></a>Verificare l'integrità dei dati.

Eseguire la procedura seguente per verificare l'integrità dei dati.

1. Eseguire `AzureExpressDiskService.ps1` per la convalida di checksum. In Esplora file passare alla cartella *AzureImportExport* dell'unità. Fare clic con il pulsante destro del mouse e selezionare **Run with PowerShell** (Esegui con PowerShell). 

    ![Eseguire il checksum](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. A seconda della dimensione dei dati, questo passaggio potrebbe richiedere del tempo. Al completamento dello script vengono visualizzati un riepilogo del processo di verifica dell'integrità dei dati e il tempo necessario per completare il processo. È possibile premere **Invio** per uscire dalla finestra di comando.

    ![Output del checksum](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. Se si usano più dischi, eseguire il comando per ciascun disco.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Copiare i dati sul Data Box Disk.
> * Verificare l'integrità dei dati.

Passare all'esercitazione successiva per informazioni su come restituire il Data Box Disk e verificare il caricamento dei dati in Azure.

> [!div class="nextstepaction"]
> [Spedizione di Azure Data Box a Microsoft](./data-box-disk-deploy-picked-up.md)

