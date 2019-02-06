---
title: Copiare dati da Office 365 tramite Azure Data Factory (anteprima) | Microsoft Docs
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
ms.date: 01/29/2019
ms.author: jingwang
ms.openlocfilehash: b86aef7de048690d689a87d4fb844f77ea986445
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297465"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Copiare dati da Office 365 in Azure tramite Data Factory (anteprima) 

Azure Data Factory consente di portare in Azure i dati aziendali importanti presenti nel proprio tenant di Office 365 in modo scalabile, di creare applicazioni di analisi e di estrarre informazioni dettagliate basate su questi asset di dati importanti. L'integrazione con Privileged Access Management fornisce un controllo di accesso protetto per i dati importanti in Office 365.  Per altre informazioni sulla connessione dei dati di Microsoft Graph, vedere [questo collegamento](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da Office 365. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Attualmente, nell'ambito di una singola attività di copia, è possibile solo **copiare dati da Office 365 in [Archiviazione BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) e [Azure Data Lake Storage Gen2 (anteprima)](connector-azure-data-lake-storage.md) in formato JSON** (tipo setOfObjects). Se si vuole caricare Office 365 in altri tipi di archivi dati o in altri formati, è possibile concatenare la prima attività di copia con un'attività di copia successiva per caricare ulteriormente i dati in uno qualsiasi degli [archivi di destinazione di ADF supportati](copy-activity-overview.md#supported-data-stores-and-formats). Fare riferimento alla colonna "Supportato come sink" nella tabella "Archivi dati e formati supportati".

>[!IMPORTANT]
>- La sottoscrizione di Azure che contiene la data factory e l'archivio dati di sink deve essere nello stesso tenant Azure Active Directory (Azure AD) del tenant di Office 365.
>- Assicurarsi che l'area di Azure Integration Runtime usata per l'attività di copia, nonché la destinazione, siano nella stessa area in cui si trova la cassetta postale degli utenti del tenant di Office 365. Per informazioni sulla modalità in cui viene determinata la posizione di Azure IR, vedere [questo articolo](concepts-integration-runtime.md#integration-runtime-location). Per un elenco delle aree di Office e delle corrispondenti aree di Azure supportate, fare riferimento a [questa tabella](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions).
>-  Se si caricano i dati di Office 365 in **Archiviazione BLOB di Azure** come destinazione, assicurarsi che sia in uso l'**[autenticazione dell'entità servizio](connector-azure-blob-storage.md#service-principal-authentication)** quando si definisce il servizio collegato in Archiviazione BLOB di Azure e che non siano in uso le autenticazioni della [chiave dell'account](connector-azure-blob-storage.md#account-key-authentication), della [firma di accesso condiviso](connector-azure-blob-storage.md#shared-access-signature-authentication) o delle [identità gestite per le risorse di Azure](connector-azure-blob-storage.md#managed-identity).
>-  Se si caricano i dati di Office 365 in **Azure Data Lake Storage Gen1** come destinazione, assicurarsi che sia in uso l'[**autenticazione dell'entità servizio**](connector-azure-data-lake-store.md#use-service-principal-authentication) quando si definisce il servizio collegato in Azure Data Lake Storage Gen1 e che non sia in uso l'[autenticazione delle identità gestite per le risorse di Azure](connector-azure-data-lake-store.md#managed-identity).

## <a name="prerequisites"></a>Prerequisiti

Prima di copiare dati da Office 365 in Azure, è necessario completare i passaggi seguenti:

- L'amministratore del tenant di Office 365 deve completare le azioni di onboarding come descritto in [questa pagina](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Creare e configurare un'applicazione Web di Azure AD in Azure Active Directory.  Per istruzioni, vedere [Creare un'applicazione Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Prendere nota dei valori seguenti che si useranno per definire il servizio collegato per Office 365:
    - ID tenant. Per istruzioni, vedere [Ottenere l'ID tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - ID applicazione e chiave di autenticazione.  Per istruzioni, vedere [Ottenere l'ID applicazione e la chiave di autenticazione](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Aggiungere l'identità utente che effettua la richiesta di accesso ai dati come proprietario dell'applicazione Web di Azure AD (dall'applicazione Web di Azure AD > Impostazioni > Proprietari > Aggiungi proprietario). 
    - L'identità utente deve essere presente nell'organizzazione Office 365 da cui si ottengono i dati e non deve essere un utente guest.

## <a name="approving-new-data-access-requests"></a>Approvazione di nuove richieste di accesso ai dati

Se questa è la prima volta che si richiedono dati per questo contesto (una combinazione di elementi come la tabella dati a cui occorre accedere, l'account di destinazione in cui devono essere caricati i dati e l'identità che sta richiedendo l'accesso ai dati), si noterà che lo stato dell'attività di copia è "In corso" e solo quando si seleziona il collegamento "Dettagli" nel riquadro [Azioni](copy-activity-overview.md#monitoring) si visualizzerà lo stato "RequestingConsent".  Un membro del gruppo dei responsabili dell'approvazione dell'accesso ai dati deve approvare la richiesta in Privileged Access Management prima che l'estrazione dei dati prosegua.

Per informazioni sul modo in cui il responsabile dell'approvazione può approvare la richiesta di accesso ai dati, vedere [questo articolo](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests). Per una spiegazione sull'integrazione generale con Privileged Access Management, incluse informazioni su come configurare il gruppo di responsabili dell'approvazione all'accesso ai dati, vedere [questo articolo](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management).

## <a name="policy-validation"></a>Convalida dei criteri

Se Azure Data Factory viene creato come parte di un'app gestita e le assegnazioni dei criteri di Azure vengono eseguite sulle risorse all'interno del gruppo di risorse di gestione, quindi per ogni esecuzione dell'attività di copia, Azure Data Factory verificherà che vengano applicate le assegnazioni dei criteri. Per un elenco dei criteri supportati, vedere [questo articolo](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies).

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
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Yes |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. | Yes |
| connectVia | Runtime di integrazione da usare per la connessione all'archivio dati.  Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No  |

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
| tableName | Nome del set di dati da estrarre da Office 365. Per l'elenco dei set di dati di Office 365 disponibili per l'estrazione, vedere [questo articolo](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets). | Yes |
| predicate | Un'espressione del predicato che può essere usata per filtrare le righe specifiche da estrarre da Office 365.  Per trovare le colonne che possono essere usate per il filtro predicato per ogni tabella e il formato dell'espressione di filtro, vedere [questo articolo](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters). | No <br>Se non viene fornita alcuna proprietà predicate, l'impostazione predefinita prevede l'estrazione dei dati per gli ultimi 30 giorni. |

**Esempio**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
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
