<properties 
	pageTitle="Come utilizzare la rete CDN | Microsoft Azure" 
	description="Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure per distribuire contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Uso della rete CDN per Azure

La rete per la distribuzione di contenuti (rete CDN) di Azure è il blocco predefinito fondamentale per la scalabilità di qualsiasi applicazione HTTP in Azure. Offre ai clienti di Azure una soluzione globale memorizzando nella cache e distribuendo contenuti vicino agli utenti finali. Invece di raggiungere ogni volta l'origine, quindi, le richieste utente vengono indirizzate in modo intelligente al POP perimetrale della rete CDN con le prestazioni migliori. Ciò migliora in modo significativo le prestazioni e l'esperienza utente. Per un elenco aggiornato delle località in cui si trovano i nodi della rete CDN, vedere [Località POP della rete per la distribuzione di contenuti (CDN) di Azure](cdn-pop-locations.md).

L'uso della rete CDN per la memorizzazione dei dati Azure nella cache offre molti vantaggi, inclusi i seguenti:

- Migliore esperienza utente e migliori prestazioni per gli utenti finali, che sono lontani dall'origine del contenuto e che usano applicazioni in cui sono necessarie numerose richieste HTTP per caricare contenuto.
- Elevata scalabilità distribuita, per una gestione migliore dei carichi elevati istantanei, ad esempio nelle fasi iniziali di un evento quale il lancio di un prodotto.
- Mediante la distribuzione delle richieste utente e la gestione dei contenuti da POP perimetrali globali, sarà possibile inviare meno traffico all'origine.

>[AZURE.TIP]La rete CDN di Azure può distribuire contenuto da un'ampia gamma di origini. Le origini integrate all'interno di Azure includono il servizio app, i Servizi Cloud, l'archiviazione BLOB e i Servizi multimediali. È inoltre possibile definire un'origine personalizzata usando qualsiasi indirizzo Web accessibile pubblicamente.

##Come abilitare la rete CDN

1. Creare un profilo della rete CDN con endpoint che puntano all'origine

	Un profilo della rete CDN è una raccolta di endpoint della rete CDN. Ogni profilo contiene uno o più endpoint della rete CDN. Dopo aver creato un profilo della rete CDN, è possibile creare un nuovo endpoint della rete CDN usando l'origine selezionata.
	
	>[AZURE.NOTE]Ogni sottoscrizione di Azure è limitata a quattro profili della rete CDN. Ogni profilo della rete CDN è limitato a quattro endpoint della rete CDN.
	>
	> I prezzi della rete CDN vengono applicati a livello di profilo della rete CDN. Se si desidera usare una combinazione di funzionalità della rete CDN Standard e Premium, è necessario disporre di più profili della rete CDN.
	
	Per un'esercitazione dettagliata sulla creazione di profili ed endpoint della rete CDN, vedere [Come abilitare la rete per la distribuzione di contenuti (CDN) per Azure](cdn-create-new-endpoint.md).
	
2. Impostare la configurazione della rete CDN

	È possibile abilitare alcune funzionalità per l'endpoint della rete CDN, ad esempio [i criteri di memorizzazione della cache](cdn-caching-policy.md), [la memorizzazione nella cache delle stringhe di query](cdn-query-string.md), [il motore regole](cdn-rules-engine.md) e così via. Per informazioni dettagliate, vedere il menu **Gestisci** a sinistra.

3. Accedere al contenuto della rete CDN

	Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. L'indirizzo per un oggetto BLOB memorizzato nella cache, ad esempio, sarà simile al seguente: `http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Memorizzazione di contenuto nella cache dal servizio di archiviazione di Azure

