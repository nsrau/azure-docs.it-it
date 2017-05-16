---
title: Gestione delle macchine virtuali con Azure Explorer per Eclipse | Microsoft Docs
description: Informazioni su come gestire macchine virtuali di Azure con Azure Explorer per Eclipse.
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
ms.openlocfilehash: f8631c5fe566762862f64b47843e154b68bb8031
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-eclipse"></a>Gestione delle macchine virtuali con Azure Explorer per Eclipse

Azure Explorer, che fa parte del Toolkit di Azure per Eclipse, offre agli sviluppatori Java una soluzione di facile uso per la gestione delle macchine virtuali con il proprio account Azure nell'ambiente IDE di Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-eclipse"></a>Creazione di una macchina virtuale in Eclipse

I passaggi seguenti illustrano come creare una macchina virtuale con Azure Explorer.

1. Accedere al proprio account Azure tramite la procedura descritta nell'articolo [Istruzioni di accesso ad Azure per il Toolkit di Azure per Eclipse].

1. Nella visualizzazione **Azure Explorer** espandere il nodo **Azure**, fare clic con il pulsante destro del mouse su **Macchine virtuali** e quindi fare clic su **Crea macchina virtuale**.
   ![Menu Crea macchina virtuale][CR01]

1. Quando viene visualizzata la procedura guidata **Creare una nuova macchina virtuale**, scegliere la propria sottoscrizione e quindi fare clic su **Avanti**.
   ![Procedura guidata Creare una nuova macchina virtuale][CR02]

1. Nella schermata successiva della procedura guidata specificare le opzioni seguenti e quindi fare clic su **Avanti**:  ![Creare una nuova macchina virtuale][CR03]

   a. **Posizione**: specifica la posizione in cui verrà creata la macchina virtuale, ad esempio "Stati Uniti occidentali".

   b. **Autore**: specifica l'autore che ha creato l'immagine che verrà usata per creare la macchina virtuale, ad esempio "Microsoft".

   c. **Offerta**: specifica la macchina virtuale offerta da usare dall'autore selezionato, ad esempio "JDK".

   d. **SKU**: specifica la *SKU* da usare nell'offerta selezionata, ad esempio "JDK_8".

   e. **Versione #**(N. versione): specifica la versione da usare della SKU selezionata.

1. Nella schermata successiva della procedura guidata specificare le opzioni seguenti e quindi fare clic su **Avanti**: ![Creare una nuova macchina virtuale][CR04]

   a. **Nome macchina virtuale**: specifica il nome della nuova macchina virtuale, che deve iniziare con una lettera e contenere solo lettere, numeri e trattini.

   b. **Dimensioni**: specifica il numero di core e la quantità di memoria da allocare per la macchina virtuale.

   c. **Nome utente**: specifica l'account amministratore da creare per la gestione della macchina virtuale.

   d. **Password** e **Conferma**: specifica la password per l'account di amministratore.

