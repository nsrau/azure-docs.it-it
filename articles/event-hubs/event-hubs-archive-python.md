<properties
	pageTitle="Procedura dettagliata sull'archivio di Hub eventi di Azure | Microsoft Azure"
	description="Un esempio che usa l'SDK di Azure Python per illustrare l'uso della funzionalità di archivio dell'Hub eventi."
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# Procedura dettagliata sull'archivio di Hub eventi: Python

L'archivio è una nuova funzionalità di Hub eventi che consente di distribuire automaticamente i dati del flusso nell'Hub eventi a un account di archiviazione BLOB di Azure di propria scelta. Questa funzionalità rende più semplice eseguire l'elaborazione di batch su dati di streaming in tempo reale. In questo articolo viene descritto come usare l'archivio di Hub eventi con Python. Per altre informazioni sull'archivio di Hub eventi, vedere l'[articolo con la panoramica](event-hubs-archive-overview.md).

Questo esempio utilizza l'SDK di Azure Python per illustrare l'uso della funzionalità di archivio. sender.py invia una simulazione di telemetria ambientale a Hub eventi in formato JSON. L'Hub eventi è configurato per usare la funzione di archiviazione per scrivere i dati nell'archiviazione BLOB in batch. archivereader.py legge quindi questi BLOB, crea un file aggiuntivo per dispositivo e scrive i dati in file .csv.

Contenuto dell'esercitazione

1.  Creazione di un account di archiviazione BLOB di Azure e di un contenitore BLOB al suo interno con il portale di Azure

2.  Creazione di uno spazio dei nomi di Hub eventi tramite il portale di Azure

3.  Creazione di un Hub eventi con la funzione di archivio abilitata tramite il portale di Azure

4.  Invio dei dati all'Hub eventi con uno script Python

5.  Lettura dei file dall'archivio ed elaborazione con un altro script Python

Prerequisiti

1.  Python 2.7.x

2.  Una sottoscrizione di Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Creare un account di Archiviazione di Azure

1.  Accedere al [portale di Azure][].

2.  Nel riquadro di spostamento sinistro del portale fare clic su Nuovo, quindi su Data + Archiviazione e su Account di archiviazione.

3.  Completare i campi nel pannello dell'account di archiviazione e fare clic su **Crea**.

    ![][1]

4.  Una volta visualizzato il messaggio **Distribuzioni riuscite** fare clic sul nuovo account di archiviazione e su **BLOB** nel pannello **Essentials**. Quando si apre il pannello **Servizio BLOB**, fare clic su **+ Contenitore** in alto. Assegnare al contenitore il nome **archivio**, quindi chiudere il pannello **Servizio BLOB**.

5.  Fare clic su **Tasti di scelta** nel pannello sinistro e copiare il nome dell'account di archiviazione e il valore **key1**. Salvare questi valori nel Blocco note o in un'altra posizione temporanea.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## Creazione di uno script Python per inviare gli eventi all'Hub eventi

1.  Aprire l'editor preferito di Python, ad esempio [Visual Studio Code][].

2.  Creare uno script chiamato **sender.py**. Questo script invierà 200 eventi all'Hub eventi. Si tratta di semplici letture ambientali inviate in JSON.

3.  Incollare il seguente codice in sender.py:

	```
	import uuid
	import datetime
	import random
	import json
	from azure.servicebus import ServiceBusService
	
	sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
	devices = []
	for x in range(0, 10):
	    devices.append(str(uuid.uuid4()))
	
	for y in range(0,20):
	    for dev in devices:
	        reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
	        s = json.dumps(reading)
	        sbs.send\_event('myhub', s)
	    print y
	```
4.  Aggiornare il codice precedente in modo che usi il nome dello spazio dei nomi e i valori chiave ottenuti al momento della creazione dello spazio dei nomi dell'Hub eventi.

## Creazione di uno script Python per leggere i file dell'archivio

1.  Compilare il pannello e fare clic su **Crea**.

2.  Creare uno script chiamato **archivereader.py**. Questo script leggerà i file di archivio e creerà un file per dispositivo in modo da scrivere i dati solo per quel dispositivo.

3.  Incollare il seguente codice in archivereader.py:

	```
    import os
	import string
	import json
	import avro.schema
	from avro.datafile import DataFileReader, DataFileWriter
	from avro.io import DatumReader, DatumWriter
	from azure.storage.blob import BlockBlobService
	
	def processBlob(filename):
	    reader = DataFileReader(open(filename, 'rb'), DatumReader())
	    dict = {}
	    for reading in reader:
	        parsed\_json = json.loads(reading["Body"])
	        if not 'id' in parsed\_json:
	            return
	        if not dict.has\_key(parsed\_json['id']):
	        list = []
	        dict[parsed\_json['id']] = list
	    else:
	        list = dict[parsed\_json['id']]
	        list.append(parsed\_json)
	    reader.close()
	    for device in dict.keys():
	        deviceFile = open(device + '.csv', "a")
	        for r in dict[device]:
	            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

	def startProcessing(accountName, key, container):
	    print 'Processor started using path: ' + os.getcwd()
	    block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
	    generator = block\_blob\_service.list\_blobs(container)
	    for blob in generator:
	        if blob.properties.content\_length != 0:
	            print('Downloaded a non empty blob: ' + blob.name)
	            cleanName = string.replace(blob.name, '/', '\_')
	            block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
	            processBlob(cleanName)
	            os.remove(cleanName)
	        block\_blob\_service.delete\_blob(container, blob.name)
	startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Assicurarsi di incollare i valori appropriati per il nome e la chiave dell'account di archiviazione e nella chiamata a `startProcessing`.

## Esecuzione degli script

1.  Aprire un prompt dei comandi con un percorso contenente Python, quindi eseguire questi comandi per installare i pacchetti dei prerequisiti di Python:

	```
    pip install azure-storage
	pip install azure-servicebus
	pip install avro
    ```
  
    Se si dispone di una versione precedente di Azure o dell'archiviazione di Azure, potrebbe essere necessario usare l'opzione **aggiornamento**.

    Potrebbe inoltre essere necessario eseguire il comando seguente (non necessario nella maggior parte dei sistemi):

    ```
    pip install cryptography
    ```

2.  Modificare la directory nel punto in cui sono stati salvati sender.py e archivereader.py ed eseguire questo comando:

    ```
    start python sender.py
    ```
    
    Viene avviato un nuovo processo di Python per eseguire il mittente.

3. A questo punto attendere alcuni minuti per l'esecuzione dell'archivio. Digitare quindi il comando seguente nella finestra di comando originale:

    ```
    python archivereader.py
    ```

Questo processore dell'archivio usa la directory locale per scaricare tutti i BLOB dal contenitore o dall'account di archiviazione. Elaborerà quelli che non sono vuoti e scriverà i risultati come file .csv nella directory locale.

## Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

- [Panoramica dell'archivio di Hub eventi][]
- Un'[applicazione di esempio completa che usa Hub eventi][].
- Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][].
- [Panoramica di Hub eventi][]
 

[portale di Azure]: https://portal.azure.com/
[Panoramica dell'archivio di Hub eventi]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->