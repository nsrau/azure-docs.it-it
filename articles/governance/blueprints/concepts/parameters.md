---
title: Usare i parametri per creare progetti dinamici
description: Informazioni sui parametri statici e dinamici e su come usarli per creare progetti dinamici.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: ac7b662bc9ef4f3ae675c4cbde18e159383d3d8e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767043"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Creazione di progetti dinamici tramite parametri

Un progetto completamente definito con vari artefatti, ad esempio gruppi di risorse, modelli di Resource Manager, criteri o assegnazioni di ruolo, consente la creazione rapida e coerente di oggetti all'interno di Azure. Per abilitare l'uso flessibile di questi contenitori e schemi progettuali riutilizzabili, Azure Blueprint supporta i parametri. Il parametro offre flessibilità nella modifica delle proprietà sugli elementi distribuiti dal progetto, sia durante la definizione che durante l'assegnazione.

Un esempio semplice è l'artefatto gruppo di risorse. Quando si crea un gruppo di risorse, occorre fornire due valori obbligatori: nome e località. Quando si aggiunge un gruppo di risorse al progetto, se questi parametri non esistono occorre definire il nome e la località per ogni uso del progetto. Questa ripetizione potrebbe portare alla creazione di artefatti nello stesso gruppo di risorse a ogni uso del progetto. Le risorse all'interno di tale gruppo potrebbero essere duplicate e provocare un conflitto.

> [!NOTE]
> Non è un problema se due diversi progetti diversi includono un gruppo di risorse con lo stesso nome.
> Se un gruppo di risorse incluso in un progetto già esiste, il progetto continua a creare gli artefatti correlati in tale gruppo di risorse. Questo potrebbe causare un conflitto, perché all'interno di una sottoscrizione non possono esistere due risorse con lo stesso nome e tipo di risorsa.

La soluzione a questo problema è rappresentata dai parametri. I progetti consentono di definire il valore per ogni proprietà dell'artefatto durante l'assegnazione a una sottoscrizione. Questo parametro rende possibile riutilizzare un progetto che crea un gruppo di risorse e altre risorse all'interno di una singola sottoscrizione senza conflitti.

## <a name="blueprint-parameters"></a>Parametri di progetto

I parametri possono essere creati nel progetto stesso tramite l'API REST. Questi parametri sono diversi da quelli presenti in ciascuno degli elementi supportati. Quando nel progetto viene creato un parametro, questo può essere usato dagli artefatti in tale progetto. Un esempio può essere il prefisso per la denominazione del gruppo di risorse. L'artefatto può usare il parametro del progetto per crearne uno "principalmente dinamico". Poiché il parametro può essere definito anche durante l'assegnazione, questo modello consente una coerenza che può essere conforme alle regole di denominazione. Per la procedura, vedere [Impostazione di parametri statici - Parametro a livello di progetto](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Uso dei parametri secureString e secureObject

Anche se gli _artefatti_ di un modello di Resource Manager supportano parametri di tipo **secureString** e **secureObject**, Azure Blueprint richiede che ognuno di essi sia connesso ad Azure Key Vault.
Questa misura di sicurezza impedisce la rischiosa prassi di archiviare i segreti insieme al progetto e incoraggia l'impiego di modelli sicuri. Progetti di Azure supporta questa misura di protezione, rilevamento l'inclusione dei parametri sicuro in un _artefatto_ del modello di Resource Manager. Il servizio richiede quindi, durante l'assegnazione, le seguenti proprietà di Key Vault per ogni parametro sicuro rilevato:

- ID risorsa dell'insieme di credenziali delle chiavi
- Nome del segreto dell'insieme di credenziali delle chiavi
- Versione del segreto dell'insieme di credenziali delle chiavi

Se l'assegnazione di progetto usa un **identità assegnata dal sistema gestito**, il riferimento Key Vault _deve_ esiste nella stessa sottoscrizione la definizione di progetto viene assegnata a.

Se l'assegnazione di progetto usa un **identità assegnata dall'utente gestito**, il riferimento Key Vault _potrebbe_ esistenti in una sottoscrizione centralizzata. L'identità gestita deve disporre dei diritti appropriati in Key Vault prima dell'assegnazione progetto.

