---
title: Gestione degli account di archiviazione con Azure Explorer per IntelliJ | Microsoft Docs
description: Informazioni su come gestire gli account di archiviazione di Azure con Azure Explorer per IntelliJ.
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
ms.openlocfilehash: 22930bf1d37d7b6039bd02792cdd70ec321d6fa7
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-intellij"></a>Gestione degli account di archiviazione con Azure Explorer per IntelliJ

Azure Explorer, che fa parte del Toolkit di Azure per IntelliJ, offre agli sviluppatori Java una soluzione di facile uso per la gestione degli account di archiviazione con il proprio account Azure nell'ambiente IDE di IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-intellij"></a>Creazione di un account di archiviazione in IntelliJ

I passaggi seguenti illustrano come creare account di archiviazione con Azure Explorer.

1. Accedere al proprio account Azure tramite la procedura descritta nell'articolo [Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse].

1. Nella finestra degli strumenti di **Azure Explorer** espandere il nodo **Azure** nodo, fare doppio clic su **Account di archiviazione** e quindi fare clic su **Crea account di archiviazione**.
   ![Menu Crea account di archiviazione][CS01]

1. Quando viene visualizzata la finestra **Crea account di archiviazione**, specificare le opzioni seguenti: ![Finestra di dialogo Crea un nuovo account di archiviazione][CS02]

   a. **Nome**: specifica il nome per il nuovo account di archiviazione.

   b. **Tipologia account**: specifica il tipo di account di archiviazione da creare, ad esempio "archiviazione BLOB". Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure].

   c. **Prestazioni**: specifica l'account di archiviazione offerto da usare dall'autore selezionato, ad esempio "Premium". Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

   d. **Replica**: specifica la replica per l'account di archiviazione di account, ad esempio "Con ridondanza della zona". Per altre informazioni, vedere [Replica di Archiviazione di Azure].

   e. **Sottoscrizione**: specifica la sottoscrizione di Azure da usare per il nuovo account di archiviazione.

   f. **Posizione**: specifica la posizione in cui verrà creato l'account di archiviazione, ad esempio "Stati Uniti occidentali".

   g. **Gruppo di risorse**: specifica il gruppo di risorse per la macchina virtuale. Scegliere una delle opzioni indicate di seguito.
      * **Crea nuovo**: specifica che si intende creare un nuovo gruppo di risorse.
      * **Usa esistente**: specifica che sarà possibile scegliere in un elenco i gruppi di risorse associati all'account di Azure.

1. Dopo aver specificato tutte le opzioni precedenti, fare clic su **OK**.

## <a name="creating-a-storage-container-in-intellij"></a>Creazione di un contenitore di archiviazione in IntelliJ

I passaggi seguenti illustrano come creare un contenitore di archiviazione con Azure Explorer.

1. In Azure Explorer fare doppio clic sull'account di archiviazione in cui si intende creare un contenitore e quindi fare clic su **Crea contenitore BLOB**.
   ![Menu Crea contenitore di archiviazione][CC01]

1. Quando viene visualizzata la finestra di dialogo **Crea contenitore BLOB**, specificare il nome per il contenitore e quindi fare clic su **OK**. Per altre informazioni sulla denominazione dei contenitori di archiviazione, vedere [Naming and Referencing Containers, Blobs, and Metadata] (Denominazione e riferimento a contenitori, BLOB e metadati). ![Finestra di dialogo Crea contenitore di archiviazione][CC02]

## <a name="deleting-a-storage-container-in-intellij"></a>Eliminazione di un contenitore di archiviazione in IntelliJ

Per eliminare un contenitore di archiviazione con Azure Explorer, eseguire queste operazioni:

1. In Azure Explorer fare clic con il pulsante destro del mouse sul contenitore di archiviazione e quindi fare clic su **Elimina**.
   ![Menu Elimina contenitore di archiviazione][DC01]

1. Quando viene richiesto di eliminare il contenitore di archiviazione, fare clic su **Sì**.
   ![Finestra di dialogo Elimina contenitore di archiviazione][DC02]

## <a name="deleting-a-storage-account-in-intellij"></a>Eliminazione di un account di archiviazione in IntelliJ

Per eliminare un account di archiviazione con Azure Explorer, eseguire queste operazioni:

1. Nella finestra degli strumenti di **Azure Explorer** fare clic con il pulsante destro del mouse sull'account di archiviazione e quindi scegliere **Elimina**.
   ![Menu Elimina account di archiviazione][DS01]

1. Quando viene richiesto di eliminare l'account di archiviazione, fare clic su **Sì**.
   ![Finestra di dialogo Elimina account di archiviazione][DS02]

## <a name="see-also"></a>Vedere anche
Per altre informazioni sugli account di archiviazione, sulle dimensioni e sui prezzi di Azure, vedere i collegamenti seguenti:

* [Introduzione ad Archiviazione di Microsoft Azure]
* [Informazioni sugli account di archiviazione di Azure]
* Dimensioni degli account di archiviazione di Azure
   * [Sizes for Windows storage accounts in Azure] (Dimensioni degli account di archiviazione Windows in Azure)
   * [Sizes for Linux storage accounts in Azure] (Dimensioni degli account di archiviazione Linux in Azure)
* Prezzi per gli account di archiviazione di Azure
   * [Windows Storage Accounts Pricing] (Prezzo per gli account di archiviazione di Windows)
   * [Linux Storage Accounts Pricing] (Prezzo per gli account di archiviazione di Linux)

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

[Introduzione ad Archiviazione di Microsoft Azure]: /azure/storage/storage-introduction
[Informazioni sugli account di archiviazione di Azure]: /azure/storage/storage-create-storage-account
[Replica di Archiviazione di Azure]: /azure/storage/storage-redundancy
[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: /azure/storage/storage-scalability-targets
[Naming and Referencing Containers, Blobs, and Metadata]: http://go.microsoft.com/fwlink/?LinkId=255555

[Sizes for Windows storage accounts in Azure]: /azure/virtual-machines/virtual-machines-windows-sizes (Dimensioni degli account di archiviazione Windows in Azure)
[Sizes for Linux storage accounts in Azure]: /azure/virtual-machines/virtual-machines-linux-sizes (Dimensioni degli account di archiviazione Linux in Azure)
[Windows Storage Accounts Pricing]: /pricing/details/virtual-machines/windows/ (Prezzo per gli account di archiviazione di Windows)
[Linux Storage Accounts Pricing]: /pricing/details/virtual-machines/linux/ (Prezzo per gli account di archiviazione di Linux)

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png

