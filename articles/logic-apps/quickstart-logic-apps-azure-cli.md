---
title: Creare e gestire app per la logica con l'interfaccia della riga di comando di Azure
description: Usare l'interfaccia della riga di comando di Azure per creare un'app per la logica, quindi gestire tale app usando operazioni quali list, show (get), update e delete.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperfq2
ms.date: 11/23/2020
ms.openlocfilehash: f5b8497772a14e9613977c9cdc22025e8e58b92c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95509468"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Avvio rapido: Creare e gestire app per la logica usando l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido illustra come creare e gestire app per la logica usando l'[estensione app per la logica dell'interfaccia della riga di comando di Azure](/cli/azure/ext/logic/logic) (`az logic`). Dalla riga di comando è possibile creare un'app per la logica usando il file JSON per la definizione flusso di lavoro di un'app per la logica. È quindi possibile gestire l'app per la logica eseguendo operazioni come `list`, `show` (`get`), `update` e `delete` dalla riga di comando.

> [!WARNING]
> L'estensione app per la logica dell'interfaccia della riga di comando di Azure è attualmente *sperimentale* e non è *coperta dall'assistenza clienti*. Usare questa estensione dell'interfaccia della riga di comando con cautela, soprattutto se si sceglie di usare l'estensione negli ambienti di produzione.

