---
title: Istruzioni di accesso per Azure Toolkit for IntelliJ
description: Informazioni su come accedere a Microsoft Azure con Azure Toolkit for IntelliJ.
services: 
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/20/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: def5957b2ed58aee2e03c879ba9fd3d6c714e358
ms.contentlocale: it-it
ms.lasthandoff: 09/02/2017

---

# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Istruzioni di accesso per Azure Toolkit for IntelliJ

Azure Toolkit for IntelliJ consente di accedere all'account Azure in due metodi diversi:

  * **Automated** (Automatico): si crea un file di credenziali che è possibile usare per accedere automaticamente all'account Azure.
  * **Interactive** (Interattivo): immettere le credenziali di Azure ogni volta che si accede all'account Azure.

Le sezioni seguenti descrivono come usare ogni metodo.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-automatically"></a>Accedere automaticamente all'account Azure

La sezione seguente illustra come creare un file di credenziali che contiene i dati dell'entità servizio. Dopo avere completato questo processo, Eclipse usa il file di credenziali per l'accesso ad Azure ogni volta che si apre il progetto.

1. Aprire il progetto con IntelliJ IDEA.

2. Fare clic su **Tools** (Strumenti), puntare su **Azure** e quindi fare clic su **Azure Sign In** (Accesso ad Azure).

   ![Comando di accesso ad Azure in IntelliJ][A01]

3. Nella finestra **Azure Sign In** (Accesso ad Azure) selezionare **Automated** (Automatico) e quindi fare clic su **New** (Nuovo).

   ![Finestra di accesso ad Azure con opzione automatica selezionata][A02]

4. Nella finestra **Azure Log In** (Accedi ad Azure) immettere le credenziali di Azure e quindi fare clic su **Sign In** (Accedi).

   ![Finestra di dialogo di accesso ad Azure][A03]

5. Nella finestra **Create authentication files** (Crea file di autenticazione) selezionare le sottoscrizioni da usare, scegliere la directory di destinazione e quindi fare cli su **Start** (Avvio).

   ![Finestra di creazione dei file di autenticazione][A04]

6. Nella finestra di dialogo **Service Principal Creation Status** (Stato creazione entità servizio) fare clic su **OK** al termine della creazione dei file.

   ![Finestra di dialogo sullo stato di creazione dell'entità servizio][A05]

7. Nella finestra **Azure Sign In** (Accesso ad Azure) fare clic su **Sign in** (Accedi).

   ![Finestra di dialogo di accesso ad Azure][A06]

8. Nella finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni) selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][A07]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-automatically"></a>Disconnettersi dall'account di Azure dopo che l'accesso è stato in modo automatico

Dopo avere configurato l'account usando i passaggi precedenti, Azure Toolkit esegue automaticamente la connessione all'account Azure a ogni riavvio di IntelliJ IDEA. Per disconnettersi dall'account Azure e impedire l'accesso automatico eseguito da Azure Toolkit, eseguire queste operazioni:

1. In IntelliJ IDEA fare clic su **Tools** (Strumenti), puntare su **Azure** e quindi fare clic su **Azure Sign Out** (Disconnessione da Azure).

   ![Comando di disconnessione da Azure in IntelliJ][L01]

2. Nella finestra di conferma **Azure Sign Out** (Disconnessione da Azure) fare clic su **Yes** (Sì).

   ![Finestra di conferma della disconnessione da Azure][L03]

## <a name="sign-in-to-your-azure-account-automatically-by-using-an-existing-credentials-file"></a>Accedere al proprio account Azure automaticamente usando un file di credenziali esistente

Se si esegue la disconnessione dall'account Azure quando si usa IntelliJ IDEA, è necessario usare un file esistente di credenziali per accedere di nuovo automaticamente all'account. Per configurare Azure Toolkit for Eclipse in modo da usare un file di credenziali esistente, eseguire queste operazioni:

1. Aprire il progetto con IntelliJ IDEA.

2. Fare clic su **Tools** (Strumenti), puntare su **Azure** e quindi fare clic su **Azure Sign In** (Accesso ad Azure).

   ![Comando di accesso ad Azure in IntelliJ][A01]

3. Nella finestra **Azure Sign In** (Accesso ad Azure) selezionare **Automated** (Automatico) e quindi fare clic su **Browse** (Sfoglia).

   ![Finestra di accesso ad Azure con opzione automatica selezionata][A02]

4. Nella finestra di dialogo **Select Authenticated File** (Seleziona file autenticazione) selezionare un file di credenziali creato in precedenza e quindi fare clic su **Select** (Seleziona).

   ![Finestra di dialogo di selezione del file di autenticazione][A08]

5. Nella finestra **Azure Sign In** (Accesso ad Azure) fare clic su **Sign in** (Accedi).

   ![Finestra di accesso ad Azure con opzione automatica selezionata][A06]

6. Nella finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni) selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][A07]

## <a name="sign-in-to-your-azure-account-interactively"></a>Accedere all'account Azure in modo interattivo

Per accedere ad Azure immettendo manualmente le credenziali, eseguire queste operazioni:

1. Aprire il progetto con IntelliJ IDEA.

2. Fare clic su **Tools** (Strumenti), su **Azure** e quindi fare clic su **Azure Sign In** (Accesso ad Azure).

   ![Comando di accesso ad Azure in IntelliJ][I01]

3. Quando viene visualizzata la finestra **Azure Sign In** (Accesso ad Azure), selezionare **Interactive** (Interattivo) e quindi fare clic su **Sign In** (Accedi).

   ![Finestra di accesso in Azure con opzione interattiva selezionata][I02]

4. Nella finestra di dialogo **Azure Log In** (Accedi ad Azure) immettere le credenziali di Azure e quindi fare clic su **Sign In** (Accedi).

   ![Finestra di dialogo di accesso ad Azure][I03]

5. Nella finestra di dialogo **Select Subscriptions** (Seleziona sottoscrizioni) selezionare le sottoscrizioni da usare e quindi fare clic su **OK**.

   ![Finestra di dialogo Seleziona sottoscrizioni][I04]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-interactively"></a>Disconnettersi dall'account di Azure dopo che l'accesso è stato eseguito in modo interattivo

Dopo avere configurato l'account usando i passaggi precedenti, la disconnessione dall'account Azure verrà eseguita automaticamente a ogni riavvio di IntelliJ IDEA. Se tuttavia si vuole disconnettersi dall'account di Azure *senza* riavviare IntelliJ IDEA, eseguire queste operazioni.

1. In IntelliJ IDEA fare clic su **Tools** (Strumenti), puntare su **Azure** e quindi fare clic su **Azure Sign Out** (Disconnessione da Azure).

   ![Comando di disconnessione da Azure in IntelliJ][L01]

2. Nella finestra di conferma **Azure Sign Out** (Disconnessione da Azure) fare clic su **Yes** (Sì).

   ![Finestra di conferma della disconnessione da Azure][L02]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World web app for Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign-in instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[What's new in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's new in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L03.png

