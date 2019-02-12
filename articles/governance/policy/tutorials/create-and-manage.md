---
title: Usare Criteri di Azure per creare e gestire i criteri per applicare la conformità
description: Usare Criteri di Azure per applicare gli standard, soddisfare i requisiti di conformità e controllo normativi, controllare i costi, mantenere la coerenza di sicurezza e prestazioni e imporre principi di progettazione all'azienda nel suo complesso.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: cb6f033c784832efab23c747057b1c0ef1b317fa
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730272"
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

   ![Ricerca di criteri](../media/create-and-manage/search-policy.png)

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure. Un'assegnazione è un criterio che è stato assegnato per l'implementazione in un ambito specifico.

   ![Selezionare le assegnazioni](../media/create-and-manage/select-assignments.png)

1. Selezionare **Assegna criterio** nella parte superiore della pagina **Criteri - Assegnazioni**.

   ![Assegnare una definizione di criteri](../media/create-and-manage/select-assign-policy.png)

1. Nella pagina **Assegna criterio** selezionare il valore di **Ambito** facendo clic sui puntini di sospensione e quindi selezionando un gruppo di gestione o una sottoscrizione. Facoltativamente, selezionare un gruppo di risorse. L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri  Fare quindi clic su **Seleziona** nella parte inferiore della pagina **Ambito**.

   Questo esempio descrive come usare la sottoscrizione **Contoso**. ma le opzioni disponibili sono diverse.

1. Le risorse possono essere escluse in base all'**Ambito**.  Le **esclusioni** iniziano a un livello inferiore rispetto al livello dell'**Ambito**. Le **esclusioni** sono facoltative quindi per il momento è possibile lasciare vuoto il campo.

1. Selezionare i puntini di sospensione per **Definizione criteri** per aprire l'elenco delle definizioni disponibili. È possibile filtrare il **Tipo** della definizione del criterio su *Built-in* per visualizzarli tutti e leggere le rispettive descrizioni.

1. Selezionare **Richiedere SQL Server versione 12.0**. Se non è possibile trovarlo subito, digitare **richiedere sql server** nella casella di ricerca poi premere INVIO o fare clic al di fuori della casella di ricerca. Fare clic su **Seleziona** nella parte inferiore della pagina **Definizioni disponibili** dopo aver individuato e selezionato la definizione criteri.

   ![Individuare un criterio](../media/create-and-manage/select-available-definition.png)

1. Il valore di **Nome dell'assegnazione** viene popolato automaticamente con il nome dei criteri selezionato, che è possibile modificare. In questo esempio lasciare *Richiedere SQL Server versione 12.0*. È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su questa assegnazione dei criteri.  **Assegnato da** viene riempito automaticamente in base a chi ha eseguito l'accesso. Questo campo è facoltativo, quindi è possibile inserire valori personalizzati.

