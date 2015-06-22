<properties 
	pageTitle="Panoramica della rete CDN di Azure" 
	description="Informazioni sulla rete per la distribuzione di contenuti (rete CDN) di Azure e su come usarla per distribuire contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione nella cache di BLOB e contenuto statico." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="akucer" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="akucer"/>

#Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure

La rete per la distribuzione di contenuti (rete CDN) di Azure memorizza nella cache il contenuto statico e i BLOB di Azure usati dai servizi cloud in posizioni strategiche per offrire livelli massimi di larghezza di banda per la distribuzione del contenuto agli utenti. 

I clienti esistenti della rete CDN possono ora gestire gli endpoint della rete CDN attraverso il [portale di gestione di Microsoft Azure](https://manage.windowsazure.com). 


>[AZURE.NOTE] Alla rete CDN di Azure è associato un [piano di fatturazione](http://www.microsoft.com/windowsazure/pricing/) diverso rispetto ad Archiviazione di Azure o a Servizi cloud di Azure.
 

La rete CDN offre agli sviluppatori una soluzione globale per distribuire contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici ubicati in tutto il mondo. Per un elenco aggiornato delle località in cui si trovano i nodi della rete CDN, vedere [Località POP della rete per la distribuzione di contenuti (CDN) di Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

L'uso della rete CDN per memorizzare nella cache dati di Azure offre diversi vantaggi, inclusi i seguenti:

- Prestazioni ed esperienza utente migliori per utenti finali distanti da un'origine contenuto e che usano applicazioni in cui sono necessari molti passaggi in Internet per caricare il contenuto.
- Distribuzione su larga scala per gestire al meglio un carico elevato immediato, come all'inizio di un evento per il lancio di un prodotto. 


>[AZURE.IMPORTANT] Quando si crea o abilita un endpoint della rete CDN, la propagazione nel mondo può richiedere fino a 60 minuti.
 
Quando la richiesta di un oggetto viene prima effettuata alla rete CDN, l'oggetto viene recuperato direttamente dal servizio BLOB o dal servizio cloud. Quando viene effettuata una richiesta usando la sintassi della rete CDN, la richiesta viene reindirizzata all'endpoint della rete CDN più vicino alla località da cui è stata avviata la richiesta per fornire accesso all'oggetto. Se l'oggetto non viene trovato nell'endpoint, viene recuperato dal servizio e memorizzato nella cache nell'endpoint, in cui viene configurata un'impostazione di durata (TTL) per l'oggetto memorizzato nella cache.
 
##Memorizzazione di contenuto nella cache dal servizio di archiviazione di Azure

Una volta abilitata la rete CDN in un account di archiviazione di Azure, tutti i BLOB inclusi in contenitori pubblici e disponibili per l'accesso anonimo vengono memorizzati nella cache attraverso la rete CDN. Solo i BLOB disponibili pubblicamente possono essere memorizzati nella cache con la rete CDN di Azure. Per rendere un BLOB disponibile pubblicamente per l'accesso anonimo, è necessario specificarne il contenitore come pubblico. Una volta eseguita questa operazione, tutti i BLOB all'interno del contenitore saranno disponibili per l'accesso anonimo in lettura. È possibile scegliere di rendere pubblici anche i dati del contenitore o di limitare l'accesso solo ai BLOB al suo interno. Per informazioni sulla gestione del controllo di accesso per contenitori e BLOB, vedere [Limitare l'accesso a contenitori e BLOB](http://msdn.microsoft.com/library/azure/dd179354.aspx).

Per ottenere prestazioni ottimali, usare la memorizzazione nella cache perimetrale della rete CDN per distribuire BLOB di dimensioni minori di 10 GB.

Quando si abilita l'accesso alla rete CDN per un account di archiviazione, il portale di gestione offre un nome di dominio della rete CDN nel formato seguente: http://<identifier>.vo.msecnd.net/. È possibile usare questo nome di dominio per accedere ai BLOB in un contenitore pubblico. Ad esempio, gli utenti possono accedere ai BLOB in un contenitore pubblico denominato music di un account di archiviazione denominato myaccount mediante uno dei due URL seguenti:


- **URL del servizio BLOB di Azure**: `http://myAccount.blob.core.windows.net/music/` 
- **URL della rete CDN di Azure**: `http://<identifier>.vo.msecnd.net/music/` 

##Memorizzazione di contenuto nella cache da siti Web di Azure

È possibile abilitare la rete CDN dai siti Web per memorizzare nella cache il contenuto Web, come immagini, script e fogli di stile. Vedere [Integrare un sito Web di Azure con la rete CDN di Azure](../cdn-websites-with-cdn.md).

Quando si abilita l'accesso alla rete CDN per un sito Web, il portale di gestione fornisce un nome di dominio della rete CDN in questo formato: http://<identificatore>.vo.msecnd.net/. Questo nome di dominio può essere usato per recuperare oggetti da un sito Web. Ad esempio, supponendo un contenitore pubblico chiamato cdn e un file di immagine chiamato music.png, gli utenti possono accedere all'oggetto usando uno dei due URL seguenti:

- **URL del sito Web di Azure**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **URL della rete CDN di Azure**: `http://<identifier>.vo.msecnd.net/cdn/music.png` 
##Memorizzazione di contenuto nella cache da servizi cloud di Azure

È possibile memorizzare nella cache nella rete CDN oggetti forniti da un servizio cloud di Azure. 

La memorizzazione nella cache per i servizi cloud presenta i vincoli seguenti: 


- la rete CDN deve essere usata per memorizzare nella cache solo contenuto statico.

	>[AZURE.WARNING] La memorizzazione nella cache di contenuto estremamente volatile o dinamico può avere effetti negativi sulle prestazioni o causare problemi, con il conseguente aumento dei costi.
- Il servizio cloud deve essere distribuito in una distribuzione di produzione.
- Il servizio cloud deve fornire l'oggetto sulla porta 80 tramite HTTP.
- Il servizio cloud deve posizionare il contenuto in modo che venga memorizzato nella cache nella, o distribuito dalla, cartella /cdn del servizio cloud.

Quando si abilita l'accesso alla rete CDN per un servizio cloud, il portale di gestione fornisce un nome di dominio della rete CDN in questo formato: http://<identificatore>.vo.msecnd.net/. Questo nome di dominio può essere usato per recuperare oggetti da un servizio cloud. Ad esempio, supponendo un servizio cloud chiamato myHostedService e una pagina Web ASP.NET chiamata music.aspx che distribuisce contenuto, gli utenti possono accedere all'oggetto usando uno dei due URL seguenti:


- **URL del servizio cloud di Azure**: `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **URL della rete CDN di Azure**: `http://<identifier>.vo.msecnd.net/music.aspx` 


###Memorizzazione di contenuto specifico nella cache con stringhe di query

È possibile usare stringhe di query per differenziare gli oggetti recuperati da un servizio cloud. Ad esempio, se il servizio cloud visualizza un grafico che può variare, è possibile passare una stringa di query per recuperare il grafico specifico necessario. Ad esempio: 

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

Le stringhe di query vengono passate come valori letterali stringa. Se si dispone di un servizio che accetta due parametri, come `?area=2&item=1`, e si effettua una chiamata successiva al servizio usando `?item=1&area=2`, verranno memorizzate nella cache due copie dello stesso oggetto.
 

##Accesso al contenuto memorizzato nella cache tramite HTTPS


Azure permette di recuperare contenuto dalla rete CDN usando chiamate HTTPS. In questo modo, è possibile integrare il contenuto memorizzato nella cache nella rete CDN in pagine Web sicure senza ricevere avvisi tu tipi di contenuto con sicurezza mista.

L'accesso al contenuto della rete CDN tramite HTTPS presenta i vincoli seguenti:


- È necessario usare il certificato fornito dalla rete CDN. I certificati di terze parti non sono supportati.
- È necessario usare il dominio della rete CDN per accedere al contenuto. Il supporto HTTPS non è disponibile per i nomi di dominio personalizzati (CNAME) perché la rete CDN attualmente non supporta i certificati personalizzati.



Anche se HTTPS è abilitato, il contenuto della rete CDN può essere recuperato usando sia HTTP sia HTTPS.

Per altre informazioni sull'abilitazione di HTTPS per il contenuto della rete CDN, vedere la pagina relativa all'[abilitazione della rete per la distribuzione di contenuti (rete CDN) per Azure](http://msdn.microsoft.com/library/azure/gg680301.aspx).


##Accesso al contenuto memorizzato nella cache con domini personalizzati

È possibile eseguire il mapping dell'endpoint HTTP della rete CDN a un nome di dominio personalizzato e usare questo nome per richiedere oggetti dalla rete CDN.

Per altre informazioni sul mapping di un dominio personalizzato, vedere [Come eseguire il mapping di un dominio personalizzato all'endpoint della rete per la distribuzione di contenuti (rete CDN)](http://msdn.microsoft.com/library/azure/gg680307.aspx).


<!--HONumber=49--> 