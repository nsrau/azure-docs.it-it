<properties
    pageTitle="Elenco di Account di archiviazione di Azure"
    description="Gestire le impostazioni dell'account di archiviazione tramite il Toolkit di Azure per Eclipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="01/09/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# Elenco di Account di archiviazione di Azure #

Gli account di archiviazione di Azure abilitano dei percorsi di download da usare per il JDK, per il server dell’applicazione e per i componenti arbitrari, nonché per lo stato di archiviazione quando si utilizza la memorizzazione nella cache. Eclipse mantiene un elenco di account di archiviazione noti disponibili per i progetti nell'area di lavoro di Eclipse. Per aprire la finestra di dialogo **Account di archiviazione** che viene utilizzata per gestire tale elenco, in Eclipse, fare clic su **Finestra**, fare clic su **Preferenze**, espandere **Azure**, quindi fare clic su **Account di archiviazione**.

Di seguito viene visualizzata la finestra di dialogo **Account di archiviazione**.

![][ic719496]

Questa finestra di dialogo può anche essere aperta da un collegamento **Account** nelle finestre di dialogo che utilizzano gli account di archiviazione, come ad esempio le gli elementi seguenti:

* La scheda **JDK** della finestra di **Configurazione Server**.
* La scheda **Server** della finestra di **Configurazione Server**.
* La finestra di dialogo **Aggiungi componente**.
* La finestra di dialogo delle proprietà di **Memorizzazione nella cache**

## Per importare gli account di archiviazione usando un file di impostazioni di pubblicazione ##

1. All'interno della finestra di dialogo **Account di archiviazione**, fare clic su **Importa da file di IMPOSTAZIONI DI PUBBLICAZIONE**.
2. (Saltare questo passaggio se è già stato salvato un file di impostazioni di pubblicazione nel computer locale). Nella finestra di dialogo **Importazione delle informazioni di sottoscrizione**, fare clic su **Download del File delle IMPOSTAZIONI DI PUBBLICAZIONE**. Se non ci si è ancora connessi al proprio account Azure, verrà richiesto di farlo. Quindi verrà richiesto di salvare un file di impostazioni di pubblicazione di Azure. (È possibile ignorare le istruzioni risultanti visualizzate nelle pagine di accesso, sono fornite dal portale di Azure e sono destinate agli utenti di Visual Studio.) Salvarlo nel computer locale.
3. Sempre nella finestra di dialogo **Importazione delle informazioni di sottoscrizione**, fare clic sul pulsante **Sfoglia**, selezionare il file di impostazioni di pubblicazione che è stato salvato precedentemente in locale e quindi fare clic su **Apri**.
4. Fare clic su **OK** per chiudere la finestra di dialogo **Importazione delle informazioni di sottoscrizione**.

## Per creare un nuovo account di archiviazione ##

1. Nella finestra di dialogo **Account di archiviazione**, fare clic su **Aggiungi**.
2. Nella finestra di dialogo **Aggiungi account di archiviazione**, fare clic su **Nuovo**.
3. All'interno della finestra di dialogo **Nuovo Account di archiviazione**, specificare i valori per le operazioni seguenti:
    * Nome dell'account di archiviazione.
    * Posizione dell'account di archiviazione.
    * Descrizione dell'account di archiviazione.
    * La sottoscrizione a cui appartiene l'account di archiviazione.
4. Fare clic su **OK** per chiudere la finestra di dialogo **Nuovo Account di archiviazione**.

Potrebbero volerci alcuni minuti per creare l'account di archiviazione. Dopo averlo creato, fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi Account di archiviazione** e il nuovo account di archiviazione verrà aggiunto all'elenco di account di archiviazione disponibili.

## Aggiungere un account di archiviazione esistente all’elenco ##

1. Se non si dispone ancora di un account di archiviazione di Azure, crearne uno seguendo i passaggi elencati in **Creare una nuova sezione di account di archiviazione**. (In alternativa, è possibile creare un nuovo account di archiviazione nel [portale di gestione di Azure][].)
2. Nella finestra di dialogo **Account di archiviazione**, fare clic su **Aggiungi**.
3. All'interno della finestra di dialogo **Aggiungi Account di archiviazione**, immettere i valori per **Nome** e **Chiave di accesso**. Ci devono essere la chiave dell'account e la chiave di accesso per un account di archiviazione di Azure esistente. Utilizzare la sezione **archiviazione** del [Portale di gestione di Azure][] per visualizzare i nomi degli account di archiviazione e le chiavi. la finestra di dialogo **Aggiungi Account di archiviazione** sarà simile a quella seguente.

    ![][ic719497]

4. Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi account di archiviazione**.

## Modificare un account di archiviazione per l'utilizzo di una nuova chiave di accesso ##

1. Nella finestra di dialogo **Account di archiviazione**, scegliere l'account di archiviazione che si desidera modificare e quindi fare clic su **Modifica**.
2. Nella finestra di dialogo **Modifica la chiave d’accesso all’account di archiviazione**, modificare il valore della **Chiave di accesso**.
3. Fare clic su **OK** per chiudere la finestra di dialogo **Modifica la chiave di accesso all’account di archiviazione**.

## Per rimuovere un account di archiviazione dall'elenco gestito in Eclipse ##

1. Nella finestra di dialogo **Account di archiviazione**, fare clic sull'account di archiviazione che si desidera modificare e quindi fare clic su **Rimuovi**.
2. Fare clic su **OK** quando viene richiesto di rimuovere l'account di archiviazione.

>[AZURE.NOTE]Rimuovendo l’account di archiviazione tramite la finestra di dialogo **Account di archiviazione**, esso viene rimosso solo dall'elenco di account di archiviazione visualizzabile in Eclipse. Non viene rimosso l'account di archiviazione dalla sottoscrizione di Azure. Inoltre, l'account di archiviazione potrebbe apparire di nuovo nell'elenco dopo che Eclipse ricarica i dettagli della sottoscrizione.

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Installare il Toolkit di Azure per Eclipse.][]

[Creare un'applicazione Hello World per Azure in Eclipse][]

Per altre informazioni su come usare Azure con Java, visitare il [Centro per sviluppatori Java di Azure][].

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creare un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!---HONumber=AcomDC_0114_2016-->