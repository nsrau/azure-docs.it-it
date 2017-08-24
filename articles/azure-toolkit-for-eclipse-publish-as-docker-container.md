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
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 746bb0a073396b84fa8592adda6748a2a5692ee8
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---

# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse"></a>Pubblicare un'app Web come contenitore Docker usando il Toolkit di Azure per Eclipse

I contenitori Docker sono un metodo molto diffuso per la distribuzione di applicazioni Web. L'uso dei contenitori Docker permette agli sviluppatori di consolidare tutti i file di progetto e le dipendenze in un unico pacchetto per la distribuzione in un server. Il Toolkit di Azure per Eclipse semplifica questo processo per gli sviluppatori Java aggiungendo la funzionalità *Publish as Docker Container* (Pubblica come contenitore Docker) per la distribuzione in Microsoft Azure. Questo articolo illustra la procedura da seguire per pubblicare le applicazioni in Azure come contenitori Docker.

> [!NOTE]
> Altre informazioni su Docker sono disponibili nel [sito Web Docker].
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Pubblicare l'app Web in Azure usando un contenitore Docker

1. Aprire il progetto dell'applicazione Web in Eclipse.

2. Per avviare la procedura guidata **Publish as Docker Container** (Pubblica come contenitore Docker), eseguire una delle operazioni seguenti:

   * Nella vista **Navigator** (Strumento di navigazione) fare clic con il pulsante destro del mouse sul progetto, fare clic su **Azure** e quindi fare clic su **Publish as Docker Container** (Pubblica come contenitore Docker).

      ![Comando Publish as Docker Container (Pubblica come contenitore Docker) nella vista Navigator (Strumento di navigazione)][PUB01]

   * Sulla barra degli strumenti di Eclipse fare clic sul pulsante **Publish** (Pubblica) e quindi fare clic su **Publish as Docker Container** (Pubblica come contenitore Docker).

      ![Comando Publish as Docker Container (Pubblica come contenitore Docker) sulla barra degli strumenti di Eclipse][PUB02]
      
    Verrà visualizzata la procedura guidata **Deploy Docker Container on Azure** (Distribuisci contenitore Docker in Azure).

    ![Procedura guidata Deploy Docker Container on Azure (Distribuisci contenitore Docker in Azure)][PUB03]

3. Nella finestra **Type an image name, select the artifact's path and check a Docker host to be used** (Digitare un nome immagine, scegliere il percorso dell'elemento e selezionare un host Docker da usare) seguire questa procedura:

    a. Nella casella **Docker image name** (Nome immagine Docker) immettere un nome univoco per l'host Docker. La procedura guidata crea automaticamente un nome, ma è possibile modificarlo.

    b. Nell'area **Hosts** (Host) vengono visualizzati tutti gli host Docker già creati. Eseguire una di queste operazioni:

    * Se è disponibile un host Docker esistente, è possibile distribuirvi l'app Web.
    * Per creare un nuovo host Docker, fare clic su **Aggiungi**.  
      
    Verrà visualizzata la finestra di dialogo **Create Docker Host** (Crea host Docker).

    ![Procedura guidata Deploy Docker Container on Azure (Distribuisci contenitore Docker in Azure)][PUB04a]

4. Nella finestra **Configure the new virtual machine** (Configura la nuova macchina virtuale) specificare le opzioni seguenti per l'host Docker. La procedura guidata genera automaticamente la maggior parte delle opzioni, ma è possibile modificarle.

   a. **Name** (Nome): immettere un nome univoco per l'host Docker. Questo non è lo stesso nome immagine Docker specificato in precedenza.

   b. **Subscription** (Sottoscrizione): immettere la sottoscrizione di Azure usata per l'host.

   c. **Region** (Area): immettere l'area geografica in cui si trova l'host.

   d. Nella scheda **Host OS and Size** (Sistema operativo e dimensioni host) specificare le opzioni seguenti:
     * **Host OS** (Sistema operativo host): immettere il sistema operativo della macchina virtuale in cui è presente l'host.
     * **Size** (Dimensioni): immettere le dimensioni della macchina virtuale per l'host.

   e. Nella scheda **Resource Group** (Gruppo di risorse) specificare le opzioni seguenti:
     * **New resource group** (Nuovo gruppo di risorse): creare un nuovo gruppo di risorse per l'host.
     * **Existing resource group** (Gruppo di risorse esistente): immettere un gruppo di risorse dal proprio account Azure.

   f. Nella scheda **Network** (Rete) specificare le opzioni seguenti:
     * **New virtual network** (Nuova rete virtuale): creare una nuova rete virtuale per l'host.
     * **Existing virtual network** (Rete virtuale esistente): immettere una rete virtuale esistente dal proprio account Azure.

   g. Nella scheda **Storage** (Archiviazione) specificare le opzioni seguenti:
     * **New storage account** (Nuovo account di archiviazione): creare un nuovo account di archiviazione per l'host.
     * **Existing storage account** (Account di archiviazione esistente): immettere un account di archiviazione esistente dal proprio account Azure.

5. Fare clic su **Avanti**.

