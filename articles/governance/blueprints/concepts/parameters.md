---
title: Creazione di progetti dinamici tramite parametri in Azure Blueprint
description: Informazioni sui parametri statici e dinamici e su come usarli per creare progetti dinamici.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f6485b01c391ba336799ceb35ee67402b3603585
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093752"
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

L'insieme di credenziali delle chiavi a cui si fa riferimento deve esistere nella stessa sottoscrizione a cui viene assegnato il progetto.
Inoltre, deve avere l'opzione **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli** configurata nella pagina **Criteri di accesso** di Key Vault. Per istruzioni su come abilitare questa funzionalità, vedere [Key Vault - Abilitare la distribuzione di modelli](../../../managed-applications/key-vault-access.md#enable-template-deployment). Per altre informazioni sull'insieme di credenziali di Azure, vedere [Panoramica di Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipi di parametro

### <a name="static-parameters"></a>Parametri statici

Un valore di parametro definito nella definizione di un progetto viene chiamato **parametro statico**, perché l'artefatto sarà distribuito con tale valore statico a ogni uso del progetto. Nell'esempio del gruppo di risorse, anche se questo non ha senso per il nome, potrebbe averlo per la località. Ogni assegnazione del progetto creerebbe infatti il gruppo di risorse, comunque sia denominato, nella stessa località. Questa flessibilità consente di essere selettivi nella definizione di ciò che è obbligatorio e di ciò che può essere modificato durante l'assegnazione.

#### <a name="setting-static-parameters-in-the-portal"></a>Impostazione dei parametri statici nel portale

1. Fare clic su **Tutti i servizi** e quindi cercare e selezionare **Criteri** nel riquadro sinistro. Nella pagina **Criteri** fare clic su **Progetti**.

1. Selezionare **Definizioni di progetto** nella pagina a sinistra.

1. Fare clic su un progetto esistente e quindi fare clic su **Modifica progetto** OPPURE fare clic su **+ Crea progetto** e immettere le informazioni nella scheda **Generale**.

1. Fare clic su **Avanti: Artefatti** oppure fare clic sulla scheda **Artefatti**.

1. Gli artefatti aggiunti al progetto che hanno opzioni dei parametri mostrano l'indicazione **X di Y parametri popolati** nella colonna **Parametri**. Fare clic sulla riga dell'artefatto per modificarne i parametri.

   ![Parametri di progetto](../media/parameters/parameter-column.png)

1. La pagina **Modifica artefatto** mostra le opzioni del valore appropriate per l'artefatto selezionato. Ogni parametro dell'artefatto ha un titolo, una casella del valore e una casella di controllo. Deselezionare la casella di controllo per renderlo un **parametro statico**. Nell'esempio seguente, solo _Località_ è un **parametro statico** perché la casella è deselezionata, mentre è selezionata in _Nome gruppo di risorse_.

   ![Parametri di progetto statici](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Impostazione dei parametri statici dall'API REST

In ogni URI dell'API REST vengono usate variabili che è necessario sostituire con i propri valori:

- `{YourMG}`: sostituire con il nome del gruppo di gestione
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

##### <a name="blueprint-level-parameter"></a>Parametro a livello di progetto

Quando si crea un progetto tramite l'API REST, è possibile creare [parametri del progetto](#blueprint-parameters). A questo scopo, usare il formato del corpo e l'URI dell'API REST seguenti:

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
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
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
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
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
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

L'opposto di un parametro statico è un **parametro dinamico**. Questo parametro non viene definito nel progetto, bensì durante ogni assegnazione del progetto. Nell'esempio del gruppo di risorse, l'uso di un **parametro dinamico** ha senso per il nome del gruppo di risorse. Questo fornisce un nome diverso per ogni assegnazione del progetto.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Impostazione dei parametri dinamici nel portale

1. Fare clic su **Tutti i servizi** e quindi cercare e selezionare **Criteri** nel riquadro sinistro. Nella pagina **Criteri** fare clic su **Progetti**.

1. Selezionare **Definizioni di progetto** nella pagina a sinistra.

1. Fare clic con il tasto destro del mouse sul progetto che si desidera assegnare. Selezionare **Assegna progetto** OPPURE fare clic sul progetto da assegnare e quindi fare clic sul pulsante **Assegna progetto**.

1. Nella pagina **Assegna progetto** trovare la sezione **Parametri artefatto**. Ogni artefatto con almeno un **parametro dinamico** mostra l'artefatto e le opzioni di configurazione. Immettere i valori richiesti per i parametri prima di assegnare il progetto. Nell'esempio seguente, _Nome_ è un **parametro dinamico** che deve essere definito per completare l'assegnazione del progetto.

   ![Parametro dinamico del progetto](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Impostazione dei parametri dinamici dall'API REST

L'impostazione dei **parametri dinamici** durante l'assegnazione si esegue inserendo direttamente il valore.
Invece di usare una funzione, ad esempio `parameters()`, il valore fornito è una stringa appropriata.
Gli artefatti per un gruppo di risorse sono definiti con un nome di modello e le proprietà **name** e **location**. Tutti gli altri parametri per ogni artefatto incluso sono definiti in **parameters** con una coppia di chiavi **\<name\>** e **value**. Se il progetto è configurato per un parametro dinamico non fornito durante l'assegnazione, l'assegnazione avrà esito negativo.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
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

- Informazioni sul [ciclo di vita del progetto](lifecycle.md)
- Informazioni sulla personalizzazione della [sequenziazione di progetto](sequencing-order.md)
- Scoprire come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md)
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)