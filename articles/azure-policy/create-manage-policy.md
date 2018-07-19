---
title: Usare Criteri di Azure per creare e gestire i criteri per applicare la conformità aziendale
description: Usare Criteri di Azure per applicare gli standard, soddisfare i requisiti di conformità e controllo normativi, controllare i costi, mantenere la coerenza di sicurezza e prestazioni e imporre principi di progettazione all'azienda nel suo complesso.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/13/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: b8ac93da2f0dd4099ab1aa2df93e5d979ecdd285
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049745"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Creare e gestire i criteri per applicare la conformità

Comprendere come creare e gestire i criteri in Azure è importante per assicurare la conformità agli standard e ai contratti di servizio aziendali. Questa esercitazione illustra come usare Criteri di Azure per eseguire alcune delle attività più comuni correlate alla creazione, all'assegnazione e alla gestione dei criteri dell'organizzazione, ad esempio:

> [!div class="checklist"]
> - Assegnare un criterio per applicare una condizione alle risorse che verranno create in futuro
> - Creare e assegnare una definizione di iniziativa per tenere traccia della conformità di più risorse
> - Risolvere una risorsa non conforme o non consentita
> - Implementare un nuovo criterio all'interno dell'organizzazione

Se si vuole assegnare un criterio per identificare lo stato di conformità corrente delle risorse esistenti, gli articoli introduttivi illustrano come eseguire questa operazione. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="assign-a-policy"></a>Assegnare i criteri

Il primo passaggio per applicare la conformità a Criteri di Azure consiste nell'assegnare una definizione dei criteri, che consente di definire le condizioni in base alle quali un criterio viene applicato e il relativo effetto. In questo esempio viene assegnata una definizione dei criteri predefinita, chiamata *Richiedere SQL Server versione 12.0*, per applicare la condizione in base alla quale tutti i database di SQL Server devono essere conformi alla versione 12.0.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

   ![Ricerca di criteri](media/create-manage-policy/search-policy.png)

2. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure. Un'assegnazione è un criterio che è stato assegnato per l'implementazione in un ambito specifico.
3. Selezionare **Assegna criterio** nella parte superiore della pagina **Criteri - Assegnazioni**.

   ![Assegnare una definizione di criteri](media/create-manage-policy/select-assign-policy.png)

4. Nella pagina **Assegna criterio** selezionare il valore di **Ambito** facendo clic sui puntini di sospensione e quindi selezionando una sottoscrizione (obbligatorio) e un gruppo di risorse (facoltativo). L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri  Fare quindi clic su **Seleziona** nella parte inferiore della pagina **Ambito**.

   Questo esempio descrive come usare la **sottoscrizione Contoso**. ma le opzioni disponibili sono diverse.

5. Per escludere uno o più gruppi di risorse (se come ambito è stata definita solo una sottoscrizione) o risorse specifiche all'interno di un gruppo di risorse (per entrambi i tipi di ambito), è possibile configurare l'opzione **Esclusioni** per l'assegnazione dei criteri. Per il momento, lasciare vuoto il campo.

6. Selezionare i puntini di sospensione per **Definizione criteri** per aprire l'elenco delle definizioni disponibili. È possibile filtrare il **Tipo** della definizione del criterio su *Built-in* per visualizzarli tutti e leggere le rispettive descrizioni.

7. Selezionare **Richiedere SQL Server versione 12.0**. Se non si riesce a trovarlo subito, digitare **richiedere SQL Server** nella casella di ricerca e quindi premere INVIO o fare clic al di fuori della casella di ricerca. Fare clic su **Seleziona** nella parte inferiore della pagina **Definizioni disponibili** dopo aver individuato e selezionato la definizione criteri.

   ![Individuare un criterio](media/create-manage-policy/select-available-definition.png)

8. Il valore di **Nome dell'assegnazione** viene popolato automaticamente con il nome dei criteri selezionato, che è possibile modificare. In questo esempio lasciare *Richiedere SQL Server versione 12.0*. È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su questa assegnazione dei criteri.

