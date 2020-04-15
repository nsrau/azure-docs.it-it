---
title: Dettagli della struttura delle definizioni dei criteri
description: Descrive come vengono utilizzate le definizioni dei criteri per stabilire le convenzioni per le risorse di Azure nell'organizzazione.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 017878c4c47a5645ea8815580d2176c7a2ff5d66
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314007"
---
# <a name="azure-policy-definition-structure"></a>Struttura delle definizioni di criteri di Azure

Criteri di Azure stabilisce le convenzioni per le risorse. Le definizioni dei criteri descrivono [le condizioni](#conditions) di conformità delle risorse e l'effetto da avere se viene soddisfatta una condizione. Una condizione confronta un [campo](#fields) della proprietà della risorsa con un valore obbligatorio. L'accesso ai campi delle proprietà delle risorse viene eseguito tramite [alias](#aliases). Un campo della proprietà della risorsa è un campo a valore singolo o [una matrice](#understanding-the--alias) di più valori. La valutazione delle condizioni è diversa nelle matrici.
Ulteriori informazioni sulle [condizioni](#conditions).

Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che tutte le risorse abbiano un tag specifico. I criteri vengono ereditati da tutte le risorse figlio. Se un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

Lo schema di definizione dei criteri è disponibile qui:The policy definition schema is found here:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

- mode
- parametri
- nome visualizzato
- description
- regola dei criteri
  - valutazione logica
  - effetto

Ad esempio, la notazione JSON seguente illustra un criterio che limita i punti in cui vengono distribuite le risorse:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
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

Tutti gli esempi di Criteri di Azure sono disponibili in Esempi di criteri di Azure.All Azure Policy samples are at [Azure Policy samples](../samples/index.md).

## <a name="mode"></a>Mode

**La modalità** viene configurata a seconda che il criterio sia destinato a una proprietà di Azure Resource Manager o a una proprietà del provider di risorse.

### <a name="resource-manager-modes"></a>Modalità di Resource Manager

Il parametro **mode** (modalità) determina quali tipi di risorse verranno valutate per l'assegnazione dei criteri. Le modalità supportate sono:

- `all`: vengono valutati i gruppi di risorse e tutti i tipi di risorse
- `indexed`: vengono valutati solo i tipi di risorse che supportano tag e il percorso

Ad esempio, `Microsoft.Network/routeTables` la risorsa supporta i tag e la posizione e viene valutata in entrambe le modalità. Tuttavia, `Microsoft.Network/routeTables/routes` la risorsa non può essere contrassegnata e non viene valutata in `Indexed` modalità.

Nella maggior parte dei casi, è consigliabile impostare il parametro **mode** su `all`. Tutte le definizioni di criteri create tramite il portale usano la modalità `all`. Se si usa PowerShell o l'interfaccia della riga di comando di Azure è necessario specificare il parametro **mode** manualmente. Se la definizione dei criteri non include un valore **mode**, assume il valore predefinito `all` in Azure PowerShell e `null` nell'interfaccia della riga di comando di Azure. Un valore mode `null` equivale all'utilizzo di `indexed` per supportare la compatibilità con le versioni precedenti.

`indexed` deve essere usato durante la creazione di criteri che applicano tag o percorsi. Sebbene non sia necessario, evita che le risorse che non supportano tag e percorsi vengano visualizzate come non conformi nei risultati sulla conformità. L'eccezione è rappresentata dai **gruppi di risorse**. Per i criteri che applicano percorsi o tag a un gruppo di risorse, impostare il parametro **mode** su `all` e specificare una destinazione specifica per il tipo `Microsoft.Resources/subscriptions/resourceGroups`. Per un esempio, vedere [Applicare tag di gruppi di risorse](../samples/enforce-tag-rg.md). Per un elenco delle risorse che supportano i tag, vedere Supporto dei tag per le risorse di [Azure.](../../../azure-resource-manager/management/tag-support.md)

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Modalità provider di risorse (anteprima)Resource Provider modes (preview)

Le seguenti modalità del provider di risorse sono attualmente supportate durante l'anteprima:The following Resource Provider modes are currently supported during preview:

- `Microsoft.ContainerService.Data`per la gestione delle regole del controller di ammissione nel [servizio Azure Kubernetes](../../../aks/intro-kubernetes.md). I criteri che utilizzano questa modalità del provider di risorse devono utilizzare l'effetto [EnforceRegoPolicy.Policies](./effects.md#enforceregopolicy) using this Resource Provider mode **must** use the EnforceRegoPolicy effect.
- `Microsoft.Kubernetes.Data`per la gestione dei cluster AKS Engine kubernetes autogestiti in Azure.For managing self-managed AKS Engine Kubernetes clusters on Azure.
  I criteri che utilizzano questa modalità del provider di risorse **devono** utilizzare l'effetto [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
- `Microsoft.KeyVault.Data`per la gestione di insiemi di credenziali e certificati in [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> Le modalità del provider di risorse supportano solo le definizioni dei criteri predefinite e non supportano le iniziative durante l'anteprima.

## <a name="parameters"></a>Parametri

I parametri consentono di semplificare la gestione dei criteri, riducendone il numero di definizioni. I parametri possono essere paragonati ai campi di un modulo: `name`, `address`, `city`, `state`. Questi parametri rimangono sempre invariati, ma i loro valori cambiano a seconda dei dati immessi durante la compilazione del modulo da parte dei singoli utenti.
I parametri funzionano nello stesso modo durante la creazione di criteri. L'inclusione dei parametri in una definizione dei criteri consente di riutilizzare i singoli criteri in vari scenari mediante l'uso di valori diversi.

> [!NOTE]
> I parametri possono essere aggiunti a una definizione esistente e assegnata. Il nuovo parametro deve includere la proprietà **defaultValue**. In questo modo le assegnazioni esistenti dei criteri o dell'iniziativa non possono essere rese indirettamente non valide.

### <a name="parameter-properties"></a>Proprietà parametro

Un parametro presenta le proprietà seguenti, usate nella definizione di criteri:

- **name**: Il nome del parametro. Usato dalla funzione di distribuzione `parameters` all'interno della regola dei criteri. Per altre informazioni, vedere [Usare un valore di parametro](#using-a-parameter-value).
- `type`: determina se il parametro è una **stringa**, **matrice**, **object**, **boolean**, **integer**, **float**o **datetime**.
- `metadata`: definisce le sottoproprietà utilizzate principalmente dal portale di Azure per visualizzare informazioni di facile utilizzo:: Defines subproperties primarily used by the Azure portal to display user-friendly information:
  - `description`: spiegazione dell'utilizzo del parametro. Può essere usata per fornire esempi di valori accettabili.
  - `displayName`: il nome descrittivo visualizzato nel portale per il parametro.
  - `version`: (Facoltativo) Tiene traccia dei dettagli relativi alla versione del contenuto di una definizione dei criteri.

    > [!NOTE]
    > Il servizio Criteri `version` `preview`di `deprecated` Azure usa , e le proprietà per trasmettere il livello di modifica a una definizione o a un'iniziativa e allo stato predefiniti. Il formato `version` di `{Major}.{Minor}.{Patch}`è: . Stati specifici, ad esempio _preview_ _deprecato_ o anteprima `version` , vengono aggiunti alla proprietà o in un'altra proprietà come **valore booleano.**

  - `category`: (Facoltativo) Determina in quale categoria nel portale di Azure viene visualizzata la definizione dei criteri.
  - `strongType`: (Facoltativo) Utilizzato durante l'assegnazione della definizione dei criteri tramite il portale. Fornisce un elenco con riconoscimento del contesto. Per altre informazioni, vedere [strongType](#strongtype).
  - `assignPermissions`: (Facoltativo) Impostare su true per fare in modo che il portale di Azure crei assegnazioni di ruolo durante l'assegnazione dei criteri.: (Optional) Set as _true_ to have Azure portal create role assignments during policy assignment. Questa proprietà è utile nel caso in cui si desideri assegnare autorizzazioni all'esterno dell'ambito di assegnazione. Esiste un'assegnazione di ruolo per definizione di ruolo nei criteri (o per definizione di ruolo in tutti i criteri dell'iniziativa). Il valore del parametro deve essere una risorsa o un ambito valido.
- `defaultValue`: (Facoltativo) Imposta il valore del parametro in un'assegnazione se non viene specificato alcun valore.
  Obbligatoria quando si aggiorna una definizione di criteri esistente già assegnata.
- `allowedValues`: (Facoltativo) Fornisce una matrice di valori accettati dal parametro durante l'assegnazione.

Ad esempio, è possibile definire una definizione di criteri per limitare i percorsi in cui le risorse possono essere distribuite. Un parametro per questa definizione di criteri potrebbe essere **allowedLocations**. Questo parametro è stato usato da ogni assegnazione della definizione di criteri per limitare i valori accettati. L'uso di **strongType** offre un'esperienza migliorata nel completamento dell'assegnazione tramite portale:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Usare un valore di parametro

Nella regola dei criteri, si `parameters` fa riferimento a parametri con la sintassi della funzione seguente:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Questo esempio fa riferimento al parametro **allowedLocations** illustrato nella sezione relativa alle [proprietà dei parametri](#parameter-properties).

### <a name="strongtype"></a>strongType

Nella proprietà `metadata` è possibile usare **strongType** per fornire un elenco di opzioni di selezione multipla nel portale di Azure. strongType può essere un _tipo di risorsa_ supportato o un valore consentito.strongType can be a supported resource type or an allowed value. **strongType** Per determinare se un _tipo di risorsa_ è valido per **strongType**, utilizzare [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Alcuni _tipi di risorse_ non restituiti da **Get-AzResourceProvider** sono supportati. Questi sono:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

I valori consentiti per il tipo di risorsa non consentiti per strongType sono:The non _resource type_ allowed values for **strongType** are:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Posizione della definizione

Durante la creazione di iniziative o criteri è importante specificare la posizione della definizione. La posizione della definizione deve essere specificata come un gruppo di gestione o una sottoscrizione. Tale posizione determina l'ambito al quale la definizione delle iniziative o dei criteri può essere assegnata. Le risorse devono essere membri diretti o elementi figli all'interno della gerarchia della posizione della definizione da destinare all'assegnazione.

Se la posizione della definizione è:

- Una **sottoscrizione**: solo le risorse all'interno di tale sottoscrizione possono essere assegnate ai criteri.
- Un **gruppo di gestione**: solo le risorse all'interno dei gruppi di gestione figlio e delle sottoscrizioni figlio possono essere assegnate ai criteri. Se si intende applicare questa definizione di criteri a più sottoscrizioni, la posizione deve essere un gruppo di gestione contenente tali sottoscrizioni.

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Usare **displayName** e **description** per identificare la definizione dei criteri e fornire il contesto d'uso. **displayName** ha una lunghezza massima di _128_ caratteri e **description** una lunghezza massima di _512_ caratteri.

> [!NOTE]
> Durante la creazione o l'aggiornamento di una definizione di criteri, **id**, **type**e **name** sono definiti da proprietà esterne a JSON e non sono necessarie nel file JSON. Il seraggio della definizione dei criteri tramite SDK restituisce le proprietà **id**, **type**e **name** come parte del codice JSON, ma ognuna di esse sono informazioni di sola lettura relative alla definizione dei criteri.

## <a name="policy-rule"></a>Regola dei criteri

La regola dei criteri è costituita dai blocchi **If** e **Then** blocchi. Nel blocco **If**, si definiscono una o più condizioni che specificano quando i criteri vengono applicati. È possibile applicare gli operatori logici a queste condizioni per definire con precisione lo scenario di un criterio.

Nel blocco **Then** si definisce l'effetto che si verifica quando le condizioni **If** sono soddisfatte.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operatori logici

Gli operatori logici supportati sono:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

La sintassi **not** inverte il risultato della condizione. La sintassi **allOf** (simile all'operazione logica **And**) richiede che tutte le condizioni siano vere. La sintassi **anyOf** (simile all'operazione logica **Or**) richiede che una o più condizioni siano vere.

È possibile annidare gli operatori logici. L'esempio seguente illustra un'operazione **not** nidificata in un'operazione **allOf**.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Condizioni

Una condizione valuta se una funzione di accesso **field** o **value** soddisfa determinati criteri. Le condizioni supportate sono:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

Per **less**, **lessOrEquals**, **greater**e **greaterOrEquals**, se il tipo di proprietà non corrisponde al tipo di condizione, viene generato un errore. I confronti tra `InvariantCultureIgnoreCase`stringhe vengono effettuati utilizzando .

Quando si usano le condizioni **like** e **notLike**, è possibile inserire un carattere jolly `*` nel valore.
Il valore non deve contenere più di un carattere jolly `*`.

Quando si utilizzano le condizioni **match** e `?` **notMatch,** specificare `#` di trovare una corrispondenza con una cifra, per una lettera, `.` per la corrispondenza con qualsiasi carattere e qualsiasi altro carattere corrispondente al carattere effettivo. Mentre, **match** e **notMatch** sono case-sensitive, tutte le altre condizioni che valutano un _stringValue_ sono case senza distinzione tra maiuscole e minuscole. Alternative senza distinzione tra maiuscole e minuscole sono disponibili in **matchInsensitively** e **notMatchInsensitively**.

In ** \[ \* \] ** un valore di campo di matrice alias, ogni elemento della matrice viene valutato singolarmente con elementi logici **e** intermedi. Per ulteriori informazioni, vedere [Valutazione \[ \* \] dell'alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Campi

Le condizioni vengono formate usando i campi. Un campo rappresenta le proprietà nel payload delle richieste di risorse e descrive lo stato della risorsa.

Sono supportati i seguenti campi:

- `name`
- `fullName`
  - Restituisce il nome completo della risorsa. Il nome completo di una risorsa è il nome della risorsa con anteposti gli eventuali nomi delle risorse padre (ad esempio "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Usare **global** per le risorse che sono indipendenti dalla posizione.
- `identity.type`
  - Restituisce il tipo di [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md) abilitato per la risorsa.
- `tags`
- `tags['<tagName>']`
  - Questa sintassi tra parentesi quadre supporta nomi di tag con segni di punteggiatura, ad esempio un trattino, punti o spazi.
  - Dove ** \<tagName\> ** è il nome del tag per cui convalidare la condizione.
  - Esempi: `tags['Acct.CostCenter']` dove **Acct.CostCenter** è il nome del tag.
- `tags['''<tagName>''']`
  - Questa sintassi tra parentesi quadre supporta nomi di tag contenenti apostrofi eseguendo l'escape con esso in caratteri di escape con apostrofi doppi.
  - Dove **\<'\>tagName '** è il nome del tag per cui convalidare la condizione.
  - Esempio: `tags['''My.Apostrophe.Tag''']` dove **'My.Apostrophe.Tag'** è il nome del tag.
- alias delle proprietà; per un elenco, vedere [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` e `tags[tag.with.dots]` sono comunque modi accettabili per dichiarare un campo tags. Tuttavia, le espressioni preferibili sono quelle elencate in precedenza.

#### <a name="use-tags-with-parameters"></a>Usare tag con parametri

È possibile passare a un campo di tag un valore di parametro. Passare un parametro a un campo di tag aumenta la flessibilità della definizione dei criteri durante l'assegnazione dei criteri.

Nell'esempio seguente, `concat` viene usato per creare una ricerca nei campi di tag per il tag che ha come nome il valore del parametro **tagName**. Se tale tag non esiste, l'effetto di **modifica** viene utilizzato per aggiungere il tag utilizzando il valore `resourcegroup()` dello stesso tag denominato impostato nel gruppo di risorse padre delle risorse controllate utilizzando la funzione di ricerca.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Valore

Le condizioni possono essere formate anche usando **value**. **value** controlla le condizioni rispetto a [parametri](#parameters), [funzioni di modello supportate](#policy-functions) o valori letterali.
**value** è associato a qualsiasi [condizione](#conditions) supportata.

> [!WARNING]
> Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri non riesce. Una valutazione non riuscita è un risultato **deny** implicito. Per altre informazioni, vedere come [evitare errori dei modelli](#avoiding-template-failures). Utilizzare [enforcementMode](./assignment-structure.md#enforcement-mode) di **DoNotEnforce** per evitare l'impatto di una valutazione non riuscita su risorse nuove o aggiornate durante il test e la convalida di una nuova definizione dei criteri.

#### <a name="value-examples"></a>Esempi d'uso di value

Questa regola dei criteri usa **value** per confrontare il risultato della funzione `resourceGroup()` e la proprietà restituita **name** rispetto a una condizione **like** di `*netrg`. La regola respinge qualsiasi risorsa che non faccia parte di  **type**`Microsoft.Network/*` in qualsiasi gruppo di risorse il cui nome termina con `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Questa regola dei criteri usa **value** per verificare se il risultato di più funzioni annidate corrisponde a **equals** `true`. La regola respinge qualsiasi risorsa che non dispone di almeno tre tag.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Evitare errori di modello

L'uso di funzioni di _modello_ nel **valore** consente molte funzioni annidate complesse. Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri non riesce. Una valutazione non riuscita è un risultato **deny** implicito. Un esempio di un valore che ha esito negativo in determinati scenari:An example of a **value** that fails in certain scenarios:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

La regola dei criteri di esempio precedente utilizza [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) per confrontare i primi tre caratteri del **nome con** **abc**. Se **name** è più breve `substring()` di tre caratteri, la funzione genera un errore. Questo errore fa sì che il criterio diventi un effetto **di negazione.**

Utilizzate invece la funzione [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) per verificare se i primi tre caratteri del **nome** sono uguali a **abc** senza consentire a un **nome** inferiore a tre caratteri di causare un errore:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Con la regola dei `if()` criteri modificata, controlla la `substring()` lunghezza del **nome** prima di tentare di ottenere un valore su un valore con meno di tre caratteri. Se **name** è troppo breve, viene restituito il valore "not starting with abc" e confrontato con **abc**. Una risorsa con un nome breve che non inizia con **abc** continua a non riuscire alla regola dei criteri, ma non causa più un errore durante la valutazione.

### <a name="count"></a>Conteggio

Le condizioni che contano quanti membri di una matrice nel payload della risorsa soddisfano un'espressione di condizione che possono essere formate usando l'espressione **count.** Scenari comuni stanno controllando se 'almeno uno di', 'esattamente uno di', 'all of' o 'nessuno dei' i membri della matrice soddisfano la condizione. **count** valuta [ \[ \* \] ](#understanding-the--alias) ogni membro della matrice di alias per un'espressione di condizione e somma i risultati _reali,_ che vengono quindi confrontati con l'operatore di espressione.

La struttura dell'espressione **count** è:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Le seguenti proprietà vengono utilizzate con **count**:

- **count.field** (obbligatorio): contiene il percorso della matrice e deve essere un alias di matrice. Se la matrice non è presente, l'espressione viene valutata su _false_ senza considerare l'espressione della condizione.
- **count.where** (facoltativo): l'espressione di [ \[ \* \] ](#understanding-the--alias) condizione per valutare singolarmente ogni membro della matrice di alias di **count.field**. Se questa proprietà non viene fornita, tutti i membri della matrice con il percorso di 'field' vengono valutati su _true_. Qualsiasi [condizione](../concepts/definition-structure.md#conditions) può essere utilizzata all'interno di questa proprietà.
  [Gli operatori logici](#logical-operators) possono essere utilizzati all'interno di questa proprietà per creare requisiti di valutazione complessi.
- condition (required): il valore viene confrontato con il numero di elementi che hanno soddisfatto l'espressione della condizione **count.where.** ** \<\> ** Deve essere utilizzata una [condizione](../concepts/definition-structure.md#conditions) numerica.

#### <a name="count-examples"></a>Esempi di conteggio

Esempio 1: Controllare se una matrice è vuotaExample 1: Check if an array is empty

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Esempio 2: Verificare la presenza di un solo membro della matrice per soddisfare l'espressione di condizioneExample 2: Check for only one array member to meet the condition expression

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Esempio 3: Verificare la presenza di almeno un membro della matrice per soddisfare l'espressione di condizioneExample 3: Check for at least one array member to meet the condition expression

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Esempio 4: Verificare che tutti i membri dell'array di oggetti soddisfino l'espressione di condizioneExample 4: Check that all object array members meet the condition expression

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Esempio 5: Verificare che tutti i membri della matrice di stringhe soddisfino l'espressione di condizioneExample 5: Check that all string array members meet the condition expression

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Esempio 6: Utilizzare il **valore** **field** inside per verificare che tutti i membri della matrice soddisfino l'espressione della condizione

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Esempio 7: verificare che almeno un membro della matrice corrisponda a più proprietà nell'espressione della condizioneExample 7: Check that at least one array member matches multiple properties in the condition expression

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Effetto

Criteri di Azure supporta i tipi di effetto seguenti:Azure Policy supports the following types of effect:

- **Append**: aggiunge il set di campi definiti alla richiesta
- **Audit**: genera un evento di avviso nel log attività, ma non nega la richiesta
- **AuditIfNotExists**: genera un evento di avviso nel registro attività se non esiste una risorsa correlata
- **Deny**: genera un evento nel log attività e nega la richiesta
- **DeployIfNotExists:** distribuisce una risorsa correlata se non esiste già
- **Disabled**: non valuta le risorse per garantire la conformità alla regola dei criteri
- **EnforceOPAConstraint** (anteprima): configura il controller di ammissione dell'agente criteri aperto con Gatekeeper v3 per i cluster Kubernetes autogestiti in Azure (anteprima)EnforceOPAConstraint (preview): configures the Open Policy Agent admissions controller with Gatekeeper v3 for self-managed Kubernetes clusters on Azure (preview)
- **EnforceRegoPolicy** (anteprima): configura il controller di ammissione dell'agente criteri aperto con Gatekeeper v2 nel servizio Azure KubernetesEnforceRegoPolicy (preview): configures the Open Policy Agent admissions controller with Gatekeeper v2 in Azure Kubernetes Service
- **Modifica**: aggiunge, aggiorna o rimuove i tag definiti da una risorsa

Per informazioni dettagliate su ogni effetto, ordine di valutazione, proprietà ed esempi, vedere Informazioni sugli effetti dei criteri di [Azure.](effects.md)

### <a name="policy-functions"></a>Funzioni dei criteri

Tutte le funzioni modello di Resource Manager sono disponibili per l'utilizzo all'interno di una regola dei criteri, ad eccezione delle funzioni e delle funzioni definite dall'utente seguenti:All [Resource Manager template](../../../azure-resource-manager/templates/template-functions.md) functions are available to use within a policy rule, except the following functions and user-defined functions:

- copyIndex()
- deployment()
- list*
- newGuid()
- pick-ones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> Queste funzioni sono ancora `details.deployment.properties.template` disponibili all'interno della parte della distribuzione del modello in una definizione dei criteri **deployIfNotExists.**

La funzione seguente è disponibile per l'uso in una regola dei criteri, ma è diversa dall'uso in un modello di Azure Resource Manager:The following function is available to use in a policy rule, but differs from use in an Azure Resource Manager template:

- `utcNow()`- A differenza di un modello di Resource Manager, questo può essere usato al di fuori di defaultValue.- Unlike a Resource Manager template, this can be used outside defaultValue.
  - Restituisce una stringa impostata sulla data e sull'ora correnti nel formato ISO 8601 DateTime universale 'aaaa-MM-ggTHH:mm:ss.fffffff'

Nelle regole dei criteri sono disponibili solo le funzioni seguenti:The following functions are only available in policy rules:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **stringa dateTime**: [Obbligatorio] stringa - Stringa nel formato ISO 8601 Universale DateTime 'aaaa-MM-ggTHH:mm:ss.fffffff'
  - **numberOfDaysToAdd**: [Obbligatorio] intero - Numero di giorni da aggiungere
- `field(fieldName)`
  - **fieldName**: stringa [Obbligatorio] - Nome del [campo](#fields) da recuperare
  - Restituisce il valore di tale campo dalla risorsa che viene valutata dalla condizione If
  - `field` viene principalmente usata con **AuditIfNotExists** e **DeployIfNotExists** per fare riferimento ai campi sulla risorsa che viene valutata. Altre informazioni sono disponibili nell'esempio [DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Restituisce la versione API della richiesta che `2019-09-01`ha attivato la valutazione dei criteri (esempio: ).
    Questa sarà la versione dell'API utilizzata nella richiesta PUT/PATCH per le valutazioni sulla creazione/aggiornamento delle risorse. La versione api più recente viene sempre usata durante la valutazione della conformità sulle risorse esistenti.
  
#### <a name="policy-function-example"></a>Esempio di funzione dei criteri

Questo esempio di regola dei criteri usa la `resourceGroup` funzione risorsa per ottenere la proprietà **name**, in combinazione con la matrice `concat` e la funzione oggetto per creare una condizione `like` che fa in modo che il nome della risorsa inizi con il nome del gruppo di risorse.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Alias

Usare gli alias delle proprietà per accedere alle proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

L'elenco degli alias è in costante crescita. Per scoprire quali alias sono attualmente supportati da Criteri di Azure usare uno dei metodi seguenti:

- Estensione dei criteri di Azure per il codice di Visual Studio (scelta consigliata)Azure Policy extension for Visual Studio Code (recommended)

  Usare [l'estensione Criteri](../how-to/extension-for-vscode.md) di Azure per Visual Studio Code per visualizzare e individuare gli alias per le proprietà delle risorse.

  ![Azure Policy extension for Visual Studio Code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Diagramma delle risorse di Azure

  Utilizzare `project` l'operatore per visualizzare **l'alias** di una risorsa.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informazioni sull'alias [*]

Molti degli alias disponibili hanno una versione che appare come un nome ** \[ \* ** 'normale' e un altro che è collegato ad esso. Ad esempio:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

L'alias 'normale' rappresenta il campo come un singolo valore. Questo campo è per gli scenari di confronto delle corrispondenze esatte quando l'intero set di valori deve essere esattamente come definito, né più né meno.

L'alias ** \[ \* ** consente di eseguire il confronto con il valore di ogni elemento nella matrice e con le proprietà specifiche di ogni elemento. Questo approccio consente di confrontare le proprietà degli elementi per gli scenari 'if', 'if any of'o'. Per scenari più complessi, usare l'espressione della condizione [di conteggio.](#count) Utilizzando **\[\*ipRules**, un esempio potrebbe essere la convalida che ogni _azione_ è _Deny_, ma non preoccuparsi di quante regole esistono o quale sia il _valore_ IP .
Questa regola di esempio verifica la presenza di eventuali corrispondenze di **\[\*\]ipRules .value** in **10.0.4.1** e applica **effectType** solo se non trova almeno una corrispondenza:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Per ulteriori informazioni, vedere [valutazione dell'alias\*[ ]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Iniziative

Le iniziative consentono di raggruppare più definizioni di criteri correlati per semplificare le assegnazioni e la gestione e quindi di usare un gruppo come se fosse un unico elemento. È possibile ad esempio raggruppare tutte le definizioni di criteri relativi ai tag in una singola iniziativa. Invece di assegnare ciascun criterio singolarmente, si applica l'iniziativa.

> [!NOTE]
> Una volta assegnata un'iniziativa, i parametri a livello di iniziativa non possono essere modificati. Per questo motivo, si consiglia di impostare un **defaultValue** quando si definisce il parametro.

L'esempio seguente illustra come creare un'iniziativa per la gestione di due tag: `costCenter` e `productName`. Usa due criteri predefiniti per applicare il valore di tag predefinito.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