Se non si ha familiarità con App per la logica, è anche possibile ottenere le istruzioni su come creare la prima app per la logica [tramite il portale di Azure](quickstart-create-first-logic-app-workflow.md), [in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) e [in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) installata nel computer locale.
* L'[estensione app per la logica dell'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-list) installata nel computer. Per installare questa estensione, usare il comando seguente: `az extension add --name logic`
* Un [gruppo di risorse di Azure](#example---create-resource-group) in cui creare un'app per la logica.

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Prima di iniziare, convalidare l'ambiente:

* Accedere al portale di Azure e verificare che la sottoscrizione sia attiva eseguendo `az login`.
* Controllare la versione dell'interfaccia della riga di comando di Azure in una finestra terminale o di comando eseguendo `az --version`. Per la versione più recente, vedere le [ultime note sulla versione](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se non si dispone della versione più recente, aggiornare l'installazione seguendo la [guida all'installazione per il sistema operativo o la piattaforma in uso](/cli/azure/install-azure-cli).

### <a name="example---create-resource-group"></a>Esempio: creare il gruppo di risorse

Se non si dispone già di un gruppo di risorse per l'app per la logica, crearlo con il comando `az group create`. Il comando seguente ad esempio crea un gruppo di risorse denominato `testResourceGroup` nella posizione `westus`.

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

L'output mostra `provisioningState` come `Succeeded` quando il gruppo di risorse viene creato correttamente:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Definizione flusso di lavoro

Prima di [creare una nuova app per la logica](#create-logic-apps-from-cli) o di [aggiornare un'app per la logica esistente](#update-logic-apps-from-cli) usando l'interfaccia della riga di comando di Azure, è necessaria una definizione flusso di lavoro per l'app per la logica. Nel portale di Azure è possibile visualizzare la definizione flusso di lavoro sottostante dell'app per la logica in formato JSON passando dalla visualizzazione **Finestra di progettazione** a **Visualizzazione codice**.

Quando si eseguono i comandi per creare o aggiornare l'app per la logica, la definizione flusso di lavoro viene caricata come parametro obbligatorio (`--definition`). È necessario creare la definizione flusso di lavoro come file JSON che segue lo [schema del linguaggio di definizione flusso di lavoro](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Creare app per la logica dall'interfaccia della riga di comando

È possibile creare il flusso di lavoro di un'app per la logica dall'interfaccia della riga di comando di Azure usando il comando [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) con un file JSON per la definizione.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Il comando deve includere i [parametri obbligatori](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) seguenti:

| Parametro | Valore | Descrizione |
| --------- | ----- | ----------- |
| Definizione flusso di lavoro | `--definition` | File JSON con la [definizione flusso di lavoro](#workflow-definition) dell'app per la logica. |
| Location | `--location -l` | Area di Azure in cui si trova l'app per la logica. |
| Nome | `--name -n` | Nome dell'app per la logica. Il nome può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`()`) e punti (`.`). Il nome inoltre deve essere univoco tra le aree. |
| Nome del gruppo di risorse | `--resource-group -g` | [Gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) in cui si intende creare un'app per la logica. Prima di iniziare, [creare un gruppo di risorse](#example---create-resource-group) se non ne è già presente uno per l'app per la logica. |

È anche possibile includere altri [parametri facoltativi](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) per configurare i controlli di accesso, gli endpoint, l'account di integrazione, l'ambiente del servizio di integrazione, lo stato e i tag delle risorse dell'app per la logica.

### <a name="example---create-logic-app"></a>Esempio: creare l'app per la logica

In questo esempio viene creato un flusso di lavoro denominato `testLogicApp` nel gruppo di risorse `testResourceGroup` nella posizione `westus`. Il file JSON `testDefinition.json` contiene la definizione flusso di lavoro.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Quando il flusso di lavoro viene creato correttamente, l'interfaccia della riga di comando mostra il codice JSON della nuova definizione flusso di lavoro. Se la creazione del flusso di lavoro non riesce, vedere l'[elenco dei possibili errori](#errors).

## <a name="update-logic-apps-from-cli"></a>Aggiornare app per la logica dall'interfaccia della riga di comando

È anche possibile aggiornare il flusso di lavoro di un'app per la logica dall'interfaccia della riga di comando di Azure usando il comando [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create).

Il comando deve includere gli stessi [parametri obbligatori](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) usati quando si [crea un'app per la logica](#create-logic-apps-from-cli). È anche possibile aggiungere gli stessi [parametri facoltativi](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) usati per la creazione di un'app per la logica.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Esempio: aggiornare l'app per la logica

In questo esempio il [flusso di lavoro di esempio creato nella sezione precedente](#example---create-logic-app) viene aggiornato in modo da usare un file di definizione JSON diverso, `newTestDefinition.json`, e aggiungere due tag di risorsa `testTag1` e `testTag2` con valori di descrizione.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Quando il flusso di lavoro viene aggiornato correttamente, l'interfaccia della riga di comando mostra la definizione flusso di lavoro aggiornata dell'app per la logica. Se l'aggiornamento non riesce, vedere l'[elenco dei possibili errori](#errors).

## <a name="delete-logic-apps-from-cli"></a>Eliminare app per la logica dall'interfaccia della riga di comando

È possibile eliminare il flusso di lavoro di un'app per la logica dall'interfaccia della riga di comando di Azure usando il comando [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete).

Il comando deve includere i [parametri obbligatori](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters) seguenti:

| Parametro | Valore | Descrizione |
| --------- | ----- | ----------- |
| Nome | `--name -n` | Nome dell'app per la logica. |
| Nome del gruppo di risorse | `-resource-group -g` | Gruppo di risorse in cui si trova l'app per la logica. |

È anche possibile includere un [parametro facoltativo](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) per ignorare le richieste di conferma, `--yes -y`.

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

L'interfaccia della riga di comando chiede quindi di confermare l'eliminazione dell'app per la logica. È possibile ignorare la richiesta di conferma usando con il comando il parametro facoltativo `--yes -y`.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

È possibile confermare l'eliminazione di un'app per la logica [elencando le app per la logica nell'interfaccia della riga di comando](#list-logic-apps-in-cli) oppure visualizzandole nel portale di Azure.

### <a name="example---delete-logic-app"></a>Esempio: eliminare l'app per la logica

In questo esempio viene eliminato il [flusso di lavoro di esempio creato in una sezione precedente](#example---create-logic-app).

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Dopo aver risposto alla richiesta di conferma con `y`, l'app per la logica viene eliminata.

## <a name="show-logic-apps-in-cli"></a>Mostrare le app per la logica nell'interfaccia della riga di comando

È possibile ottenere il flusso di lavoro di un'app per la logica specifica usando il comando [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show).

```azurecli

az logic workflow show --name
                       --resource-group

```

Il comando deve includere i [parametri obbligatori](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters) seguenti:

| Parametro | Valore | Descrizione |
| --------- | ----- | ----------- |
| Nome | `--name -n` | Nome dell'app per la logica. |
| Nome del gruppo di risorse | `--resource-group -g` | Nome del gruppo di risorse in cui si trova l'app per la logica. |

### <a name="example---get-logic-app"></a>Esempio: ottenere l'app per la logica

In questo esempio viene restituita l'app per la logica `testLogicApp` nel gruppo di risorse `testResourceGroup` con log completi per il debug.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Elencare le app per la logica nell'interfaccia della riga di comando

È possibile elencare le app per la logica in base alla sottoscrizione usando il comando [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list). Questo comando restituisce il codice JSON per i flussi di lavoro delle app per la logica.

È possibile filtrare i risultati in base ai [parametri facoltativi](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters) seguenti:

| Parametro | Valore | Descrizione |
| --------- | ----- | ----------- |
| Nome del gruppo di risorse | `--resource-group -g` | Nome del gruppo di risorse in base al quale filtrare i risultati. |
| Numero di elementi | `--top` | Numero di elementi inclusi nei risultati. |
| Filtro | `--filter` | Tipo di filtro che si sta usando nell'elenco. È possibile filtrare in base allo stato (`State`), al trigger (`Trigger`) e all'identificatore della risorsa a cui si fa riferimento (`ReferencedResourceId`). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Esempio: elencare le app per la logica

In questo esempio vengono restituiti tutti i flussi di lavoro abilitati nel gruppo di risorse `testResourceGroup` in un formato di tabella ASCII.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Errors

L'errore seguente indica che l'estensione app per la logica dell'interfaccia della riga di comando di Azure non è installata. Seguire la procedura descritta nella sezione relativa ai prerequisiti per [installare l'estensione app per la logica](#prerequisites) nel computer.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

L'errore seguente potrebbe indicare che il percorso di file per il caricamento della definizione flusso di lavoro non è corretto.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Parametri globali

Con i comandi `az logic` è possibile usare i parametri facoltativi globali dell'interfaccia della riga di comando di Azure seguenti:

| Parametro | Valore | Descrizione |
| --------- | ----- | ----------- |
| Formato di output | `--output -o` | Cambia il [formato di output](/cli/azure/format-output-azure-cli) rispetto al formato JSON predefinito. |
| Mostra solo errori | `--only-show-errors` | Non visualizza gli avvisi e mostra solo gli errori. |
| Dettagliato | `--verbose` | Mostra log dettagliati. |
| Debug | `--debug` | Mostra tutti i log di debug. |
| Messaggio di aiuto | `--help -h` | Visualizza la finestra della Guida. |
| Query | `--query` | Imposta una stringa di query JMESPath per l'output JSON. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/).

Per altri esempi di script dell'interfaccia della riga di comando di App per la logica, vedere [Esplora gli esempi di codice](/samples/browse/?products=azure-logic-apps).

Successivamente, è possibile creare un'app per la logica di esempio tramite l'interfaccia della riga di comando di Azure usando una definizione del flusso di lavoro e uno script di esempio.

> [!div class="nextstepaction"]
> [Creare un'app per la logica con uno script di esempio](sample-logic-apps-cli-script.md).
