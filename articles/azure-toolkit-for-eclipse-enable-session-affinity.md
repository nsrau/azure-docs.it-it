<properties
    pageTitle="Abilitare l'affinità di sessione tramite il Toolkit di Azure per Eclipse"
    description="Informazioni su come abilitare l'affinità di sessione tramite il Toolkit di Azure per Eclipse"
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
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# Abilitare l'affinità di sessione #

Nel Toolkit di Azure per Eclipse, è possibile abilitare l'affinità di sessione HTTP, o "sessioni permanenti", per i ruoli. La figura seguente mostra la finestra di dialogo delle proprietà di **Bilanciamento del carico** utilizzata per abilitare la funzionalità di affinità di sessione:

![][ic719492]

## Per abilitare l'affinità di sessione per il ruolo ##

1. Fare clic con il tasto destro sul ruolo in Project Explorer di Eclipse, fare clic su **Azure**, quindi fare clic su **Bilanciamento del carico**.
1. Nella finestra di dialogo **Proprietà per il bilanciamento del carico del WorkerRole1**:
    1. Controllare **Abilita l'affinità di sessione attiva HTTP (sessioni permanenti) per questo ruolo.**
    1. Per gli **Endpoint di input da utilizzare**, selezionare un endpoint di input da utilizzare, ad esempio, **http (public: 80, private: 8080)**. L'applicazione deve usare questo endpoint come l’endpoint HTTP. È possibile abilitare più endpoint per il ruolo, ma è possibile selezionare solo uno di essi per supportare le sessioni permanenti.
    1. Ricompilare l'applicazione

Una volta abilitato, se si dispone di più di un'istanza del ruolo, le richieste HTTP provenienti da un determinato client continueranno ad essere gestite dalla stessa istanza del ruolo.

Il Toolkit di Eclipse lo consente installando un modulo IIS speciale denominato Application Request Routing (ARR) in tutte le istanze del ruolo. ARR reindirizza le richieste HTTP all'istanza del ruolo appropriato. Il toolkit riconfigura automaticamente l'endpoint selezionato in modo che il traffico HTTP in ingresso viene instradato al software ARR. Il toolkit crea anche un nuovo endpoint interno al quale server Java deve stare attenti per configurazione. Si tratta dell'endpoint usato da ARR per reindirizzare il traffico HTTP all'istanza del ruolo appropriato. In questo modo, ogni istanza del ruolo nella distribuzione multi-istanza funge da proxy inverso per tutte le altre istanze, abilitando le sessioni permanenti.

## Note sull'affinità di sessione ##

* L’affinità di sessione non funziona nell'emulatore di calcolo. Le impostazioni possono essere applicate nell'emulatore di calcolo senza interferire con il processo di compilazione o con l’esecuzione dell'emulatore di calcolo, ma la funzionalità di per sé non funziona nell'emulatore di calcolo.
* L’abilitazione dell’affinità di sessione comporterà un aumento della quantità di spazio nel disco preso dalla distribuzione in Azure, come software aggiuntivo verrà scaricato e installato nelle istanze del ruolo quando il servizio viene avviato nel cloud di Azure.
* Il tempo necessario per inizializzare ogni ruolo sarà maggiore.
* Verrà aggiunto un endpoint interno, che fungerà da rerouter di traffico, come indicato in precedenza.

Per un esempio di come gestire i dati della sessione quando viene abilitata l'affinità di sessione, vedere [Come mantenere i dati della sessione con l’Affinità di sessione][].

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Creazione di un'applicazione Hello World per Azure in Eclipse][]

[Installare il Toolkit di Azure per Eclipse.][]

[Come gestire i dati della sessione con affinità di sessione][]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][].

<!-- URL List -->  

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Come gestire i dati della sessione con affinità di sessione]: http://go.microsoft.com/fwlink/?LinkID=699539
[Come mantenere i dati della sessione con l’Affinità di sessione]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->  

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!---HONumber=AcomDC_0817_2016-->