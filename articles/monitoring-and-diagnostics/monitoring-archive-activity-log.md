---
title: "Archiviare il registro attività di Azure | Microsoft Docs"
description: "Informazioni su come archiviare il log attività di Azure per la conservazione a lungo termine in un account di archiviazione."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 0b041cc6a986c6f7a11d213f03294c9716c20d04
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="archive-the-azure-activity-log"></a>Archiviare il log attività di Azure
In questo articolo viene illustrato come è possibile usare il Portale di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando multipiattaforma per archiviare il [**registro attività di Azure**](monitoring-overview-activity-logs.md) in un account di archiviazione. Questa opzione è utile per conservare il log attività per più di 90 giorni (con il controllo completo sui criteri di conservazione) per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi per non più di 90 giorni, non è necessario configurare l'archiviazione in un account di archiviazione, perché gli eventi del log attività vengono conservati nella piattaforma Azure per 90 giorni senza abilitare l'archiviazione.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario [creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) in cui poter archiviare il log attività. È consigliabile non usare un account di archiviazione esistente in cui sono archiviati altri dati non di monitoraggio, per poter controllare meglio l'accesso ai dati di monitoraggio. Se tuttavia in un account di archiviazione si archiviano anche log di diagnostica e metriche, può avere senso non solo usare tale account di archiviazione per il log attività, ma anche tenere tutti i dati di monitoraggio in una posizione centrale. L'account di archiviazione usato deve essere un account di archiviazione per utilizzo generico, non un account di archiviazione BLOB. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della sottoscrizione che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="log-profile"></a>Profilo del log
Per archiviare il log attività con uno dei metodi seguenti, impostare il **profilo del log** per una sottoscrizione. Il profilo del log definisce il tipo di eventi archiviati o trasmessi e gli output (account di archiviazione e/o hub eventi). Definisce anche i criteri di conservazione (numero di giorni di conservazione) per gli eventi archiviati in un account di archiviazione. Se il criterio di conservazione viene impostato su zero, gli eventi vengono archiviati a tempo indeterminato. In caso contrario, si può impostare un valore qualsiasi compreso tra 1 e 2147483647. I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata, i log relativi a tale giornata non rientrano quindi più nei criteri di conservazione e verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. [Fare clic qui per altre informazioni sui profili dei log](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archiviare il log attività con il portale
1. Nel portale fare clic sul collegamento **Log attività** a sinistra. Se il collegamento Log attività non è visualizzato, fare prima clic sul collegamento **Tutti i servizi**.
   
    ![Passare al pannello Log attività](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Nella parte superiore del pannello fare clic su **Esporta**.
   
    ![Fare clic sul pulsante Esporta](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Nel pannello visualizzato selezionare la casella **Esporta in un account di archiviazione** e selezionare un account di archiviazione.
   
    ![Impostare un account di archiviazione](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Usando il dispositivo di scorrimento o la casella di testo, definire un numero di giorni per cui gli eventi del log attività devono essere conservati nell'account di archiviazione. Se si preferisce che i dati rimangano nell'account di archiviazione a tempo indeterminato, impostare questo numero su zero.
5. Fare clic su **Save**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiviare il log attività con PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Proprietà | Obbligatoria | DESCRIZIONE |
| --- | --- | --- |
| StorageAccountId |No  |ID risorsa dell'account di archiviazione in cui salvare i log attività. |
| Località |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. È possibile visualizzare un elenco di tutte le aree [visitando questa pagina](https://azure.microsoft.com/en-us/regions) o usando [l'API REST di gestione di Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere mantenuti, compreso tra 1 e 2147483647. Se il valore è zero, i log vengono mantenuti per un periodo illimitato. |
| Categorie |Sì |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |

## <a name="archive-the-activity-log-via-cli"></a>Archiviare il log attività con l'interfaccia della riga di comando
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Proprietà | Obbligatoria | DESCRIZIONE |
| --- | --- | --- |
| name |Sì |Nome del profilo di log. |
| storageId |No  |ID risorsa dell'account di archiviazione in cui salvare i log attività. |
| Località |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. È possibile visualizzare un elenco di tutte le aree [visitando questa pagina](https://azure.microsoft.com/en-us/regions) o usando [l'API REST di gestione di Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere mantenuti, compreso tra 1 e 2147483647. Se il valore è zero, i log vengono archiviati per un periodo illimitato. |
| Categorie |Sì |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |

## <a name="storage-schema-of-the-activity-log"></a>Schema di archiviazione del log attività
Una volta configurata l'archiviazione, verrà creato un contenitore di archiviazione nell'account di archiviazione non appena si verificherà un evento del log attività. I BLOB nel contenitore seguono lo stesso formato nel log attività e nei log di diagnostica. La struttura di questi BLOB è:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID sottoscrizione}/y={anno a quattro cifre}/m={mese a due cifre}/d={giorno a due cifre}/h={ora a due cifre in formato 24 ore}/m=00/PT1H.json
> 
> 

Ad esempio, un nome BLOB potrebbe essere:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m=00) è sempre 00, perché gli eventi del log attività vengono sempre suddivisi in singoli BLOB per ogni ora.

Nel file PT1H.json ogni evento viene archiviato nella matrice "records", con questo formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nome dell'elemento | DESCRIZIONE |
| --- | --- |
| time |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| ResourceId |ID risorsa della risorsa interessata. |
| operationName |Nome dell'operazione. |
| category |Categoria dell'azione, ad esempio scrittura o lettura. |
| resultType |Il tipo di risultato, ad esempio operazione riuscita, esito negativo, avvio |
| resultSignature |Dipende dal tipo di risorsa. |
| durationMs |Durata dell'operazione in millisecondi |
| callerIpAddress |Indirizzo IP dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. |
| correlationId |In genere un GUID in formato stringa. Gli eventi che condividono un elemento correlationId appartengono alla stessa azione. |
| identity |BLOB JSON che descrive l'autorizzazione e le attestazioni. |
| autorizzazione |BLOB delle proprietà RBAC dell'evento. In genere include le proprietà "action", "role" e "scope". |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose" |
| location |Area in cui si trova la località (o global). |
| properties |Set di coppie `<Key, Value>` ad esempio Dictionary, che descrivono i dettagli dell'evento. |

> [!NOTE]
> Le proprietà e l'utilizzo di tali proprietà possono variare a seconda della risorsa.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione all'archivio BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Stream the Activity Log to Event Hubs (Trasmettere il log attività a Hub eventi)](monitoring-stream-activity-logs-event-hubs.md)
* [Read more about the Activity Log (Altre informazioni sul log attività)](monitoring-overview-activity-logs.md)

