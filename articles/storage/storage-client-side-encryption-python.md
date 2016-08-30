<properties
	pageTitle="Crittografia lato client con Python per Archiviazione di Microsoft Azure | Microsoft Azure"
	description="La libreria client di archiviazione di Azure per Python offre supporto per la crittografia lato client per la massima sicurezza delle applicazioni di archiviazioni Azure."
	services="storage"
	documentationCenter="python"
	authors="rickle-msft"
	manager="carmonm"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="t-rickle"/>


# Crittografia lato client con Python per Archiviazione di Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## Overview

La [libreria client di archiviazione di Azure per Python](https://pypi.python.org/pypi/azure-storage) supporta la crittografia dei dati all'interno delle applicazioni client prima del caricamento nell'Archiviazione di Azure, nonché la decrittografia dei dati durante il download al client.

>[AZURE.NOTE] La libreria Python di archiviazione di Azure è disponibile in anteprima.

## Crittografia e decrittografia tramite la tecnica basata su envelope
Le procedure di crittografia e decrittografia seguono la tecnica basata su envelope.

### Crittografia tramite la tecnica basata su envelope
La crittografia tramite la tecnica basata su envelope funziona nel modo seguente:

1.	La libreria di Azure Storage Client genera una chiave di crittografia del contenuto (CEK) che funziona come chiave simmetrica monouso.

2.	I dati utente vengono crittografati con questa chiave CEK.

3.	Viene quindi eseguito il wrapping della chiave CEK mediante la chiave di crittografia della chiave (KEK). La chiave KEK è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica gestita localmente. La libreria del client Archiviazione non ha mai accesso alla chiave KEK. Richiama solo l'algoritmo di wrapping della chiave fornito dalla chiave KEK. Gli utenti possono scegliere di usare provider personalizzati per il wrapping o la rimozione del wrapping delle chiavi, se lo desiderano.

4.	I dati crittografati vengono quindi caricati nel servizio Archiviazione di Azure. La chiave con wrapping assieme ad alcuni metadati di crittografia aggiuntivi viene archiviata come metadati (su un BLOB) o interpolata con i dati crittografati (entità della tabella e messaggi in coda).

### Decrittografia tramite la tecnica basata su envelope
La decrittografia tramite la tecnica basata su envelope funziona nel modo seguente:

1.	La libreria client presuppone che l'utente gestisce la chiave di crittografia della chiave (KEK) in locale. L'utente non deve necessariamente conoscere la chiave specifica utilizzata per la crittografia. Al contrario, è possibile impostare e utilizzare un resolver di chiavi che risolve diversi identificatori di chiave per le chiavi.

2.	La libreria client scarica i dati crittografati insieme al materiale di crittografia archiviato nel servizio.

3.	La chiave di crittografia dei contenuti (CEK) con wrapping viene quindi sottoposta a rimozione del wrapping (decrittografata) utilizzando la chiave di crittografia della chiave (KEK). Anche in questo caso, la libreria client non ha accesso alla KEK. Richiama semplicemente l'algoritmo di rimozione del wrapping personalizzato del provider.

4.	La chiave di crittografia del contenuto (CEK) viene quindi utilizzata per decrittografare i dati utente crittografati.

## Meccanismo di crittografia  
La libreria client di archiviazione usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) per la crittografia dei dati utente. In particolare, si avvale della modalità [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) con AES. Ogni servizio funziona in modo diverso, pertanto qui verrà illustrato ciascuno di essi.

### Blobs
La libreria client attualmente supporta la crittografia solo di interi BLOB. In particolare, la crittografia è supportata quando gli utenti utilizzano i metodi **create***. Per i download, sono supportati sia i download completi che quelli di intervallo ed è disponibile il calcolo parallelo dei download e degli upload.

Durante la crittografia, la libreria client genererà un vettore di inizializzazione (IV) casuale di 16 byte con una chiave di crittografia del contenuto (CEK) casuale di 32 byte ed eseguirà la crittografia envelope dei dati BLOB utilizzando queste informazioni. La CEK con wrapping e alcuni metadati di crittografia aggiuntivi vengono quindi archiviati come metadati BLOB insieme al BLOB crittografato nel servizio.

>[AZURE.WARNING] Se si modificano o si caricano i propri metadati per il BLOB, è necessario assicurarsi che tali metadati siano conservati. Se si caricano nuovi metadati senza questi metadati, la CEK con wrapping, il vettore di inizializzazione e altri metadati andranno persi e il contenuto del BLOB non potrà mai più essere recuperato.

Il download di un BLOB crittografato comporta il recupero del contenuto dell'intero BLOB usando i metodi di servizio **get***. La CEK con wrapping viene sottoposta a rimozione del wrapping e utilizzata con il vettore di inizializzazione (archiviato come metadati BLOB in questo caso) per restituire i dati decrittografati agli utenti.

