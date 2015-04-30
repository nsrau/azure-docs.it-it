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

# Configurare le stringhe di connessione di archiviazione di Azure

## Informazioni generali

Le stringhe di connessione contengono le informazioni necessarie ad accedere a livello di codice al proprio account di archiviazione in Azure. Le stringhe di connessione possono essere configurate per la connessione all'archiviazione di Azure come indicato di seguito:

- Eseguire la connessione all'emulatore di archiviazione di Azure mentre si esegue il test locale del servizio o dell'applicazione.

- Eseguire la connessione a un account di archiviazione in Azure usando gli endpoint per i servizi di archiviazione.

- Eseguire la connessione a un account di archiviazione in Azure usando endpoint espliciti per i servizi di archiviazione.

Se l'applicazione è un servizio cloud in esecuzione in Azure, in genere la stringa di connessione viene salvata nello [Schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Se l'applicazione è in esecuzione in un altro ambiente (ad esempio, sul desktop), la stringa di connessione viene generalmente salvata in un file app.config o in un altro file di configurazione. È possibile usare la classe  `CloudConfigurationManager` di Azure per accedere alla stringa di connessione in fase di esecuzione indipendentemente dall'ambiente di esecuzione.

## Creare una stringa di connessione nell'emulatore di archiviazione

L'emulatore di archiviazione è un account locale con un nome e una chiave ben noti. Poiché il nome account e la chiave sono identici per tutti gli utenti, è possibile usare un formato di stringa di collegamento per fare riferimento all'emulatore di archiviazione all'interno di una stringa di connessione. Impostare il valore della stringa di connessione su  `UseDevelopmentStorage=true`.

È inoltre possibile specificare un proxy HTTP da usare quando si esegue il test del servizio nell'emulatore di archiviazione. Ciò può essere utile per osservare le richieste HTTP e le risposte in fase di debug delle operazioni nei servizi di archiviazione. Per specificare un proxy, aggiungere l'opzione  `DevelopmentStorageProxyUri` alla stringa di connessione, e impostarne il valore sull'URI del proxy. Ad esempio, di seguito viene presentata una stringa di connessione che punta all'emulatore di archiviazione e configura un proxy HTTP:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Creare una stringa di connessione in un account di archiviazione di Azure

Per creare una stringa di connessione nell'account di archiviazione di Azure, usare il formato della stringa di connessione riportato di seguito. Indicare se si desidera eseguire la connessione all'account di archiviazione tramite HTTP o HTTPS (consigliato), sostituire  `myAccountName` con il nome dell'account di archiviazione e sostituire  `myAccountKey` con la chiave di accesso del proprio account:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ad esempio, la stringa di connessione dovrebbe somigliare all'esempio seguente:

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] L'archiviazione di Azure supporta sia HTTP che HTTPS in una stringa di connessione. Tuttavia, è consigliabile usare HTTPS.
    
## Creazione di una stringa di connessione in un endpoint di archiviazione esplicito

È possibile specificare in modo esplicito gli endpoint di servizio nella stringa di connessione se:

- È stato mappato un nome di dominio personalizzato per l'account di archiviazione con il servizio BLOB.
- Si dispone di una firma di accesso condiviso per accedere alle risorse di archiviazione in un account di archiviazione.

Per creare una stringa di connessione che specifica un endpoint BLOB esplicito, specificare l'endpoint di servizio completo per ciascun servizio, inclusa la specifica del protocollo(HTTP o HTTPS), usando il formato seguente:

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[credentials]
```

È necessario specificare almeno un endpoint di servizio, ma non occorre specificarli tutti. Se, ad esempio, si sta creando una stringa di connessione da usare con un endpoint BLOB personalizzato, la specifica degli endpoint di coda e di tabella è facoltativa. Se si sceglie di omettere gli endpoint di coda e di tabella dalla stringa di connessione, non sarà più possibile accedere ai servizi code e tabelle dalla coda mediante tale stringa di connessione.

Quando si specificano in modo esplicito gli endpoint di servizio nella stringa di connessione, sono disponibili due opzioni per specificare  `credentials` nella stringa precedente:

- È possibile specificare il nome dell'account e la chiave: `AccountName=myAccountName;AccountKey=myAccountKey` 
- È possibile specificare una firma di accesso condiviso: `SharedAccessSignature=base64Signature`

### Specifica di un endpoint BLOB con un nome di dominio personalizzato 

Se è stato registrato un nome di dominio personalizzato da usare con il servizio BLOB, è possibile decidere di configurare in modo esplicito l'endpoint BLOB nella stringa di connessione. Il valore dell'endpoint elencato nella stringa di connessione, viene usato per costruire gli URI delle richieste per il servizio BLOB, e determina la forma di qualsiasi URI restituito al codice. 

Ad esempio, una stringa di connessione in un endpoint BLOB di un dominio personalizzato è simile a:

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### Specifica di un endpoint BLOB con una firma di accesso condiviso 

È possibile creare una stringa di connessione con endpoint espliciti per accedere alle risorse di archiviazione tramite una firma di accesso condiviso. In tal caso, è possibile specificare la firma di accesso condiviso come parte della stringa di connessione, piuttosto che le credenziali di nome account e chiave. Il token della firma di accesso condiviso incapsula informazioni sulla risorsa a cui è necessario effettuare l'accesso, il periodo di tempo per il quale è disponibile e le autorizzazioni concesse. Per altre informazioni sulle firme di accesso condiviso, vedere [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

Per creare una stringa di connessione che include una firma di accesso condiviso, specificare la stringa nel seguente formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

L'endpoint può essere l'endpoint di servizio predefinito oppure un endpoint personalizzato.  `base64Signature` corrisponde alla parte di una firma di accesso condiviso relativa alla firma. La firma è un HMAC calcolato sulla base di una stringa valida da firmare e della chiave mediante l'algoritmo SHA256, e quindi codificato con la codifica Base64.

### Creazione di una stringa di connessione con un suffisso dell'endpoint

Per creare una stringa di connessione per il servizio di archiviazione in aree o istanze con suffissi dell'endpoint diversi, ad esempio per Azure China o Azure Governance, usare il seguente formato per la stringa di connessione. Indicare se si desidera eseguire la connessione all'account di archiviazione tramite HTTP o HTTPS, quindi sostituire  `myAccountName` con il nome dell'account di archiviazione,  `myAccountKey` con la chiave di accesso del proprio account e  `mySuffix` con il suffisso URI:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Ad esempio, la stringa di connessione dovrebbe avere l'aspetto della stringa di connessione di esempio seguente:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;


<!--HONumber=52-->