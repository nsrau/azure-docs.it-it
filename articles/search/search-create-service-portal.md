<properties
	pageTitle="Creare un servizio di Ricerca di Azure nel portale | Microsoft Azure"
	description="Aggiungere un servizio di Ricerca di Azure gratuito o standard a una sottoscrizione esistente tramite il portale di Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Creare un servizio di Ricerca di Azure nel portale di Azure

Ricerca di Microsoft Azure è un nuovo servizio che consente di incorporare funzionalità di ricerca in applicazioni personalizzate. Fornisce il motore di ricerca e il servizio di archiviazione per i dati di ricerca, ai quali è possibile accedere e che possono essere gestiti tramite il portale di Azure, un .NET SDK o un'API REST. Funzionalità chiave includono query di completamento automatico, corrispondenza fuzzy, esplorazione in base a facet, collaborazione e supporto multilingue. Per altre informazioni sulle funzionalità di Ricerca di Azure, vedere l'articolo di [introduzione a Ricerca di Azure](fundamentals-azure-search-chappell/).

## Aggiungere Ricerca di Azure alla sottoscrizione  

In qualità di amministratore, è possibile aggiungere Ricerca di Azure a una sottoscrizione esistente senza costi aggiuntivi se si sceglie il servizio condiviso o a un prezzo standard se si sceglie la soluzione con risorse dedicate.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nell'indice fare clic su **Nuovo** > **Dati e archiviazione** > **Ricerca**.

     ![][1]

3. Configurare il nome del servizio, il piano tariffario, il gruppo di risorse, la sottoscrizione e la posizione. Queste impostazioni sono necessarie e non possono essere modificate dopo il provisioning del servizio.

     ![][2]

	- Il **nome del servizio** deve essere univoco, in lettere minuscole e lungo massimo 15 caratteri senza spazi. Questo nome diventa parte dell'endpoint del servizio Ricerca di Azure. Per altre informazioni sulle convenzioni di denominazione, vedere [Regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx).

	- Il **piano tariffario** determina capacità e fatturazione. Entrambi i livelli offrono le stesse funzionalità, ma a livelli diversi di risorse.

		- Il livello **gratuito** viene eseguito in cluster condivisi con altri sottoscrittori. Offre capacità sufficienti per provare le esercitazioni e scrivere codice per i modelli di prova, ma non è consigliato per le applicazioni in ambienti di produzione. La distribuzione di un servizio gratuito in genere richiede solo pochi minuti.
		- Il livello **standard** viene eseguito su risorse dedicate ed è altamente scalabile. Inizialmente viene eseguito il provisioning di un servizio standard con una replica e una partizione, ma è possibile regolare la capacità una volta creato il servizio. La distribuzione di un servizio standard richiede più tempo, in genere circa 15 minuti.

	- I **gruppi di risorse** sono contenitori per i servizi e le risorse usati per uno scopo comune. Ad esempio, se si intende creare un'applicazione di ricerca personalizzata basata su Ricerca di Azure, la funzionalità per app Web di Servizio app di Azure e il servizio di archiviazione BLOB di Azure, è possibile creare un gruppo di risorse per raggruppare questi servizi nelle pagine di gestione del portale.

	- L’opzione **Sottoscrizione** consente di scegliere tra più sottoscrizioni, se si dispone di più di una sottoscrizione.

	- La **posizione** è l'area geografica del data center. Attualmente tutte le risorse devono essere eseguite nello stesso data center. La distribuzione di risorse tra più data center non è supportata.

4. Fare clic su **Crea** per eseguire il provisioning del servizio.

Controllare le notifiche nell'indice. Quando il servizio è pronto per l'uso, viene visualizzato un avviso.

<a id="sub-2"></a>
## Individuare il nome del servizio e le chiavi API del servizio Ricerca di Azure

Dopo aver creato il servizio, è possibile tornare al portale di Azure per ottenere l'URL o `api-key`. Per le connessioni al servizio Ricerca di Azure è necessario disporre sia dell'URL che di una `api-key` per l'autenticazione della chiamata.

1. Nell'indice fare clic su **Home** e quindi sul servizio Ricerca di Azure per aprire il dashboard del servizio.

2. Nel dashboard del servizio, saranno riportate sezioni per informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.

  	![][3]

3. Copiare l'URL del servizio e una chiave di amministrazione. Sarà necessario utilizzarli per l'attività successiva, [testare le operazioni di servizio](#sub-4).

<a id="sub-3"></a>
## Eseguire l'aggiornamento al livello standard

Molti clienti iniziano con il servizio gratuito, quindi eseguono l'aggiornamento al livello standard quando le prestazioni delle query o le risorse di archiviazione diventano insufficienti per i carichi di lavoro da eseguire. Il livello standard consente di ottenere risorse dedicate che possono essere usate solo dall'utente in un data center di Azure. Le operazioni di Ricerca di Azure richiedono repliche di archiviazione e del servizio. Quando si effettua l'iscrizione per la ricerca standard, è possibile ottimizzare la configurazione del servizio per usare una maggiore quantità della risorsa più importante per lo scenario corrente.

Per usare il livello standard, ripetere i passaggi precedenti riportati in questo articolo per creare un nuovo servizio di Ricerca di Azure scegliendo il piano tariffario Standard. Si noti che la configurazione di risorse dedicate può richiedere fino a 15 minuti o oltre.

Non esiste alcun aggiornamento sul posto della versione gratuita. Il passaggio al livello Standard, che include la scalabilità, richiede un nuovo servizio. Sarà necessario ricaricare gli indici e i documenti usati dall'applicazione di ricerca.

Un servizio di Ricerca di Azure al livello standard viene creato con una replica e una partizione, ma può essere facilmente ridimensionato a livelli più elevati di risorse.

1.	Dopo avere creato il servizio, tornare al dashboard del servizio.

2.	Fare clic sul riquadro **Scale**.

3.	Usare i dispositivi di scorrimento per aggiungere repliche, partizioni o entrambe.

Le repliche e le partizioni aggiuntive vengono fatturate in termini di unità di ricerca. Le unità di ricerca totali per supportare qualsiasi configurazione di risorsa specifica vengono visualizzate nella pagina, durante l'aggiunta delle risorse.

Per informazioni sul prezzo per unità, vedere [Dettagli prezzi](http://go.microsoft.com/fwlink/p/?LinkID=509792). Per assistenza su come configurare le combinazioni di partizione e replica, vedere [Limitazioni e vincoli](search-limits-quotas-capacity.md).

<a id="sub-4"></a>
## Testare le operazioni del servizio

L'ultimo passaggio della configurazione di Ricerca di Azure prevede la conferma che il servizio sia operativo e accessibile da un'applicazione client. È possibile usare uno dei collegamenti seguenti per un approccio senza scrittura di codice alla verifica della disponibilità del servizio.

- [Come utilizzare Chrome Postman con Ricerca di Azure](search-chrome-postman.md)
- [Come utilizzare Telerik Fiddler con Ricerca di Azure](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
## Passaggi successivi

Gli argomenti riportati di seguito contengono informazioni su come creare e gestire applicazioni di ricerca basate su Ricerca di Azure.

- [Come utilizzare Ricerca di Azure in .NET](search-howto-dotnet-sdk.md)

- [Gestire la soluzione di ricerca in Microsoft Azure](search-manage.md)

- [Ricerca di Azure su MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Video di Channel 9: Introduzione a Ricerca di Azure](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=Oct15_HO3-->