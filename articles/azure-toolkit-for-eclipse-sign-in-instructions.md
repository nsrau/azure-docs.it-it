---
title: Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse | Microsoft Docs
description: Informazioni su come accedere a Microsoft Azure con il Toolkit di Azure per Eclipse.
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
ms.openlocfilehash: 02dd9935086c4c40d9ed54cc9ff2412ca96889f5
ms.lasthandoff: 04/22/2017


---

# <a name="azure-sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse

Il Toolkit di Azure per Eclipse consente di accedere all'account Azure in due metodi diversi:

  * **Interattivo**: le credenziali di Azure vengono immesse ogni volta che si accede all'account Azure.
  * **Automatico**: viene creato un file di credenziali che contiene i dati dell'entità servizio e in seguito è possibile usare tale file per accedere automaticamente all'account Azure.

I passaggi nelle sezioni seguenti descrivono come usare ogni metodo.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="signing-into-your-azure-account-interactively"></a>Accesso all'account Azure in modo interattivo

I passaggi seguenti illustrano come accedere ad Azure immettendo manualmente le credenziali.

1. Aprire il progetto con Eclipse.

1. Fare clic su **Tools** (Strumenti), su **Azure** e quindi fare clic su **Sign In** (Accedi).

   ![Menu di Eclipse per l'accesso ad Azure][I01]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign In** (Accesso ad Azure), selezionare **Interactive** (Interattivo) e quindi fare clic su **Sign In** (Accedi).

   ![Finestra di dialogo di accesso][I02]

1. Quando viene visualizzata la finestra di dialogo **Azure Log In** (Accedi ad Azure), immettere le credenziali di Azure e quindi fare clic su **Sign In** (Accedi).

   ![Finestra di dialogo di accesso ad Azure][I03]

1. Quando viene selezionata la finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni), selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][I04]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-interactively"></a>Disconnessione dell'account di Azure quando l'accesso è stato eseguito in modo interattivo

Dopo aver configurato i passaggi nella sezione precedente, la disconnessione dell'account Azure verrà eseguita automaticamente ogni riavvio di Eclipse. Se tuttavia si vuole disconnettersi dall'account di Azure senza riavviare Eclipse, eseguire queste operazioni.

1. In Eclipse fare clic su **Tools** (Strumenti), su **Azure** e quindi su **Sign Out** (Disconnetti).

   ![Menu di Eclipse per la disconnessione da Azure][L01]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign Out** (Disconnessione da Azure), fare clic su **Yes** (Sì).

   ![Finestra di dialogo di disconnessione][L02]

## <a name="signing-into-your-azure-account-automatically-and-creating-a-credentials-file-to-use-in-the-future"></a>Accesso automatico all'account Azure e creazione di un file di credenziali da usare in seguito

I passaggi seguenti illustrano come creare un file di credenziali che contiene i dati dell'entità servizio. Dopo aver completato questi passaggi, Eclipse userà automaticamente il file di credenziali per l'accesso ad Azure ogni volta che si apre il progetto.

1. Aprire il progetto con Eclipse.

1. Fare clic su **Tools** (Strumenti), su **Azure** e quindi fare clic su **Sign In** (Accedi).

   ![Menu di Eclipse per l'accesso ad Azure][A01]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign In** (Accesso ad Azure), selezionare **Automated** (Automatico) e quindi fare clic su **New** (Nuovo).

   ![Finestra di dialogo di accesso][A02]

1. Quando viene visualizzata la finestra di dialogo **Azure Log In** (Accedi ad Azure), immettere le credenziali di Azure e quindi fare clic su **Sign In** (Accedi).

   ![Finestra di dialogo di accesso ad Azure][A03]

1. Quando viene visualizzata la finestra di dialogo **Create authentication files** (Crea file di autenticazione), selezionare le sottoscrizioni da usare, scegliere la directory di destinazione e quindi fare cli su **Start** (Avvio).

   ![Finestra di dialogo di accesso ad Azure][A04]

1. Verrà visualizzata la finestra di dialogo **Service Principal Creatation Status** (Stato creazione entità servizio) e al termine della creazione dei file fare clic su **OK**.

   ![Finestra di dialogo Stato creazione entità servizio][A05]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign In** (Accesso ad Azure), fare clic su **Accedi**.

   ![Finestra di dialogo di accesso ad Azure][A06]

1. Quando viene selezionata la finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni), selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][A07]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-automatically"></a>Disconnessione dell'account Azure quando l'accesso è stato eseguito in modo automatico

Dopo aver configurato i passaggi nella sezione precedente, l'accesso automatico ad Azure verrà eseguito a ogni riavvio di Eclipse. Per disconnettersi dall'account Azure e impedire l'accesso automatico eseguito dal Toolkit di Azure, eseguire queste operazioni.

1. In Eclipse fare clic su **Tools** (Strumenti), su **Azure** e quindi su **Sign Out** (Disconnetti).

   ![Menu di Eclipse per la disconnessione da Azure][L01]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign Out** (Disconnessione da Azure), fare clic su **Yes** (Sì).

   ![Finestra di dialogo di disconnessione][L03]

## <a name="signing-into-your-azure-account-automatically-using-a-credentials-file-which-you-have-already-created"></a>Accesso automatico all'account Azure usando un file di credenziali già creato

Se ci si disconnette da Azure quando si usa Eclipse, è necessario riconfigurare il Toolkit di Azure per Eclipse per usare un file di credenziali creato in precedenza per accedere automaticamente all'account Azure. I passaggi seguenti illustrano come configurare il Toolkit di Azure per usare un file di credenziali esistente.

1. Aprire il progetto con Eclipse.

1. Fare clic su **Tools** (Strumenti), su **Azure** e quindi fare clic su **Sign In** (Accedi).

   ![Menu di Eclipse per l'accesso ad Azure][A01]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign In** (Accesso ad Azure), selezionare **Automated** (Automatico) e quindi fare clic su **Browse** (Sfoglia).

   ![Finestra di dialogo di accesso][A02]

1. Quando viene visualizzata al finestra di dialogo **Select Authenticated File** (Seleziona file autenticazione), selezionare un file di credenziali creato in precedenza e quindi fare clic su **Select** (Seleziona).

   ![Finestra di dialogo di accesso][A08]

1. Quando viene visualizzata la finestra di dialogo **Azure Sign In** (Accesso ad Azure), fare clic su **Accedi**.

   ![Finestra di dialogo di accesso ad Azure][A06]

1. Quando viene selezionata la finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni), selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][A07]

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Novità di Azure Toolkit per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * *Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse (questo articolo)*
  * [Creare un'app Web Hello World per Azure in Eclipse]
* [Toolkit di Azure per IntelliJ]
  * [Novità del Toolkit di Azure per IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Istruzioni di accesso ad Azure per il Toolkit di Azure per IntelliJ]
  * [Creare un'App Web Hello World per Azure in IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Istruzioni di accesso ad Azure per il Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/L03.png

