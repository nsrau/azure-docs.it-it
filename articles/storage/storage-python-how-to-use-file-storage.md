<properties
	pageTitle="Come usare l'archiviazione file da Python | Microsoft Azure"
	description="Informazioni su come usare l'archiviazione file di Azure da Python per caricare, elencare, scaricare ed eliminare file."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="emgerner"/>

# Come usare l'archiviazione file di Azure da Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## Panoramica

In questo articolo verranno illustrati diversi scenari comuni di uso dell'archiviazione file. Gli esempi sono scritti in Python e usano [Microsoft Azure Storage SDK per Python]. Gli scenari presentati includono caricamento, visualizzazione dell'elenco, download ed eliminazione di file.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare una condivisione

L'oggetto **FileService** consente di usare condivisioni, directory e file. Il codice seguente consente di creare un oggetto **FileService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

	from azure.storage.file import FileService

Il codice seguente consente di creare un oggetto **FileService** usando il nome e la chiave dell'account di archiviazione. Sostituire 'myaccount' e 'mykey' con l'account e la chiave da usare.

	file_service = **FileService** (account_name='myaccount', account_key='mykey')

Nell'esempio di codice seguente, è possibile usare un oggetto **FileService** per creare la condivisione, se non esiste.

	file_service.create_share('myshare')

## Caricare un file in una condivisione

Una condivisione di archiviazione file di Azure contiene almeno una directory radice in cui possono risiedere i file. In questa sezione verrà illustrato come caricare un file dall'archiviazione locale nella directory radice di una condivisione.

Per creare un file e caricare i dati, usare il metodo **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** o **create\_file\_from\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

**create\_file\_from\_path** carica i contenuti di un file dal percorso specificato, **create\_file\_from\_stream** carica i contenuti da un file o da un flusso già aperto. **create\_file\_from\_bytes** carica una matrice di byte e **create\_file\_from\_text** carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

Nell'esempio seguente viene caricato il contenuto del file **sunset.png** nel file **myfile**.

	from azure.storage.file import ContentSettings
	file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## Procedura: creare una directory

È inoltre possibile organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice. Il servizio di archiviazione file di Azure permette di creare tante directory quante ne sono consentite dall'account. Il codice riportato di seguito creerà una sottodirectory denominata **sampledir** nella directory radice.

	file_service.create_directory('myshare', 'sampledir')

## Procedura: elencare i file e le directory in una condivisione

Per elencare i file e le directory di una condivisione, usare il metodo **list\_directories\_and\_files**. Questo metodo restituisce un generatore. Il codice seguente consente di inviare alla console il valore **name** di ogni file e directory in una condivisione.

	generator = file_service.list_directories_and_files('myshare')
	for file_or_dir in generator:
		print(file_or_dir.name)

## Download dei file

Per scaricare dati da un file, usare **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** o **get\_file\_to\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di **get\_file\_to\_path** per scaricare il contenuto del file **myfile** e archiviarlo nel file **out-sunset.png**.

	file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## Eliminare un file

Per eliminare un file, infine, chiamare **delete\_file**.

	file_service.delete_file('myshare', None, 'myfile')

## Passaggi successivi

A questo punto, dopo avere appreso le nozioni di base dell'archiviazione file, visitare i collegamenti seguenti per altre informazioni.

- [Centro per sviluppatori di Python](/develop/python/)
- [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del team di Archiviazione di Azure]
- [Microsoft Azure Storage SDK per Python]

[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0727_2016-->