1. Lasciare l'opzione **Crea un'identità gestita** deselezionata. Questa casella _deve_ essere selezionata quando il criterio o l'iniziativa assegnati includono un criterio con l'effetto [deployIfNotExists](../concepts/effects.md#deployifnotexists). Poiché il criterio usato per questa esercitazione non lo include, lasciare vuoto. Per altre informazioni, vedere le [identità gestite](../../../active-directory/managed-identities-azure-resources/overview.md) e il [funzionamento della sicurezza di monitoraggio e aggiornamento](../how-to/remediate-resources.md#how-remediation-security-works).

1. Fare clic su **Assegna**.

## <a name="implement-a-new-custom-policy"></a>Implementare un nuovo criterio personalizzato

Dopo avere assegnato una definizione predefinita del criterio, è possibile eseguire altre operazioni con Criteri di Azure. Creare quindi un nuovo criterio personalizzato per ridurre i costi convalidando che le macchine virtuali create nell'ambiente non siano della serie G. In questo modo, ogni volta che un utente dell'organizzazione prova a creare una macchina virtuale della serie G, la richiesta viene negata.

1. Selezionare **Definizioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

   ![Definizione in Creazione](../media/create-and-manage/definition-under-authoring.png)

1. Selezionare **+ Definizione criteri** nella parte superiore della pagina. Questo pulsante apre la pagina **Definizione criteri**.

1. Immettere le seguenti informazioni:

   - Il gruppo di gestione o la sottoscrizione in cui viene salvata la definizione criteri. Selezionare con i puntini di sospensione del **Percorso definizione**.

     > [!NOTE]
     > Se si intende applicare questa definizione di criteri a più sottoscrizioni, il percorso deve essere un gruppo di gestione contenente le sottoscrizioni a cui si assegnerà il criterio. Lo stesso vale per una definizione iniziativa.

   - Il nome della definizione del criterio: *Require VM SKUs smaller than the G series* (Richiedere SKU di VM inferiori rispetto alla serie G).
   - La descrizione della finalità della definizione del criterio: *questa definizione di criterio fa sì che tutte le macchine virtuali create in questo ambito abbiamo SKU inferiori rispetto alla serie G per ridurre i costi.*
   - Scegliere una delle opzioni esistenti (ad esempio _Calcolo_) o creare una nuova categoria per questa definizione dei criteri.
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

    La proprietà *campo* nella regola dei criteri deve essere uno dei valori seguenti: Nome, tipo, posizione, tag o un alias. Un esempio di un alias potrebbe essere `"Microsoft.Compute/VirtualMachines/Size"`.

    Per visualizzare altri esempi di criteri di Azure, vedere [Esempi di Criteri di Azure](../samples/index.md).

1. Selezionare **Salva**.

## <a name="create-a-policy-definition-with-rest-api"></a>Creare una definizione di criteri con l'API REST

È possibile creare un criterio con l'API REST per le definizioni dei criteri. L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti. Per creare una definizione criteri, usare l'esempio seguente:

```http-interactive
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
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

È possibile creare una definizione di criterio usando il cmdlet `New-AzPolicyDefinition`.

Per creare una definizione dei criteri da un file passare il percorso al file. Per un file esterno, usare l'esempio seguente:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Per un file locale, usare l'esempio seguente:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Per creare una definizione criteri con una regola inline, usare l'esempio seguente:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
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

L'output viene archiviato in un oggetto `$definition` che viene usato durante l'assegnazione del criterio. L'esempio seguente crea una definizione del criterio che include i parametri:

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

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Visualizzare le definizioni dei criteri con PowerShell

Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Restituisce tutte le definizioni dei criteri disponibili, inclusi i criteri predefiniti. Tutti i criteri vengono restituiti nel formato seguente:

```output
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

È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure con il comando di definizione dei criteri. Per creare una definizione criteri con una regola inline, usare l'esempio seguente:

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

Una definizione di iniziativa consente di raggruppare più definizioni di criteri per raggiungere un obiettivo globale. Un'iniziativa valuta le risorse nell'ambito dell'assegnazione per la conformità ai criteri inclusi. Per altre informazioni sulle definizioni delle iniziative, vedere l'articolo [Azure Policy overview](../overview.md) (Cenni preliminari su Criteri di Azure).

### <a name="create-an-initiative-definition"></a>Creare una definizione di iniziativa

1. Selezionare **Definizioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

   ![Selezionare le definizioni](../media/create-and-manage/definition-under-authoring.png)

1. Selezionare **+ Definizione dell'iniziativa** nella parte superiore della pagina per aprire la pagina **Definizione dell'iniziativa**.

   ![Definizione di iniziativa](../media/create-and-manage/initiative-definition.png)

1. Usare i puntini di sospensione di **Percorso definizione** per selezionare un gruppo di gestione o una sottoscrizione in cui archiviare la definizione. Se la pagina precedente è stata spostata in un singolo gruppo di gestione o sottoscrizione, il **percorso definizione** viene popolato automaticamente.

1. Inserire il **Nome** e la **Descrizione** dell'iniziativa.

   Questo esempio convalida che le risorse siano conformi alle definizioni dei criteri relativi alla protezione. Assegnare un nome all'iniziativa **Proteggi** e impostare la descrizione come di seguito: **Questa iniziativa è stata creata per gestire tutte le definizioni dei criteri associati alla protezione delle risorse**.

1. Per **Categoria** scegliere una delle opzioni esistenti o creare una nuova categoria.

1. Scorrere l'elenco delle **definizioni disponibili** (proprio a metà della pagina **Definizione dell'iniziativa**)e selezionare una o più definizioni di criteri da aggiungere all'iniziativa. Per l'iniziativa **Get Secure**, aggiungere le definizioni criteri predefinite seguenti, facendo clic su **+** accanto alle informazioni sulla definizione criteri o facendo clic su una riga della definizione criteri e quindi sull'opzione **+ Aggiungi** nella pagina dei dettagli:

   - Richiedere SQL Server versione 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Dopo aver selezionato la definizione di criteri nell'elenco, questa viene aggiunta in **Criteri e parametri**.

   ![Definizioni di iniziativa](../media/create-and-manage/initiative-definition-2.png)

