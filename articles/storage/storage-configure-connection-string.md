<properties 
	pageTitle="Configurare una stringa di connessione all'archiviazione di Azure | Microsoft Azure"
	description="Imparare come configurare una stringa di connessione in un account di archiviazione di Azure Una stringa di connessione include le informazioni necessarie per autenticare l'accesso a livello di codice alle risorse in un account di archiviazione. La stringa di connessione può incapsulare la chiave di accesso account per un account che si è proprietari o può includere una firma di accesso condiviso per accedere alle risorse in un account senza la chiave di accesso."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="tamram"/>

# Configurare le stringhe di connessione di archiviazione di Azure

## Panoramica

Una stringa di connessione include le informazioni necessarie ad accedere a livello di codice del proprio account di archiviazione di Azure. L'applicazione utilizza la stringa di connessione per fornire archiviazione di Azure, le informazioni necessarie per autenticare l'accesso.

Le stringhe di connessione possono essere configurate come indicato di seguito:

- Eseguire la connessione all'emulatore di archiviazione di Azure mentre si esegue il test locale del servizio o dell'applicazione.
- Eseguire la connessione a un account di archiviazione in Azure usando gli endpoint per i servizi di archiviazione per i servizi di archiviazione o esplicitare gli endpoint che si sono definiti.
- Accedere alle risorse in un account di archiviazione tramite una firma di accesso condiviso (SAS).

## Recupero della stringa di connessione

L'applicazione sarà necessario archiviare la stringa di connessione per autenticare l'accesso all'archiviazione di Azure quando è in esecuzione. Si sono disponibili diverse opzioni per l'archiviazione della stringa di connessione:

- Per un'applicazione in esecuzione sul desktop o in un dispositivo, è possibile archiviare la stringa di connessione in un file app. config o un altro file di configurazione. Se si utilizza un file app. config, aggiungere la stringa di connessione per il **AppSettings** sezione.
- Se l'applicazione è un servizio cloud in esecuzione in Azure, in genere la stringa di connessione viene salvata nello [Schema di configurazione dei servizi di Azure (file .cscfg).](https://msdn.microsoft.com/library/ee758710.aspx) Aggiungere la stringa di connessione per il **ConfigurationSettings** sezione del file di configurazione del servizio.

Archiviare la stringa di connessione all'interno di un file di configurazione consente di aggiornare la stringa di connessione per alternare l'emulatore di archiviazione e un account di archiviazione di Azure nel cloud. È sufficiente modificare la stringa di connessione in modo che punti all'account di archiviazione.

È possibile utilizzare la classe [CloudConfigurationManager](https://msdn.microsoft.com/library/microsoft.windowsazure.cloudconfigurationmanager.aspx) di Azure per accedere alla stringa di connessione in fase di runtime a prescindere dall'ambiente di esecuzione.

## Creare una stringa di connessione nell'emulatore di archiviazione

[AZURE.INCLUDE [risorsa di archiviazione-emulatore-connessione-stringa-includere](../../includes/storage-emulator-connection-string-include.md)]

Vedere [utilizzare l'emulatore di archiviazione di Azure per lo sviluppo e Testing](storage-use-emulator.md) per ulteriori informazioni sull'emulatore di archiviazione.

## Creare una stringa di connessione in un account di archiviazione di Azure

Per creare una stringa di connessione nell'account di archiviazione di Azure, usare il formato della stringa di connessione riportato di seguito. Indicare se si desidera eseguire la connessione all'account di archiviazione tramite HTTP o HTTPS (consigliato), sostituire `myAccountName`con il nome dell'account di archiviazione e sostituire`myAccountKey` con la chiave di accesso del proprio account:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ad esempio, la stringa di connessione dovrebbe somigliare all'esempio seguente:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>

> [AZURE.NOTE]L'archiviazione di Azure supporta sia HTTP che HTTPS in una stringa di connessione. Tuttavia, è consigliabile usare HTTPS.

## Creazione di una stringa di connessione in un endpoint di archiviazione esplicito

È possibile specificare in modo esplicito gli endpoint di servizio nella stringa di connessione se:

- È stato mappato un nome di dominio personalizzato per l'account di archiviazione con il servizio BLOB.
- Si dispone di una firma di accesso condiviso per accedere alle risorse di archiviazione in un account di archiviazione.

Per creare una stringa di connessione che specifica un endpoint BLOB esplicito, specificare l'endpoint di servizio completo per ciascun servizio, inclusa la specifica del protocollo(HTTP o HTTPS), usando il formato seguente:

	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	[credentials]


È necessario specificare almeno un endpoint di servizio, ma non occorre specificarli tutti. Se, ad esempio, si sta creando una stringa di connessione da usare con un endpoint BLOB personalizzato, la specifica degli endpoint di coda e di tabella è facoltativa. Se si sceglie di omettere gli endpoint di coda e di tabella dalla stringa di connessione, non sarà più possibile accedere ai servizi code e tabelle dalla coda mediante tale stringa di connessione.

Quando si specificano in modo esplicito gli endpoint di servizio nella stringa di connessione, sono disponibili due opzioni per specificare `credentials`nella stringa precedente:

- È possibile specificare il nome dell'account e la chiave:`AccountName=myAccountName;AccountKey=myAccountKey`
- È possibile specificare una firma di accesso condiviso:`SharedAccessSignature=base64Signature`

### Specifica di un endpoint BLOB con un nome di dominio personalizzato

Se è stato registrato un nome di dominio personalizzato da usare con il servizio BLOB, è possibile decidere di configurare in modo esplicito l'endpoint BLOB nella stringa di connessione. Il valore dell'endpoint elencato nella stringa di connessione, viene usato per costruire gli URI delle richieste per il servizio BLOB, e determina la forma di qualsiasi URI restituito al codice.

Ad esempio, una stringa di connessione in un endpoint BLOB di un dominio personalizzato è simile a:

	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=<account-key>


### Specifica di un endpoint BLOB con una firma di accesso condiviso

È possibile creare una stringa di connessione con endpoint espliciti per accedere alle risorse di archiviazione tramite una firma di accesso condiviso. In tal caso, è possibile specificare la firma di accesso condiviso come parte della stringa di connessione, piuttosto che le credenziali di nome account e chiave. Il token della firma di accesso condiviso incapsula informazioni sulla risorsa a cui è necessario effettuare l'accesso, il periodo di tempo per il quale è disponibile e le autorizzazioni concesse. Per altre informazioni sulle firme di accesso condiviso, vedere [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

Per creare una stringa di connessione che include una firma di accesso condiviso, specificare la stringa nel seguente formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

L'endpoint può essere l'endpoint di servizio predefinito oppure un endpoint personalizzato. `base64Signature`corrisponde alla parte di una firma di accesso condiviso relativa alla firma. La firma è un HMAC calcolato sulla base di una stringa valida da firmare e della chiave mediante l'algoritmo SHA256, e quindi codificato con la codifica Base64.

### Creazione di una stringa di connessione con un suffisso dell'endpoint

Per creare una stringa di connessione per il servizio di archiviazione in aree o istanze con suffissi dell'endpoint diversi, ad esempio per Azure China o Azure Governance, usare il seguente formato per la stringa di connessione. Indicare se si desidera eseguire la connessione all'account di archiviazione tramite HTTP o HTTPS, quindi sostituire `myAccountName`con il nome dell'account di archiviazione, `myAccountKey`con la chiave di accesso del proprio account e `mySuffix` con il suffisso URI:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Ad esempio, la stringa di connessione dovrebbe avere l'aspetto della stringa di connessione di esempio seguente:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

<!---HONumber=AcomDC_0114_2016-->