Una volta abilitata la rete CDN in un account di archiviazione di Azure, tutti i BLOB inclusi in contenitori pubblici e disponibili per l'accesso anonimo vengono memorizzati nella cache attraverso la rete CDN. Solo i BLOB disponibili pubblicamente possono essere memorizzati nella cache con la rete CDN di Azure. Per rendere un BLOB disponibile pubblicamente per l'accesso anonimo, è necessario specificarne il contenitore come pubblico. Una volta eseguita questa operazione, tutti i BLOB all'interno del contenitore saranno disponibili per l'accesso anonimo in lettura. È possibile scegliere di rendere pubblici anche i dati del contenitore o di limitare l'accesso solo ai BLOB al suo interno. Per informazioni sulla gestione del controllo di accesso per contenitori e BLOB, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../storage/storage-manage-access-to-resources.md).

Per ottenere prestazioni ottimali, usare la memorizzazione nella cache perimetrale della rete CDN per distribuire BLOB di dimensioni minori di 10 GB.

Quando si abilita l'accesso alla rete CDN per un account di archiviazione, il portale di gestione offre un nome di dominio della rete CDN nel formato seguente: `http://<identifier>.azureedge.net/`. Questo nome di dominio può essere usato per accedere ai BLOB in un contenitore pubblico. Ad esempio, dato un contenitore pubblico denominato music in un account di archiviazione denominato myaccount, gli utenti possono accedere ai BLOB in tale contenitore usando uno dei due URL seguenti:

- **URL del servizio BLOB di Azure**: `http://myAccount.blob.core.windows.net/music/` 
- **URL della rete CDN di Azure**: `http://<identifier>.azureedge.net/music/` 

## Memorizzazione di contenuto nella cache da siti Web di Azure

È possibile abilitare la rete CDN dai siti Web per memorizzare nella cache il contenuto Web, come immagini, script e fogli di stile. Vedere [Integrare un sito Web di Azure con la rete CDN di Azure](../app-service-web/cdn-websites-with-cdn.md).

Quando si abilita l'accesso alla rete CDN per un sito Web, il portale di gestione fornisce un nome di dominio della rete CDN nel formato seguente: `http://<identifier>.azureedge.net/`. Questo nome di dominio può essere usato per recuperare oggetti da un sito Web. Ad esempio, supponendo un contenitore pubblico chiamato cdn e un file di immagine chiamato music.png, gli utenti possono accedere all'oggetto usando uno dei due URL seguenti:

- **URL del sito Web di Azure**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **URL della rete CDN di Azure**: `http://<identifier>.azureedge.net/cdn/music.png`
 
## Memorizzazione di contenuto nella cache da servizi cloud di Azure

È possibile memorizzare nella cache nella rete CDN oggetti forniti da un servizio cloud di Azure.

La memorizzazione nella cache per i servizi cloud presenta i vincoli seguenti:


- la rete CDN deve essere usata per memorizzare nella cache solo contenuto statico.

	>[AZURE.WARNING]La memorizzazione nella cache di contenuto estremamente volatile o dinamico può avere effetti negativi sulle prestazioni o causare problemi, con il conseguente aumento dei costi.
- Il servizio cloud deve essere distribuito in una distribuzione di produzione.
- Il servizio cloud deve fornire l'oggetto sulla porta 80 tramite HTTP.
- Il servizio cloud deve posizionare il contenuto in modo che venga memorizzato nella cache nella, o distribuito dalla, cartella /cdn del servizio cloud.

Quando si abilita l'accesso alla rete CDN per un servizio cloud, il portale di gestione fornisce un nome di dominio della rete CDN nel formato seguente: `http://<identifier>.azureedge.net/`. Questo nome di dominio può essere usato per recuperare oggetti da un servizio cloud. Ad esempio, supponendo un servizio cloud chiamato myHostedService e una pagina Web ASP.NET chiamata music.aspx che distribuisce contenuto, gli utenti possono accedere all'oggetto usando uno dei due URL seguenti:


- **URL del servizio cloud di Azure**: `http://myHostedService.cloudapp.net/music.aspx` 
- **URL della rete CDN di Azure**: `http://<identifier>.azureedge.net/music.aspx` 

## Memorizzazione nella cache di contenuti da origini personalizzate

È possibile memorizzare nella cache nella rete CDN oggetti forniti da qualsiasi applicazione Web accessibile pubblicamente.

