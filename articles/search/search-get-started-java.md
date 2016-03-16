<properties
	pageTitle="Introduzione a Ricerca di Azure in Java | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Come compilare un'applicazione di ricerca cloud ospitata in Azure usando Java come linguaggio di programmazione."
	services="search"
	documentationCenter=""
	authors="EvanBoyle"
	manager="pablocas"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="03/08/2016"
	ms.author="evboyle"/>

# Introduzione a Ricerca di Azure in Java
> [AZURE.SELECTOR]
- [Portale](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Informazioni su come compilare un'applicazione di ricerca Java personalizzata che utilizza Ricerca di Azure per l’esperienza di ricerca. L'esercitazione utilizza l’[API REST del servizio Ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per costruire gli oggetti e le operazioni utilizzati in questo esercizio.

Per eseguire questo esempio, è necessario un servizio di Ricerca di Azure, a cui è possibile iscriversi nel [portale di Azure](https://portal.azure.com). Per istruzioni dettagliate, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

Per compilare e testare questo esempio è stato utilizzato il seguente software:

- [Eclipse IDE for Java EE Developers](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Assicurarsi di scaricare la versione EE: uno dei passaggi di verifica richiede una funzionalità presente solo in questa edizione.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## Informazioni sui dati

L’applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) con il filtro dello stato del Rhode Island per ridurre la dimensione del set di dati. Tali dati saranno utilizzati per compilare un'applicazione di ricerca che restituisce gli edifici di riferimento quali ospedali e scuole nonché caratteristiche geologiche come fiumi, laghi e vette.

In questa applicazione, il programma **SearchServlet.java** compila e carica l'indice utilizzando un costrutto [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando il set di dati filtrato dei servizi geologici degli Stati Uniti da un database SQL di Azure pubblico. Nel codice del programma vengono fornite credenziali predefinite e la connessione all’origine dati online. In termini di accesso ai dati, non è necessaria alcuna ulteriore configurazione.

> [AZURE.NOTE] A questo set di dati è stato applicato un filtro per restare sotto il limite di 10.000 documenti del livello di prezzo gratuito. Se si utilizza il livello standard, questo limite non viene applicato ed è possibile modificare il codice per l'utilizzo di un set di dati più grande. Per ulteriori informazioni sulla capacità per ogni livello di prezzo, vedere [Limiti e vincoli](search-limits-quotas-capacity.md).

## Informazioni sui file di programma

Nell'elenco seguente vengono descritti i file che sono rilevanti per questo esempio.

- Search.jsp: fornisce l'interfaccia utente
- SearchServlet.java: fornisce i metodi (simile a un controller MVC)
- SearchServiceClient.java: gestisce le richieste HTTP
- SearchServiceHelper.java: una classe di supporto che fornisce metodi statici
- Document.Java: fornisce il modello di dati
- config.properties: imposta l'URL del servizio di ricerca e la chiave dell'api
- Pom.xml: una dipendenza Maven

<a id="sub-2"></a>
## Individuare il nome del servizio e la chiave API del servizio Ricerca di Azure

Per tutte le chiamate API REST a Ricerca di Azure è necessario specificare l'URL del servizio e una chiave API.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di spostamento fare clic su **Servizio di ricerca** per elencare tutti i servizi di Ricerca di Azure di cui è stato effettuato il provisioning per la sottoscrizione.
3. Selezionare il servizio che si vuole usare.
4. Nel dashboard del servizio saranno presenti i riquadri per le informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.

  	![][3]

5. Copiare l'URL del servizio e una chiave di amministrazione. Sarà necessario utilizzarli in seguito, quando vengono aggiunti al file **config.properties**.

## Scaricare i file di esempio

1. Passare a [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) su GitHub.

2. Fare clic su **Scarica ZIP**, salvare il file con estensione zip su disco e quindi estrarre tutti i file in esso contenuti. È consigliabile estrarre i file nell'area di lavoro Java per trovare più facilmente il progetto in un secondo momento.

3. I file di esempio sono di sola lettura. Fare clic con il pulsante destro del mouse sulle proprietà della cartella e deselezionare l'attributo di sola lettura.

Tutte le successive modifiche e le istruzioni di esecuzione verranno effettuate sui file in questa cartella.

## Importare il progetto

1. In Eclipse scegliere **File** > **Importa** > **Generale** > **Progetti esistenti nell'area di lavoro**.

    ![][4]

2. In **Select root directory**, passare alla cartella contenente i file di esempio. Selezionare la cartella che contiene la cartella .project. Il progetto verrà visualizzato nell’elenco **Projects** come elemento selezionato.

    ![][12]

3. Fare clic su **Finish**.

4. Utilizzare **Project Explorer** per visualizzare e modificare i file. Se non è già aperto, fare clic su **Finestra** > **Mostra visualizzazione** > **Esplora progetti** oppure usare il collegamento per aprirlo.

## Configurare l'URL del servizio e la chiave API

1. In **Project Explorer**, fare doppio clic su **config.properties** per modificare le impostazioni di configurazione contenenti il nome del server e la chiave dell'api.

2. Vedere i passaggi descritti in precedenza in questo articolo, in cui sono stati trovati l'URL del servizio e la chiave API nel [portale di Azure](https://portal.azure.com), per ottenere i valori da immettere in **config.properties**.

3. In **config.properties**, sostituire "Api Key" con la chiave dell’api appropriata per il servizio. Successivamente, il nome del servizio (il primo componente dell'URL http://servicename.search.windows.net) sostituisce "service name" nello stesso file.

	![][5]

## Configurare gli ambienti di progetto, compilazione e runtime

1. In Eclipse, fare clic in Esplora progetti sul progetto e quindi su **Proprietà** > **Facet progetto**.

2. Selezionare **Dynamic Web Module**, **Java** e **JavaScript**.

    ![][6]

3. Fare clic su **Apply**.

4. Selezionare **Finestra** > **Preferenze** > **Server** > **Ambienti di runtime** > **Aggiungi**.

5. Espandere Apache e selezionare la versione del server Apache Tomcat installata in precedenza. In questo sistema è installata la versione 8.

	![][7]

6. Nella pagina successiva, specificare la directory di installazione di Tomcat. In un computer Windows, sarà probabilmente C:\\Programmi\\Microsoft Files\\Apache Software Foundation\\Tomcat *versione*.

6. Fare clic su **Finish**.

7. Selezionare **Finestra** > **Preferenze** > **Java** > **JREs installato** > **Aggiungi**.

8. In **Add JRE**, selezionare **Standard VM**.

10. Fare clic su **Avanti**.

11. Nella definizione dell'ambiente JRE, nella home di JRE, fare clic su **Directory**.

12. Passare a **Programmi** > **Java** e selezionare il JDK installato in precedenza. È importante selezionare JDK come JRE.

13. In Installed JREs, scegliere il **JDK**. Il risultato finale dovrebbe essere simile a quello riportato nella schermata seguente.

    ![][9]

14. Facoltativamente, selezionare **Finestra** > **Web Browser** > **Internet Explorer** per aprire l'applicazione in una finestra del browser esterno. L’utilizzo di un browser esterno offre una migliore esperienza di applicazione Web.

    ![][8]

La configurazione del dispositivo è stata completata. A questo punto, compilare ed eseguire il progetto.

## Compilare il progetto

1. In Esplora progetti, fare clic con il pulsante destro del mouse sul progetto e scegliere **Esegui come** > **Build Maven...** per configurare il progetto.

    ![][10]

8. In Edit Configuration, in Goals, digitare "clean install", quindi fare clic su **Run**.

I messaggi di stato vengono visualizzati nella finestra della console. Un messaggio di compilazione completata indica che il progetto è stato compilato senza errori.

## Esecuzione dell'app

In questo ultimo passaggio, verrà eseguita l'applicazione in un ambiente di runtime del server locale.

Se ancora non è stato specificato un ambiente di runtime del server in Eclipse, è necessario eseguire innanzitutto tale operazione.

1. In Project Explorer espandere **WebContent**.

5. Fare clic con il pulsante destro del mouse su **Search.jsp** > **Esegui come** > **Esegui come controllo server**. Selezionare il server Apache Tomcat, quindi fare clic su **Run**.

> [AZURE.TIP] Se è stata utilizzata un'area di lavoro non predefinita per archiviare il progetto, è necessario modificare la **configurazione di esecuzione** in modo che punti al percorso del progetto per evitare un errore di avvio del server. In Esplora progetti, fare clic con il pulsante destro del mouse su **Search.jsp** > **Esegui come** > **Configurazione di esecuzione**. Selezionare il server Apache Tomcat. Fare clic su **Arguments**. Fare clic su **Workspace** o **File system** per impostare la cartella contenente il progetto.

Quando si esegue l'applicazione viene visualizzata una finestra del browser con una casella di ricerca per l'immissione di termini.

Attendere circa un minuto prima di fare clic su **Ricerca** per consentire al servizio di creare e caricare l'indice. Se si verifica un errore HTTP 404, è necessario attendere un po' più a lungo prima di riprovare.

## Eseguire ricerche sui dati dei servizi geologici degli Stati Uniti

Il set di dati dei servizi geologici degli Stati Uniti include i dati relativi allo stato del Rhode Island. Se si fa clic su **Ricerca** su una casella di ricerca vuota, si otterranno le prime 50 voci, ossia l'impostazione predefinita.

L’immissione di un termine di ricerca fornirà al motore di ricerca un elemento con cui continuare. Provare a immettere un nome locale. "Roger Williams" è stato il primo governatore del Rhode Island. Numerosi parchi, edifici e scuole prendono il suo nome.

![][11]

È inoltre possibile tentare con uno dei termini seguenti:

- Pawtucket
- Pembroke
- goose +cape

## Passaggi successivi

Questa è la prima esercitazione di Ricerca di Azure basata su Java e sul set di dati dei servizi geologici degli Stati Uniti. Nel corso del tempo, l’esercitazione sarà ampliata per illustrare le funzionalità di ricerca aggiuntive che potrebbero essere utili nelle soluzioni personalizzate.

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile utilizzare questo esempio come base per ulteriore sperimentazione, ad esempio ampliando la [pagina di ricerca](search-pagination-page-layout.md) o implementando l’[esplorazione basata su facet](search-faceted-navigation.md). È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](https://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. È inoltre possibile visualizzare i collegamenti nell'[elenco di video ed esercitazioni](search-video-demo-tutorial-list.md) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

<!---HONumber=AcomDC_0309_2016-->