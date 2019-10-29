---
title: "Esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB - Archiviazione di Azure"
description: Informazioni su come configurare un account di archiviazione per l'hosting di siti Web statici e come distribuire un sito Web statico in Archiviazione di Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: cd3db902d713910de5a8199df85089d62569f9d7
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757558"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB

Questa è la prima di una serie di esercitazioni. Qui si apprenderà come creare e distribuire un sito Web statico in Archiviazione di Azure. Al termine, sarà presente un sito Web statico cui gli utenti potranno accedere a livello pubblico. 

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Configurare l'hosting di siti Web statici
> * Distribuire un sito Web Hello World

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Assicurarsi di creare un account di archiviazione per utilizzo generico v2. I siti Web statici non sono disponibili in nessun altro tipo di account di archiviazione.

Questa esercitazione usa [Visual Studio Code](https://code.visualstudio.com/download), uno strumento gratuito per i programmatori che permette di creare il sito Web statico e di distribuirlo in un account di archiviazione di Azure.

Dopo aver installato Visual Studio Code, installare l'estensione di anteprima Archiviazione di Azure. Questa estensione integra funzionalità di gestione di Archiviazione di Azure con Visual Studio Code. L'estensione verrà usata per distribuire il sito Web statico in Archiviazione di Azure. Per installare l'estensione:

1. Avviare Visual Studio Code.
2. Sulla barra degli strumenti fare clic su **Estensioni**. Cercare *Archiviazione di Azure* e selezionare l'estensione **Archiviazione di Azure** nell'elenco. Fare clic sul pulsante **Installa** per scaricare e installare l'estensione

    ![Installare l'estensione Archiviazione di Azure in Visual Studio Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/) per iniziare.

## <a name="configure-static-website-hosting"></a>Configurare l'hosting di siti Web statici

Il primo passaggio consiste nel configurare l'account di archiviazione per ospitare un sito Web statico nel portale di Azure. Quando si configura l'account per l'hosting di siti Web statici, Archiviazione di Azure crea automaticamente un contenitore denominato *$web*. Il contenitore *$web* includerà i file per il sito Web statico. 

1. Nel Web browser aprire il [portale di Azure](https://portal.azure.com/). 
1. Individuare l'account di archiviazione e visualizzare la sezione della panoramica dell'account.
1. Selezionare **Sito Web statico** per visualizzare la pagina di configurazione per i siti Web statici.
1. Selezionare **Abilitato** per abilitare l'hosting di siti Web statici per l'account di archiviazione.
1. Nel campo **Nome del documento di indice** specificare una pagina di indice predefinita denominata *index.html*. La pagina di indice predefinita viene visualizzata quando un utente passa alla radice del sito Web statico.  
1. Nel campo **Percorso del documento di errore** specificare una pagina di errore predefinita denominata *404.html*. La pagina di errore predefinita viene visualizzata quando un utente tenta di passare a una pagina inesistente nel sito Web statico.
1. Fare clic su **Save**. Il portale di Azure visualizza ora l'endpoint del sito Web statico. 

    ![Abilitare l'hosting di siti Web statici per un account di archiviazione](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Distribuire un sito Web Hello World

Creare quindi una pagina Web Hello World con Visual Studio Code e distribuirla nel sito Web statico ospitato nell'account di archiviazione di Azure.

1. Creare una cartella vuota denominata *mywebsite* nel file system locale. 
1. Avviare Visual Studio Code e aprire la cartella appena creata dalla **finestra di esplorazione**.

    ![Aprire la cartella in Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Creare il file di indice predefinito nella cartella *mywebsite* e assegnare al file il nome *index.html*.

    ![Creare il file di indice predefinito in Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Aprire *index.html* nell'editor, incollare il testo seguente nel file e salvarlo:

    ```
    <h1>Hello World!</h1>
    ```

1. Creare il file di errore predefinito e assegnare al file il nome *404.html*.
1. Aprire *404.html* nell'editor, incollare il testo seguente nel file e salvarlo:

    ```
    <h1>404</h1>
    ```

1. Fare clic con il pulsante destro del mouse nella cartella *mywebsite* nella **finestra di esplorazione** e scegliere **Distribuisci in sito Web statico** per distribuire il sito Web. Verrà chiesto di accedere ad Azure per recuperare un elenco di sottoscrizioni.

1. Selezionare la sottoscrizione contenente l'account di archiviazione per cui è stato abilitato l'hosting di siti Web statici. Selezionare quindi l'account di archiviazione quando viene richiesto.

Visual Studio Code caricherà i file nell'endpoint Web e visualizzerà la barra di stato con esito positivo. Avviare il sito Web per visualizzarlo in Azure.

![Visualizzare la distribuzione del sito Web statico in Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

L'esercitazione è stata completata correttamente ed è stato distribuito un sito Web statico in Azure.

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte di questa esercitazione si è appreso a configurare l'account di archiviazione di Azure per l'hosting di siti Web statici e come distribuire un sito Web statico in un endpoint di Azure.

Passare ora alla seconda parte, in cui si configurerà un dominio personalizzato con SSL per il sito Web statico con la rete CDN di Azure.

> [!div class="nextstepaction"]
> [Usare la rete CDN di Azure per abilitare un dominio personalizzato con SSL per un sito Web statico](storage-blob-static-website-custom-domain.md)
