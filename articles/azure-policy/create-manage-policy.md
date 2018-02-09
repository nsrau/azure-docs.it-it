---
title: "Usare Criteri di Azure per creare e gestire i criteri per applicare la conformità aziendale | Microsoft Docs"
description: "Usare Criteri di Azure per applicare gli standard, soddisfare i requisiti di conformità e controllo normativi, controllare i costi, mantenere la coerenza di sicurezza e prestazioni e imporre principi di progettazione all'azienda nel suo complesso."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/18/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: a3d47abcbf41133b9bc7194fd97f9b66a70003ff
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Creare e gestire i criteri per applicare la conformità

Comprendere come creare e gestire i criteri in Azure è importante per assicurare la conformità agli standard e ai contratti di servizio aziendali. Questa esercitazione illustra come usare Criteri di Azure per eseguire alcune delle attività più comuni correlate alla creazione, all'assegnazione e alla gestione dei criteri dell'organizzazione, ad esempio:

> [!div class="checklist"]
> * Assegnare un criterio per applicare una condizione alle risorse che verranno create in futuro
> * Creare e assegnare una definizione di iniziativa per tenere traccia della conformità di più risorse
> * Risolvere una risorsa non conforme o non consentita
> * Implementare un nuovo criterio all'interno dell'organizzazione

Se si vuole assegnare un criterio per identificare lo stato di conformità corrente delle risorse esistenti, gli articoli introduttivi illustrano come eseguire questa operazione. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="assign-a-policy"></a>Assegnare i criteri

Il primo passaggio per applicare la conformità a Criteri di Azure consiste nell'assegnare una definizione dei criteri, che consente di definire le condizioni in base alle quali un criterio viene applicato e l'azione da intraprendere. In questo esempio viene assegnata una definizione dei criteri predefinita, chiamata *Richiedere SQL Server versione 12.0*, per applicare la condizione in base alla quale tutti i database di SQL Server devono essere conformi alla versione 12.0.

1. Avviare il servizio Criteri di Azure nel portale di Azure cercando e selezionando **Policy**(Criterio) nel riquadro a sinistra.

   ![Ricerca di criteri](media/assign-policy-definition/search-policy.png)

2. Selezionare **Assegnazioni**nel riquadro a sinistra della pagina Criteri di Azure. Un'assegnazione è un criterio che è stato assegnato per l'implementazione in un ambito specifico.
3. Selezionare **Assegna criterio** nella parte superiore del riquadro **Assegnazioni**.

   ![Assegnare una definizione di criteri](media/create-manage-policy/select-assign-policy.png)

4. Nella pagina **Assegna criterio** fare clic sul ![pulsante per la definizione dei criteri](media/assign-policy-definition/definitions-button.png) accanto al campo **Policy** (Criterio) per aprire l'elenco delle definizioni disponibili. È possibile filtrare il **Tipo** della definizione del criterio su *BuiltIn* per visualizzarli tutti e leggere le rispettive descrizioni.

   ![Aprire le definizioni di criteri disponibili](media/create-manage-policy/open-policy-definitions.png)

5. Selezionare **Richiedere SQL Server versione 12.0**. Se non si riesce a trovarlo subito, digitare **Richiedere SQL Server versione 12.0** nella casella di ricerca e quindi premere INVIO.

   ![Individuare un criterio](media/create-manage-policy/select-available-definition.png)

6. Il **Nome** visualizzato viene popolato automaticamente, ma è possibile modificarlo. In questo esempio usare *Richiedere SQL Server versione 12.0*. È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su come questa assegnazione di criteri garantisce che tutte le istanze di SQL Server create in questo ambiente siano nella versione 12.0.

7. Modificare il piano tariffario impostando **Standard** per garantire che i criteri vengano applicati alle risorse esistenti.

   Per Criteri di Azure esistono due piani tariffari, ovvero *Gratuito* e *Standard*. Con il piano Gratuito è possibile applicare i criteri solo alle risorse future, mentre con quello Standard è possibile applicarli anche a risorse esistenti per ottenere una migliore comprensione dello stato di conformità. Poiché Criteri di Azure è disponibile in anteprima, non è ancora stato rilasciato un modello di determinazione dei prezzi, quindi non si riceverà alcuna fattura selezionando *Standard*. Per altre informazioni sui prezzi, vedere [Prezzi di Criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy).

8. Selezionare lo **scope**: l'abbonamento (o gruppo di risorse) registrato in precedenza. L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri e può variare da una sottoscrizione a gruppi di risorse.

   Questo esempio usa la sottoscrizione **Azure Analytics Capacity Dev**. ma le opzioni disponibili sono diverse.

10. Selezionare **Assegna**.

## <a name="implement-a-new-custom-policy"></a>Implementare un nuovo criterio personalizzato

Dopo avere assegnato una definizione predefinita del criterio, è possibile eseguire altre operazioni con Criteri di Azure. Creare quindi un nuovo criterio personalizzato per ridurre i costi assicurando che le macchine virtuali create nell'ambiente non siano della serie G. In questo modo, ogni volta che un utente dell'organizzazione prova a creare una macchina virtuale della serie G, la richiesta viene negata.

