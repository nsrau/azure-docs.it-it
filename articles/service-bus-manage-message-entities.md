<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Come gestire le entità di messaggistica del bus di servizio

In questo argomento vengono descritte la creazione e la gestione delle entità del bus di servizio mediante l'uso del [portale di gestione di Azure][portale di gestione di Azure]. È possibile usare il portale per creare nuovi spazi dei nomi servizio o entità di messaggistica (code, argomenti o sottoscrizioni). È inoltre possibile eliminare entità o modificarne lo stato.

## Sommario

-   [Procedura: Creare un'entità del bus di servizio][Procedura: Creare un'entità del bus di servizio]
-   [Procedura: Eliminare un'entità del bus di servizio][Procedura: Eliminare un'entità del bus di servizio]
-   [Procedura: Abilitare o disabilitare un'entità del bus di servizio][Procedura: Abilitare o disabilitare un'entità del bus di servizio]
-   [Risorse aggiuntive][Risorse aggiuntive]

## <span id="create"></span></a>Procedura: Creare un'entità del bus di servizio

Il portale di gestione di Azure supporta due metodi per creare un'entità del bus di servizio: *Quick Create* o *Custom Create*.

### Quick Create

Quick Create consente di creare una coda, argomento o spazio dei nomi del servizio Inoltro del bus di servizio in un semplice passaggio. Per creare un'entità del bus di servizio, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sull'icona **New** in basso a sinistra nel portale di gestione.
3.  Fare clic sull'icona **App Services**, quindi fare clic su **Service Bus Queue** (argomento o inoltro). Fare clic su **Quick Create**, quindi immettere il nome della coda, l'area e l'ID sottoscrizione di Azure.

    a. Se questo è il primo spazio dei nomi nell'area selezionata, il portale suggerisce una coda di spazi dei nomi: [nome\_entità]-ns. È possibile modificare questo valore.

    b. Se si dispone già di almeno uno spazio dei nomi servizio in questa area, ne verrà selezionato uno automaticamente. È possibile modificare lo spazio dei nomi selezionato.

4.  Fare clic sul segno di spunta a fianco di **create a new queue** (o topic).
5.  Dopo la creazione della coda o dell'argomento, verrà visualizzato il messaggio **Creation of Queue ‘[Queue Name]’ Completed**.

    a. Se non si dispone di uno spazio dei nomi in questa area o nella sottoscrizione di Azure, ne verrà creato uno automaticamente. In questo caso, si riceveranno due messaggi che indicano il completamento dell'operazione: uno per la creazione dello spazio dei nomi e l'altro per la creazione dell'entità.

    ![][]

Fare clic sull'icona **Service Bus** nella barra di spostamento a sinistra per ottenere un elenco di spazi dei nomi. Sarà disponibile il nuovo spazio dei nomi appena creato. Fare clic sullo spazio dei nomi nell'elenco. Verrà visualizzata l'entità appena creata sotto quello spazio dei nomi.

**Nota** Lo spazio dei nomi potrebbe non venire visualizzato immediatamente. La creazione dello spazio dei nomi servizio e l'aggiornamento dell'interfaccia del portale richiedono alcuni secondi.

**Nota** L'utilizzo di **Quick Create** per un inoltro non crea un nuovo endpoint di inoltro ma crea solamente uno spazio dei nomi sotto cui è possibile creare endpoint di inoltro a livello di codice. Per altre informazioni, vedere la [documentazione del bus di servizio][documentazione del bus di servizio].

### Custom Create

**Custom Create** è una versione più dettagliata che offre i controlli necessari per modificare i valori predefiniti delle proprietà di un'entità (coda o argomento) in corso di creazione. Per creare un argomento o entità usando **Custom Create**, eseguire la procedura seguente:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **New** in basso a sinistra nel portale di gestione.
3.  Fare clic sull'icona **App Services**, quindi fare clic su **Service Bus Queue** (argomento o inoltro). Fare clic su Custom Create.
4.  Nella prima finestra di dialogo immettere il nome della coda, l'area e l'ID sottoscrizione di Azure.

    a. Se questo è il primo spazio dei nomi servizio nell'area selezionata, il portale suggerisce una coda di spazi dei nomi: [nome\_entità]-ns. È possibile modificare questo valore.

    b. Se si dispone già di almeno uno spazio dei nomi in questa area, ne verrà selezionato uno automaticamente. È possibile modificare lo spazio dei nomi selezionato.