1. Nell'ultima schermata della procedura guidata specificare le opzioni seguenti:

   a. **Gruppo di risorse**: specifica il gruppo di risorse per la macchina virtuale. Scegliere una delle opzioni indicate di seguito.
      * **Crea nuovo**: specifica che si intende creare un nuovo gruppo di risorse.
      * **Usa esistente**: specifica che sarà possibile scegliere in un elenco i gruppi di risorse associati all'account di Azure.

   b. **Account di archiviazione**: specifica l'account di archiviazione da usare per archiviare la macchina virtuale. È possibile scegliere un account di archiviazione esistente o creare un nuovo account. Se si sceglie  **&lt; &lt;Crea nuovo&gt;&gt;**, verrà visualizzata la finestra di dialogo seguente:

      ![Finestra di dialogo Crea un nuovo account di archiviazione][CR05]

   c. **Rete virtuale** e **Subnet**: specificano la rete virtuale e la subnet a cui si connetterà alla macchina virtuale. È possibile scegliere una rete e una subnet esistenti da usare per la macchina virtuale oppure è possibile creare una rete e una subnet nuove. Se si sceglie  **&lt; &lt;Crea nuovo&gt;&gt;**, verrà visualizzata la finestra di dialogo seguente:<br/>

      ![Finestra di dialogo Crea una nuova rete virtuale][CR06]

   d. **Indirizzo IP pubblico**: specifica un indirizzo IP esterno per la macchina virtuale. È possibile scegliere di creare un nuovo indirizzo IP o scegliere **(Nessuno)** se la macchina virtuale non avrà un indirizzo IP pubblico.

   e. **Gruppo di sicurezza di rete**: specifica un firewall di rete facoltativo che verrà usato dalla macchina virtuale. È possibile scegliere un firewall esistente o scegliere **(Nessuno)** se la macchina virtuale non usa alcun firewall di rete.

   f. **Set di disponibilità**: specifica un set di disponibilità a cui la macchina virtuale può appartenere. È possibile scegliere un set di disponibilità esistente o crearne uno nuovo oppure scegliere **(Nessuno)** se la macchina virtuale non apparterrà ad alcun set di disponibilità.

1. Dopo aver immesso tutte le opzioni elencate in precedenza, fare clic su **Fine**: ![Creare una nuova macchina virtuale][CR07]

1. Dopo aver completato i passaggi precedenti, la nuova macchina virtuale verrà visualizzata nella finestra dello strumento Azure Explorer.
   ![Nuova macchina virtuale][CR08]

## <a name="restarting-a-virtual-machine-in-eclipse"></a>Riavvio di una macchina virtuale in Eclipse

Per riavviare una macchina virtuale con Azure Explorer in Eclipse, eseguire queste operazioni:

1. Nella visualizzazione **Azure Explorer** fare clic con il pulsante destro del mouse sulla macchina virtuale e quindi scegliere **Riavvia**.
   ![Riavvio di una macchina virtuale][RE01]

1. Quando viene richiesto di riavviare la macchina virtuale, fare clic su **Sì**.
   ![Riavvio di una macchina virtuale][RE02]

## <a name="shutting-down-a-virtual-machine-in-eclipse"></a>Arresto di una macchina virtuale in Eclipse

Per arrestare una macchina virtuale in esecuzione con Azure Explorer in Eclipse, eseguire queste operazioni:

1. Nella visualizzazione **Azure Explorer** fare clic con il pulsante destro del mouse sulla macchina virtuale e quindi scegliere **Chiudi**.
   ![Arresto di una macchina virtuale][SH01]

1. Quando viene richiesto di arrestare la macchina virtuale, fare clic su **Sì**.
   ![Arresto di una macchina virtuale][SH02]

## <a name="deleting-a-virtual-machine-in-eclipse"></a>Eliminazione di una macchina virtuale in Eclipse

Per eliminare una macchina virtuale con Azure Explorer in Eclipse, eseguire queste operazioni:

1. Nella visualizzazione **Azure Explorer** fare clic con il pulsante destro del mouse sulla macchina virtuale e quindi scegliere **Elimina**.
   ![Eliminazione di una macchina virtuale][DE01]

1. Quando viene richiesto di eliminare la macchina virtuale, fare clic su **Sì**.
   ![Eliminazione di una macchina virtuale][DE02]

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulle dimensioni e sui prezzi delle macchine virtuali in Azure, vedere i collegamenti seguenti:

* Dimensioni delle macchine virtuali in Azure
  * [Dimensioni per le macchine virtuali Windows in Azure]
  * [Dimensioni delle macchine virtuali Linux in Azure]
* Prezzi per le macchine virtuali in Azure
  * [Prezzi di Macchine virtuali Windows]
  * [Prezzi di Macchine virtuali Linux]

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

[Dimensioni per le macchine virtuali Windows in Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Dimensioni delle macchine virtuali Linux in Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Prezzi di Macchine virtuali Windows]: /pricing/details/virtual-machines/windows/
[Prezzi di Macchine virtuali Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR08.png

