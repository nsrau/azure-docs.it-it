---
title: Esempio di script CLI di Azure-creare un'app per la logica
description: Script di esempio per creare un'app per la logica tramite l'estensione app per la logica nell'interfaccia della riga di comando di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: e66edb1325d1c603e89f877f1d34f60c136eb1db
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740726"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Esempio di script CLI di Azure-creare un'app per la logica

Questo script crea un'app per la logica di esempio tramite l' [estensione app](/cli/azure/ext/logic/logic?view=azure-cli-latest)per la logica dell'interfaccia della riga di comando di Azure `az logic` . Per una guida dettagliata alla creazione e gestione di app per la logica tramite l'interfaccia della riga di comando di Azure, vedere la Guida [introduttiva alle app per la logica per l'interfaccia](quickstart-logic-apps-azure-cli.md)

> [!WARNING]
> L'estensione app per la logica dell'interfaccia della riga di comando di Azure è attualmente *sperimentale* e non è *coperta dall'assistenza clienti* . Usare questa estensione dell'interfaccia della riga di comando con cautela, soprattutto se si sceglie di usare l'estensione negli ambienti di produzione.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) installata nel computer locale.
* L'[estensione app per la logica dell'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-list?view=azure-cli-latest) installata nel computer. Per installare questa estensione, usare il comando seguente: `az extension add --name logic`
* [Definizione del flusso di lavoro](quickstart-logic-apps-azure-cli.md#workflow-definition) per l'app per la logica. Questo file JSON deve seguire lo [schema del linguaggio di definizione del flusso di lavoro](logic-apps-workflow-definition-language.md).
* Una connessione API a un account di posta elettronica tramite un [connettore di app](../connectors/apis-list.md) per la logica supportato nello stesso gruppo di risorse dell'app per la logica. Questo esempio usa il connettore [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) , ma è anche possibile usare altri connettori, ad esempio [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Prima di iniziare, convalidare l'ambiente:

* Accedere al portale di Azure e verificare che la sottoscrizione sia attiva eseguendo `az login`.

* Controllare la versione dell'interfaccia della riga di comando di Azure in una finestra terminale o di comando eseguendo `az --version`. Per la versione più recente, vedere le [ultime note sulla versione](/cli/azure/release-notes-azure-cli?tabs=azure-cli&view=azure-cli-latest).

  * Se non si dispone della versione più recente, aggiornare l'installazione seguendo la [guida all'installazione per il sistema operativo o la piattaforma in uso](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sample-workflow-explanation"></a>Esempio di spiegazione del flusso di lavoro

Questo file di definizione del flusso di lavoro di esempio crea la stessa app per la logica di base della [Guida introduttiva per portale di Azure le app per la logica](quickstart-create-first-logic-app-workflow.md). 

Questo flusso di lavoro di esempio: 

1. Specifica uno schema, `$schema` , per l'app per la logica.

1. Definisce un trigger per l'app per la logica nell'elenco di trigger `triggers` . Il trigger si ripete ( `recurrence` ) ogni 3 ore. Le azioni vengono attivate quando viene pubblicato un nuovo elemento del feed ( `When_a_feed_item_is_published` ) per il feed RSS specificato ( `feedUrl` ).

1. Definisce un'azione per l'app per la logica nell'elenco di azioni `actions` . L'azione Invia un messaggio di posta elettronica ( `Send_an_email_(V2)` ) tramite Microsoft 365 con i dettagli dagli elementi del feed RSS come specificato nella sezione Body ( `body` ) degli input dell'azione ( `inputs` ).

## <a name="sample-workflow-definition"></a>Definizione del flusso di lavoro di esempio

Prima di eseguire lo script di esempio, è necessario innanzitutto creare una [definizione di flusso di lavoro](#prerequisites)di esempio.

1. Creare un file JSON `testDefinition.json` nel computer. 

1. Copiare il contenuto seguente nel file JSON: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Aggiornare i valori segnaposto con le informazioni personali:

    1. Sostituire l'indirizzo di posta elettronica segnaposto ( `"To": "test@example.com"` ). È necessario usare un indirizzo di posta elettronica compatibile con i connettori delle app per la logica. Per ulteriori informazioni, vedere i [prerequisiti](#prerequisites).

    1. Sostituire i dettagli aggiuntivi del connettore se si usa un altro connettore di posta elettronica rispetto al connettore Office 365 Outlook.

    1. Sostituire i valori di sottoscrizione segnaposto ( `00000000-0000-0000-0000-000000000000` ) per gli identificatori di connessione ( `connectionId` e `id` ) nel parametro connections ( `$connections` ) con i propri valori di sottoscrizione.

1. Salvare le modifiche.

## <a name="sample-script"></a>Script di esempio

> [!NOTE]
> Questo esempio è scritto per la `bash` Shell. Se si vuole eseguire questo esempio in un'altra shell, ad esempio Windows PowerShell o il prompt dei comandi, potrebbe essere necessario apportare modifiche allo script.

Prima di eseguire questo script di esempio, eseguire questo comando per connettersi ad Azure:

```azurecli-interactive

az login

```

Passare quindi alla directory in cui è stata creata la definizione del flusso di lavoro. Ad esempio, se il file JSON di definizione del flusso di lavoro è stato creato sul desktop:

```azurecli

cd ~/Desktop

```

Eseguire quindi questo script per creare un'app per la logica. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Al termine dell'uso dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le relative risorse nidificate, inclusa l'app per la logica.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script di esempio usa i comandi seguenti per creare un nuovo gruppo di risorse e un'app per la logica.

| Comando | Note |
| ------- | ----- |
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Crea un gruppo di risorse in cui sono archiviate le risorse dell'app per la logica. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create) | Crea un'app per la logica in base al flusso di lavoro definito nel parametro `--definition` . |
| [`az group delete`](/cli/azure/vm/extension?view=azure-cli-latest) | Elimina un gruppo di risorse e tutte le relative risorse nidificate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/?view=azure-cli-latest).

Per altri esempi di script dell'interfaccia della riga di comando di App per la logica, vedere [Esplora gli esempi di codice](/samples/browse/?products=azure-logic-apps).