1. Se una definizione di criteri aggiunta all'iniziativa contiene parametri, questi compaiono sotto il nome dei criteri nell'area **Criteri e parametri**. Il _valore_ può essere impostato su 'Imposta valore' (hardcoded per tutte le assegnazioni di questa iniziativa) o 'Usa parametro dell'iniziativa' (impostato durante ogni assegnazione dell'iniziativa). Se si seleziona 'Imposta valore', l'elenco a discesa a destra di _Valori_ consente di immettere o selezionare il/i valore/i. Se si seleziona 'Usa parametro dell'iniziativa', viene visualizzata una nuova sezione **Parametri dell'iniziativa** che consente di definire il parametro impostato durante l'assegnazione dell'iniziativa. I valori consentiti per questo parametro dell'iniziativa possono limitare ulteriormente ciò che può essere impostato durante l'assegnazione dell'iniziativa.

   ![Parametri della definizione dell'iniziativa](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > Nel caso di alcuni parametri `strongType`, l'elenco di valori non può essere determinato automaticamente. In questi casi vengono visualizzati i puntini di sospensione a destra della riga del parametro. Fare clic su di essi per aprire la pagina 'Ambito del parametro (&lt;nome parametro&gt;)'. In questa pagina selezionare la sottoscrizione da usare per fornire le opzioni dei valori. Questo ambito del parametro viene usato solo durante la creazione della definizione dell'iniziativa e non ha alcun impatto sulla valutazione dei criteri o sull'ambito dell'iniziativa quando viene assegnata.

1. Fare clic su **Save**.

### <a name="assign-an-initiative-definition"></a>Assegnare una definizione di iniziativa

1. Selezionare **Definizioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Individuare la definizione dell'iniziativa **Get Secure** creata in precedenza e selezionarla. Selezionare **Assegna** nella parte superiore della pagina per aprire la pagina **Proteggi: Assegna iniziativa**.

   ![Assegnare una definizione](../media/create-and-manage/assign-definition.png)

   È anche possibile fare clic con il tasto destro sulla riga selezionata o fare clic con il tasto sinistro sui puntini di sospensione alla fine della riga per un menu di scelta rapida.  Quindi selezionare **Assegna**.

   ![Fare clic con il pulsante destro del mouse su una riga](../media/create-and-manage/select-right-click.png)

1. Compilare la pagina **Proteggi: Assegna iniziativa** immettendo le informazioni di esempio seguenti. È possibile usare informazioni personalizzate.

   - Ambito: Il gruppo di gestione o la sottoscrizione in cui è stata salvata l'iniziativa, diventa l'impostazione predefinita.  È possibile modificare l'ambito per assegnare l'iniziativa a una sottoscrizione o gruppo di risorse all'interno del percorso di salvataggio.
   - Esclusioni: Configurare le risorse all'interno dell'ambito per impedire che l'assegnazione dell'iniziativa venga applicata a esse.
   - Definizione dell'iniziativa e nome dell'assegnazione: Proteggi (prepopolato come nome dell'iniziativa assegnata).
   - Descrizione: Questa assegnazione dell'iniziativa è ottimizzata in modo da applicare questo gruppo di definizioni dei criteri.
   - Assegnato da: Compilato automaticamente in base a chi ha eseguito l'accesso. Questo campo è facoltativo, quindi è possibile inserire valori personalizzati.

1. Lasciare l'opzione **Crea un'identità gestita** deselezionata. Questa casella _deve_ essere selezionata quando il criterio o l'iniziativa assegnati includono un criterio con l'effetto [deployIfNotExists](../concepts/effects.md#deployifnotexists). Poiché il criterio usato per questa esercitazione non lo include, lasciare vuoto. Per altre informazioni, vedere le [identità gestite](../../../active-directory/managed-identities-azure-resources/overview.md) e il [funzionamento della sicurezza di monitoraggio e aggiornamento](../how-to/remediate-resources.md#how-remediation-security-works).

1. Fare clic su **Assegna**.

## <a name="check-initial-compliance"></a>Controllare la conformità iniziale

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure.

1. Individuare l'iniziativa **Get Source**. È probabile che sia ancora nello _Stato di conformità_ o **Non avviato**. Fare clic sull'iniziativa per ottenere i dettagli completi sullo stato di avanzamento dell'assegnazione.

   ![Conformità - non avviata](../media/create-and-manage/compliance-status-not-started.png)

1. Dopo aver completato l'assegnazione dell'iniziativa, la pagina di conformità viene aggiornata con lo _Stato di conformità_ **Conforme**.

   ![Conformità - conforme](../media/create-and-manage/compliance-status-compliant.png)

1. Facendo clic su qualsiasi criterio nella pagina di conformità dell'iniziativa verrà visualizzata la pagina dei dettagli di conformità per i criteri. Questa pagina fornisce i dettagli a livello di risorse per la conformità.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Esentare una risorsa non conforme o non consentita tramite l'esclusione

Seguendo l'esempio precedente, dopo l'assegnazione della definizione dei criteri per richiedere SQL Server versione 12.0, la creazione di un'istanza di SQL Server con una versione diversa da 12.0 verrà rifiutata. In questa sezione viene illustrato come risolvere una richiesta negata di creazione di un server SQL creando un'esclusione in un singolo gruppo di risorse. L'esclusione impedisce l'imposizione del criterio (o iniziative) sulla risorsa.
Nell'esempio seguente una versione di SQL Server è consentita in un singolo gruppo di risorse. Un'esclusione può essere applicata a una sottoscrizione, a un gruppo di risorse oppure è possibile limitare l'esclusione a singole risorse.

Una distribuzione impedita da un criterio assegnato o da un'iniziativa può essere visualizzata in due percorsi:

- Nel gruppo di risorse di destinazione della distribuzione: Selezionare **Distribuzioni** sul lato sinistro della pagina e fare clic sul **Nome distribuzione** della distribuzione non riuscita. La risorsa che è stata negata verrà elencata con lo stato _Non consentito_. Per determinare il criterio o l'iniziativa e l'assegnazione che ha negato la risorsa, fare clic su **Operazione non riuscita. Fare clic qui per altri dettagli ->** nella pagina Panoramica della distribuzione. Sul lato destro della pagina si aprirà una finestra con le informazioni sull'errore. Nei **Dettagli errore** sono indicati gli identificatori univoci globali degli oggetti di criterio correlati.

  ![Distribuzione negata dall'assegnazione criteri](../media/create-and-manage/rg-deployment-denied.png)

- Nella pagina Criteri di Azure: Selezionare **Conformità** sul lato sinistro della pagina e fare clic sul criterio **Richiedere SQL Server versione 12.0**. Nella pagina che si apre verrà visualizzato un aumento del conteggio di **Nega**. Nella scheda **Eventi** viene inoltre visualizzato l'utente che ha tentato la distribuzione negata dal criterio.

  ![Panoramica della conformità di un criterio assegnato](../media/create-and-manage/compliance-overview.png)

In questo esempio, Trent Baker, uno degli specialisti della virtualizzazione Senior di Contoso, stava eseguendo il lavoro richiesto. Bisogna concedergli un'eccezione, ma non si vogliono SQL server diversi dalla versione 12.0 in nessun gruppo di risorse. È stato creato un nuovo gruppo di risorse **SQLServers_Excluded** e verrà ora concessa un'eccezione a questa assegnazione di criteri.

### <a name="update-assignment-with-exclusion"></a>Aggiornare l'assegnazione con esclusione

1. Selezionare **Assegnazioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Esplorare tutte le assegnazioni dei criteri e aprire l'assegnazione *Richiedere SQL Server versione 12.0*.

1. Impostare l'**Esclusione** facendo clic sui puntini di sospensione e selezionando il gruppo di risorse per escludere *SQLServers_Excluded* in questo esempio.

   ![Richiesta di esclusione](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > A seconda del criterio e del relativo effetto, l'esclusione potrebbe anche essere concessa a risorse specifiche all'interno di un gruppo di risorse all'interno dell'ambito dell'assegnazione. Dato che in questa esercitazione è stato utilizzato l'effetto **Nega**, non avrebbe senso impostare l'esclusione su una specifica risorsa già esistente.

1. Fare clic su **Seleziona** e quindi su **Salva**.

In questa sezione, è stata risolta la negazione della richiesta creando un'esclusione in un unico gruppo di risorse.

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse di questa esercitazione non sono più necessarie, usare i passaggi seguenti per eliminare tutte le assegnazioni o definizioni create:

1. Selezionare **Definizioni** (o **Assegnazioni** se si sta tentando di eliminare un'assegnazione) in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Cercare la nuova iniziativa o definizione (o assegnazione) di criteri da rimuovere.

1. Fare clic con il pulsante destro del mouse sulla riga o selezionare i puntini di sospensione alla fine della definizione (o assegnazione) e quindi **Elimina definizione** o **Elimina assegnazione**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le attività seguenti:

> [!div class="checklist"]
> - Assegnazione di un criterio per applicare una condizione alle risorse che verranno create in futuro.
> - Creazione e assegnazione di una definizione di iniziativa per tenere traccia della conformità di più risorse.
> - Risoluzione di una risorsa non conforme o non consentita.
> - Implementazione di un nuovo criterio all'interno dell'organizzazione.

Per altre informazioni sulle strutture delle definizioni di criteri, vedere:

> [!div class="nextstepaction"]
> [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md)