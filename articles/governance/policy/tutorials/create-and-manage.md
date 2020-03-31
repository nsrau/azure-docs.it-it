---
title: 'Esercitazione: Creare criteri per applicare la conformità'
description: In questa esercitazione vengono usati i criteri per applicare gli standard, controllare i costi, mantenere la sicurezza e applicare principi di progettazione a livello aziendale.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: 15a6c3df26938332d42ecbcfff43f958577062c4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239941"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Esercitazione: Creare e gestire i criteri per applicare la conformità

Comprendere come creare e gestire i criteri in Azure è importante per assicurare la conformità agli standard e ai contratti di servizio aziendali. Questa esercitazione illustra come usare Criteri di Azure per eseguire alcune delle attività più comuni correlate alla creazione, all'assegnazione e alla gestione dei criteri dell'organizzazione, ad esempio:

> [!div class="checklist"]
> - Assegnare un criterio per applicare una condizione alle risorse che verranno create in futuro
> - Creare e assegnare una definizione di iniziativa per tenere traccia della conformità di più risorse
> - Risolvere una risorsa non conforme o non consentita
> - Implementare un nuovo criterio all'interno dell'organizzazione

Se si vuole assegnare un criterio per identificare lo stato di conformità corrente delle risorse esistenti, gli articoli introduttivi illustrano come eseguire questa operazione.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="assign-a-policy"></a>Assegnare i criteri

Il primo passaggio per applicare la conformità a Criteri di Azure consiste nell'assegnare una definizione dei criteri, che consente di definire le condizioni in base alle quali un criterio viene applicato e il relativo effetto. In questo esempio assegnare la definizione dei criteri predefinita denominata _Eredita un tag dal gruppo di risorse se mancante_ per aggiungere il tag specificato con il relativo valore dal gruppo di risorse padre a risorse nuove o aggiornate prive del tag.

1. Passare al portale di Azure per assegnare i criteri. Cercare e selezionare **Criteri**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Cercare i criteri nella barra di ricerca" border="false":::

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure. Un'assegnazione è un criterio che è stato assegnato per l'implementazione in un ambito specifico.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Selezionare Assegnazioni nella pagina Panoramica criteri" border="false":::

1. Selezionare **Assegna criterio** nella parte superiore della pagina **Criteri - Assegnazioni**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Assegnare una definizione dei criteri nella pagina Assegnazioni" border="false":::

1. Nella scheda **Informazioni di base** della pagina **Assegna criterio** selezionare il valore di **Ambito** facendo clic sui puntini di sospensione e quindi selezionando un gruppo di gestione o una sottoscrizione. Facoltativamente, selezionare un gruppo di risorse. L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri
   Selezionare quindi **Seleziona** nella parte inferiore della pagina **Ambito**.

   Questo esempio descrive come usare la sottoscrizione **Contoso**. ma le opzioni disponibili sono diverse.

1. Le risorse possono essere escluse in base all'**Ambito**. Le **esclusioni** iniziano a un livello inferiore rispetto al livello dell'**Ambito**. Le **esclusioni** sono facoltative quindi per il momento è possibile lasciare vuoto il campo.

1. Selezionare i puntini di sospensione per **Definizione criteri** per aprire l'elenco delle definizioni disponibili. È possibile filtrare il **Tipo** della definizione del criterio su _Built-in_ per visualizzarli tutti e leggere le rispettive descrizioni.

1. Selezionare **Eredita un tag dal gruppo di risorse se mancante**. Se non lo si trova immediatamente, digitare **eredita un tag** nella casella di ricerca e quindi premere INVIO o selezionare un punto all'esterno della casella di ricerca.
   Selezionare **Seleziona** nella parte inferiore della pagina **Definizioni disponibili** dopo aver individuato e selezionato la definizione del criterio.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Usare il filtro di ricerca per individuare un criterio":::

1. Il valore di **Nome dell'assegnazione** viene popolato automaticamente con il nome dei criteri selezionato, che è possibile modificare. Per questo esempio, lasciare _Eredita un tag dal gruppo di risorse se mancante_. È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su questa assegnazione dei criteri.