1. Selezionare **Definizione** in **Creazione** nel riquadro a sinistra.

   ![Definizione in Creazione](media/create-manage-policy/definition-under-authoring.png)

2. Selezionare **+ 	Definizione criteri**.
3. Immettere le informazioni seguenti:

   - Il nome della definizione del criterio: *Require VM SKUs smaller than the G series* (Richiedere SKU di VM inferiori rispetto alla serie G).
   - La descrizione della finalità della definizione del criterio: questa definizione di criterio fa sì che tutte le macchine virtuali create in questo ambito abbiamo SKU inferiori rispetto alla serie G per ridurre i costi.
   - La sottoscrizione in cui si trova la definizione del criterio. In questo caso la definizione del criterio si trova in **Advisor Analytics Capacity Dev**, ma le opzioni disponibili sono diverse.
   - Scegliere una delle opzioni esistenti o creare una nuova categoria per questa definizione del criterio.
   - Copiare il codice JSON seguente e quindi aggiornarlo per adattarlo alle proprie esigenze con:
      - Parametri dei criteri.
      - Le regole/condizioni del criterio, in questo caso: dimensione dello SKU della VM uguale alla serie G.
      - L'effetto del criterio, in questo caso: **Nega**.

    Di seguito viene mostrato l'aspetto del codice JSON. Incollare il codice rivisto nel portale di Azure.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
    ```

    Il valore della *proprietà field* nella regola del criterio deve essere uno dei seguenti: Name, Type, Location, Tags o un alias. Ad esempio, `"Microsoft.Compute/VirtualMachines/Size"`.

    Per visualizzare altri esempi di codice JSON, leggere l'articolo [Modelli per Criteri di Azure](json-samples.md).

4. Selezionare **Salva**.

## <a name="create-a-policy-definition-with-rest-api"></a>Creare una definizione di criteri con l'API REST

È possibile creare un criterio con l'API REST per le definizioni dei criteri. L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti.
Per creare una definizione criteri, usare l'esempio seguente:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Includere un corpo della richiesta in modo simile all'esempio seguente:

```
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Creare una definizione criteri con PowerShell

Prima di passare all'esempio di PowerShell, verificare di avere installato la versione più recente di Azure PowerShell. I parametri dei criteri sono stati aggiunti nella versione 3.6.0. Se si ha una versione precedente, gli esempi restituiscono un errore che indica che non è possibile trovare il parametro.

È possibile creare una definizione di criterio usando il cmdlet `New-AzureRmPolicyDefinition`.

Per creare una definizione dei criteri da un file passare il percorso al file. Per un file esterno, usare l'esempio seguente:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Per un file locale, usare l'esempio seguente:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Per creare una definizione criteri con una regola inline, usare l'esempio seguente:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

L'output viene archiviato in un oggetto `$definition` che viene usato durante l'assegnazione del criterio.
L'esempio seguente crea una definizione del criterio che include i parametri:

```
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Visualizzare le definizioni dei criteri

Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```
Get-AzureRmPolicyDefinition
```

Restituisce tutte le definizioni dei criteri disponibili, inclusi i criteri predefiniti. Tutti i criteri vengono restituiti nel formato seguente:

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Creare una definizione criteri con l'interfaccia della riga di comando di Azure

È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure con il comando di definizione dei criteri.
Per creare una definizione criteri con una regola inline, usare l'esempio seguente:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Visualizzare le definizioni dei criteri

Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```
az policy definition list
```

Restituisce tutte le definizioni dei criteri disponibili, inclusi i criteri predefiniti. Tutti i criteri vengono restituiti nel formato seguente:

```
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Creare e assegnare una definizione di iniziativa

Una definizione di iniziativa consente di raggruppare più definizioni di criteri per raggiungere un obiettivo globale. Una definizione di iniziativa viene creata per garantire che le risorse incluse nell'ambito siano conformi alle definizioni dei criteri che costituiscono la definizione dell'iniziativa.  Vedere l'articolo [Azure Policy overview](./azure-policy-introduction.md) (Cenni preliminari su Criteri di Azure) per altre informazioni sulle definizioni di iniziative.

### <a name="create-an-initiative-definition"></a>Creare una definizione di iniziativa

1. Selezionare **Definizioni** in **Creazione** nel riquadro a sinistra.

   ![Selezionare le definizioni](media/create-manage-policy/select-definitions.png)

2. Selezionare **Initiative Definition** (Definizione iniziativa) nella parte superiore della pagine, per aprire il modulo **Initiative Definition** (Definizione iniziativa).
3. Inserire il nome e la descrizione dell'iniziativa.

   In questo esempio si vuole garantire che le risorse siano conformi alle definizioni dei criteri relativi alla protezione. Il nome dell'iniziativa potrebbe quindi essere **Get Secure** e la descrizione potrebbe essere **This initiative has been created to handle all policy definitions associated with securing resources**.

   ![Definizione di iniziativa](media/create-manage-policy/initiative-definition.png)