5.  Fare clic su **Avanti** per inserire le proprietà rimanenti.

    ![][1]

6.  Fare clic sul segno di spunta per creare la coda.

    ![][2]

Fare clic sull'icona **Service Bus** nella barra di spostamento a sinistra per ottenere l'elenco di spazi dei nomi servizio. Sarà disponibile il nuovo spazio dei nomi appena creato. Fare clic sullo spazio dei nomi nell'elenco. Verrà visualizzata l'entità appena creata sotto quello spazio dei nomi.

## <span id="delete"></span></a>Procedura: Eliminare un'entità del bus di servizio

È possibile eliminare un'entità di messaggistica del bus di servizio usando il portale. Ciò si applica a code, argomenti e sottoscrizioni ad argomenti. Per eliminare una coda o argomento, eseguire le operazioni seguenti:

1.  Passare alla visualizzazione elenco degli spazi dei nomi servizio e fare clic sullo spazio dei nomi sotto cui si è creata l'entità (coda o argomento).
2.  Fare clic sull'icona **Delete** nella parte inferiore della pagina e confermare l'operazione di eliminazione.

    ![][3]

**Nota** L'eliminazione di un'entità è un'operazione irreversibile. Una volta eliminata un'entità, non sarà più possibile recuperarla. È tuttavia possibile creare un'altra entità con lo stesso nome.

Per eliminare la sottoscrizione a un argomento, eseguire le operazioni seguenti:

1.  Passare alla visualizzazione elenco degli spazi dei nomi e fare clic sullo spazio dei nomi sotto cui si è creata l'entità (coda o argomento).
2.  Fare clic sull'argomento sotto cui si è creata la sottoscrizione.
3.  Fare clic sulla scheda **Subscriptions** e selezionare la sottoscrizione che si desidera eliminare.
4.  Fare clic sull'icona **Delete** nella parte inferiore della pagina e confermare l'operazione di eliminazione.

## <span id="disableenable"></span></a>Procedura: Abilitare o disabilitare un'entità del bus di servizio

È possibile usare il portale per modificare lo stato di un'entità del bus di servizio. Ciò di applica a code e argomenti. Per abilitare o disabilitare una coda o argomento, eseguire le operazioni seguenti:

1.  Passare alla visualizzazione elenco degli spazi dei nomi servizio e fare clic sullo spazio dei nomi sotto cui si è creata l'entità (coda o argomento).
2.  Fare clic su **Disable** (o **Enable**)nella parte inferiore della pagina.

    ![][4]

## <span id="seealso"></span></a>Risorse aggiuntive

[Bus di servizio di Azure][Bus di servizio di Azure]

[Centro per sviluppatori .NET][Centro per sviluppatori .NET] nel sito Web di Azure

[Creazione di applicazioni che utilizzano argomenti e sottoscrizioni del bus di servizio][Creazione di applicazioni che utilizzano argomenti e sottoscrizioni del bus di servizio]

[Code, argomenti e sottoscrizioni][Code, argomenti e sottoscrizioni]

  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Procedura: Creare un'entità del bus di servizio]: #create
  [Procedura: Eliminare un'entità del bus di servizio]: #delete
  [Procedura: Abilitare o disabilitare un'entità del bus di servizio]: #disableenable
  [Risorse aggiuntive]: #seealso
  []: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [documentazione del bus di servizio]: http://www.windowsazure.com/it-it/develop/net/how-to-guides/service-bus-relay/
  [1]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [2]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [3]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [4]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Bus di servizio di Azure]: http://go.microsoft.com/fwlink/?LinkId=266834
  [Centro per sviluppatori .NET]: http://go.microsoft.com/fwlink/?LinkID=262187
  [Creazione di applicazioni che utilizzano argomenti e sottoscrizioni del bus di servizio]: http://go.microsoft.com/fwlink/?LinkId=264293
  [Code, argomenti e sottoscrizioni]: http://go.microsoft.com/fwlink/?LinkId=264291