La memorizzazione nella cache per le origini personalizzate presenta i vincoli seguenti:

- la rete CDN deve essere usata per memorizzare nella cache solo contenuto statico.

	>[AZURE.WARNING]La memorizzazione nella cache di contenuto estremamente volatile o dinamico può avere effetti negativi sulle prestazioni o causare problemi, con il conseguente aumento dei costi.
- Il contenuto nell'origine personalizzata deve essere ospitato in un server con un indirizzo IP pubblico. I nodi perimetrali della rete CDN non sono in grado di recuperare le risorse dai server Intranet in presenza di un firewall.

Quando si abilita l'accesso alla rete CDN per un'origine personalizzata, il portale di Azure fornisce un nome di dominio della rete CDN nel formato seguente: `http://<identifier>.azureedge.net/`. Questo nome di dominio può essere usato per recuperare oggetti dall'origine personalizzata. Ad esempio, supponendo un sito che si trova all'indirizzo www.contoso.com e una pagina Web ASP.NET chiamata music.aspx che distribuisce contenuto, gli utenti possono accedere all'oggetto usando uno dei due URL seguenti:


- **URL dell'origine personalizzata**: `http://www.contoso.com/music.aspx` 
- **URL della rete CDN di Azure**: `http://<identifier>.azureedge.net/music.aspx` 

## Memorizzazione di contenuto specifico nella cache con stringhe di query

È possibile usare stringhe di query per differenziare gli oggetti recuperati da un'origine. Ad esempio, se l'origine visualizza un grafico che può variare, è possibile passare una stringa di query per recuperare il grafico specifico necessario. Ad esempio:

`http://<identifier>.azureedge.net/chart.aspx?item=1`

Le stringhe di query vengono passate come valori letterali stringa. Se si dispone di un servizio che accetta due parametri, come `?area=2&item=1`, e si effettuano chiamate successive all'origine usando `?item=1&area=2`, verranno memorizzate nella cache due copie dello stesso oggetto.

Per altre informazioni sulla memorizzazione nella cache di stringhe di query, vedere l'articolo relativo al [controllo del comportamento di memorizzazione nella cache delle richieste relative alla rete CDN con stringhe di query](cdn-query-string.md).

## Accesso al contenuto memorizzato nella cache tramite HTTPS

Azure permette di recuperare contenuto dalla rete CDN usando chiamate HTTPS. In questo modo, è possibile integrare il contenuto memorizzato nella cache nella rete CDN in pagine Web sicure senza ricevere avvisi tu tipi di contenuto con sicurezza mista.

L'accesso al contenuto della rete CDN tramite HTTPS presenta i vincoli seguenti:


- È necessario usare il certificato fornito dalla rete CDN. I certificati di terze parti non sono supportati.
- È necessario usare il dominio della rete CDN per accedere al contenuto. Il supporto HTTPS non è disponibile per i nomi di dominio personalizzati (CNAME) perché la rete CDN attualmente non supporta i certificati personalizzati.

Per altre informazioni sull'abilitazione di HTTPS per il contenuto della rete CDN, vedere [Come abilitare la rete per la distribuzione di contenuti (rete CDN) per Azure](cdn-create-new-endpoint.md).


## Accesso al contenuto memorizzato nella cache con domini personalizzati

È possibile eseguire il mapping dell'endpoint HTTP della rete CDN a un nome di dominio personalizzato e usare questo nome per richiedere oggetti dalla rete CDN.

Per altre informazioni sul mapping di un dominio personalizzato, vedere [Come eseguire il mapping del contenuto della rete per la distribuzione di contenuti (CDN) a un dominio personalizzato](cdn-map-content-to-custom-domain.md).

## Vedere anche

- [Come abilitare la rete per la distribuzione di contenuti (CDN) per Azure](cdn-create-new-endpoint.md)
- [Panoramica della Rete di distribuzione di contenuti (CDN) di Azure](cdn-overview.md)
 

<!---HONumber=AcomDC_1203_2015-->