4. Scorrere l'elenco delle **definizioni disponibili** e selezionare una o più definizioni di criteri da aggiungere all'iniziativa. Per l'iniziativa **Get secure**, **aggiungere** le definizioni seguenti di criteri predefiniti:
   - Richiedere SQL Server versione 12.0
   - Monitor unprotected web applications in Security Center (Monitorare le applicazioni Web non protette nel Centro sicurezza).
   - Monitor permissive network across in Security Center (Monitorare la rete permissiva nel Centro sicurezza).
   - Monitor possible app Whitelisting in Security Center (Monitorare potenziali elenchi di app consentite nel Centro sicurezza).
   - Monitor unencrypted VM Disks in Security Center (Monitorare dischi di VM non crittografati nel Centro sicurezza).

   ![Definizioni di iniziativa](media/create-manage-policy/initiative-definition-2.png)

   Dopo aver selezionato le definizioni dei criteri nell'elenco, queste verranno visualizzate in **Policies and parameters** (Criteri e parametri), come mostrato nell'immagine precedente.

5. Usare **Percorso definizione** per selezionare una sottoscrizione in cui archiviare la definizione. Selezionare **Salva**.

### <a name="assign-an-initiative-definition"></a>Assegnare una definizione di iniziativa

1. Passare alla scheda **Definitions** (Definizioni) in **Authoring** (Creazione).
2. Cercare la definizione dell'iniziativa **Assicura protezione** appena creata.
3. Selezionare la definizione di iniziativa, quindi fare clic su **Assign** (Assegna).

   ![Assegnare una definizione](media/create-manage-policy/assign-definition.png)

4. Compilare il modulo **Assegnazione** immettendo le informazioni di esempio seguenti. È possibile usare informazioni personalizzate.
   - Nome: ottenere un'assegnazione sicura
   - Descrizione: questa assegnazione di iniziativa è ottimizzata in modo da applicare questo gruppo di definizioni dei criteri alla sottoscrizione **Azure Advisor Capacity Dev**.
   - Piano tariffario: Standard
   - Ambito a cui si vuole applicare questa assegnazione: **Azure Advisor Capacity Dev** È possibile scegliere una sottoscrizione e un gruppo di risorse personalizzati.

5. Selezionare **Assegna**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Esentare una risorsa non conforme o non consentita tramite l'esclusione

Facendo riferimento all'esempio precedente, dopo aver assegnato la definizione dei criteri per richiedere un'istanza di SQL Server versione 12.0, non viene consentita la creazione di un server SQL con una versione diversa. In questa sezione viene risolto un tentativo negato di creare un server SQL di una versione diversa richiedendo un'esclusione. L'esclusione impedisce essenzialmente l'applicazione dei criteri. Un'esclusione può essere applicata a un gruppo di risorse oppure è possibile limitare l'esclusione a singole risorse.

1. Selezionare **Assegnazioni** nel riquadro a sinistra.
2. Esplorare tutte le assegnazioni dei criteri e aprire l'assegnazione *Richiedere SQL Server versione 12.0*.
3. **Selezionare** un'esclusione per le risorse nel gruppo di risorse in cui si sta provando a creare l'istanza di SQL Server. In questo esempio escludere Microsoft.Sql/servers/databases: *azuremetrictest/testdb* e *azuremetrictest/testdb2*.

   ![Richiesta di esclusione](media/create-manage-policy/request-exclusion.png)

   Per risolvere una risorsa non consentita è anche raggiungere il contatto associato al criterio se è possibile giustificare con certezza la necessità del server SQL server, e modificare direttamente il criterio se è possibile accedervi.

4. Fare clic su **Assegna**.

In questa sezione è stato risolto il rifiuto al tentativo di creare un server SQL con la versione 12.0, richiedendo un'esclusione per le risorse.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare le esercitazioni successive, non eliminare le risorse create in questa guida. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le assegnazioni o definizioni create.

1. Selezionare **Definitions** (Definizioni) o **Assegnazioni** se si vuole eliminare un'assegnazione nel riquadro a sinistra.
2. Cercare la nuova iniziativa o definizione (o assegnazione) di criteri appena creata.
3. Selezionare i puntini di sospensione alla fine della definizione o dell'assegnazione e quindi **Delete Definition** (Elimina definizione) o **Elimina assegnazione**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è eseguito quanto segue:

> [!div class="checklist"]
> * Assegnazione di un criterio per applicare una condizione alle risorse che verranno create in futuro.
> * Creazione e assegnazione di una definizione di iniziativa per tenere traccia della conformità di più risorse.
> * Risoluzione di una risorsa non conforme o non consentita.
> * Implementazione di un nuovo criterio all'interno dell'organizzazione.

Per altre informazioni sulle strutture delle definizioni di criteri, vedere:

> [!div class="nextstepaction"]
> [Struttura delle definizioni di Criteri di Azure](policy-definition.md)