Il download di un intervallo arbitrario (metodi **get*** con parametri di intervallo passati) nel BLOB crittografato implica la regolazione dell'intervallo fornito dagli utenti per ottenere una piccola quantità di dati aggiuntivi che possono essere utilizzati per decrittografare correttamente l'intervallo richiesto.

I BLOB in blocchi e BLOB di pagine possono essere crittografati/decrittografati solamente con questo schema. Attualmente non è disponibile nessun supporto per la crittografia dei blob di accodamento.

### Queues
Poiché i messaggi in coda possono essere in qualsiasi formato, la libreria client definisce un formato personalizzato che include il vettore di inizializzazione (IV) e la chiave di crittografia del contenuto crittografato (CEK) nel testo del messaggio.

Durante la crittografia, la libreria client genera un vettore di inizializzazione casuale di 16 byte con una CEK casuale di 32 byte ed esegue la crittografia envelope del testo del messaggio in coda utilizzando queste informazioni. Al messaggio in coda crittografato vengono quindi aggiunti la CEK con wrapping e alcuni metadati di crittografia aggiuntivi. Questo messaggio modificato (illustrato di seguito) viene archiviato nel servizio.

	<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante la decrittografia, la chiave con wrapping viene estratta dal messaggio in coda e sottoposta a rimozione del wrapping. Anche il vettore di inizializzazione viene estratto dal messaggio in coda e utilizzato con la chiave senza wrapping per decrittografare i dati del messaggio in coda. Si noti che i metadati di crittografia sono di piccole dimensioni (inferiori a 500 byte), quindi mentre vengono tenuti in considerazione per il limite di 64 KB di un messaggio in coda, l'impatto dovrebbe essere gestibile.

### Tabelle
La libreria client supporta la crittografia di proprietà di entità per le operazioni di inserimento e sostituzione.

>[AZURE.NOTE] L’unione non è attualmente supportata. Poiché un subset di proprietà potrebbe essere stato crittografato in precedenza utilizzando una chiave diversa, la semplice unione delle nuove proprietà e l’aggiornamento dei metadati comportano la perdita di dati. L'unione richiede chiamate a servizi aggiuntivi per la lettura dell’entità preesistente dal servizio o l’utilizzo di una nuova chiave per ogni proprietà, entrambe operazioni non idonee per motivi di prestazioni.

La crittografia dei dati della tabella funziona nel modo seguente:

1.	Gli utenti specificano le proprietà che devono essere crittografate.

2.	La libreria client genera un vettore di inizializzazione (IV) casuale di 16 byte con una chiave di crittografia del contenuto (CEK) casuale di 32 byte per ogni entità ed esegue la crittografia envelope sulle singole proprietà che devono essere crittografate mediante la derivazione di un nuovo vettore di inizializzazione per ciascuna proprietà. La proprietà di crittografia viene memorizzata come dati binari.

3.	La CEK con wrapping e alcuni metadati di crittografia aggiuntivi vengono quindi archiviati come due proprietà riservate aggiuntive. La prima proprietà riservata (\_ClientEncryptionMetadata1) è una proprietà stringa che contiene le informazioni su vettore di inizializzazione, versione e chiave con wrapping. La seconda proprietà riservata (\_ClientEncryptionMetadata2) è una proprietà binaria che contiene le informazioni sulle proprietà che vengono crittografate. Le informazioni contenute in questa seconda proprietà (\_ClientEncryptionMetadata2) sono crittografate.

