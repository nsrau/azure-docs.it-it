<properties
	pageTitle="Introduzione a Ricerca di Azure in Java"
	description="Illustra la creazione di un'applicazione Ricerca di Azure personalizzata utilizzando Java come linguaggio di programmazione."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na"
	ms.date="06/24/2015"
	ms.author="heidist"/>

#Introduzione a Ricerca di Azure in Java#

Informazioni su come compilare un'applicazione di ricerca Java personalizzata che utilizza Ricerca di Azure per l’esperienza di ricerca. L'esercitazione utilizza l’[API REST del servizio Ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per costruire gli oggetti e le operazioni utilizzate in questo esercizio.

Per compilare e testare questo esempio è stato utilizzato il seguente software:

- [Eclipse IDE for Java EE Developers](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Assicurarsi di scaricare la versione EE: uno dei passaggi di verifica richiede una funzionalità presente solo in questa edizione.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

Per eseguire questo esempio, è necessario disporre di un servizio Ricerca di Azure, a cui è possibile iscriversi nel [portale di gestione di Azure](https://portal.azure.com).

> [AZURE.TIP]Scaricare il codice sorgente per questa esercitazione da [Azure Search Java demo](http://go.microsoft.com/fwlink/p/?LinkId=530197) su GitHub.

## Informazioni sui dati

L’applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) con il filtro dello stato del Rhode Island per ridurre la dimensione del set di dati. Tali dati saranno utilizzati per compilare un’applicazione di ricerca che restituisce gli edifici di riferimento quali ospedali e scuole nonché caratteristiche geologiche come fiumi, laghi e vette.

In questa applicazione, il programma **SearchServlet.java** compila e carica l'indice utilizzando un costrutto [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando il set di dati filtrato dei servizi geologici degli Stati Uniti da un database SQL di Azure pubblico. Nel codice del programma vengono fornite credenziali predefinite e la connessione all’origine dati online. In termini di accesso ai dati, non è necessaria alcuna ulteriore configurazione.

> [AZURE.NOTE]A questo set di dati è stato applicato un filtro per restare sotto il limite di 10.000 documenti del livello di prezzo gratuito. Se si utilizza il livello standard, questo limite non viene applicato ed è possibile modificare il codice per l'utilizzo di un set di dati più grande. Per ulteriori informazioni sulla capacità per ogni livello di prezzo, vedere [Limiti e vincoli](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Informazioni sui file di programma

Nell'elenco seguente vengono descritti i file che sono rilevanti per questo esempio.

- Search.jsp: fornisce l'interfaccia utente
- SearchServlet.java: fornisce i metodi (simile a un controller MVC)
- SearchServiceClient.java: gestisce le richieste HTTP
- SearchServiceHelper.java: una classe di supporto che fornisce metodi statici
- Document.Java: fornisce il modello di dati
- config.properties: imposta l'URL del servizio di ricerca e la chiave dell'api
- Pom.xml: una dipendenza Maven


## Creare il servizio

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nell'indice, fare clic su **Nuovo** | **Dati + archiviazione** | **Ricerca**.

     ![][1]

3. Configurare il nome del servizio, il livello di prezzo, il gruppo di risorse, la sottoscrizione e la posizione. Queste impostazioni sono necessarie e non possono essere modificate dopo il provisioning del servizio.

     ![][2]

	- Il **nome del servizio** deve essere univoco, in lettere minuscole, lungo massimo 15 caratteri e senza spazi. Questo nome diventa parte dell'endpoint del servizio Ricerca di Azure. Vedere [Regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) per ulteriori informazioni sulle convenzioni di denominazione.

	- Il **livello di prezzo** determina capacità e fatturazione. Entrambi i livelli offrono le stesse funzionalità, ma a livelli diversi di risorse.

		- Il livello **gratuito** viene eseguito in cluster condivisi con altri sottoscrittori. Offre capacità sufficienti per provare le esercitazioni e scrivere codice per i modelli di prova, ma non è consigliato per le applicazioni in ambienti di produzione. La distribuzione di un servizio gratuito in genere richiede solo pochi minuti.
		- Il livello **standard** viene eseguito su risorse dedicate ed è altamente scalabile. Inizialmente viene eseguito il provisioning di un servizio standard con una replica e una partizione, ma è possibile regolare la capacità una volta creato il servizio. La distribuzione di un servizio standard richiede più tempo, in genere circa 15 minuti.

	- I **gruppi di risorse** sono contenitori per i servizi e le risorse usati per uno scopo comune. Ad esempio, se si intende creare un'applicazione di ricerca personalizzata basata su Ricerca di Azure, Siti Web di Azure, il servizio di archiviazione BLOB di Azure, è possibile creare un gruppo di risorse per raggruppare questi servizi nelle pagine di gestione del portale.

	- L’opzione **Sottoscrizione** consente di scegliere tra più sottoscrizioni, se si dispone di più di una sottoscrizione.

	- La **posizione** è l'area geografica del data center. Attualmente, tutte le risorse devono essere eseguite nello stesso data center. La distribuzione di risorse tra più data center non è supportata.

4. Fare clic su **Crea** per eseguire il provisioning del servizio.

Controllare le notifiche nell'indice. Quando il servizio è pronto per l'utilizzo, viene visualizzato un avviso.

<a id="sub-2"></a>
## Individuare il nome del servizio e la chiave API del servizio Ricerca di Azure

Dopo aver creato il servizio, è possibile tornare al portale per ottenere l'URL e `api-key`. Per le connessioni al servizio Ricerca è necessario disporre sia dell'URL che di una `api-key` per l'autenticazione della chiamata.

1. Nell’indice, fare clic su **Home**, quindi fare clic sul servizio Ricerca per aprire il dashboard del servizio.

2. Nel dashboard del servizio, saranno riportate sezioni per informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.

  	![][3]

3. Copiare l'URL del servizio e una chiave di amministrazione. Sarà necessario utilizzarli in seguito, quando vengono aggiunti al file **config.properties**.

## Scaricare i file di esempio

1. Andare a [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) su Github.

2. Fare clic su **Scarica ZIP**, salvare il file con estensione zip su disco e quindi estrarre tutti i file in esso contenuti. È consigliabile estrarre i file nell'area di lavoro Java per trovare più facilmente il progetto in un secondo momento.

3. I file di esempio sono di sola lettura. Fare clic con il pulsante destro del mouse sulle proprietà della cartella e deselezionare l'attributo di sola lettura.

Tutte le successive modifiche e le istruzioni di esecuzione verranno effettuate sui file in questa cartella.

## Importare il progetto

1. In Eclipse scegliere **File** | **Import** | **General** | **Existing Projects into Workspace**.

    ![][4]

2. In **Select root directory**, passare alla cartella contenente i file di esempio. Selezionare la cartella che contiene la cartella .project. Il progetto verrà visualizzato nell’elenco **Projects** come elemento selezionato.

    ![][12]

3. Fare clic su **Finish**.

4. Utilizzare **Project Explorer** per visualizzare e modificare i file. Se non è già aperto, fare clic su **Window** | **Show View** | **Project Explorer** oppure utilizzare il collegamento per aprirlo.

## Configurare l'URL del servizio e la chiave Api

1. In **Project Explorer**, fare doppio clic su **config.properties** per modificare le impostazioni di configurazione contenenti il nome del server e la chiave dell'api.

2. Fare riferimento ai passaggi descritti in precedenza in questo articolo, in cui sono forniti l'URL del servizio e la chiave api nel [portale Azure](https://portal.azure.com), per ottenere i valori da immettere in **config.properties**.

3. In **config.properties**, sostituire "Api Key" con la chiave dell’api appropriata per il servizio. Successivamente, il nome del servizio (il primo componente dell'URL http://servicename.search.windows.net) sostituisce "service name" nello stesso file.

	![][5]

## Configurare gli ambienti di progetto, compilazione e runtime

1. In Eclipse, in Project Explorer, fare clic con il pulsante destro del mouse sul progetto | **Properties** | **Project Facets**.

2. Selezionare **Dynamic Web Module**, **Java** e **JavaScript**.

    ![][6]

3. Fare clic su **Apply**.

4. Selezionare **Window** | **Preferences** | **Server** | **Runtime Environments** | **Add..**.

5. Espandere Apache e selezionare la versione del server Apache Tomcat installata in precedenza. In questo sistema è installata la versione 8.

	![][7]

6. Nella pagina successiva, specificare la directory di installazione di Tomcat. In un computer Windows, sarà probabilmente C:\Programmi\Microsoft Files\Apache Software Foundation\Tomcat *versione*.

6. Fare clic su **Finish**.

7. Selezionare **Window** | **Preferences** | **Java** | **Installed JREs** | **Add**.

8. In **Add JRE**, selezionare **Standard VM**.

10. Fare clic su **Avanti**.

11. Nella definizione dell'ambiente JRE, nella home di JRE, fare clic su **Directory**.

12. Passare a **Program Files** | **Java** e selezionare il JDK installato in precedenza. È importante selezionare JDK come JRE.

13. In Installed JREs, scegliere il **JDK**. Il risultato finale dovrebbe essere simile a quello riportato nella schermata seguente.

    ![][9]

14. Facoltativamente, selezionare **Window** | **Web Browser** | **Internet Explorer** per aprire l'applicazione in una finestra del browser esterno. L’utilizzo di un browser esterno offre una migliore esperienza di applicazione Web.

    ![][8]

La configurazione del dispositivo è stata completata. A questo punto, compilare ed eseguire il progetto.

## Compilare il progetto

1. In Project Explorer, fare clic con il pulsante destro del mouse sul progetto e scegliere **Run As** | **Maven build...** per configurare il progetto.

    ![][10]

8. In Edit Configuration, in Goals, digitare "clean install", quindi fare clic su **Run**.

I messaggi di stato vengono visualizzati nella finestra della console. Un messaggio di compilazione completata indica che il progetto è stato compilato senza errori.

## Esecuzione dell'app

In questo ultimo passaggio, verrà eseguita l'applicazione in un ambiente di runtime del server locale.

Se ancora non è stato specificato un ambiente di runtime del server in Eclipse, è necessario eseguire innanzitutto tale operazione.

1. In Project Explorer espandere **WebContent**.

5. Fare clic con il pulsante destro del mouse su **Search.jsp** | **Run As** | **Run on Server**. Selezionare il server Apache Tomcat, quindi fare clic su **Run**.

> [AZURE.TIP]Se si utilizza un'area di lavoro non predefinita per archiviare il progetto, è probabilmente necessario modificare la **configurazione di esecuzione** in modo che punti al percorso del progetto per evitare un errore di avvio del server. In Project Explorer, fare clic con il pulsante destro del mouse su **Search.jsp** | **Run As** | **Run Configurations**. Selezionare il server Apache Tomcat. Fare clic su **Arguments**. Fare clic su **Workspace** o **File system** per impostare la cartella contenente il progetto.

Quando si esegue l'applicazione viene visualizzata una finestra del browser con una casella di ricerca per l'immissione di termini.

Attendere circa un minuto prima di fare clic su **Search** per consentire al servizio di creare e caricare l'indice. Se si verifica un errore HTTP 404, è necessario attendere un po' più a lungo prima di riprovare.

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

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile utilizzare questo esempio come base per ulteriore sperimentazione, ad esempio ampliando la [pagina di ricerca](search-pagination.md) o implementando l’[esplorazione basata su facet](../search-faceted-navigation/). È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](http://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. È inoltre possibile visualizzare i collegamenti nell'[elenco di video ed esercitazioni](https://msdn.microsoft.com/library/azure/dn798933.aspx) per accedere a ulteriori informazioni.

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
 

<!---HONumber=July15_HO4-->