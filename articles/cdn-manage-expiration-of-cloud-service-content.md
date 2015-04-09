<properties 
 pageTitle="Come gestire la scadenza del contenuto del servizio cloud nella rete per la distribuzione di contenuti (rete CDN) di Azure"
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="08/01/2014" 
 ms.author="mazha"/>

#Come gestire la scadenza del contenuto del servizio cloud nella rete per la distribuzione di contenuti (rete CDN) di Azure

Gli oggetti per cui è più utile la memorizzazione nella cache della rete CDN di Azure sono quelli cui si accede di frequente per tutta la loro durata (TTL). Un oggetto resta nella cache per tutto il TTL e viene quindi aggiornato dal servizio cloud allo scadere di tale periodo. Il processo viene quindi ripetuto.  

Se non si specificano valori per la cache, il TTL per un oggetto è 7 giorni.   

Per il contenuto statico, come immagini e fogli di stile, è possibile definire la frequenza di aggiornamento aggiungendo un file web.config nella cartella CDN che include il contenuto e modificando le impostazioni **clientCache** per controllare l'intestazione Cache-Control per il contenuto. Le impostazioni di web.config influiranno su qualsiasi elemento presente nella cartella e in tutte le sottocartelle, a meno di non eseguirne l'override per un'altra sottocartella di livello ancora inferiore.  Ad esempio, è possibile impostare una durata (TTL) predefinita nella radice per memorizzare nella cache tutto il contenuto statico per tre giorni, ma impostare un valore di sei ore per una sottocartella che include contenuto più variabile.  

L'esempio di codice XML seguente mostra l'impostazione di **clientCache** in modo da specificare una durata massima di tre giorni:  

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

Specificando **UseMaxAge**, si aggiunge un'intestazione Cache-Control: max-age=<nnn> alla risposta in base al valore specificato nell'attributo **CacheControlMaxAge**. Il formato dell'intervallo di tempo per l'attributo **cacheControlMaxAge** è <giorni>.<ore>:<minuti>:<secondi>. Per altre informazioni sul nodo **clientCache** vedere la pagina relativa alla [cache client <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

Per il contenuto restituito da applicazioni come le pagine aspx, è possibile impostare a livello di codice il comportamento di memorizzazione nella cache della rete CDN impostando la proprietà **HttpResponse.Cache**. Per altre informazioni sulla proprietà **HttpResponse.Cache**, vedere [Proprietà HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se si vuole memorizzare nella cache il contenuto dell'applicazione a livello di codice, assicurarsi che tale contenuto sia contrassegnato come inseribile nella cache impostando HttpCacheability su *Public*. Assicurarsi anche che sia impostato un validator della cache. Il validator della cache può essere un timestamp dell'ultima modifica impostato chiamando SetLastModified oppure un valore etag impostato chiamando SetETag. Facoltativamente, è anche possibile specificare una scadenza della cache chiamando SetExpires oppure affidarsi all'euristica predefinita della cache descritta prima in questo documento.  

Ad esempio, per memorizzare nella cache il contenuto per un'ora, aggiungere il codice seguente:  

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

#Vedere anche

[Come gestire la scadenza del contenuto dei BLOB nella rete per la distribuzione di contenuti (rete CDN) di Azure](./cdn-manage-expiration-of-blob-content.md
)

<!--HONumber=49-->