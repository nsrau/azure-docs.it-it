---
title: Installazione di Azure Toolkit for IntelliJ | Documentazione Microsoft
description: Informazioni su come installare il Toolkit di Azure per IntelliJ.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2eae9a0339965165c825c8990867164b10a96f4e


---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Installazione del Toolkit di Azure per IntelliJ
Il Toolkit di Azure per IntelliJ offre modelli e funzionalità che consentono di creare, sviluppare, testare e distribuire con facilità applicazioni Azure tramite l'ambiente di sviluppo IntelliJ IDEA. Il Toolkit di Azure per IntelliJ è un progetto Open Source, il cui codice sorgente è disponibile con la licenza MIT dal sito del progetto in GitHub all'indirizzo seguente:

<https://github.com/microsoft/azure-tools-for-java>

Esistono due metodi di installazione del Toolkit di Azure per IntelliJ: dalla finestra di dialogo Impostazioni e dal menu Configurazione nella schermata iniziale. Entrambi i metodi di installazione verranno illustrati nella procedura seguente.

[!INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Per installare il Toolkit di Azure per IntelliJ dalla finestra di dialogo Impostazioni
1. Avviare IntelliJ IDEA.
2. Quando si apre IntelliJ IDEA, fare clic su **File**, quindi fare clic su **Settings** (Impostazioni).
   
    ![Aprire la finestra di dialogo Impostazioni di IntelliJ IDEA][01a]
3. Nella finestra di dialogo Settings (Impostazioni) fare clic su **Plugins** (Plug-in) e selezionare **Browse repositories** (Sfoglia repository).
   
    ![Finestra di dialogo Impostazioni di IntelliJ IDEA][02a]
4. Nella finestra di dialogo **Browse Repositories** (Sfoglia repository) digitare "Azure" nella casella di ricerca. Selezionare **Azure Toolkit for IntelliJ** e quindi fare clic su **Install** (Installa).
   
    ![Cercare il Toolkit di Azure per IntelliJ][03]
   
    IntelliJ IDEA mostrerà lo stato dell'installazione in una finestra di dialogo.
   
    ![Stato dell'installazione][04]
5. Al termine dell'installazione fare clic su **Restart IntelliJ IDEA**(Riavvia IntelliJ IDEA).
   
    ![Restart IntelliJ IDEA][05]
6. Fare clic su **OK** per chiudere la finestra di dialogo.
   
    ![Chiudere la finestra di dialogo Impostazioni di IntelliJ IDEA][06]
7. Quando viene richiesto di riavviare IntelliJ IDEA o posticipare, fare clic su **Restart**(Riavvia).
   
    ![Restart IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Per installare il Toolkit di Azure per IntelliJ dalla schermata iniziale
1. Avviare IntelliJ IDEA.
2. Nella schermata iniziale di IntelliJ IDEA fare clic su **Configure** (Configura) e selezionare **Plugins** (plug-in).
   
    ![Installare i plug-in di IntelliJ IDEA][01b]
3. Nella finestra di dialogo **Plugins** (Plug-in) fare clic su **Browse repositories** (Sfoglia repository).
   
    ![Sfogliare i repository dei plug-in di IntelliJ IDEA][02b]
4. Nella finestra di dialogo **Browse Repositories** (Sfoglia repository) digitare "Azure" nella casella di ricerca. Selezionare **Azure Toolkit for IntelliJ** e quindi fare clic su **Install** (Installa).
   
    ![Cercare il Toolkit di Azure per IntelliJ][03]
   
    IntelliJ IDEA mostrerà lo stato dell'installazione in una finestra di dialogo.
   
    ![Stato dell'installazione][04]
5. Al termine dell'installazione fare clic su **Restart IntelliJ IDEA**(Riavvia IntelliJ IDEA).
   
    ![Restart IntelliJ IDEA][05]
6. Quando viene richiesto di riavviare IntelliJ IDEA o posticipare, fare clic su **Restart**(Riavvia).
   
    ![Restart IntelliJ IDEA][07]

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Creare un'app Web Hello World per Azure in Eclipse]
  * [Novità di Azure Toolkit per Eclipse]
* [Toolkit di Azure per IntelliJ]
  * *Installazione del Toolkit di Azure per IntelliJ (questo articolo)*
  * [Creare un'App Web Hello World per Azure in IntelliJ]
  * [Novità del Toolkit di Azure per IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png



<!--HONumber=Nov16_HO3-->


