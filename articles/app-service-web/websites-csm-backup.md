<properties
	pageTitle="Usare REST per eseguire il backup e il ripristino di app del servizio App"
	description="Informazioni su come usare chiamate API RESTful per eseguire il backup e il ripristino di un'app in Servizio app di Azure"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="nicking"/>  
# Usare REST per eseguire il backup e il ripristino di app del servizio App

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API REST](websites-csm-backup.md)

È possibile eseguire il backup di [app del servizio App](https://azure.microsoft.com/services/app-service/web/) come BLOB nell'archiviazione di Azure. Il backup può contenere anche i database delle app. Se si elimina l'app accidentalmente o se è necessario ripristinarne una versione precedente, è possibile farlo da un backup precedente. I backup possono essere eseguito in qualsiasi momento su richiesta o pianificati a intervalli appropriati.

Questo articolo illustra le procedure di backup e ripristino di un'app con le richieste API RESTful. Se si vogliono creare e gestire i backup di app graficamente tramite il portale di Azure, vedere [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md).

<a name="gettingstarted"></a>
## Introduzione
Per inviare richieste REST, è necessario conoscere **nome**, **gruppo di risorse** e **ID sottoscrizione** dell'app. Per trovare queste informazioni, fare clic sull'app nel pannello **Servizio app** del [portale di Azure](https://portal.azure.com). Per gli esempi in questo articolo, verrà configurato il sito Web **backuprestoreapiexamples.azurewebsites.net**. È archiviato nel gruppo di risorse Default-Web-WestUS ed è in esecuzione in una sottoscrizione con l'ID 00001111-2222-3333-4444-555566667777.

![Informazioni sul sito Web di esempio][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## API REST per backup e ripristino
Verranno ora esaminati alcuni esempi relativi all'uso dell'API REST per il backup e il ripristino di un'app. Ogni esempio include un URL e il corpo della richiesta HTTP. L'URL di esempio contiene segnaposto racchiusi tra parentesi graffe, ad esempio {subscription-id}. Sostituire i segnaposto con le informazioni corrispondenti per la propria app. Come riferimento, ecco una descrizione di ogni segnaposto visualizzato negli URL di esempio.

* subscription-id: ID della sottoscrizione di Azure che include l'app
* resource-group-name: nome del gruppo di risorse che include l'app
* name: nome dell'app
* backup-id: ID del backup dell'app

Per la documentazione completa dell'API, inclusi i diversi parametri opzionali che possono essere inclusi nella richiesta HTTP, vedere [Esplora risorse di Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## Eseguire il backup di un'app su richiesta
Per eseguire immediatamente il backup di un'app, inviare una richiesta **POST** a **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Ecco l'aspetto dell'URL se si usa il sito Web di esempio: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

È necessario fornire un oggetto JSON nel corpo della richiesta per specificare l'account di archiviazione da usare per archiviare il backup. L'oggetto JSON deve avere una proprietà denominata **storageAccountUrl**, che contiene un [URL di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md) che concede dell'accesso in scrittura al contenitore di archiviazione di Azure che contiene il BLOB di backup. Se si vuole eseguire il backup dei database, è necessario fornire anche un elenco contenente i nomi, tipi e le stringhe di connessione dei database di cui eseguire il backup.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Il backup dell'app inizia immediatamente alla ricezione della richiesta. Il completamento del processo di backup può richiedere molto tempo. La risposta HTTP contiene un ID che è possibile usare in un'altra richiesta per visualizzare lo stato del backup. Ecco un esempio del corpo della risposta HTTP alla richiesta di backup.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] I messaggi di errore si trovano nella proprietà log della risposta HTTP.

<a name="schedule-automatic-backups"></a>
## Pianificare backup automatici
Oltre al backup di un'app su richiesta, è anche possibile pianificare l'esecuzione automatica di un backup.

### Configurare una nuova pianificazione di backup automatico
Per configurare una pianificazione di backup, inviare una richiesta **PUT** a **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Ecco l'aspetto dell'URL per il sito Web di esempio: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

Il corpo della richiesta deve includere un oggetto JSON che specifica la configurazione di backup. Ecco un esempio con tutti i parametri necessari.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Questo esempio configura l'app per l'esecuzione automatica del backup ogni sette giorni. I parametri **frequencyInterval** e **frequencyUnit** determinano insieme la frequenza con cui verranno eseguiti i backup. I valori validi per **frequencyUnit** sono **hour** e **day**. Ad esempio, per eseguire il backup di un'app per ogni 12 ore, impostare frequencyInterval su 12 e frequencyUnit su hour.

I backup precedenti saranno rimossi automaticamente dall'account di archiviazione. È possibile controllare la durata dei backup impostando il parametro **retentionPeriodInDays**. Se si vuole avere sempre almeno un backup salvato, indipendentemente dalla durata definita, impostare **keepAtLeastOneBackup** su true.

### Ottenere una pianificazione di backup automatico
Per ottenere una configurazione di backup per un'app, inviare una richiesta **POST** all'URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

L'URL per il sito di esempio è **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## Ottenere lo stato di un backup
A seconda delle dimensioni dell'app, il completamento di un backup può richiedere alcuni minuti. I backup possono anche non riuscire, raggiungere il timeout o riuscire parzialmente. Per visualizzare lo stato di tutti i backup di un'app, inviare una richiesta **GET** all'URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Per visualizzare lo stato di un backup specifico, inviare una richiesta GET all'URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Ecco l'aspetto dell'URL per il sito Web di esempio: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

Il corpo della risposta contiene un oggetto JSON simile a questo esempio.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

Lo stato di un backup è un tipo enumerato. Ecco tutti gli stati possibili.

* 0 – InProgress: il backup è stato avviato ma non è ancora stato completato.
* 1 – Failed: il backup non riuscito.
* 2 – Succeeded: il backup è stato completato correttamente.
* 3 – TimedOut: il backup non è stato completato entro il tempo previsto ed è stato annullato.
* 4 – Created: la richiesta di backup è in coda, ma non è stata avviata.
* 5 – Skipped: il backup non ha potuto continuare, perché una pianificazione ha attivato troppi backup.
* 6 – PartiallySucceeded: il backup è riuscito, ma alcuni file non sono stati inclusi nel backup perché non hanno potuto essere letti. Ciò accade di solito perché è stato attivato un blocco esclusivo sui file.
* 7 – DeleteInProgress: è stata richiesta l'eliminazione del backup, ma l'operazione non è ancora stata completata.
* 8 – DeleteFailed: non è stato possibile eliminare il backup. Ciò può verificarsi perché l'URL di firma di accesso condiviso usata per creare il backup è scaduta.
* 9 – Deleted: il backup è stato eliminato.

<a name="restore-app"></a>
## Ripristinare un'app da un backup
Se l'app è stata eliminata o se si vuole tornare a una versione precedente dell'app, è possibile ripristinarla da un backup. Per richiamare un'operazione di ripristino, inviare una richiesta **POST** all'URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Ecco l'aspetto dell'URL per il sito Web di esempio: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore**

Nel corpo della richiesta inviare un oggetto JSON che contiene le proprietà per l'operazione di ripristino. Di seguito è riportato un esempio che contiene tutte le proprietà richieste:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### Ripristinare una nuova app
Quando si ripristina un backup, in alcuni casi è consigliabile creare una nuova app, invece di sovrascriverne una esistente. A tale scopo, modificare l'URL della richiesta in modo che punti alla nuova app che si vuole creare e modificare la proprietà **overwrite** in JSON su **false**.

<a name="delete-app-backup"></a>
## Eliminare un backup dell'app
Per eliminare un backup, inviare una richiesta **DELETE** all'URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Ecco l'aspetto dell'URL per il sito Web di esempio: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## Gestire L'URL di firma di accesso condiviso di un backup
Servizio app di Azure tenta di eliminare il backup dall'archiviazione di Azure usando l'URL di firma di accesso condiviso specificato al momento della creazione del backup. Se questo URL di firma di accesso condiviso non è più valido, il backup non potrà essere eliminato tramite l'API REST. È tuttavia possibile aggiornare l'URL di firma di accesso condiviso associato a un backup inviando una richiesta **POST** all'URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Ecco l'aspetto dell'URL per il sito Web di esempio: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list**

Nel corpo della richiesta inviare un oggetto JSON che contiene il nuovo URL di firma di accesso condiviso. Di seguito è fornito un esempio.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Per motivi di sicurezza, l'URL di firma di accesso condiviso associato a un backup non viene restituito quando si invia una richiesta GET per un backup specifico. Se si vuole visualizzare l'URL di firma di accesso condiviso associato a un backup, inviare una richiesta POST allo stesso URL precedente e includere semplicemente un oggetto JSON vuoto nel corpo della richiesta. La risposta dal server contiene tutte le informazioni del backup, incluso l'URL di firma di accesso condiviso.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_0810_2016-->
