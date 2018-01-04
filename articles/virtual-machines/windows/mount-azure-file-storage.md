---
title: Montare l'archiviazione file di Azure da una macchina virtuale Windows di Azure | Microsoft Docs
description: Archiviare i file nel cloud con l'archiviazione file di Azure e montare la condivisione file nel cloud da una macchina virtuale (VM) Azure.
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Usare le condivisioni file di Azure con le macchine virtuali Windows 

Le condivisioni file di Azure possono essere usate per archiviare file e accedervi da una macchina virtuale. Ad esempio, è possibile archiviare uno script o il file di configurazione di un'applicazione che si intende condividere con tutte le macchine virtuali. In questo articolo è illustrato come creare e montare una condivisione di file di Azure e illustrato come caricare e scaricare file.

## <a name="connect-to-a-file-share-from-a-vm"></a>Connettersi a una condivisione file da una macchina virtuale

In questa sezione si presuppone che la condivisione file a cui si desidera connettersi sia già stata creata. Se è necessario crearne uno, vedere [creare una condivisione file](#create-a-file-share) più avanti in questo articolo.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Account di archiviazione** nel menu a sinistra.
3. Scegliere l'account di archiviazione.
4. Nella pagina **Panoramica** selezionare **File** dalla sezione **Servizi**.
5. Selezionare una condivisione file oppure fare clic su **+ condivisione File** per creare una nuova condivisione di file da utilizzare.
6. Fare clic su **Connetti** per aprire una pagina in cui viene visualizzata la sintassi della riga di comando per montare la condivisione file da Windows o Linux.
7. In **lettera di unità**, selezionare la lettera che si desidera utilizzare per identificare l'unità.
8. Scegliere la sintassi da utilizzare e quindi selezionare il pulsante Copia a destra per copiarlo negli Appunti. Incollarlo in luoghi in cui è possibile accedervi facilmente. 
8. Connettersi alla macchina virtuale e aprire un prompt dei comandi.
9. Incollare la sintassi di connessione modificata e premere **INVIO**.
10. Quando la connessione è stata creata, verrà visualizzato il messaggio **Esecuzione comando riuscita.**
11. Verificare la connessione digitando la lettera di unità per passare all'unità e quindi digitare **dir** per vedere il contenuto della condivisione file.



## <a name="create-a-file-share"></a>Creare una condivisione file 
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Account di archiviazione** nel menu a sinistra.
3. Scegliere l'account di archiviazione.
4. Nella pagina **Panoramica** selezionare **File** dalla sezione **Servizi**.
5. Nella pagina del servizio di File, fare clic su **+ condivisione File**.
6. Immettere il nome della condivisione file. I nomi delle condivisioni file possono contenere lettere minuscole, numeri e trattini singoli. Il nome non può iniziare con un trattino e non sono consentiti più trattini consecutivi. 
7. Immettere un limite per le dimensioni del file, fino a 5120 GB.
8. Fare clic su **OK** per creare la condivisione di file.
   
## <a name="upload-files"></a>Caricare file
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Account di archiviazione** nel menu a sinistra.
3. Scegliere l'account di archiviazione.
4. Nella pagina **Panoramica** selezionare **File** dalla sezione **Servizi**.
5. Selezionare una condivisione file.
6. Fare clic su **Carica** per aprire la pagina **Carica file**.
7. Fare clic sull'icona della cartella per individuare nel file system locale il file da caricare.   
8. Fare clic su **Carica** per caricare il file nella condivisione file.

## <a name="download-files"></a>Download dei file
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Account di archiviazione** nel menu a sinistra.
3. Scegliere l'account di archiviazione.
4. Nella pagina **Panoramica** selezionare **File** dalla sezione **Servizi**.
5. Selezionare una condivisione file.
6. Fare clic con il pulsante destro del mouse sul file e scegliere **Scarica** per scaricarlo nel computer locale.
   

## <a name="next-steps"></a>Passaggi successivi

Le condivisioni file possono essere create e gestite anche usando PowerShell. Per altre informazioni, vedere [Introduzione ad Archiviazione file di Azure in Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
