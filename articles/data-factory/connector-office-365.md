---
title: Copiare dati da Office 365 usando Azure Data Factory
description: Informazioni su come copiare dati da Office 365 in archivi dati di sink supportati usando attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: d97b3caccc92f0fdfeb229d94e30ee6499c26181
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912406"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiare dati da Office 365 in Azure usando Azure Data Factory

Azure Data Factory si integra con [Microsoft Graph Data Connect](https://docs.microsoft.com/graph/data-connect-concept-overview), consentendo di portare i dati aziendali avanzati nel tenant di Office 365 in Azure in modo scalabile e di compilare applicazioni di analisi ed estrarre informazioni dettagliate in base a questi preziosi asset di dati. L'integrazione con Privileged Access Management fornisce un controllo di accesso protetto per i dati importanti in Office 365.  Fare riferimento a [questo collegamento](https://docs.microsoft.com/graph/data-connect-concept-overview) per una panoramica su Microsoft Graph Data Connect e fare riferimento a [questo collegamento](https://docs.microsoft.com/graph/data-connect-policies#licensing) per informazioni sulle licenze.

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da Office 365. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate
Il connettore ADF Office 365 e Microsoft Graph Data Connect abilitano l'inserimento su larga scala di diversi tipi di set di dati dalle cassette postali abilitate per la posta elettronica di Exchange, inclusi i contatti della Rubrica, gli eventi del calendario, i messaggi di posta elettronica, le informazioni utente così via.  Fare riferimento a [qui](https://docs.microsoft.com/graph/data-connect-datasets) per visualizzare l'elenco completo dei set di impostazioni disponibili.

Per il momento, all'interno di una singola attività di copia è possibile **copiare i dati solo da Office 365 nell' [Archivio BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md) in formato JSON** (tipo setOfObjects). Se si vuole caricare Office 365 in altri tipi di archivi dati o in altri formati, è possibile concatenare la prima attività di copia con un'attività di copia successiva per caricare ulteriormente i dati in uno qualsiasi degli [archivi di destinazione di ADF supportati](copy-activity-overview.md#supported-data-stores-and-formats). Fare riferimento alla colonna "Supportato come sink" nella tabella "Archivi dati e formati supportati".

>[!IMPORTANT]
>- La sottoscrizione di Azure che contiene la data factory e l'archivio dati di sink deve essere nello stesso tenant Azure Active Directory (Azure AD) del tenant di Office 365.
>- Assicurarsi che l'area di Azure Integration Runtime usata per l'attività di copia, nonché la destinazione, siano nella stessa area in cui si trova la cassetta postale degli utenti del tenant di Office 365. Per informazioni sulla modalità in cui viene determinata la posizione di Azure IR, vedere [questo articolo](concepts-integration-runtime.md#integration-runtime-location). Per un elenco delle aree di Office e delle corrispondenti aree di Azure supportate, fare riferimento a [questa tabella](https://docs.microsoft.com/graph/data-connect-datasets#regions).
>- L'autenticazione basata su entità servizio è l'unico meccanismo di autenticazione supportato per archiviazione BLOB di Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 come archivi di destinazione.

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

## <a name="getting-started"></a>Inizia ora

>[!TIP]
>Per una procedura dettagliata sull'uso del connettore di Office 365, vedere l'articolo [Caricare dati da Office 365](load-office-365-data.md).

È possibile creare una pipeline con l'attività di copia usando uno degli strumenti o degli SDK seguenti. Selezionare un collegamento per passare a un'esercitazione con istruzioni dettagliate per creare una pipeline con un'attività di copia. 

- [Azure portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Modello di Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md) 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore di Office 365.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Office 365 sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Office 365** | SÌ |
| office365TenantId | ID tenant di Azure a cui appartiene l'account di Office 365. | SÌ |
| servicePrincipalTenantId | Specificare le informazioni sul tenant in cui si trova l'applicazione Web di Azure AD. | SÌ |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | SÌ |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. | SÌ |
| connectVia | Runtime di integrazione da usare per la connessione all'archivio dati.  Se non diversamente specificato, viene usato il runtime di integrazione di Azure predefinito. | No |

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

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **Office365Table** | SÌ |
| tableName | Nome del set di dati da estrarre da Office 365. Per l'elenco dei set di dati di Office 365 disponibili per l'estrazione, vedere [questo articolo](https://docs.microsoft.com/graph/data-connect-datasets#datasets). | SÌ |

Se si imposta `dateFilterColumn`, `startTime`, `endTime`e `userScopeFilterUri` nel set di dati, è ancora supportata così com'è, mentre si consiglia di usare il nuovo modello in origine attività in futuro.

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
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di Office 365.

### <a name="office-365-as-source"></a>Office 365 come origine

Per copiare dati da Office 365, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su: **Office365Source** | SÌ |
| allowedGroups | Predicato di selezione del gruppo.  Utilizzare questa proprietà per selezionare un massimo di 10 gruppi di utenti per i quali verranno recuperati i dati.  Se non viene specificato alcun gruppo, i dati verranno restituiti per l'intera organizzazione. | No |
| userScopeFilterUri | Quando `allowedGroups` proprietà non è specificata, è possibile usare un'espressione del predicato applicata all'intero tenant per filtrare le righe specifiche da estrarre da Office 365. Il formato del predicato deve corrispondere al formato di query delle API di Microsoft Graph, ad esempio `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | No |
| dateFilterColumn | Nome della colonna di filtro DateTime. Usare questa proprietà per limitare l'intervallo di tempo per cui vengono estratti i dati di Office 365. | Sì se il set di dati contiene una o più colonne DateTime. Per un elenco dei set di impostazioni che richiedono questo filtro DateTime, vedere [qui](https://docs.microsoft.com/graph/data-connect-filtering#filtering) . |
| startTime | Valore DateTime iniziale da filtrare. | Sì se è specificato `dateFilterColumn` |
| endTime | Valore DateTime finale da filtrare. | Sì se è specificato `dateFilterColumn` |
| outputColumns | Matrice delle colonne da copiare nel sink. | No |

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
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