In entrambi i casi, è necessario disporre di Key Vault **abilitare l'accesso ad Azure Resource Manager per la distribuzione dei modelli** configurato nel **criteri di accesso** pagina. Per istruzioni su come abilitare questa funzionalità, vedere [Key Vault - Abilitare la distribuzione di modelli](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Per altre informazioni sull'insieme di credenziali di Azure, vedere [Panoramica di Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipi di parametro

### <a name="static-parameters"></a>Parametri statici

Un valore di parametro definito nella definizione di un progetto viene chiamato **parametro statico**, perché l'artefatto sarà distribuito con tale valore statico a ogni uso del progetto. Nell'esempio del gruppo di risorse, anche se questo non ha senso per il nome, potrebbe averlo per la località. Ogni assegnazione del progetto creerebbe infatti il gruppo di risorse, comunque sia denominato, nella stessa località. Questa flessibilità consente di essere selettivi nella definizione di ciò che è obbligatorio e di ciò che può essere modificato durante l'assegnazione.

#### <a name="setting-static-parameters-in-the-portal"></a>Impostazione dei parametri statici nel portale

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Definizioni di progetto** nella pagina a sinistra.

1. Fare clic su un progetto esistente e quindi fare clic su **linee guida per la modifica** oppure fare clic su **+ crea blueprint** e immettere le informazioni sul **nozioni di base** scheda.

1. Fare clic su **Avanti: Artefatti** oppure fare clic sulla scheda **Artefatti**.

1. Gli artefatti aggiunti al progetto che hanno opzioni dei parametri mostrano l'indicazione **X di Y parametri popolati** nella colonna **Parametri**. Fare clic sulla riga dell'artefatto per modificarne i parametri.

   ![Parametri del progetto in una definizione di progetto](../media/parameters/parameter-column.png)

1. La pagina **Modifica artefatto** mostra le opzioni del valore appropriate per l'artefatto selezionato. Ogni parametro dell'artefatto ha un titolo, una casella del valore e una casella di controllo. Deselezionare la casella di controllo per renderlo un **parametro statico**. Nell'esempio seguente, solo _Località_ è un **parametro statico** perché la casella è deselezionata, mentre è selezionata in _Nome gruppo di risorse_.

   ![Parametri statici del progetto in un elemento di progetto](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Impostazione dei parametri statici dall'API REST

In ogni URI dell'API REST vengono usate variabili che è necessario sostituire con i propri valori:

- `{YourMG}`: sostituire con il nome del gruppo di gestione
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

##### <a name="blueprint-level-parameter"></a>Parametro a livello di progetto

Quando si crea un progetto tramite l'API REST, è possibile creare [parametri del progetto](#blueprint-parameters). A questo scopo, usare il formato del corpo e l'URI dell'API REST seguenti:

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Dopo aver creato un parametro a livello di progetto, è possibile usarlo sugli artefatti aggiunti al progetto.
L'esempio di API REST seguente crea un artefatto assegnazione di ruolo nel progetto e usa il parametro a livello di progetto.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

In questo esempio, la proprietà **principalIds** usa il parametro **owners** a livello di progetto specificando un valore `[parameters('owners')]`. L'impostazione di un parametro in un artefatto usando un parametro a livello di progetto è un altro esempio di **parametro statico**. Il parametro a livello di progetto non può essere impostato durante l'assegnazione del progetto e avrà lo stesso valore per ogni assegnazione.

##### <a name="artifact-level-parameter"></a>Parametro a livello di artefatto

La creazione di **parametri statici** su un artefatto è simile, ma accetta un valore diretto anziché usare la funzione `parameters()`. L'esempio seguente crea due parametri statici, **tagName** e **tagValue**. Il valore di ognuno viene fornito direttamente e non usa una chiamata di funzione.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parametri dinamici

L'opposto di un parametro statico è un **parametro dinamico**. Questo parametro non viene definito nel progetto, bensì durante ogni assegnazione del progetto. Nell'esempio del gruppo di risorse, l'uso di un **parametro dinamico** ha senso per il nome del gruppo di risorse. Questo fornisce un nome diverso per ogni assegnazione del progetto. Per un elenco di funzioni di progetto, vedere la [blueprint funzioni](../reference/blueprint-functions.md) riferimento.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Impostazione dei parametri dinamici nel portale

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Definizioni di progetto** nella pagina a sinistra.

1. Fare clic con il tasto destro del mouse sul progetto che si desidera assegnare. Selezionare **blueprint assegnare** o fa clic sul progetto che si desidera assegnare e quindi il **Assign blueprint** pulsante.

1. Nel **Assign blueprint** pagina, trovare il **parametri dell'elemento** sezione. Ogni artefatto con almeno un **parametro dinamico** mostra l'artefatto e le opzioni di configurazione. Immettere i valori richiesti per i parametri prima di assegnare il progetto. Nell'esempio seguente, _Nome_ è un **parametro dinamico** che deve essere definito per completare l'assegnazione del progetto.

   ![Parametro dinamico del progetto durante l'assegnazione progetto](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Impostazione dei parametri dinamici dall'API REST

L'impostazione dei **parametri dinamici** durante l'assegnazione si esegue inserendo direttamente il valore. Invece di usare una funzione, ad esempio [parameters()](../reference/blueprint-functions.md#parameters), il valore fornito è una stringa appropriata. Gli artefatti per un gruppo di risorse sono definiti con un nome di modello e le proprietà **name** e **location**. Tutti gli altri parametri per ogni artefatto incluso sono definiti in **parameters** con una coppia di chiavi **\<name\>** e **value**. Se il progetto è configurato per un parametro dinamico non fornito durante l'assegnazione, l'assegnazione avrà esito negativo.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare l'elenco delle [blueprint funzioni](../reference/blueprint-functions.md).
- Informazioni sul [ciclo di vita del progetto](lifecycle.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).