1. Lasciare **Imposizione dei criteri** su _Abilitata_. Quando è _Disabilitata_, questa impostazione consente di testare il risultato del criterio senza attivare l'effetto. Per altre informazioni, vedere [Modalità di imposizione](../concepts/assignment-structure.md#enforcement-mode).

1. **Assegnato da** viene riempito automaticamente in base a chi ha eseguito l'accesso. Questo campo è facoltativo, quindi è possibile inserire valori personalizzati.

1. Selezionare la scheda **Parametri** nella parte superiore della procedura guidata.

1. Per **Nome tag**, immettere _Ambiente_.

1. Selezionare la scheda **Correzione** nella parte superiore della procedura guidata.

1. Lasciare l'opzione **Crea un'attività di correzione** deselezionata. Questa casella consente di creare un'attività per modificare le risorse esistenti, oltre a risorse nuove o aggiornate. Per altre informazioni, vedere [Correggere le risorse](../how-to/remediate-resources.md).

1. **Crea un'identità gestita** è automaticamente selezionata perché questa definizione dei criteri usa l'effetto [modify](../concepts/effects.md#modify). Le **Autorizzazioni** sono automaticamente impostate su _Collaboratore_ in base alla definizione del criterio. Per altre informazioni, vedere le [identità gestite](../../../active-directory/managed-identities-azure-resources/overview.md) e il [funzionamento della sicurezza di monitoraggio e aggiornamento](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selezionare la scheda **Rivedi e crea** nella parte superiore della procedura guidata.

1. Esaminare le selezioni e quindi fare clic su **Crea** nella parte inferiore della pagina.

## <a name="implement-a-new-custom-policy"></a>Implementare un nuovo criterio personalizzato

Dopo avere assegnato una definizione predefinita del criterio, è possibile eseguire altre operazioni con Criteri di Azure. Creare quindi un nuovo criterio personalizzato per ridurre i costi convalidando che le macchine virtuali create nell'ambiente non siano della serie G. In questo modo, ogni volta che un utente dell'organizzazione prova a creare una macchina virtuale della serie G, la richiesta viene negata.

1. Selezionare **Definizioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Pagina di definizione nel gruppo di creazione" border="false":::

1. Selezionare **+ Definizione criteri** nella parte superiore della pagina. Questo pulsante apre la pagina **Definizione criteri**.

1. Immettere le seguenti informazioni:

   - Il gruppo di gestione o la sottoscrizione in cui viene salvata la definizione criteri. Selezionare con i puntini di sospensione del **Percorso definizione**.

     > [!NOTE]
     > Se si intende applicare questa definizione di criteri a più sottoscrizioni, il percorso deve essere un gruppo di gestione contenente le sottoscrizioni a cui si assegnerà il criterio. Lo stesso vale per una definizione iniziativa.

   - Nome della definizione del criterio - _*_Require VM SKUs smaller than the G series_ (Richiedi SKU di macchina virtuale inferiori rispetto alla serie G)
   - La descrizione della finalità della definizione del criterio: _questa definizione di criterio fa sì che tutte le macchine virtuali create in questo ambito abbiamo SKU inferiori rispetto alla serie G per ridurre i costi._
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

   La proprietà _campo_ nella regola dei criteri deve essere costituita da un valore supportato. Un elenco completo dei valori è disponibile nei [campi della struttura delle definizioni di criteri](../concepts/definition-structure.md#fields). Un esempio di un alias potrebbe essere `"Microsoft.Compute/VirtualMachines/Size"`.

   Per visualizzare altri esempi di criteri di Azure, vedere [Esempi di Criteri di Azure](../samples/index.md).

1. Selezionare **Salva**.

## <a name="create-a-policy-definition-with-rest-api"></a>Creare una definizione di criteri con l'API REST

È possibile creare un criterio con l'API REST per le definizioni dei Criteri di Azure. L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti. Per creare una definizione criteri, usare l'esempio seguente:

```http
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

Prima di passare all'esempio di PowerShell, verificare di avere installato la versione più recente del modulo Az di Azure PowerShell.

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

È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure con il comando `az policy definition`. Per creare una definizione criteri con una regola inline, usare l'esempio seguente:

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

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Selezionare la definizione nella pagina Definizioni" border="false":::

1. Selezionare **+ Definizione dell'iniziativa** nella parte superiore della pagina per aprire la pagina **Definizione dell'iniziativa**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Esaminare la pagina Definizione dell'iniziativa" border="false":::

1. Usare i puntini di sospensione di **Percorso definizione** per selezionare un gruppo di gestione o una sottoscrizione in cui archiviare la definizione. Se la pagina precedente è stata spostata in un singolo gruppo di gestione o sottoscrizione, il **percorso definizione** viene popolato automaticamente. Una volta selezionato, viene popolato l'elenco di **Definizioni disponibili**.

1. Inserire il **Nome** e la **Descrizione** dell'iniziativa.

   Questo esempio convalida che le risorse siano conformi alle definizioni dei criteri relativi alla protezione. Assegnare un nome all'iniziativa **Proteggi** e impostare la descrizione come di seguito: **Questa iniziativa è stata creata per gestire tutte le definizioni dei criteri associati alla protezione delle risorse**.

1. Per **Categoria** scegliere una delle opzioni esistenti o creare una nuova categoria.

1. Scorrere l'elenco delle **definizioni disponibili** (proprio a metà della pagina **Definizione dell'iniziativa**)e selezionare una o più definizioni di criteri da aggiungere all'iniziativa. Per l'iniziativa **Get Secure**, aggiungere le definizioni di criteri predefinite seguenti selezionando **+** accanto alle informazioni sulle definizioni di criteri o selezionando una riga di definizione di criteri e quindi l'opzione **+ Aggiungi** nella pagina dei dettagli:

   - Percorsi consentiti
   - Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
   - Le regole per i gruppi di sicurezza di rete delle macchine virtuali con connessione Internet devono essere rafforzate
   - La soluzione Backup di Azure deve essere abilitata per le macchine virtuali
   - La crittografia del disco deve essere applicata nelle macchine virtuali

   Dopo aver selezionato la definizione di criteri nell'elenco, questa verrà aggiunta in **Categoria**.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Esaminare i parametri della definizione dell'iniziativa" border="false":::

1. Se una definizione di criteri aggiunta all'iniziativa contiene parametri, questi vengono visualizzati sotto il nome dei criteri nell'area **Categoria**. Il _valore_ può essere impostato su 'Imposta valore' (hardcoded per tutte le assegnazioni di questa iniziativa) o 'Usa parametro dell'iniziativa' (impostato durante ogni assegnazione dell'iniziativa). Se si seleziona 'Imposta valore', l'elenco a discesa a destra di _Valore/i_ consente di immettere o selezionare i valori. Se si seleziona 'Usa parametro dell'iniziativa', viene visualizzata una nuova sezione **Parametri dell'iniziativa** che consente di definire il parametro impostato durante l'assegnazione dell'iniziativa. I valori consentiti per questo parametro dell'iniziativa possono limitare ulteriormente ciò che può essere impostato durante l'assegnazione dell'iniziativa.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Modificare i parametri della definizione dell'iniziativa dai valori consentiti" border="false":::

   > [!NOTE]
   > Nel caso di alcuni parametri `strongType`, l'elenco di valori non può essere determinato automaticamente. In questi casi vengono visualizzati i puntini di sospensione a destra della riga del parametro. Selezionarli per aprire la pagina "Ambito del parametro (&lt;nome parametro&gt;)". In questa pagina selezionare la sottoscrizione da usare per fornire le opzioni dei valori. Questo ambito del parametro viene usato solo durante la creazione della definizione dell'iniziativa e non ha alcun impatto sulla valutazione dei criteri o sull'ambito dell'iniziativa quando viene assegnata.

   Impostare il parametro 'Località consentite' su 'Stati Uniti orientali 2' e lasciare gli altri come valore predefinito 'AuditifNotExist'.

1. Selezionare **Salva**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Creare una definizione dell'iniziativa dei criteri con l'interfaccia della riga di comando di Azure

È possibile creare una definizione dell'iniziativa dei criteri usando l'interfaccia della riga di comando di Azure con il comando `az policy set-definition`. Per creare una definizione dell'iniziativa dei criteri con una definizione di criteri esistente, usare l'esempio seguente:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Creare una definizione dell'iniziativa dei criteri con Azure PowerShell

È possibile creare una definizione dell'iniziativa dei criteri usando Azure PowerShell con il cmdlet `New-AzPolicySetDefinition`. Per creare una definizione dell'iniziativa dei criteri con una definizione di criteri esistente, usare il file di definizione dell'iniziativa dei criteri esistente `VMPolicySet.json`:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Assegnare una definizione di iniziativa

1. Selezionare **Definizioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Individuare la definizione dell'iniziativa **Get Secure** creata in precedenza e selezionarla. Selezionare **Assegna** nella parte superiore della pagina per aprire la pagina **Proteggi: Assegna iniziativa**.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Assegnare una definizione nella pagina Definizione dell'iniziativa" border="false":::

   È anche possibile fare clic con il pulsante destro del mouse sulla riga selezionata o selezionare i puntini di sospensione alla fine della riga per visualizzare un menu di scelta rapida. Quindi selezionare **Assegna**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Opzioni alternative per un'iniziativa" border="false":::

1. Compilare la pagina **Proteggi: Assegna iniziativa** immettendo le informazioni di esempio seguenti. È possibile usare informazioni personalizzate.

   - Ambito: Il gruppo di gestione o la sottoscrizione in cui è stata salvata l'iniziativa, diventa l'impostazione predefinita.
     È possibile modificare l'ambito per assegnare l'iniziativa a una sottoscrizione o gruppo di risorse all'interno del percorso di salvataggio.
   - Esclusioni: Configurare le risorse all'interno dell'ambito per impedire che l'assegnazione dell'iniziativa venga applicata a esse.
   - Definizione dell'iniziativa e nome dell'assegnazione: Proteggi (prepopolato come nome dell'iniziativa assegnata).
   - Descrizione: Questa assegnazione dell'iniziativa è ottimizzata in modo da applicare questo gruppo di definizioni dei criteri.
   - Imposizione dei criteri: Lasciare il valore predefinito, _Abilitata_.
   - Assegnato da: Compilato automaticamente in base a chi ha eseguito l'accesso. Questo campo è facoltativo, quindi è possibile inserire valori personalizzati.

1. Selezionare la scheda **Parametri** nella parte superiore della procedura guidata. Se è stato configurato un parametro dell'iniziativa nei passaggi precedenti, impostare un valore qui.

1. Selezionare la scheda **Correzione** nella parte superiore della procedura guidata. Lasciare l'opzione **Crea un'identità gestita** deselezionata. Questa casella _deve_ essere selezionata quando il criterio o l'iniziativa assegnati includono un criterio con gli effetti [deployIfNotExists](../concepts/effects.md#deployifnotexists) o [modify](../concepts/effects.md#modify). Poiché il criterio usato per questa esercitazione non lo include, lasciare vuoto. Per altre informazioni, vedere le [identità gestite](../../../active-directory/managed-identities-azure-resources/overview.md) e il [funzionamento della sicurezza di monitoraggio e aggiornamento](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selezionare la scheda **Rivedi e crea** nella parte superiore della procedura guidata.

1. Esaminare le selezioni e quindi fare clic su **Crea** nella parte inferiore della pagina.

## <a name="check-initial-compliance"></a>Controllare la conformità iniziale

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure.

1. Individuare l'iniziativa **Get Secure**. È probabile che sia ancora nello _Stato di conformità_ o **Non avviato**.
   Selezionare l'iniziativa per ottenere i dettagli completi sullo stato di avanzamento dell'assegnazione.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Pagina Conformità dell'iniziativa - valutazioni non avviate" border="false":::

1. Dopo aver completato l'assegnazione dell'iniziativa, la pagina di conformità viene aggiornata con lo _Stato di conformità_**Conforme**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Pagina Conformità dell'iniziativa - risorse conformi" border="false":::

1. Selezionando un qualsiasi criterio nella pagina di conformità dell'iniziativa verrà visualizzata la pagina dei dettagli di conformità il criterio corrispondente. Questa pagina fornisce i dettagli a livello di risorse per la conformità.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Esentare una risorsa non conforme o non consentita tramite l'esclusione

Dopo aver assegnato un'iniziativa dei criteri per richiedere una località specifica, viene negata qualsiasi risorsa creata in una località diversa. In questa sezione viene illustrato come risolvere una richiesta negata per la creazione di una risorsa tramite la definizione di un'esclusione in un singolo gruppo di risorse. L'esclusione impedisce l'imposizione del criterio (o dell'iniziativa) sul gruppo di risorse. Nell'esempio seguente tutte le località sono consentite nel gruppo di risorse escluso. Un'esclusione può essere applicata a una sottoscrizione, a un gruppo di risorse o a una singola risorsa.

Le distribuzioni impedite da un criterio o un'iniziativa assegnata possono essere visualizzate nel gruppo di risorse di destinazione della distribuzione: Selezionare **Distribuzioni** sul lato sinistro della pagina e quindi selezionare il **Nome distribuzione** della distribuzione non riuscita. La risorsa che è stata negata verrà elencata con lo stato _Non consentito_. Per determinare il criterio o l'iniziativa e l'assegnazione che ha negato la risorsa, selezionare **Operazione non riuscita. Fare clic qui per altri dettagli ->** nella pagina Panoramica della distribuzione. Sul lato destro della pagina si aprirà una finestra con le informazioni sull'errore. Nei **Dettagli errore** sono indicati gli identificatori univoci globali degli oggetti di criterio correlati.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Distribuzione negata dall'assegnazione dei criteri" border="false":::

Nella pagina Criteri di Azure: Selezionare **Conformità** sul lato sinistro della pagina e quindi selezionare l'iniziativa dei criteri **Get Secure**. In questa pagina si rileva un aumento del numero di effetti **Nega** per le risorse bloccate. Nella scheda **Eventi** sono disponibili informazioni dettagliate su chi ha tentato di creare o distribuire la risorsa negata dalla definizione dei criteri.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Panoramica della conformità di un criterio assegnato" border="false":::

In questo esempio, Trent Baker, uno degli specialisti della virtualizzazione Senior di Contoso, stava eseguendo il lavoro richiesto. È necessario concedere a Trent un'eccezione a questa assegnazione. È stato creato un nuovo gruppo di risorse **LocationsExcluded** e verrà ora concessa un'eccezione a questa assegnazione di criteri.

### <a name="update-assignment-with-exclusion"></a>Aggiornare l'assegnazione con esclusione

1. Selezionare **Assegnazioni** in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Esplorare tutte le assegnazioni dei criteri e aprire l'assegnazione dei criteri _Get Secure_.

1. Impostare l'**Esclusione** selezionando i puntini di sospensione e quindi il gruppo di risorse da escludere, ossia _LocationsExcluded_ in questo esempio. Selezionare **Aggiungi all'ambito selezionato** quindi selezionare **Salva**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Aggiungere un gruppo di risorse escluso all'assegnazione dei criteri" border="false":::

   > [!NOTE]
   > A seconda della definizione dei criteri e del relativo effetto, l'esclusione potrebbe anche essere concessa a risorse specifiche all'interno di un gruppo di risorse entro l'ambito dell'assegnazione. Poiché in questa esercitazione è stato usato l'effetto **Nega**, non avrebbe senso impostare l'esclusione su una risorsa specifica già esistente.

1. Selezionare **Rivedi e salva** e quindi **Salva**.

In questa sezione, è stata risolta la negazione della richiesta creando un'esclusione in un unico gruppo di risorse.

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse di questa esercitazione non sono più necessarie, usare la procedura seguente per eliminare tutte le assegnazioni o definizioni dei criteri create in precedenza:

1. Selezionare **Definizioni** (o **Assegnazioni** se si sta tentando di eliminare un'assegnazione) in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Cercare la nuova iniziativa o definizione (o assegnazione) di criteri da rimuovere.

1. Fare clic con il pulsante destro del mouse sulla riga o selezionare i puntini di sospensione alla fine della definizione (o assegnazione) e quindi **Elimina definizione** o **Elimina assegnazione**.

## <a name="review"></a>Verifica

In questa esercitazione sono state eseguite le attività seguenti:

> [!div class="checklist"]
> - Assegnazione di un criterio per applicare una condizione alle risorse che verranno create in futuro.
> - Creazione e assegnazione di una definizione di iniziativa per tenere traccia della conformità di più risorse.
> - Risoluzione di una risorsa non conforme o non consentita.
> - Implementazione di un nuovo criterio all'interno dell'organizzazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle strutture delle definizioni di criteri, vedere:

> [!div class="nextstepaction"]
> [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md)