9. Fare clic su **Assegna**.

## <a name="implement-a-new-custom-policy"></a>Implementare un nuovo criterio personalizzato

Dopo avere assegnato una definizione predefinita del criterio, è possibile eseguire altre operazioni con Criteri di Azure. Creare quindi un nuovo criterio personalizzato per ridurre i costi assicurando che le macchine virtuali create nell'ambiente non siano della serie G. In questo modo, ogni volta che un utente dell'organizzazione prova a creare una macchina virtuale della serie G, la richiesta viene negata.

1. Selezionare **Definizioni** in **CREAZIONE** sul lato sinistro della pagina Criteri di Azure.

   ![Definizione in Creazione](media/create-manage-policy/definition-under-authoring.png)

2. Selezionare **+ Definizione criteri** nella parte superiore della pagina. Verrà aperta la pagina **Definizione criteri**.
3. Immettere le informazioni seguenti:

   - Il gruppo di gestione o la sottoscrizione in cui viene salvata la definizione criteri. Selezionare con i puntini di sospensione del **Percorso definizione**.

     > [!NOTE]
     > Se si intende applicare questa definizione criteri a più sottoscrizioni, il percorso deve essere un gruppo di gestione che contiene le sottoscrizioni a cui si assegnerà il criterio. Lo stesso vale per una definizione iniziativa.

   - Il nome della definizione del criterio: *Require VM SKUs smaller than the G series* (Richiedere SKU di VM inferiori rispetto alla serie G).
   - La descrizione della finalità della definizione del criterio: *questa definizione di criterio fa sì che tutte le macchine virtuali create in questo ambito abbiamo SKU inferiori rispetto alla serie G per ridurre i costi.*
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
                "allOf": [{
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

    Il valore della *proprietà field* nella regola del criterio deve essere uno dei seguenti: Name, Type, Location, Tags o un alias. Un esempio di un alias potrebbe essere `"Microsoft.Compute/VirtualMachines/Size"`.

    Per visualizzare altri esempi di criteri di Azure, vedere [Modelli per Criteri di Azure](json-samples.md).

4. Selezionare **Salva**.

## <a name="create-a-policy-definition-with-rest-api"></a>Creare una definizione di criteri con l'API REST

È possibile creare un criterio con l'API REST per le definizioni dei criteri. L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti.
Per creare una definizione criteri, usare l'esempio seguente:

```http-interactive
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Includere un corpo della richiesta in modo simile all'esempio seguente:

```json
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

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Per un file locale, usare l'esempio seguente:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Per creare una definizione criteri con una regola inline, usare l'esempio seguente:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
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

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
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

$definition = New-AzureRmPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Visualizzare le definizioni dei criteri con PowerShell

Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```azurepowershell-interactive
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

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Visualizzare le definizioni dei criteri con l'interfaccia della riga di comando di Azure

Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```azurecli-interactive
az policy definition list
```

Restituisce tutte le definizioni dei criteri disponibili, inclusi i criteri predefiniti. Tutti i criteri vengono restituiti nel formato seguente:

```json
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

Una definizione di iniziativa consente di raggruppare più definizioni di criteri per raggiungere un obiettivo globale. Una definizione di iniziativa viene creata per garantire che le risorse incluse nell'ambito siano conformi alle definizioni dei criteri che costituiscono la definizione dell'iniziativa.  Per altre informazioni sulle definizioni delle iniziative, vedere l'articolo [Azure Policy overview](azure-policy-introduction.md) (Cenni preliminari su Criteri di Azure).

### <a name="create-an-initiative-definition"></a>Creare una definizione di iniziativa

1. Selezionare **Definizioni** in **CREAZIONE** sul lato sinistro della pagina Criteri di Azure.

   ![Selezionare le definizioni](media/create-manage-policy/select-definitions.png)

2. Selezionare **+ Definizione dell'iniziativa** nella parte superiore della pagina per aprire la pagina **Definizione dell'iniziativa**.

   ![Definizione di iniziativa](media/create-manage-policy/initiative-definition.png)

3. Usare i puntini di sospensione di **Percorso definizione** per selezionare una sottoscrizione in cui archiviare la definizione.

4. Inserire il **Nome** e la **Descrizione** dell'iniziativa.

   Questo esempio descrive come garantire che le risorse siano conformi alle definizioni dei criteri relativi alla protezione. Il nome dell'iniziativa potrebbe quindi essere **Get Secure** e la descrizione potrebbe essere **This initiative has been created to handle all policy definitions associated with securing resources**.

5. Per **Categoria** scegliere una delle opzioni esistenti o creare una nuova categoria.

6. Scorrere l'elenco delle **definizioni disponibili** (proprio a metà della pagina **Definizione dell'iniziativa**)e selezionare una o più definizioni di criteri da aggiungere all'iniziativa. Per l'iniziativa **Get Secure**, aggiungere le definizioni criteri predefinite seguenti, facendo clic su **+** accanto alle informazioni sulla definizione criteri o facendo clic su una riga della definizione criteri e quindi sull'opzione **+ Aggiungi** nella pagina dei dettagli:
   - Richiedere SQL Server versione 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Dopo aver selezionato le definizioni dei criteri nell'elenco, queste verranno visualizzate in **CRITERI E PARAMETRI**.

   ![Definizioni di iniziativa](media/create-manage-policy/initiative-definition-2.png)

7. Fare clic su **Save**.

### <a name="assign-an-initiative-definition"></a>Assegnare una definizione di iniziativa

1. Selezionare **Definizioni** in **CREAZIONE** sul lato sinistro della pagina Criteri di Azure.
2. Individuare la definizione dell'iniziativa **Get Secure** creata in precedenza e selezionarla.
3. Selezionare **Assegna** nella parte superiore della pagina per aprire la pagina **Get Secure: Assign Initiative**.

   ![Assegnare una definizione](media/create-manage-policy/assign-definition.png)

   In alternativa, è possibile fare clic sulla riga selezionata o fare clic sui puntini di sospensione alla fine della riga per un menu di scelta rapida.  Quindi selezionare **Assegna**.

   ![Fare clic con il pulsante destro del mouse su una riga](media/create-manage-policy/select-right-click.png)

4. Compilare la pagina **Get Secure: Assign Initiative** immettendo le informazioni di esempio seguenti. È possibile usare informazioni personalizzate.

   - Ambito: La sottoscrizione in cui è stata salvata l'iniziativa sarà il valore predefinito.  È possibile modificare l'ambito per assegnare l'iniziativa a un gruppo di risorse all'interno del percorso di salvataggio della sottoscrizione.
   - Esclusioni: Configurare le risorse all'interno dell'ambito per impedire che l'assegnazione dell'iniziativa venga applicata a esse.
   - Definizione dell'iniziativa e nome dell'assegnazione: ottenere Get Secure (pre-popolato come nome dell'iniziativa assegnato).
   - Descrizione: questa assegnazione di iniziativa è ottimizzata in modo da applicare questo gruppo di definizioni dei criteri.

5. Fare clic su **Assegna**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Esentare una risorsa non conforme o non consentita tramite l'esclusione

Seguendo l'esempio precedente, dopo l'assegnazione della definizione dei criteri per richiedere SQL Server versione 12.0, la creazione di un'istanza di SQL Server con una versione diversa da 12.0 verrà rifiutata. In questa sezione viene illustrato come risolvere il rifiuto di un tentativo di creare un'istanza di SQL Server creando un'esclusione in un singolo gruppo di risorse. L'esclusione impedisce l'imposizione del criterio (o iniziative) sulla risorsa. Nell'esempio seguente qualsiasi versione di SQL Server verrà consentita in un singolo gruppo di risorse. Un'esclusione può essere applicata a un gruppo di risorse oppure è possibile limitare l'esclusione a singole risorse.

Una distribuzione impedita a causa di un criterio assegnato o iniziativa può essere visualizzata in due percorsi:

- Nel gruppo di risorse di destinazione della distribuzione: selezionare **Distribuzioni** sul lato sinistro della pagina e fare clic su **Nome distribuzione** della distribuzione non riuscita. La risorsa che è stata negata verrà elencata con lo stato _Non consentito_. Per determinare il criterio o l'iniziativa e l'assegnazione che ha negato la risorsa, fare clic su **Operazione non riuscita. Fare clic qui per altri dettagli ->** nella pagina Panoramica della distribuzione. Si aprirà una finestra sul lato destro della pagina con le informazioni sull'errore. Nei **Dettagli errore** saranno indicati gli identificatori univoci globali degli oggetti criteri correlati.

   ![Distribuzione negata dall'assegnazione criteri](media/create-manage-policy/rg-deployment-denied.png)

- Nella pagina Criteri di Azure: selezionare **Conformità** sul lato sinistro della pagina e fare clic sul criterio **Richiedere SQL Server versione 12.0**. Nella pagina che si apre verrà visualizzato un aumento del conteggio di **Nega**. Nella scheda **Eventi** verrà inoltre visualizzato l'utente che ha tentato la distribuzione negata dal criterio.

   ![Panoramica della conformità di un criterio assegnato](media/create-manage-policy/compliance-overview.png)

In questo esempio Trent bianchi, uno degli specialisti di virtualizzazione Senior di Contoso, ha eseguito i lavori necessari. È necessario concedergli l'eccezione, ma non si vuole un SQL Server diverso dalla versione 12.0 in nessun gruppo di risorse. È stato creato un nuovo gruppo di risorse **SQLServers_Excluded** e verrà ora concessa un'eccezione a questa assegnazione di criteri.

### <a name="update-assignment-with-exclusion"></a>Aggiornare l'assegnazione con esclusione

1. Selezionare **Assegnazioni** in **CREAZIONE** sul lato sinistro della pagina Criteri di Azure.
2. Esplorare tutte le assegnazioni dei criteri e aprire l'assegnazione *Richiedere SQL Server versione 12.0*.
3. Impostare l'**Esclusione** facendo clic sui puntini di sospensione e selezionando il gruppo di risorse per escludere *SQLServers_Excluded* in questo esempio.

   ![Richiesta di esclusione](media/create-manage-policy/request-exclusion.png)

   > [!NOTE]
   > A seconda del criterio e del relativo effetto, l'esclusione potrebbe anche essere concessa a risorse specifiche all'interno di un gruppo di risorse all'interno dell'ambito dell'assegnazione. Dato che in questa esercitazione è stato utilizzato l'effetto **Nega**, non avrebbe senso impostare l'esclusione su una specifica risorsa già esistente.

4. Fare clic su **Seleziona** e quindi su **Salva**.

In questa sezione è stata risolta la negazione del tentativo di creare una versione di SQL server non consentita creando un'esclusione in un singolo gruppo di risorse.

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse di questa esercitazione non sono più necessarie, usare i passaggi seguenti per eliminare tutte le assegnazioni o definizioni create:

1. Selezionare **Definizioni** (o **Assegnazioni** se si sta tentando di eliminare un'assegnazione) in **CREAZIONE** sul lato sinistro della pagina Criteri di Azure.
2. Cercare la nuova iniziativa o definizione (o assegnazione) di criteri da rimuovere.
3. Fare clic con il pulsante destro del mouse sulla riga o selezionare i puntini di sospensione alla fine della definizione (o assegnazione) e quindi **Elimina definizione** o **Elimina assegnazione**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è eseguito quanto segue:

> [!div class="checklist"]
> - Assegnazione di un criterio per applicare una condizione alle risorse che verranno create in futuro.
> - Creazione e assegnazione di una definizione di iniziativa per tenere traccia della conformità di più risorse.
> - Risoluzione di una risorsa non conforme o non consentita.
> - Implementazione di un nuovo criterio all'interno dell'organizzazione.

Per altre informazioni sulle strutture delle definizioni di criteri, vedere:

> [!div class="nextstepaction"]
> [Struttura delle definizioni di Criteri di Azure](policy-definition.md)