<properties 
	pageTitle="Configurazione delle stringhe di connessione di Azure" 
	description="Informazioni su come configurare le stringhe di connessione all'account di archiviazione in Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Configurazione delle stringhe di connessione di Azure

## Panoramica
Le stringhe di connessione contengono i parametri necessari ad accedere al proprio account di archiviazione in Azure. Le stringhe di connessione possono essere configurate come indicato di seguito:

- Eseguire la connessione all'emulatore di archiviazione Azure mentre si esegue il test locale del servizio o dell'applicazione.

- Eseguire la connessione a un account di archiviazione in Azure utilizzando gli endpoint per i servizi di archiviazione.

- Eseguire la connessione a un account di archiviazione in Azure utilizzando endpoint espliciti per i servizi di archiviazione.

Se l'applicazione è un servizio cloud in esecuzione in Azure, il punto più conveniente per archiviare la stringa di connessione è lo [Schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Se l'applicazione è in esecuzione in un altro ambiente (ad esempio, sul desktop), è probabile che si desideri archiviare la stringa di connessione in un file app.config o in un altro file di configurazione. È possibile utilizzare la classe CloudConfigurationManager di Azure per accedere alla stringa di connessione in fase di runtime a prescindere dall'ambiente di esecuzione.

##Connessione all'emulatore di archiviazione

L'emulatore di archiviazione è un account locale con un nome e una chiave ben noti. Poiché il nome account e la chiave sono identici per tutti gli utenti, è possibile utilizzare un formato di stringa di collegamento per fare riferimento all'emulatore di archiviazione all'interno di una stringa di connessione. Impostare il valore della stringa di connessione su `UseDevelopmentStorage=true`.

È inoltre possibile specificare un proxy HTTP da utilizzare quando si esegue il test del servizio nell'emulatore di archiviazione. Ciò può essere utile per osservare le richieste HTTP e le risposte in fase di debug delle operazioni nei servizi di archiviazione. Per specificare un proxy, aggiungere l'opzione DevelopmentStorageProxyUri alla stringa di connessione, e impostarne il valore sull'URI del proxy. Ad esempio, di seguito viene presentata una stringa di connessione che punta all'emulatore di archiviazione e configura un proxy HTTP:

 UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

##Connessione a un account di archiviazione in Azure

È possibile definire una stringa di connessione a un account di archiviazione in Azure in uno dei seguenti modi:

- Utilizzare gli endpoint predefiniti per i servizi di archiviazione. Si tratta dell'opzione più semplice per creare una stringa di connessione. Quando si utilizza questo formato di stringa di connessione, è necessario 

- Specificare endpoint espliciti per i servizi di archiviazione. Questa opzione consente di creare una stringa di connessione più complessa. Quando si utilizza questo formato di stringa, è possibile specificare endpoint del servizio di archiviazione che includono un nome di dominio personalizzato oppure ridurre al minimo l'esposizione delle informazioni per una stringa di connessione basata sulla firma di accesso condiviso.


> [AZURE.NOTE] I servizi di archiviazione Azure supportano sia HTTP che HTTPS. Tuttavia, è consigliabile utilizzare HTTPS.

##Creazione di una stringa di connessione con endpoint predefiniti

Per creare una stringa di connessione che si basi sugli endpoint predefiniti per il servizio di archiviazione, utilizzare il seguente formato di stringa di connessione. Indicare se si desidera eseguire la connessione all'account di archiviazione tramite HTTP o HTTPS, sostituire myAccountName con il nome dell'account di archiviazione, sostituire myAccountKey con la chiave di accesso del proprio account:

 DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ad esempio, la stringa di connessione dovrebbe avere l'aspetto della stringa di connessione di esempio seguente:

 DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==

##Creazione di una stringa di connessione con endpoint espliciti

È possibile decidere di specificare in modo esplicito gli endpoint di servizio nella stringa di connessione per i seguenti motivi:

- È stato registrato un nome di dominio personalizzato per l'account di archiviazione con il servizio BLOB.

- Si dispone di una firma di accesso condiviso per accedere alle risorse di archiviazione.

###Specifica di un endpoint BLOB con un nome di dominio personalizzato 
Se è stato registrato un nome di dominio personalizzato da utilizzare con il servizio BLOB, è possibile decidere di configurare in modo esplicito l'endpoint BLOB nella stringa di connessione. Il valore dell'endpoint elencato nella stringa di connessione, viene utilizzato per costruire gli URI delle richieste per il servizio BLOB, e determina la forma di qualsiasi URI restituito al codice. 

Per creare una stringa di connessione che specifica gli endpoint espliciti, specificare l'endpoint di servizio completo per ciascun servizio, inclusa la specifica del protocollo(HTTP o HTTPS) utilizzando il formato seguente:

 BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;[credentials]

Quando si specificano in modo esplicito gli endpoint di servizio, sono disponibili due opzioni per specificare le credenziali. È possibile specificare il nome account e la chiave (`AccountName=myAccountName;AccountKey=myAccountKey`), come mostrato nella sezione precedente oppure è possibile **specificare una firma di accesso condiviso**, come mostrato nella sezione relativa alla specifica degli endpoint con una firma di accesso condiviso. Se si stanno specificando il nome account e la chiave, il formato completo della stringa è:
 BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;AccountName=myAccountName;AccountKey=myAccountKey

In una stringa di connessione è possibile specificare gli endpoint per BLOB, tabella e coda. È necessario specificare almeno un endpoint, ma non occorre specificarli tutti e tre. Se, ad esempio, si sta creando una stringa di connessione da utilizzare con un endpoint BLOB personalizzato, la specifica degli endpoint di coda e di tabella è facoltativa. Si noti che se si sceglie di omettere gli endpoint di coda e di tabella dalla stringa di connessione, non sarà più possibile accedere ai servizi code e tabelle dalla coda mediante tale stringa di connessione.

###Specifica di endpoint con una firma di accesso condiviso 
È possibile creare una stringa di connessione con endpoint espliciti per accedere alle risorse di archiviazione tramite una firma di accesso condiviso. In tal caso, è possibile specificare la firma di accesso condiviso come parte della stringa di connessione, piuttosto che le credenziali di nome account e chiave. Il token della firma di accesso condiviso incapsula informazioni sulla risorsa a cui è necessario effettuare l'accesso, il periodo di tempo per il quale è disponibile e le autorizzazioni concesse. Per ulteriori informazioni sulle firme di accesso condiviso, vedere [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

Per creare una stringa di connessione che include una firma di accesso condiviso, specificare la stringa nel seguente formato:

 BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

L'endpoint può essere l'endpoint di servizio predefinito oppure un endpoint personalizzato.  `base64Signature` corrisponde alla parte di una firma di accesso condiviso relativa alla firma. La firma è un HMAC calcolato sulla base di una stringa valida da firmare e della chiave mediante l'algoritmo SHA256, e quindi codificato con la codifica Base64.

<!--HONumber=47-->
