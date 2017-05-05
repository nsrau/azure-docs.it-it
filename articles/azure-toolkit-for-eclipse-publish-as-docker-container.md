---
title: Pubblicare un contenitore Docker usando il Toolkit di Azure per Eclipse | Microsoft Docs
description: Informazioni su come pubblicare un&quot;app Web in Microsoft Azure come contenitore Docker usando il Toolkit di Azure per Eclipse.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f738647fa1dad757b53611c7d4abe2dd99fe8838
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Come pubblicare un'app Web come contenitore Docker usando il Toolkit di Azure per Eclipse

I contenitori Docker rappresentano un metodo ampiamente usato per la distribuzione di applicazioni Web in cui gli sviluppatori possono consolidare tutti i file di progetto e le dipendenze in un unico pacchetto per la distribuzione in un server. Il Toolkit di Azure per Eclipse semplifica questo processo per gli sviluppatori Java aggiungendo le funzionalità di *pubblicazione come contenitore Docker* per la distribuzione in Microsoft Azure e questo articolo illustra i passaggi necessari per pubblicare le applicazioni in Azure come contenitori Docker.

> [!NOTE]
>
> Altre informazioni su Docker sono disponibili nel [sito Web Docker].
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Pubblicazione dell'app Web in Azure usando un contenitore Docker

1. Aprire il progetto dell'applicazione Web in Eclipse.

1. Per avviare la procedura guidata Publish as Docker Container (Pubblica come contenitore Docker), usare uno dei metodi seguenti:

   * Fare clic con il pulsante destro del mouse sul progetto nella visualizzazione **Navigator** (Navigatore), fare clic su **Azure** e quindi fare clic su **Publish as Docker Container** (Pubblica come contenitore Docker):  ![Pubblica come contenitore Docker][PUB01]

   * Sulla barra degli strumenti di Eclipse fare clic con il pulsante destro del mouse sul menu **Publish** (Pubblica) e quindi scegliere **Publish as Docker Container**(Pubblica come contenitore Docker):  ![Publish as Docker Container][PUB02] (Pubblica come contenitore Docker)

1. Quando viene visualizzata la procedura guidata **Deploy Docker Container on Azure** (Distribuire un contenitore Docker in Azure), verrà visualizzata una finestra di dialogo analoga all'immagine seguente: ![Deploy Docker Container on Azure Wizard][PUB03] (Procedura guidata Distribuire un contenitore Docker in Azure)

   a. Immettere un nome univoco nella casella di testo per l'host Docker nella casella di testo **Docker image name** (Nome immagine Docker). La procedura guidata creerà automaticamente un nome per l'utente, che è comunque possibile modificare in seguito.

   b. Nella finestra **Host** verranno visualizzati tutti gli host Docker creati.
      * Se non è presente alcun host Docker, questa sezione della finestra di dialogo è vuota.
      * Se è già stato creato un host Docker, è possibile scegliere di distribuire l'app Web a un host esistente. In caso contrario, seguire questa procedura per creare un nuovo host Docker.

