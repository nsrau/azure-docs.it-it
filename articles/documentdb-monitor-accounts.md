<properties 
	pageTitle="Monitorare un account DocumentDB | Azure" 
	description="Informazioni su come monitorare l'account DocumentDB per le metriche relative alle prestazioni (ad esempio richieste ed errori del server) e all'utilizzo (ad esempio il consumo di memoria)." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="mimig"/>

# Monitorare un account DocumentDB

È possibile monitorare gli account di DocumentDB nel [portale di anteprima di Azure][portale di anteprima di Azure]. Per ogni account DocumentDB sono disponibili le metriche delle prestazioni (errori delle richieste e del server) e le metriche di utilizzo (utilizzo dell'archiviazione).

## Contenuto dell'articolo

-   [Procedura: Visualizzare le metriche delle prestazioni per un account DocumentDB][Procedura: Visualizzare le metriche delle prestazioni per un account DocumentDB]
-   [Procedura: Personalizzare le visualizzazioni delle metriche delle prestazioni per un account DocumentDB][Procedura: Personalizzare le visualizzazioni delle metriche delle prestazioni per un account DocumentDB]
-   [Procedura: Creare grafici di metriche delle prestazioni affiancati][Procedura: Creare grafici di metriche delle prestazioni affiancati]
-   [Procedura: Visualizzare le metriche di utilizzo per un account DocumentDB][Procedura: Visualizzare le metriche di utilizzo per un account DocumentDB]
-   [Procedura: Configurare gli avvisi delle metriche delle prestazioni per un account DocumentDB][Procedura: Configurare gli avvisi delle metriche delle prestazioni per un account DocumentDB]
-   [Passaggi successivi][Passaggi successivi]

## <span id="metrics"></span></a>Procedura: Visualizzare le metriche delle prestazioni per un account DocumentDB

1.  Nel [portale di anteprima di Azure][portale di anteprima di Azure], fare clic su **Sfoglia**, su **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche delle prestazioni.
2.  Per impostazione predefinita, nella sezione **Monitoraggio** è possibile visualizzare:

    -   Richieste totali per il giorno corrente.
    -   Media delle richieste al secondo per il giorno corrente.

    ![][0]

3.  Se si fa clic sulla parte **Richieste totali o Media richieste al secondo**, verrà visualizzato il pannello **Metrica** dettagliato.
4.  Il pannello Metrica mostra i dettagli relativi alle metriche selezionate. Nella parte superiore del pannello è riportato un grafico, sotto il quale viene visualizzata una tabella contenente i valori aggregati delle metriche selezionate, ad esempio media, minimo e massimo. Nel pannello Metrica viene anche visualizzato l'elenco degli avvisi definiti, filtrato in base alle metriche visualizzate nel pannello Metrica corrente. In questo modo, se sono presenti più avvisi, nel pannello verranno presentati solo quelli pertinenti.

    ![][1]

## <span id="custom"></span></a>Procedura: Personalizzare le visualizzazioni delle metriche delle prestazioni per un account DocumentDB

1.  Per personalizzare le metriche visualizzate in una parte specifica, fare clic con il pulsante destro del mouse sul grafico della metrica e quindi scegliere **Modifica grafico**.
    ![][2]

2.  Nel pannello **Modifica grafico** sono disponibili le opzioni per modificare le metriche visualizzate nella parte, nonché il relativo intervallo di tempo.
    ![][3]

3.  Per modificare le metriche visualizzate nella parte, selezionare/deselezionare le metriche delle prestazioni disponibili e quindi fare clic su **Salva** nella parte inferiore del pannello.
4.  Per apportare cambiamenti all'intervallo di tempo è sufficiente scegliere un intervallo diverso, ad esempio **Ora precedente** e fare clic su **Salva** nella parte inferiore del pannello.
    ![][4]

5.  L'intervallo di tempo personalizzato consente di scegliere un periodo di tempo qualsiasi nelle ultime due settimane.
6.  Facendo clic su **Salva**, le modifiche verranno salvate fino all'uscita dal pannello dell'account di DocumentDB. All'accesso successivo al pannello, verranno visualizzate le metriche e l'intervallo di tempo originali.

## <span id="create"></span></a>Procedura: Creare grafici affiancati

Il portale di anteprima di Azure consente di creare grafici di metriche affiancati.

1.  Innanzitutto, fare clic con il pulsante destro del mouse sul grafico dal quale si intende iniziare e scegliere **Personalizza**.
    ![][5]

2.  Quindi fare clic su **Clone** nel menu per copiare la parte.

    ![][6]

3.  Infine, sulla barra degli strumenti nella parte superiore della schermata fare clic su **Fine**. Sarà possibile considerare questa parte come tutte le altre parti delle metriche e personalizzare le metriche e l'intervallo di tempo in essa visualizzato. Questa operazione consente di visualizzare contemporaneamente due grafici di metriche affiancati.
    ![][7]

> Si noti che l'intervallo di tempo del grafico e le metriche selezionate vengono reimpostati sui valori predefiniti della parte all'uscita dal portale di anteprima di Azure.

## <span id="view"></span></a>Procedura: Visualizzare le metriche di utilizzo per un account DocumentDB

1.  Nel [portale di anteprima di Azure][portale di anteprima di Azure], fare clic su **Sfoglia**, su **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche di utilizzo.
2.  Per impostazione predefinita, nella sezione **Utilizzo** è possibile visualizzare:

    -   Spazio di archiviazione utilizzato nell'account
    -   Spazio di archiviazione disponibile massimo dell'account
    -   Utilizzo di allegati
    -   Utilizzo di utenti e autorizzazioni
    -   Allocazione di unità di capacità
    -   Utilizzo di allegati all'interno dell'account
        ![][8]

## <span id="setup"></span></a>Procedura: Configurare gli avvisi delle metriche delle prestazioni per un account DocumentDB

1.  Nel [portale di anteprima di Azure][portale di anteprima di Azure], fare clic su **Sfoglia**, su **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono configurare gli avvisi delle metriche delle prestazioni.
2.  Nella sezione **Operazioni** fare clic sulla parte **Regole di avviso**.
    ![][9]

3.  Nel pannello Regole di avviso fare clic su **Aggiungi avviso**.
    ![][10]

4.  Nel pannello **Aggiungi una regola di avviso** specificare:

    -   Il nome della regola di avviso che si sta configurando.
    -   Una descrizione della nuova regola di avviso.
    -   La metrica per la regola di avviso.
    -   Condizione, soglia e periodo per l'attivazione della regola. Ad esempio, un numero di errori del server maggiore di 5 negli ultimi 15 minuti.
    -   Se deve essere inviato un messaggio di posta elettronica all'amministratore del servizio e ai coamministratori quando viene attivata la regola.
    -   Indirizzi di posta elettronica aggiuntivi per le notifiche degli avvisi.
        ![][11]

## <span id="next"></span></a>Passaggi successivi

-   Per altre informazioni su DocumentDB, vedere la documentazione di Azure DocumentDB su [azure.com][azure.com]

<!--Anchors-->

  [portale di anteprima di Azure]: https://portal.azure.com/
  [Procedura: Visualizzare le metriche delle prestazioni per un account DocumentDB]: #metrics
  [Procedura: Personalizzare le visualizzazioni delle metriche delle prestazioni per un account DocumentDB]: #custom
  [Procedura: Creare grafici di metriche delle prestazioni affiancati]: #create
  [Procedura: Visualizzare le metriche di utilizzo per un account DocumentDB]: #view
  [Procedura: Configurare gli avvisi delle metriche delle prestazioni per un account DocumentDB]: #setup
  [Passaggi successivi]: #next
  [0]: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319

<!--HONumber=46--> 
