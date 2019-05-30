---
title: Copiare dati da Office 365 usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Office 365 in archivi dati di sink supportati usando attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 80ef8870bafa00f3debda99db299018a39d42a82
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245047"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiare dati da Office 365 in Azure usando Azure Data Factory

Azure Data Factory consente di portare in Azure i dati aziendali importanti presenti nel proprio tenant di Office 365 in modo scalabile, di creare applicazioni di analisi e di estrarre informazioni dettagliate basate su questi asset di dati importanti. L'integrazione con Privileged Access Management fornisce un controllo di accesso protetto per i dati importanti in Office 365.  Per altre informazioni sulla connessione dei dati di Microsoft Graph, vedere [questo collegamento](https://docs.microsoft.com/graph/data-connect-concept-overview).

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da Office 365. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Per ora, all'interno di una singola attività di copia è solo possibile **copiare i dati da Office 365 in [archivio Blob di Azure](connector-azure-blob-storage.md), [Azure Data Lake archiviazione Gen1](connector-azure-data-lake-store.md), e [Gen2 di archiviazione di Azure Data Lake ](connector-azure-data-lake-storage.md) in formato JSON** (tipo setOfObjects). Se si vuole caricare Office 365 in altri tipi di archivi dati o in altri formati, è possibile concatenare la prima attività di copia con un'attività di copia successiva per caricare ulteriormente i dati in uno qualsiasi degli [archivi di destinazione di ADF supportati](copy-activity-overview.md#supported-data-stores-and-formats). Fare riferimento alla colonna "Supportato come sink" nella tabella "Archivi dati e formati supportati".

>[!IMPORTANT]
>- La sottoscrizione di Azure che contiene la data factory e l'archivio dati di sink deve essere nello stesso tenant Azure Active Directory (Azure AD) del tenant di Office 365.
>- Assicurarsi che l'area di Azure Integration Runtime usata per l'attività di copia, nonché la destinazione, siano nella stessa area in cui si trova la cassetta postale degli utenti del tenant di Office 365. Per informazioni sulla modalità in cui viene determinata la posizione di Azure IR, vedere [questo articolo](concepts-integration-runtime.md#integration-runtime-location). Per un elenco delle aree di Office e delle corrispondenti aree di Azure supportate, fare riferimento a [questa tabella](https://docs.microsoft.com/graph/data-connect-datasets#regions).
>- Autenticazione dell'entità servizio è l'unico meccanismo di autenticazione supportato per l'archiviazione Blob di Azure, Azure Data Lake archiviazione Gen1 e Gen2 di archiviazione di Azure Data Lake come archivi di destinazione.

## <a name="prerequisites"></a>Prerequisiti

Prima di copiare dati da Office 365 in Azure, è necessario completare i passaggi seguenti:

- L'amministratore del tenant di Office 365 deve completare le azioni di onboarding come descritto in [questa pagina](https://docs.microsoft.com/graph/data-connect-get-started).
- Creare e configurare un'applicazione Web di Azure AD in Azure Active Directory.  Per istruzioni, vedere [Creare un'applicazione Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Prendere nota dei valori seguenti che si useranno per definire il servizio collegato per Office 365:
    - ID tenant. Per istruzioni, vedere [Ottenere l'ID tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - ID applicazione e chiave di autenticazione.  Per istruzioni, vedere [Ottenere l'ID applicazione e la chiave di autenticazione](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Aggiungere l'identità utente che effettua la richiesta di accesso ai dati come proprietario dell'applicazione Web di Azure AD (dall'applicazione Web di Azure AD > Impostazioni > Proprietari > Aggiungi proprietario). 
    - L'identità utente deve essere presente nell'organizzazione Office 365 da cui si ottengono i dati e non deve essere un utente guest.

## <a name="approving-new-data-access-requests"></a>Approvazione di nuove richieste di accesso ai dati

Se questa è la prima volta che si richiedono dati per questo contesto (una combinazione di elementi come la tabella dati a cui occorre accedere, l'account di destinazione in cui devono essere caricati i dati e l'identità che sta richiedendo l'accesso ai dati), si noterà che lo stato dell'attività di copia è "In corso" e solo quando si seleziona il collegamento "Dettagli" nel riquadro [Azioni](copy-activity-overview.md#monitoring) si visualizzerà lo stato "RequestingConsent".  Un membro del gruppo dei responsabili dell'approvazione dell'accesso ai dati deve approvare la richiesta in Privileged Access Management prima che l'estrazione dei dati prosegua.

Per informazioni sul modo in cui il responsabile dell'approvazione può approvare la richiesta di accesso ai dati, vedere [questo articolo](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal). Per una spiegazione sull'integrazione generale con Privileged Access Management, incluse informazioni su come configurare il gruppo di responsabili dell'approvazione all'accesso ai dati, vedere [questo articolo](https://docs.microsoft.com/graph/data-connect-pam).

## <a name="policy-validation"></a>Convalida dei criteri

Se Azure Data Factory viene creato come parte di un'app gestita e le assegnazioni dei criteri di Azure vengono eseguite sulle risorse all'interno del gruppo di risorse di gestione, quindi per ogni esecuzione dell'attività di copia, Azure Data Factory verificherà che vengano applicate le assegnazioni dei criteri. Per un elenco dei criteri supportati, vedere [questo articolo](https://docs.microsoft.com/graph/data-connect-policies#policies).

## <a name="getting-started"></a>Introduzione

>[!TIP]
>Per una procedura dettagliata sull'uso del connettore di Office 365, vedere l'articolo [Caricare dati da Office 365](load-office-365-data.md).

È possibile creare una pipeline con l'attività di copia usando uno degli strumenti o degli SDK seguenti. Selezionare un collegamento per passare a un'esercitazione con istruzioni dettagliate per creare una pipeline con un'attività di copia. 

- [Portale di Azure](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [API REST](quickstart-create-data-factory-rest-api.md)
- [Modello di Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore di Office 365.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Office 365 sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Office365** | Yes |
| office365TenantId | ID tenant di Azure a cui appartiene l'account di Office 365. | Yes |
| servicePrincipalTenantId | Specificare le informazioni sul tenant in cui si trova l'applicazione Web di Azure AD. | Yes |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. | Yes |
| connectVia | Runtime di integrazione da usare per la connessione all'archivio dati.  Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No |

>[!NOTE]
> Differenza tra **office365TenantId** e **servicePrincipalTenantId** e il valore corrispondente da specificare:
>- Se l'utente è uno sviluppatore aziendale che sviluppa un'applicazione con i dati di Office 365 per l'utilizzo nell'organizzazione, è necessario specificare lo stesso ID tenant per entrambe le proprietà, ovvero l'ID tenant AAD dell'organizzazione.
>- Se l'utente è uno sviluppatore di ISV che sviluppa un'applicazione per i clienti, office365TenantId corrisponderà all'ID tenant AAD del cliente (programma di installazione dell'applicazione) e servicePrincipalTenantId corrisponderà all'ID tenant AAD della società.

**Esempio:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Office 365.

Per copiare dati da Office 365, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **Office365Table** | Yes |
| tableName | Nome del set di dati da estrarre da Office 365. Per l'elenco dei set di dati di Office 365 disponibili per l'estrazione, vedere [questo articolo](https://docs.microsoft.com/graph/data-connect-datasets#datasets). | Yes |
| allowedGroups | Predicato di selezione gruppo.  Utilizzare questa proprietà per selezionare un massimo di 10 gruppi di utenti per i quali verranno recuperati i dati.  Se non vengono specificati gruppi, i dati verranno restituiti per l'intera organizzazione. | No  |
| userScopeFilterUri | Quando `allowedGroups` proprietà non è specificata, è possibile usare un'espressione del predicato viene applicata per l'intero tenant per filtrare le righe specifiche da estrarre da Office 365. Il formato del predicato deve corrispondere al formato di query dell'API Graph Microsoft, ad esempio `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | No  |
| dateFilterColumn | Nome della colonna di filtro di data/ora. Utilizzare questa proprietà per limitare l'intervallo di tempo per cui Office 365 vengono estratti i dati. | Sì, se il set di dati contiene uno o più colonne di tipo DateTime. Fare riferimento [qui](https://docs.microsoft.com/graph/data-connect-filtering#filtering) per elenco di set di dati che richiedono questo filtro data/ora. |
| startTime | Avviare il valore DateTime da filtrare. | Sì se `dateFilterColumn` specificato |
| endTime | Terminare il valore DateTime da filtrare. | Sì se `dateFilterColumn` specificato |

**Esempio**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di Office 365.

### <a name="office-365-as-source"></a>Office 365 come origine

Per copiare dati da Office 365, impostare il tipo di origine nell'attività di copia su **Office365Source**. Nella sezione **source** dell'attività di copia non sono supportate proprietà aggiuntive.

**Esempio:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