1. Per creare un nuovo host Docker, fare clic sul pulsante **Add** (Aggiungi). Verrà visualizzata la finestra di dialogo **Create Docker Host** (Crea host Docker).
      ![Deploy Docker Container on Azure Wizard](Distribuire un contenitore Docker in Azure)[PUB04]

   a. Specificare le opzioni seguenti per l'host Docker. La procedura guidata genera automaticamente la maggior parte delle opzioni, ma è possibile modificare le opzioni che si intende personalizzare.

      * **Name** (Nome): nome univoco per l'host Docker. Questo non è lo stesso nome di immagine Docker specificato in precedenza.

      * **Subscription** (Sottoscrizione): specifica la sottoscrizione di Azure che verrà usata per l'host.
      
      * **Region** (Area): specifica l'area geografica in cui sarà situato l'host.
      
      * Nella scheda **OS and Size** (Sistema operativo e dimensioni) specificare le opzioni seguenti:
         * **Host OS** (Sistema operativo host): specifica il sistema operativo per la macchina virtuale in cui sarà presente l'host.
         * **Size** (Dimensioni): specifica la macchina virtuale per l'host.

      * Nella scheda **Resource Group** (Gruppo di risorse) specificare le opzioni seguenti:
         * **New resource group** (Nuovo gruppo di risorse): consente di creare un nuovo gruppo di risorse per l'host.
         * **Existing resource group** (Gruppo di risorse esistente): consente di specificare un gruppo di risorse nel proprio account Azure.

      * Nella scheda **Network** (Rete) specificare le opzioni seguenti:
         * **New virtual network** (Nuova rete virtuale): consente di creare una nuova rete virtuale per l'host.
         * **Existing virtual network** (Rete virtuale esistente): consente di specificare una rete virtuale esistente nel proprio account Azure.

      * Nella scheda **Storage** (Archiviazione) specificare le opzioni seguenti:
         * **New storage account** (Nuovo account di archiviazione): consente di creare un nuovo account di archiviazione per l'host.
         * **Existing storage account** (Account di archiviazione esistente): consente di specificare un account di archiviazione esistente nel proprio account Azure.

   b. Dopo aver specificato le opzioni precedenti, fare clic su **Next** (Avanti).

   c. Scegliere una delle opzioni seguenti per l'accesso a macchine virtuali dell'host di Docker:    ![Create Docker Host][PUB05] (crea host Docker)

      * **Import credentials from Azure Key Vault** (Importa credenziali da Azure Key Vault): consente di specificare un set di credenziali salvato in precedenza e archiviato nella sottoscrizione di Azure.

      > [!NOTE]
      > Un Azure Key Vault creato con un'entità servizio o un account specifico non sarà automaticamente accessibile da un altro account o entità servizio che condivide la stessa sottoscrizione. Per consentire a un altro account oppure a un'altra entità servizio di usare il Key Vault, è necessario usare il portale di Azure per aggiungere l'account o l'entità servizio.

      * **New log in credentials** (Nuove credenziali di accesso): consente di creare un nuovo set di credenziali di accesso per cui è necessario specificare le opzioni seguenti nella scheda **VM Credentials** (Credenziali macchina virtuale).
         * **Username** (Nome utente): specifica il nome utente per accedere alla macchina virtuale.
         * **Password** e **Confirm** (Conferma): specifica la password per accedere alla macchina virtuale.
         * **SSH**: specifica le impostazioni del protocollo SSH (Secure Shell) per l'host Docker. È possibile scegliere una delle opzioni seguenti:
            * **None** (Nessuna): specifica che la macchina virtuale non consentirà connessioni SSH.
            * **Auto-generate** (Genera automaticamente): consente di creare le impostazioni necessarie per la connessione tramite SSH.
            * **Import from directory** (Importa da directory): consente di specificare una directory che contiene un set di impostazioni SSH salvate in precedenza. In particolare, la directory deve contenere i due file seguenti:
               * *id_rsa*: contiene l'identificazione RSA per un utente.
               * *id_rsa.pub*: contiene la chiave pubblica RSA usata per l'autenticazione.
        
      * Nella scheda **Docker Daemon Access** (Accesso daemon Docker) specificare le opzioni seguenti: ![Create Docker Host][PUB06] (Crea host Docker)

         * **Docker Daemon port** (Porta Daemon Docker): specifica la porta TCP univoca per l'host Docker.
         * **TLS Security** (Sicurezza TLS): specifica le impostazioni TLS per l'host Docker. È possibile scegliere una delle opzioni seguenti:
            * **None** (Nessuna): specifica che la macchina virtuale non consentirà connessioni TLS.
            * **Auto-generate** (Genera automaticamente): consente di creare le impostazioni necessarie per la connessione tramite TLS.
            * **Import from directory** (Importa da directory): consente di specificare una directory che contiene un set di impostazioni TLS salvate in precedenza. In particolare, la directory deve contenere i sei file seguenti:
               * *ca.pem* e *ca-key.pem*: contengono il certificato e la chiave pubblica per l'Autorità di certificazione TLS.
               * *cert.pem* e *key.pem*: contengono il certificato client e la chiave pubblica che verranno usati per l'autenticazione TLS.
               * *server.pem* e *server-key.pem*: contengono il certificato server e la chiave pubblica per l'host.

   d. Dopo aver immesso tutte le opzioni precedenti, fare clic su **Finish** (Fine).

1. Quando la procedura guidata **Deploy Docker Container on Azure** (Distribuire un contenitore Docker in Azure) viene nuovamente visualizzata, fare clic su **Next** (Avanti).
   ![Deploy Docker Container on Azure Wizard][PUB07] (Procedura guidata Distribuire un contenitore Docker in Azure)

1. Nell'ultima schermata della procedura guidata specificare le opzioni seguenti:

   * **Docker container name** (Nome contenitore Docker): nome univoco per il contenitore Docker.

   * Scegliere una delle immagini Docker seguenti:

      * **Predefined Docker image** (Immagine Docker predefinita): consente di specificare un'immagine pre-esistente in Azure.

      > [!NOTE]
      > L'elenco di immagini Docker in questo menu a discesa è costituito da diverse immagini per cui è stato configurato il Toolkit di Azure in modo che l'elemento venga distribuito automaticamente.

      * **Custom Dockerfile** (Dockerfile personalizzato): consente di specificare un Dockerfile salvato in precedenza nel computer locale.

      > [!NOTE]
      > Questa è una funzionalità più avanzata per gli sviluppatori che intendono distribuire i propri Dockerfile. È tuttavia compito degli sviluppatori che usano questa opzione garantire che i propri Dockerfile vengano compilati correttamente. Il Toolkit di Azure non esegue la convalida del contenuto in un Dockerfile personalizzato e di conseguenza la distribuzione può non riuscire se nel Dockerfile si verificano problemi. Il Toolkit di Azure prevede anche che il Dockerfile personalizzato contenga un elemento di app Web e che tenti di aprire una connessione HTTP. Se gli sviluppatori pubblicano un diverso tipo di elemento, potrebbero ricevere errori innocui dopo la distribuzione.

   * **Port settings** (Impostazioni porta): specifica il binding porta TCP per il contenitore Docker.
   ![Deploy Docker Container on Azure Wizard][PUB08] (Procedura guidata Distribuire un contenitore Docker in Azure)

1. Dopo aver completato tutti i passaggi precedenti, fare clic su **Finish** (Fine).

Il Toolkit di Azure inizierà la distribuzione di un'app Web in Azure in un contenitore Docker. 

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Novità di Azure Toolkit per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse]
  * [Creare un'app Web Hello World per Azure in Eclipse]
* [Toolkit di Azure per IntelliJ]
  * [Novità del Toolkit di Azure per IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Istruzioni di accesso ad Azure per il Toolkit di Azure per IntelliJ]
  * [Creare un'App Web Hello World per Azure in IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per altre risorse per Docker, vedere il [sito Web Docker] ufficiale.

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Istruzioni di accesso ad Azure per il Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/

[sito Web Docker]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png

