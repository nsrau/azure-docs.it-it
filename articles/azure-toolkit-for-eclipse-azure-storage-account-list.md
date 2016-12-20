---
title: Elenco di Account di archiviazione di Azure
description: Gestire le impostazioni dell&quot;account di archiviazione tramite il Toolkit di Azure per Eclipse
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: bbacfcd8-dbf5-4265-a930-59f508de5325
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 91490e33ab2d524da90ae75bd71abe8005e45c54


---
# <a name="azure-storage-account-list"></a>Elenco di Account di archiviazione di Azure
Gli account di archiviazione di Azure abilitano dei percorsi di download da usare per il JDK, per il server dell’applicazione e per i componenti arbitrari, nonché per lo stato di archiviazione quando si utilizza la memorizzazione nella cache. Eclipse mantiene un elenco di account di archiviazione noti disponibili per i progetti nell'area di lavoro di Eclipse. Per aprire la finestra di dialogo **Account di archiviazione** che viene usata per gestire tale elenco, in Eclipse, fare clic su **Finestra**, **Preferenze**, espandere **Azure**, quindi fare clic su **Account di archiviazione**.

Di seguito viene visualizzata la finestra di dialogo **Account di archiviazione** .

![][ic719496]

Questa finestra di dialogo può anche essere aperta da un collegamento **Account** nelle finestre di dialogo che utilizzano gli account di archiviazione, come ad esempio le gli elementi seguenti:

* La scheda **JDK** della finestra di dialogo **Configurazione Server**.
* La scheda **Server** della finestra di dialogo **Configurazione Server**.
* La finestra di dialogo **Aggiungi componente** .
* La finestra di dialogo delle proprietà di **Memorizzazione nella cache**

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Per importare gli account di archiviazione usando un file di impostazioni di pubblicazione
1. All'interno della finestra di dialogo **Account di archiviazione** fare clic su **Importa da file IMPOSTAZIONI DI PUBBLICAZIONE**.
2. (Saltare questo passaggio se è già stato salvato un file di impostazioni di pubblicazione nel computer locale). Nella finestra di dialogo **Import Subscription Information** (Importa informazioni sottoscrizione) fare clic su **Download PUBLISH-SETTINGS File** (Download del file PUBLISH-SETTINGS). Se non ci si è ancora connessi al proprio account Azure, verrà richiesto di farlo. Quindi verrà richiesto di salvare un file di impostazioni di pubblicazione di Azure. (È possibile ignorare le istruzioni risultanti visualizzate nelle pagine di accesso, sono fornite dal portale di Azure e sono destinate agli utenti di Visual Studio.) Salvarlo nel computer locale.
3. Sempre nella finestra di dialogo **Import Subscription Information** (Importa informazioni sottoscrizione) fare clic sul pulsante **Browse** (Sfoglia), selezionare il file di impostazioni di pubblicazione precedentemente salvato in locale e quindi fare clic su **Open** (Apri).
4. Fare clic su **OK** per chiudere la finestra di dialogo **Import Subscription Information** (Importa informazioni sottoscrizione).

## <a name="to-create-a-new-storage-account"></a>Per creare un nuovo account di archiviazione
1. Nella finestra di dialogo **Account di archiviazione** fare clic su **Aggiungi**.
2. Nella finestra di dialogo **Aggiungi account di archiviazione** fare clic su **Nuovo**.
3. All'interno della finestra di dialogo **Nuovo Account di archiviazione** , specificare i valori per le operazioni seguenti:
   * Nome dell'account di archiviazione.
   * Posizione dell'account di archiviazione.
   * Descrizione dell'account di archiviazione.
   * Sottoscrizione a cui appartiene l'account di archiviazione.
4. Fare clic su **OK** per chiudere la finestra di dialogo **Nuovo Account di archiviazione**.

Potrebbero volerci alcuni minuti per creare l'account di archiviazione. Dopo averlo creato, fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi Account di archiviazione** e il nuovo account di archiviazione verrà aggiunto all'elenco di account di archiviazione disponibili.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Aggiungere un account di archiviazione esistente all’elenco
1. Se non si dispone ancora di un account di archiviazione di Azure, crearne uno seguendo i passaggi elencati in **Creare una nuova sezione di account di archiviazione** . In alternativa è possibile creare un nuovo account di archiviazione nel [portale di gestione di Azure][portale di gestione di Azure].
2. Nella finestra di dialogo **Account di archiviazione** fare clic su **Aggiungi**.
3. Nella finestra di dialogo **Aggiungi Account di archiviazione** immettere i valori per **Nome** e **Chiave di accesso**. Ci devono essere la chiave dell'account e la chiave di accesso per un account di archiviazione di Azure esistente. Usare la sezione **Archiviazione** del [portale di gestione di Azure][portale di gestione di Azure] per visualizzare i nomi e le chiavi degli account di archiviazione. la finestra di dialogo **Aggiungi Account di archiviazione** sarà simile a quella seguente.
   
    ![][ic719497]
4. Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi account di archiviazione**.

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Modificare un account di archiviazione per l'utilizzo di una nuova chiave di accesso
1. Nella finestra di dialogo **Account di archiviazione** scegliere l'account di archiviazione da modificare e quindi fare clic su **Modifica**.
2. Nella finestra di dialogo di modifica della **chiave di accesso dell'account di archiviazione**, modificare il valore della **chiave di accesso**.
3. Fare clic su **OK** per chiudere la finestra di dialogo di modifica della **chiave di accesso dell'account di archiviazione**.

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Per rimuovere un account di archiviazione dall'elenco gestito in Eclipse
1. Nella finestra di dialogo **Account di archiviazione** fare clic sull'account di archiviazione da modificare e quindi fare clic su **Rimuovi**.
2. Fare clic su **OK** quando viene richiesto di rimuovere l'account di archiviazione.

> [!NOTE]
> Rimuovendolo tramite la finestra di dialogo **Account di archiviazione**, l'account verrà rimosso solo dall'elenco di account di archiviazione visualizzabili in Eclipse. L'account di archiviazione non viene rimosso dalla sottoscrizione di Azure e potrebbe essere nuovamente visualizzato nell'elenco dopo che Eclipse ricarica i dettagli della sottoscrizione.
> 
> 

## <a name="see-also"></a>Vedere anche
[Toolkit di Azure per Eclipse][Toolkit di Azure per Eclipse]

[Installare il Toolkit di Azure per Eclipse.][Installare il Toolkit di Azure per Eclipse.] 

[Creare un'applicazione Hello World per Azure in Eclipse][Creare un'applicazione Hello World per Azure in Eclipse]

Per altre informazioni sull'uso di Azure con Java, vedere il [Centro per sviluppatori Java in Azure][Centro per sviluppatori Java in Azure] di Azure.

<!-- URL List -->

[Centro per sviluppatori Java in Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creare un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546
[Novità di Azure Toolkit per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->



<!--HONumber=Nov16_HO3-->


