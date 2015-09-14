<properties 
	pageTitle="Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web"
	description="Un'esercitazione che illustra come usare il contenuto da una rete CDN per migliorare le prestazioni dell'applicazione Web."
	services="cdn"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"/>

<tags 
	ms.service="cdn"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="cephalin"/>

# Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web #

Questa esercitazione illustra come sfruttare la rete CDN di Azure per migliorare la capacità e le prestazioni dell'applicazione Web. La rete CDN di Azure consente di migliorare le prestazioni dell'applicazione Web nei casi seguenti:

- Nelle pagine sono presenti molti collegamenti a contenuto statico o semistatico
- I client possono accedere all'applicazione a livello globale
- Si vuole scaricare il traffico dal server Web
- Si vuole ridurre il numero di connessioni client simultanee al server Web (per altre informazioni su questo argomento, vedere la pagina relativa alla [creazione di bundle e alla minimizzazione](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)) 
- Si vuole aumentare il tempo di caricamento/aggiornamento percepito delle pagine

## Contenuto dell'esercitazione ##

In questa esercitazione si apprenderà come eseguire le operazioni seguenti:

-	[Rendere disponibile il contenuto statico da un endpoint della rete CDN di Azure](#deploy)
-	[Automatizzare il caricamento del contenuto dall'applicazione ASP.NET all'endpoint della rete CDN](#upload)
-	[Configurare la cache della rete CDN per riflettere l'aggiornamento del contenuto desiderato](#update)
-	[Rendere immediatamente disponibile il contenuto aggiornato mediante le stringhe di query](#query)

## Prerequisiti ##

Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-	Un [account Microsoft Azure](/account/) attivo. È possibile effettuare l'iscrizione per ottenere un account di valutazione.
-	Visual Studio 2013 con [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) per la GUI di gestione dei BLOB
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (usato dalla procedura per [automatizzare il caricamento del contenuto dall'applicazione ASP.NET all'endpoint della rete CDN](#upload))

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio i siti Web. È possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

<a name="static"></a>
## Rendere disponibile il contenuto statico da un endpoint della rete CDN di Azure ##

In questa sezione dell'esercitazione si apprenderà come creare una rete CDN e usarla per rendere disponibile il contenuto statico. I passaggi principali coinvolti sono:

1. Creare un account di archiviazione
2. Creare una rete CDN collegata a un account di archiviazione
3. Creare un contenitore BLOB nell'account di archiviazione
4. Caricare il contenuto nel contenitore BLOB
5. Collegarsi al contenuto caricato con l'URL della rete CDN

Seguire i passaggi descritti di seguito per iniziare a usare la rete CDN di Azure:

1. Per creare un endpoint della rete CDN, accedere al [portale di gestione di Azure](http://manage.windowsazure.com/). 
1. Fare clic su **Nuovo > Servizi dati > Archiviazione > Creazione rapida** per creare un account di archiviazione. Specificare un URL, un percorso e fare clic su **Crea account di archiviazione**. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE]Notare che si sta usando Asia orientale come area perché è abbastanza lontana dalla postazione per testare la rete CDN dall'America del Nord in un secondo momento.

2. Non appena lo stato del nuovo account di archiviazione è **Online**, creare un nuovo endpoint della rete CDN collegato all'account di archiviazione creato. Fare clic su **Nuovo > Servizi app > Rete CDN > Creazione rapida**. Selezionare l'account di archiviazione creato e fare clic su **Crea**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE]Dopo aver creato la rete CDN, nel portale di Azure viene visualizzato il relativo URL e il dominio di origine a esso collegato. È tuttavia possibile che la completa propagazione della configurazione dell'endpoint della rete CDN a tutte le ubicazioni dei nodi richieda del tempo.

3. Eseguire il ping dell'endpoint della rete CDN per testarlo in modo da assicurarsi che sia online. Se l'endpoint della rete CDN non è stato propagato a tutti i nodi, verrà visualizzato un messaggio simile a quello mostrato di seguito.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	Attendere un'altra ora ed eseguire di nuovo il test. Non appena l'endpoint della rete CDN ha terminato la propagazione ai nodi, risponderà ai ping.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. A questo punto, si può già osservare il nodo della rete CDN più vicino stabilito dall'endpoint. Dal computer desktop da cui si sta eseguendo il test l'indirizzo IP di risposta è **93.184.215.201**. Inserirlo in un sito come [www.ip-address.org](http://www.ip-address.org) per vedere che il server si trova a Washington D.C.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	Per un elenco di tutte le ubicazioni dei nodi della rete CDN, vedere [Ubicazioni dei noti della Rete di distribuzione dei contenuti (CDN) di Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

3. Tornare al portale di Azure e nella scheda **Rete CDN** fare clic sul nome dell'endpoint della rete CDN appena creato.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. Fare clic su **Abilita stringa di query** per abilitare le stringhe di query nella cache della rete CDN di Azure. Dopo averle abilitate, lo stesso collegamento a cui si accede con diverse stringhe di query verrà memorizzato nella cache come voci separate.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE]Benché non sia necessario abilitare la stringa di query per questa parte dell'esercitazione, è consigliabile farlo prima possibile per comodità, perché la propagazione al resto dei nodi di qualsiasi modifica apportata in questa fase richiederà del tempo e non è auspicabile che contenuti non abilitati per le stringhe query intasino la cache della rete CDN (l'aggiornamento del contenuto della rete CDN verrà discusso più avanti). Il vantaggio di questa operazione verrà illustrato in [Rendere immediatamente disponibile il contenuto aggiornato mediante le stringhe di query](#query).

6. In Esplora server di Visual Studio 2013 fare clic sul pulsante **Connessione a Microsoft Azure**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  Attenersi alla richiesta per accedere all'account Azure.
8.  Dopo aver eseguito l'accesso, espandere **Microsoft Azure > Archiviazione > account di archiviazione**. Fare clic con il pulsante destro del mouse su **BLOB** e scegliere **Crea contenitore BLOB**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	Specificare un nome per il contenitore BLOB e fare clic su **OK**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	In Esplora server fare doppio clic sul contenitore BLOB creato per gestirlo. Verrà visualizzata l'interfaccia di gestione nel riquadro centrale.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	Fare clic sul pulsante **Carica BLOB** per caricare le immagini, gli script o i fogli di stile usati dalle pagine Web nel contenitore BLOB. Lo stato di avanzamento del processo di caricamento verrà visualizzato nel **Log attività di Azure** e i BLOB verranno elencati nella visualizzazione del contenitore al termine del caricamento.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	Dopo aver caricato i BLOB, è necessario renderli pubblici per l'accesso. In Esplora server fare clic con il pulsante destro del mouse sul contenitore e scegliere **Proprietà**. Impostare la proprietà **Accesso in lettura pubblico** su **BLOB**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	Testare l'accesso pubblico dei BLOB accedendo all'URL di uno dei BLOB in un browser. Ad esempio, è possibile testare la prima immagine nell'elenco caricato con `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png`.

	Notare che non viene usato l'indirizzo HTTPS fornito nell'interfaccia di gestione dei BLOB in Visual Studio. Con HTTP è possibile testare se il contenuto è accessibile pubblicamente, che rappresenta un requisito per la rete CDN di Azure.

13.	Se il rendering del BLOB viene visualizzato correttamente nel browser, modificare l'URL da `http://<yourStorageAccountName>.blob.core.windows.net` nell'URL della rete CDN di Azure. Quindi, per testare la prima immagine nell'endpoint della rete CDN, usare `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`.

	>[AZURE.NOTE]È possibile trovare l'URL dell'endpoint della rete CDN nella scheda Rete CDN del portale di gestione di Azure.

	Se si confrontano le prestazioni dell'accesso diretto al BLOB e dell'accesso alla rete CDN, si può osservare un miglioramento delle prestazioni derivante dall'uso della rete CDN di Azure. Di seguito è riportata la schermata degli strumenti di sviluppo F12 di Internet Explorer 11 per l'accesso con l'URL del BLOB dell'immagine:

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	Per l'accesso con l'URL della rete CDN si avrà la stessa immagine:

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	Prestare attenzione ai numeri per la durata della **Richiesta**, ovvero il tempo di ricezione del primo byte o il tempo impiegato per l'invio della richiesta e la ricezione della prima risposta dal server. Quando si esegue l'accesso al BLOB ospitato nell'area dell'Asia orientale, il tempo impiegato è di 266 ms, poiché la richiesta deve attraversare tutto l'Oceano Pacifico per raggiungere il server. Tuttavia, se si accede alla rete CDN di Azure, il tempo impiegato è di soli 16 ms, che rappresenta quasi un **incremento delle prestazioni di 20 volte**.
	
15.	Ora si tratta semplicemente di usare il nuovo collegamento nella pagina Web. Ad esempio, è possibile aggiungere il tag immagine seguente:

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

In questa sezione si è appreso come creare un endpoint della rete CDN, caricarvi il contenuto e collegarsi al contenuto della rete CDN da una qualsiasi pagina Web.

<a name="upload"></a>
## Automatizzare il caricamento del contenuto dall'applicazione ASP.NET all'endpoint della rete CDN ##

Se si vuole caricare facilmente tutto il contenuto statico dell'applicazione Web ASP.NET nell'endpoint della rete CDN o se si distribuisce l'applicazione Web con il recapito continuo (per un esempio, vedere [Recapito continuo per Servizi cloud in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md)), è possibile usare Azure PowerShell per automatizzare la sincronizzazione dei file di contenuto più recenti nei BLOB di Azure ogni volta che si distribuisce l'applicazione Web. Ad esempio, è possibile eseguire lo script presente nella pagina relativa al [caricamento dei file di contenuto dall'applicazione ASP.NET nei BLOB di Azure](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) per caricare tutti i file di contenuto di un'applicazione ASP.NET. Per usare questo script:

4. Dal menu **Start** eseguire **Microsoft Azure PowerShell**.
5. Nella finestra Azure PowerShell eseguire `Get-AzurePublishSettingsFile` per scaricare un file di impostazioni di pubblicazione per l'account Azure.
6. Dopo aver scaricato il file di impostazioni di pubblicazione, eseguire il comando seguente: 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE]Una volta importato, il file di impostazioni di pubblicazione diventerà l'account Azure predefinito usato per tutte le sessioni di Azure PowerShell. Ciò significa che i passaggi sopra descritti devono essere eseguiti solo una volta.
	
1. Scaricare lo script dalla [pagina di download](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a). Salvarlo nella cartella di progetto dell'applicazione ASP.NET.
2. Fare clic con il pulsante destro del mouse sullo script scaricato e scegliere **Proprietà**.
3. Fare clic su **Sblocca**.
4. Aprire una finestra di PowerShell ed eseguire il comando seguente:

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

Questo script carica tutti i file dalle cartelle *\\Content* e *\\Scripts* nell'account di archiviazione e nel contenitore specificati. Lo script presenta i vantaggi seguenti:

-	Replica automaticamente la struttura dei file del progetto di Visual Studio
-	Crea automaticamente i contenitori BLOB a seconda delle esigenze
-	Riusa lo stesso account di archiviazione ed endpoint della rete CDN di Azure per più applicazioni Web, ognuna in un contenitore BLOB separato
-	Aggiorna facilmente la rete CDN di Azure con il nuovo contenuto. Per altre informazioni sull'aggiornamento del contenuto, vedere [Configurare la cache della rete CDN per riflettere l'aggiornamento del contenuto desiderato](#update).

Per il parametro `-StorageContainer` è consigliabile usare il nome dell'applicazione Web o il nome del progetto di Visual Studio. Considerato che precedentemente è stato usato il nome generico "cdn" come nome del contenitore, se si usa il nome dell'applicazione Web è possibile organizzare il contenuto correlato nello stesso contenitore facilmente identificabile.

Una volta terminato il caricamento del contenuto, è possibile collegarsi a qualsiasi elemento delle cartelle *\\Content* e *\\Scripts* nel codice HTML, come ad esempio nei file .cshtml, usando `http://<yourCDNName>.vo.msecnd.net/<containerName>`. Di seguito è riportato un esempio di un elemento da poter usare in una visualizzazione Razor:

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Per un esempio di integrazione degli script di PowerShell nella configurazione di recapito continuo, vedere [Recapito continuo per Servizi cloud in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md).

<a name="update"></a>
## Configurare la cache della rete CDN per riflettere l'aggiornamento del contenuto desiderato ##

A questo punto, dopo aver caricato i file statici dall'app Web in un contenitore BLOB, si supponga di apportare una modifica a uno dei file nel progetto e di caricarlo di nuovo nel contenitore BLOB. Ritenendo che il file venga aggiornato automaticamente nell'endpoint della rete CDN, ci si può domandare il perché l'aggiornamento non sia presente quando si accede all'URL della rete CDN del contenuto.

Il fatto è che la rete CDN viene effettivamente aggiornata automaticamente dalla risorsa di archiviazione BLOB, ma tale operazione viene eseguita applicando al contenuto una regola predefinita di memorizzazione nella cache di 7 giorni. Ciò significa che una volta che un nodo della rete CDN estrae il contenuto dalla risorsa di archiviazione BLOB, lo stesso contenuto non viene aggiornato finché non scade nella cache.

L'aspetto positivo è che è possibile personalizzare la scadenza della cache. La rete CDN di Azure, come la maggior parte dei browser, rispetta la data di scadenza specificata nell'intestazione Cache-Control del contenuto. È possibile specificare un valore personalizzato per l'intestazione Cache-Control dal contenitore BLOB nel portale di Azure modificando le proprietà del BLOB. La schermata seguente mostra la scadenza della cache impostata su 1 ora (3600 secondi).

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

È anche possibile usare lo script di PowerShell per impostare tutte le intestazioni Cache-Control dei BLOB. Per lo script riportato nella sezione [Automatizzare il caricamento del contenuto dall'applicazione ASP.NET all'endpoint della rete CDN](#upload), trovare il frammento di codice seguente:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

e modificarlo come illustrato di seguito:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

Può essere ancora necessario attendere la scadenza del contenuto memorizzato nella cache per 7 giorni nella rete CDN di Azure prima che il nuovo contenuto venga estratto con la nuova intestazione Cache-Control. Ciò illustra il fatto che i valori personalizzati di memorizzazione nella cache non servono se si vuole che l'aggiornamento del contenuto venga pubblicato immediatamente, come ad esempio gli aggiornamenti JavaScript o CSS. Questo problema può tuttavia essere risolto rinominando i file o usando il controllo delle versioni mediante le stringhe di query. Per altre informazioni, vedere [Rendere immediatamente disponibile il contenuto aggiornato mediante le stringhe di query](#query).

Naturalmente, devono esserci i presupporti per la memorizzazione nella cache. Si può, ad esempio, avere un contenuto che non richiede un aggiornamento frequente, come i prossimi Mondiali di calcio che possono essere aggiornati ogni 3 ore, ma che genera un traffico globale sufficiente da volerlo scaricare dal proprio server Web. Tale condizione può rappresentare la situazione ottimale in cui usare la memorizzazione nella cache della rete CDN di Azure.

<a name="query"></a>
## Rendere immediatamente disponibile il contenuto aggiornato mediante le stringhe di query ##

Nella rete CDN di Azure è possibile abilitare le stringhe di query in modo tale che il contenuto degli URL con stringhe di query specifiche venga memorizzato nella cache separatamente. È un'ottima funzionalità da usare se si vuole caricare immediatamente alcuni aggiornamenti del contenuto nei browser client anziché attendere la scadenza del contenuto della rete CDN memorizzato nella cache. Si supponga di pubblicare una pagina Web con un numero di versione nell'URL.
  
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0" rel="stylesheet"/>

Se si pubblica un aggiornamento CSS, usare un numero di versione diverso nell'URL CSS:

	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1" rel="stylesheet"/>

Per un endpoint della rete CDN con le stringhe di query abilitate, i due URL sono univoci tra loro e verrà effettuata una nuova richiesta al server Web per recuperare il nuovo file *bootstrap.css*. Tuttavia, per un endpoint della rete CDN senza le stringhe di query abilitate, i due URL sono considerati uguali e verrà semplicemente distribuito il file *bootstrap.css* memorizzato nella cache.

Lo stratagemma consiste quindi nell'aggiornare automaticamente il numero di versione. In Visual Studio, si tratta di un'operazione semplice. In un file .cshtml in cui viene usato il collegamento sopra riportato, è possibile specificare un numero di versione in base al numero di assembly.

	@{
	    var cdnVersion = System.Reflection.Assembly.GetAssembly(
	        typeof(MyMvcApp.Controllers.HomeController))
	        .GetName().Version.ToString();
	}
	
	...
	
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion" rel="stylesheet"/>

Se si modifica il numero di assembly a ogni ciclo di pubblicazione, si potrà essere sicuri anche di ottenere un numero di versione univoco ogni volta che si pubblica l'app Web, che rimarrà uguale fino al successivo ciclo di pubblicazione. In alternativa, si può impostare Visual Studio in modo che incrementi automaticamente il numero di versione dell'assembly ogni volta che l'app Web viene compilata aprendo *Properties\\AssemblyInfo.cs* nel progetto di Visual Studio e usando `*` in `AssemblyVersion`. Ad esempio:

	[assembly: AssemblyVersion("1.0.0.*")]

## Informazioni sugli script e sui CSS in bundle ##

[App Web in Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) e [Servizi cloud di Azure](/services/cloud-services/) offrono la migliore integrazione della rete CDN di Azure con [creazione di bundle e minimizzazione ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

L'integrazione di Azure App Service o Servizi cloud di Azure con la rete CDN di Azure offre i vantaggi seguenti:

- Integrazione della distribuzione del contenuto (immagini, script e fogli di stile) come parte del processo di [distribuzione continua](../web-sites-publish-source-control.md) delle app Web di Azure
- Facile aggiornamento dei pacchetti NuGet gestiti dalla rete CDN, come le versioni jQuery o Bootstrap 
- Gestione dell'applicazione Web e del contenuto gestito dalla rete CDN dalla stessa interfaccia di Visual Studio

Per le relative esercitazioni, vedere:- [Usare la rete CDN di Azure in Azure App Service](../cdn-websites-with-cdn.md) - [Integrare un servizio cloud con la rete CDN di Azure](cdn-cloud-service-with-cdn.md)

Senza l'integrazione con app Web in Azure App Service o Servizi cloud di Azure è possibile usare la rete CDN di Azure per i bundle di script, con le seguenti avvertenze:

- È necessario caricare manualmente gli script in bundle nella risorsa di archiviazione BLOB. Una soluzione a livello di programmazione viene proposta in [stackoverflow](http://stackoverflow.com/a/13736433).
- Nei file .cshtml trasformare i tag script/CSS sottoposti a rendering per usare la rete CDN di Azure. Ad esempio:

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## Ulteriori informazioni ##
- [Panoramica della Rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md)
- [Usare la rete CDN di Azure in Azure App Service](../cdn-websites-with-cdn.md)
- [Integrare un servizio cloud con la rete CDN di Azure](cdn-cloud-service-with-cdn.md)
- [Come eseguire il mapping del contenuto della rete per la distribuzione di contenuti (CDN) a un dominio personalizzato](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Uso della rete CDN per Azure](cdn-how-to-use-cdn.md)
 

<!---HONumber=September15_HO1-->