6. Nella finestra **Configure log in credentials and port settings** (Configurare le credenziali di accesso e le impostazioni delle porte) selezionare una delle opzioni seguenti:

    * **Import credentials from Azure Key Vault** (Importa credenziali da Azure Key Vault): specifica un set di credenziali salvato in precedenza e archiviato nella sottoscrizione di Azure.

      >[!NOTE]
      >Un Azure Key Vault creato con un'entità servizio o un account specifico non è automaticamente accessibile da un altro account o entità servizio che condivide la sottoscrizione. Per consentire a un altro account oppure a un'altra entità servizio di usare il Key Vault, è necessario usare il portale di Azure per aggiungere l'account o l'entità servizio.

    * **New log in credentials** (Nuove credenziali di accesso): crea un nuovo set di credenziali di accesso. Se si seleziona questa opzione, seguire questa procedura:
    
      * Nella scheda **VM Credentials** (Credenziali VM) scegliere una delle opzioni seguenti per l'accesso a macchine virtuali dell'host di Docker:

          * **Username** (Nome utente): immettere il nome utente per le credenziali di accesso alla macchina virtuale.
          * **Password** e **Confirm** (Conferma): immettere la password per le credenziali di accesso alla macchina virtuale.
          * **SSH**: immettere le impostazioni Secure Shell (SSH) per l'host Docker. È possibile scegliere tra le opzioni seguenti:
            * **None** (Nessuna): specifica che la macchina virtuale non consentirà connessioni SSH.
            * **Auto-generate** (Genera automaticamente): crea automaticamente le impostazioni necessarie per la connessione tramite SSH.
            * **Import from directory** (Importa da directory): specifica una directory che contiene un set di impostazioni SSH salvate in precedenza. La directory deve contenere i due file seguenti:
                * *id_rsa*: contiene l'identificazione RSA per un utente.
                * *id_rsa.pub*: contiene la chiave pubblica RSA usata per l'autenticazione.
        
        ![Creare un host Docker][PUB05]

      * Nella scheda **Docker Daemon Credentials** (Credenziali daemon Docker) specificare le opzioni seguenti:

          * **Docker Daemon port** (Porta Daemon Docker): immettere la porta TCP univoca per l'host Docker.
          * **Sicurezza TLS**: immettere le impostazioni Transport Layer Security per l'host Docker. È possibile scegliere tra le opzioni seguenti:
            * **None** (Nessuna): specifica che la macchina virtuale non consentirà connessioni TLS.
            * **Auto-generate** (Genera automaticamente): crea automaticamente le impostazioni necessarie per la connessione tramite TLS.
            * **Import from directory** (Importa da directory): specifica una directory che contiene un set di impostazioni TLS salvate in precedenza. In particolare, la directory deve contenere i sei file seguenti:
                * *ca.pem* e *ca-key.pem*: contengono il certificato e la chiave pubblica per l'Autorità di certificazione TLS.
                * *cert.pem* e *key.pem*: contengono il certificato client e la chiave pubblica usati per l'autenticazione TLS.
                * *server.pem* e *server-key.pem*: contengono il certificato server e la chiave pubblica per l'host.

        ![Creare un host Docker][PUB06]

7. Dopo aver immesso tutte le informazioni indicate in precedenza, fare clic su **Finish** (Fine).

8. Nella procedura guidata **Deploy Docker Container on Azure** (Distribuisci contenitore Docker in Azure) fare clic su **Next** (Avanti).

   ![Procedura guidata Deploy Docker Container on Azure (Distribuisci contenitore Docker in Azure)][PUB07]

9. Nella finestra **Configure the Docker container to be created** (Configurare il contenitore Docker da creare) eseguire le operazioni seguenti:

   a. Nella casella **Docker container name** (Nome contenitore Docker) immettere un nome univoco per il contenitore Docker.

   b. Scegliere una delle immagini Docker seguenti:
     * **Predefined Docker image** (Immagine Docker predefinita): specifica un'immagine preesistente in Azure.

       >[!NOTE]
       >L'elenco di immagini Docker in questa casella è costituito da diverse immagini per cui il Toolkit di Azure è stato configurato per l'applicazione automatica delle patch, in modo che l'elemento venga distribuito automaticamente.

     * **Custom Dockerfile** (Dockerfile personalizzato): specifica un Dockerfile salvato in precedenza nel computer locale.

       >[!NOTE]
       >Questa è una funzionalità più avanzata per gli sviluppatori che intendono distribuire i propri Dockerfile. È tuttavia compito degli sviluppatori che usano questa opzione garantire che i propri Dockerfile vengano compilati correttamente. Il Toolkit di Azure non esegue la convalida del contenuto in un Dockerfile personalizzato e di conseguenza la distribuzione potrebbe non riuscire se nel Dockerfile si verificano problemi. Inoltre, il Toolkit di Azure prevede che il Dockerfile personalizzato contenga un elemento app Web e prova quindi ad aprire una connessione HTTP. Pubblicando un diverso tipo di elemento, gli sviluppatori potrebbero ricevere errori non gravi dopo la distribuzione.

   c. **Port settings** (Impostazioni porta): immettere il binding univoco della porta TCP per il contenitore Docker.

     ![Finestra Configure the Docker container to be created (Configurare il contenitore Docker da creare)][PUB08]

10. Al termine della procedura, fare clic su **Finish** (Fine).

Il Toolkit di Azure inizia a distribuire l'app Web in Azure in un contenitore Docker. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui Toolkit di Azure per gli IDE di Java, vedere le risorse seguenti:

* [Toolkit di Azure per Eclipse]
  * [Novità di Azure Toolkit for Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse]
  * [Creare un'app Web Hello World per Azure in Eclipse]
* [Toolkit di Azure per IntelliJ]
  * [Novità di Azure Toolkit for IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Istruzioni di accesso per Azure Toolkit for IntelliJ]
  * [Creare un'app Web Hello World per Azure in IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

Per altre risorse per Docker, vedere il [sito Web Docker] ufficiale.

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'app Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Istruzioni di accesso per Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novità di Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità di Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

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