4.	A causa di queste proprietà riservate aggiuntive richieste per la crittografia, gli utenti ora possono avere solo 250 proprietà personalizzate anziché 252. Le dimensioni totali dell'entità devono essere minori di 1 MB.

	Si noti che solo le proprietà di stringa possono essere crittografate. Se devono essere crittografati altri tipi di proprietà, essi devono essere convertiti in stringhe. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono convertite nuovamente in stringhe (stringhe non elaborate, non proprietà dell'entità con tipo EdmType.STRING) dopo la decrittografia.

	Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Tale operazione può essere effettuata archiviando le proprietà in oggetti TableEntity con tipo impostato su EdmType.STRING e la crittografia impostata su true oppure impostando il parametro encryption\_resolver\_function sull'oggetto tableservice. Un resolver di crittografia è una funzione che accetta una chiave di partizione, una chiave di riga e un nome di proprietà e restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client utilizzerà queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura in rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografa la proprietà A; in caso contrario crittografa le proprietà A e B). Si noti che non è necessario fornire queste informazioni durante la lettura o la query su entità.

### Operazioni batch
Un criterio di crittografia si applica a tutte le righe nel batch. La libreria client genera internamente un nuovo vettore di inizializzazione casuale e una CEK casuale per ogni riga nel batch. Gli utenti possono scegliere anche di crittografare proprietà diverse per ogni operazione nel batch mediante la definizione di questo comportamento nel resolver di crittografia. Se un batch viene creato come un gestore di contesto tramite il metodo tableservice batch(), il criterio di crittografia del tableservice verrà applicato automaticamente al batch. Se viene creato un batch in modo esplicito richiamando il costruttore, il criterio di crittografia deve essere approvato come parametro e lasciato invariato per tutto il ciclo di vita del batch. Si noti che le entità vengono crittografate quando vengono inserite nel batch mediante il criterio di crittografia del batch (le entità NON vengono crittografate al momento di fase di confermare il batch tramite il criterio di crittografia del tableservice).

### Query
Per eseguire operazioni di query, è necessario specificare un resolver di chiave in grado di risolvere tutte le chiavi nel set di risultati. Se un'entità inclusa nel risultato della query non può essere risolta in un provider, la libreria client genererà un errore. Per ogni query che esegue le proiezioni del lato server, la libreria client aggiungerà le proprietà dei metadati di crittografia speciali (\_ClientEncryptionMetadata1 e ClientEncryptionMetadata2) per impostazione predefinita alle colonne selezionate.

>[AZURE.IMPORTANT] Quando si utilizza la crittografia lato client, tenere presente i seguenti aspetti importanti:
>
>- Durante la lettura o la scrittura di un BLOB crittografato, utilizzare i comandi di caricamento completo dei BLOB e i comandi di download completo o basato sull'intervallo dei BLOB. Evitare di scrivere in un BLOB crittografato usando le operazioni di protocollo, ad esempio Put Block, Put Block List, Write Pages o Clear Pages. In caso contrario, si potrebbe danneggiare il BLOB crittografato e renderlo illeggibile.
>
>- Per le tabelle esiste un vincolo simile. Prestare attenzione a non aggiornare le proprietà crittografate senza aggiornare i metadati di crittografia.
>
>- Se si impostano i metadati nel BLOB crittografato, si potrebbero sovrascrivere i metadati correlati alla crittografia richiesti per la decrittografia, poiché i metadati di impostazione non sono additivi. Ciò vale anche per gli snapshot; evitare di specificare i metadati durante la creazione di uno snapshot di un BLOB crittografato. Per impostare i metadati, assicurarsi prima di richiamare il metodo **get\_blob\_metadata** per ottenere i metadati di crittografia correnti ed evitare le scritture simultanee durante l'impostazione dei metadati.
>
>- Abilitare il flag **require\_encryption** nell'oggetto del servizio per gli utenti che dovrebbero lavorare solo con i dati crittografati. Per ulteriori informazioni, vedere di seguito.

La libreria client di archiviazione prevede la chiave KEK fornita e un resolver di chiavi per implementare l'interfaccia seguente. Il supporto dell'[insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi KEK per Python è in sospeso e verrà integrato in questa libreria al termine del completamento.


## API client/interfaccia
Dopo la creazione di un oggetto di servizio di archiviazione (ad esempio blockblobservice), l'utente può assegnare i valori ai campi che costituiscono un criterio di crittografia: key\_encryption\_key, key\_resolver\_function e require\_encryption. Gli utenti possono fornire solo una chiave KEK, solo un resolver o entrambi. key\_encryption\_key è il tipo di chiave di base che viene identificato utilizzando un identificatore di chiave e che fornisce la logica per eseguire il wrapping o la rimozione del wrapping. key\_resolver\_function viene utilizzato per risolvere una chiave durante il processo di decrittografia. Restituisce una chiave KEK valida in base a un identificatore di chiave. Ciò fornisce agli utenti la possibilità di scegliere tra più chiavi che vengono gestite in più posizioni.

La chiave KEK deve implementare i metodi seguenti per consentire una corretta crittografia dei dati:
- wrap\_key(cek): esegue il wrapping della chiave CEK (byte) specificata utilizzando un algoritmo scelto dall'utente. Restituisce la chiave su cui è stato eseguito il wrapping.
- get\_key\_wrap\_algorithm(): restituisce l'algoritmo utilizzato per eseguire il wrapping delle chiavi.
- get\_kid(): restituisce l'ID chiave stringa per questa chiave KEK. La chiave KEK deve implementare i metodi seguenti per consentire una corretta decrittografia dei dati:
- unwrap\_key (cek, algoritmo): restituisce il formato privo di wrapping della chiave CEK specificata utilizzando l'algoritmo specificato per la stringa.
- get\_kid(): restituisce un ID chiave stringa per questa chiave KEK.

Il resolver di chiavi deve implementare almeno un metodo che, dato un ID della chiave, restituisce la chiave KEK corrispondente che implementa l'interfaccia sopra. Solo questo metodo deve essere assegnato alla proprietà key\_resolver\_function sull'oggetto di servizio.

- Per la crittografia, la chiave viene sempre utilizzata e l'assenza di una chiave genera un errore.
- Per la decrittografia:
	- Se specificato per ottenere la chiave, viene richiamato il resolver di chiave. Se il resolver è specificato, ma non dispone di un mapping per l'identificatore di chiave, viene generato un errore.
	- Se il resolver non è specificato, ma viene specificata una chiave, la chiave viene utilizzata se l’identificatore corrisponde all’identificatore della chiave richiesta. Se l'identificatore non corrisponde, viene generato un errore.

	  Gli esempi di crittografia in azure.storage.samples <fix URL> rappresentano uno scenario end-to-end più dettagliato per BLOB, code e tabelle. Esempi di implementazione del resolver di chiavi e della chiave KEK sono forniti nei file di esempio come KeyWrapper e KeyResolver.

### Modalità RequireEncryption
Gli utenti possono facoltativamente abilitare una modalità operativa quando tutte le operazioni di caricamento e download devono essere crittografate. In questa modalità, i tentativi di caricare dati senza un criterio di crittografia o di scaricare dati non crittografati nel servizio avranno esito negativo nel client. Il flag **require\_encryption** sull'oggetto di servizio controlla questo comportamento.

### Crittografia del servizio BLOB
Impostare i campi del criterio di crittografia per l'oggetto blockblobservice. Tutto il resto verrà gestito dalla libreria client internamente.

	# Create the KEK used for encryption.
	# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
	kek = KeyWrapper('local:key1') # Key identifier

	# Create the key resolver used for decryption.
	# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
	key_resolver = KeyResolver()
	key_resolver.put_key(kek)

	# Set the KEK and key resolver on the service object.
	my_block_blob_service.key_encryption_key = kek
	my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

	# Upload the encrypted contents to the blob.
	my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

	# Download and decrypt the encrypted contents from the blob.
	blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### Crittografia del servizio di accodamento
Impostare i campi del criterio di crittografia per l'oggetto queueservice. Tutto il resto verrà gestito dalla libreria client internamente.

	# Create the KEK used for encryption.
	# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
	kek = KeyWrapper('local:key1') # Key identifier

	# Create the key resolver used for decryption.
	# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
	key_resolver = KeyResolver()
	key_resolver.put_key(kek)

	# Set the KEK and key resolver on the service object.
	my_queue_service.key_encryption_key = kek
	my_queue_service.key_resolver_funcion = key_resolver.resolve_key

	# Add message
	my_queue_service.put_message(queue_name, content)

	# Retrieve message
	retrieved_message_list = my_queue_service.get_messages(queue_name)

### Crittografia del servizio tabelle
Oltre a creare un criterio di crittografia e a impostarlo nelle opzioni di richiesta, è necessario specificare **encryption\_resolver\_function** in **tableservice** oppure impostare l'attributo di crittografica nell'oggetto EntityProperty.

### Utilizzo del resolver

	# Create the KEK used for encryption.
	# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
	kek = KeyWrapper('local:key1') # Key identifier

	# Create the key resolver used for decryption.
	# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
	key_resolver = KeyResolver()
	key_resolver.put_key(kek)

	# Define the encryption resolver_function.
	def my_encryption_resolver(pk, rk, property_name):
			if property_name == 'foo':
					return True
			return False

	# Set the KEK and key resolver on the service object.
	my_table_service.key_encryption_key = kek
	my_table_service.key_resolver_funcion = key_resolver.resolve_key
	my_table_service.encryption_resolver_function = my_encryption_resolver

	# Insert Entity
	my_table_service.insert_entity(table_name, entity)

	# Retrieve Entity
	# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
	my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### Utilizzo di attributi
Come indicato sopra, una proprietà può essere contrassegnata per la crittografia archiviandola in un oggetto EntityProperty e impostando il campo di crittografia.

	encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## Crittografia e prestazioni
Si noti che la crittografia dei dati di archiviazione restituisce un overhead delle prestazioni aggiuntivo. La chiave simmetrica e il vettore devono essere generati, il contenuto stesso deve essere crittografato e metadati aggiuntivi devono essere formattati e caricati. Questo overhead varia a seconda della quantità di dati da crittografare. È consigliabile che i clienti testano sempre le proprie applicazioni per le prestazioni durante lo sviluppo.

## Passaggi successivi

- Scaricare la [libreria client di archiviazione di Azure per il pacchetto Java PyPi](https://pypi.python.org/pypi/azure-storage)
- Scaricare il [codice sorgente della libreria di archiviazione di Azure per Python da GitHub](https://github.com/Azure/azure-storage-python)

<!---HONumber=AcomDC_0817_2016-->