---
title: Dettagli della struttura delle definizioni dei criteri
description: Descrive come vengono usate le definizioni dei criteri per stabilire convenzioni per le risorse di Azure nell'organizzazione.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 2db91bd1968f816eb2a9320ee81019aeec5d2449
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874000"
---
# <a name="azure-policy-definition-structure"></a>Struttura delle definizioni di criteri di Azure

Criteri di Azure stabilisce le convenzioni per le risorse. Le definizioni di criteri descrivono le [condizioni](#conditions) di conformità delle risorse e l'effetto da eseguire se viene soddisfatta una condizione. Una condizione confronta una proprietà della risorsa [campo](#fields) con un valore obbligatorio. È possibile accedere ai campi delle proprietà delle risorse usando [alias](#aliases). Un campo della proprietà di una risorsa è un campo a valore singolo o una [matrice](#understanding-the--alias) di più valori. La valutazione della condizione è diversa nelle matrici.
Altre informazioni sulle [condizioni](#conditions).

Definendo le convenzioni, è possibile controllare i costi e gestire più facilmente le risorse. È ad esempio possibile specificare che vengano consentiti solo determinati tipi di macchine virtuali. In alternativa, è possibile richiedere che le risorse abbiano un tag specifico. Le assegnazioni dei criteri vengono ereditate dalle risorse figlio. Se un'assegnazione di criteri viene applicata a un gruppo di risorse, è applicabile a tutte le risorse nel gruppo di risorse.

Lo schema _policyRule_ della definizione dei criteri è disponibile qui: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

Per creare una definizione di criterio è possibile usare JSON. La definizione dei criteri contiene gli elementi per:

- nome visualizzato
- description
- mode
- metadata
- parametri
- regola dei criteri
  - valutazione logica
  - effetto

Ad esempio, la notazione JSON seguente illustra un criterio che limita i punti in cui vengono distribuite le risorse:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

I criteri e i modelli predefiniti di criteri di Azure sono disponibili in [esempi di criteri di Azure](../samples/index.md).

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Usare **displayName** e **description** per identificare la definizione dei criteri e fornire il contesto d'uso. **displayName** ha una lunghezza massima di _128_ caratteri e **description** una lunghezza massima di _512_ caratteri.

> [!NOTE]
> Durante la creazione o l'aggiornamento di una definizione dei criteri, **ID**, **tipo** e **nome** sono definiti dalle proprietà esterne a JSON e non sono necessari nel file JSON. Il recupero della definizione dei criteri tramite SDK restituisce le proprietà **id**, **tipo** e **nome** come parte di JSON, ma ognuna è costituita da informazioni di sola lettura correlate alla definizione dei criteri.

## <a name="type"></a>Type

Anche se non è possibile impostare la proprietà **Type** , sono disponibili tre valori restituiti da SDK e visibili nel portale:

- `Builtin`: Le definizioni dei criteri vengono fornite e gestite da Microsoft.
- `Custom`: Il valore di tutte le definizioni dei criteri create dai clienti.
- `Static`: Indica una definizione dei criteri di [conformità normativa](./regulatory-compliance.md) con la **Proprietà**Microsoft. I risultati di conformità per queste definizioni di criteri sono i risultati dei controlli di terze parti nell'infrastruttura Microsoft. Nel portale di Azure, questo valore viene talvolta visualizzato come **gestito da Microsoft**. Per ulteriori informazioni, vedere [responsabilità condivisa nel cloud](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Mode

La **modalità** viene configurata in base al fatto che i criteri siano destinati a una proprietà di Azure Resource Manager o a una proprietà del provider di risorse.

### <a name="resource-manager-modes"></a>Modalità di Resource Manager

La **modalità** determina i tipi di risorse che vengono valutati per una definizione di criteri. Le modalità supportate sono:

- `all`: vengono valutati i gruppi di risorse, le sottoscrizioni e tutti i tipi di risorse
- `indexed`: vengono valutati solo i tipi di risorse che supportano tag e il percorso

Ad esempio, la risorsa `Microsoft.Network/routeTables` supporta i tag e il percorso e viene valutata in entrambe le modalità. Tuttavia, la risorsa `Microsoft.Network/routeTables/routes` non può essere contrassegnata e non viene valutata in modalità `Indexed`.

Nella maggior parte dei casi, è consigliabile impostare il parametro **mode** su `all`. Tutte le definizioni di criteri create tramite il portale usano la modalità `all`. Se si usa PowerShell o l'interfaccia della riga di comando di Azure è necessario specificare il parametro **mode** manualmente. Se la definizione dei criteri non include un valore **mode**, assume il valore predefinito `all` in Azure PowerShell e `null` nell'interfaccia della riga di comando di Azure. Un valore mode `null` equivale all'utilizzo di `indexed` per supportare la compatibilità con le versioni precedenti.

`indexed` deve essere usato durante la creazione di criteri che applicano tag o percorsi. Sebbene non sia necessario, evita che le risorse che non supportano tag e percorsi vengano visualizzate come non conformi nei risultati sulla conformità. L'eccezione è rappresentata dai **gruppi di risorse** e dalle **sottoscrizioni**. Per le definizioni dei criteri che applicano percorsi o tag a un gruppo di risorse o a una sottoscrizione, impostare il parametro **mode** su `all` e specificare una destinazione specifica per il tipo `Microsoft.Resources/subscriptions/resourceGroups` o `Microsoft.Resources/subscriptions`. Per un esempio, vedere [Modello: Tag - Esempio n. 1](../samples/pattern-tags.md). Per un elenco di risorse che supportano i tag, vedere [Supporto dei tag per le risorse di Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Modalità del provider di risorse

La modalità del provider di risorse seguente è completamente supportata:

- `Microsoft.Kubernetes.Data` per la gestione dei cluster Kubernetes all'interno o all'esterno di Azure. Le definizioni che usano questa modalità del provider di risorse usano gli effetti _Audit_, _Deny_e _disabled_. L'uso dell'effetto [EnforceOPAConstraint](./effects.md#enforceopaconstraint) è _deprecato_.

Le modalità del provider di risorse seguenti sono attualmente supportate come **Anteprima**:

- `Microsoft.ContainerService.Data` per la gestione delle regole del controller di ammissione nel [servizio Azure Kubernetes](../../../aks/intro-kubernetes.md). Le definizioni che usano questa modalità del provider di risorse **devono** usare l'effetto [EnforceRegoPolicy](./effects.md#enforceregopolicy) . Questa modalità è _deprecata_.
- `Microsoft.KeyVault.Data` per la gestione di insiemi di credenziali e certificati in [Azure Key Vault](../../../key-vault/general/overview.md).

> [!NOTE]
> Le modalità del provider di risorse supportano solo le definizioni dei criteri predefinite.

## <a name="metadata"></a>Metadati

La proprietà facoltativa `metadata` archivia le informazioni sulla definizione dei criteri. I clienti possono definire le proprietà e i valori utili per la propria organizzazione in `metadata` . Tuttavia, esistono alcune proprietà _comuni_ utilizzate dai criteri di Azure e in quelli predefiniti.

### <a name="common-metadata-properties"></a>Proprietà dei metadati comuni

- `version` (String): tiene traccia dei dettagli sulla versione del contenuto di una definizione dei criteri.
- `category` (String): determina in quale categoria portale di Azure viene visualizzata la definizione dei criteri.
- `preview` (booleano): true o false flag per se la definizione dei criteri è _Preview_.
- `deprecated` (booleano): true o false flag per se la definizione dei criteri è stata contrassegnata come _deprecata_.

> [!NOTE]
> Il servizio Criteri di Azure usa le proprietà `version`, `preview` e `deprecated` per fornire il livello di modifica a una definizione o un'iniziativa di criteri predefinita e uno stato. Il formato di `version` è: `{Major}.{Minor}.{Patch}`. Gli stati specifici, ad esempio _deprecato_ o _anteprima_, vengono aggiunti alla proprietà `version` o a un'altra proprietà come **booleano**. Per altre informazioni sul modo in cui le versioni di criteri di Azure sono predefinite, vedere controllo delle versioni [predefinito](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametri

I parametri consentono di semplificare la gestione dei criteri, riducendone il numero di definizioni. I parametri possono essere paragonati ai campi di un modulo: `name`, `address`, `city`, `state`. Questi parametri rimangono sempre invariati, ma i loro valori cambiano a seconda dei dati immessi durante la compilazione del modulo da parte dei singoli utenti.
I parametri funzionano nello stesso modo durante la creazione di criteri. L'inclusione dei parametri in una definizione dei criteri consente di riutilizzare i singoli criteri in vari scenari mediante l'uso di valori diversi.

> [!NOTE]
> I parametri possono essere aggiunti a una definizione esistente e assegnata. Il nuovo parametro deve includere la proprietà **defaultValue**. In questo modo le assegnazioni esistenti dei criteri o dell'iniziativa non possono essere rese indirettamente non valide.

### <a name="parameter-properties"></a>Proprietà parametro

Un parametro presenta le proprietà seguenti, usate nella definizione di criteri:

- `name`: Nome del parametro. Usato dalla funzione di distribuzione `parameters` all'interno della regola dei criteri. Per altre informazioni, vedere [Usare un valore di parametro](#using-a-parameter-value).
- `type`: Determina se il parametro è una **stringa**, **matrice**, **oggetto**, **booleano**, **intero**, **float** o **datetime**.
- `metadata`: definisce le sottoproprietà usate principalmente dal portale di Azure per visualizzare informazioni di tipo descrittivo:
  - `description`: la spiegazione di ciò per cui viene usato il parametro. Può essere usata per fornire esempi di valori accettabili.
  - `displayName`: il nome descrittivo visualizzato per il parametro nel portale.
  - `strongType`: (facoltativa) usata quando si assegna la definizione di criteri tramite portale. Fornisce un elenco con riconoscimento del contesto. Per altre informazioni, vedere [strongType](#strongtype).
  - `assignPermissions`: (Facoltativo) Impostare come _true_ per consentire al portale di Azure di creare assegnazioni di ruolo durante l'assegnazione dei criteri. Questa proprietà è utile nel caso in cui si desideri assegnare autorizzazioni al di fuori dell'ambito di assegnazione. È disponibile un'assegnazione di ruolo per ogni definizione del ruolo nel criterio (o per definizione del ruolo in tutti i criteri dell'iniziativa). Il valore del parametro deve essere una risorsa o un ambito valido.
- `defaultValue`: (facoltativa) imposta il valore del parametro in un'assegnazione se non viene specificato alcun valore.
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

Nella regola dei criteri, fare riferimento ai parametri con la sintassi della funzione `parameters` seguente:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Questo esempio fa riferimento al parametro **allowedLocations** illustrato nella sezione relativa alle [proprietà dei parametri](#parameter-properties).

### <a name="strongtype"></a>strongType

Nella proprietà `metadata` è possibile usare **strongType** per fornire un elenco di opzioni di selezione multipla nel portale di Azure. **strongType** può essere un _tipo di risorsa_ supportato o un valore consentito. Per determinare se un _tipo di risorsa_ è valido per **strongType**, usare [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). Il formato di un _tipo di risorsa_ **strongType** è `<Resource Provider>/<Resource Type>` . Ad esempio: `Microsoft.Network/virtualNetworks/subnets`.

Alcuni _tipi di risorse_ non restituiti da **Get-AzResourceProvider** sono supportati. Questi tipi sono:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

I valori non _tipo di risorsa_ consentiti per **strongType** sono:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Posizione della definizione

Durante la creazione di iniziative o criteri è importante specificare la posizione della definizione. La posizione della definizione deve essere specificata come un gruppo di gestione o una sottoscrizione. Tale posizione determina l'ambito al quale la definizione delle iniziative o dei criteri può essere assegnata. Le risorse devono essere membri diretti o elementi figli all'interno della gerarchia della posizione della definizione da destinare all'assegnazione.

Se la posizione della definizione è:

- È possibile assegnare la definizione dei criteri alle risorse solo della **sottoscrizione** all'interno di tale sottoscrizione.
- **Gruppo di gestione** : è possibile assegnare la definizione dei criteri solo alle risorse solo nei gruppi di gestione figlio e nelle sottoscrizioni figlio. Se si prevede di applicare la definizione dei criteri a diverse sottoscrizioni, il percorso deve essere un gruppo di gestione che contiene ogni sottoscrizione.

Per altre informazioni, vedere [comprendere l'ambito nei criteri di Azure](./scope.md#definition-location).

## <a name="policy-rule"></a>Regola dei criteri

La regola dei criteri è costituita dai blocchi **If** e **Then** blocchi. Nel blocco **If**, si definiscono una o più condizioni che specificano quando i criteri vengono applicati. È possibile applicare gli operatori logici a queste condizioni per definire con precisione lo scenario di un criterio.

Nel blocco **Then** si definisce l'effetto che si verifica quando le condizioni **If** sono soddisfatte.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
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
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

Per **less**, **lessOrEquals**, **greater** e **greaterOrEquals**, se il tipo di proprietà non corrisponde al tipo di condizione, viene generato un errore. I confronti tra stringhe vengono eseguiti usando `InvariantCultureIgnoreCase`.

Quando si usano le condizioni **like** e **notLike**, è possibile inserire un carattere jolly `*` nel valore.
Il valore non deve contenere più di un carattere jolly `*`.

Quando si usano le condizioni **match** e **notMatch**, specificare `#` per rappresentare una cifra, `?` per rappresentare una lettera, `.` per rappresentare tutti i caratteri e qualunque altro carattere per rappresentare il carattere effettivo. Mentre **match** e **notMatch** fanno distinzione tra maiuscole e minuscole, tutte le altre condizioni che valutano un _StringValue_ non fanno distinzione tra maiuscole e minuscole. Alternative senza distinzione tra maiuscole e minuscole sono disponibili in **matchInsensitively** e **notMatchInsensitively**.

In un valore campo matrice **alias \[\*\]** , ogni elemento nella matrice viene valutato singolarmente con **and** logico tra gli elementi. Per altre informazioni, vedere [Valutazione dell'alias \[\*\]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

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
  - Restituisce il tipo di [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md) abilitata per la risorsa.
- `tags`
- `tags['<tagName>']`
  - Questa sintassi tra parentesi quadre supporta nomi di tag con segni di punteggiatura, ad esempio un trattino, punti o spazi.
  - Dove **\<tagName\>** è il nome del tag per cui convalidare la condizione.
  - Esempi: `tags['Acct.CostCenter']` dove **Acct.CostCenter** è il nome del tag.
- `tags['''<tagName>''']`
  - Questa sintassi tra parentesi quadre supporta nomi di tag contenenti apostrofi eseguendo l'escape con esso in caratteri di escape con apostrofi doppi.
  - Dove **' \<tagName\> '** è il nome del tag per cui convalidare la condizione.
  - Esempio: `tags['''My.Apostrophe.Tag''']` dove **'My.Apostrophe.Tag'** è il nome del tag.
- alias delle proprietà; per un elenco, vedere [alias](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` e `tags[tag.with.dots]` sono comunque modi accettabili per dichiarare un campo tags. Tuttavia, le espressioni preferibili sono quelle elencate in precedenza.

#### <a name="use-tags-with-parameters"></a>Usare tag con parametri

È possibile passare a un campo di tag un valore di parametro. Passare un parametro a un campo di tag aumenta la flessibilità della definizione dei criteri durante l'assegnazione dei criteri.

Nell'esempio seguente, `concat` viene usato per creare una ricerca nei campi di tag per il tag che ha come nome il valore del parametro **tagName**. Se il tag non esiste, viene usato l'effetto **modify** per aggiungere il tag usando il valore del tag con lo stesso nome impostato sul gruppo di risorse padre delle risorse controllate tramite la funzione di ricerca `resourcegroup()`.

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

### <a name="value"></a>valore

Le condizioni possono essere formate anche usando **value**. **value** controlla le condizioni rispetto a [parametri](#parameters), [funzioni di modello supportate](#policy-functions) o valori letterali. **value** è associato a qualsiasi [condizione](#conditions) supportata.

> [!WARNING]
> Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri non riesce. Una valutazione non riuscita è un risultato **deny** implicito. Per altre informazioni, vedere come [evitare errori dei modelli](#avoiding-template-failures). Usare [enforcementMode](./assignment-structure.md#enforcement-mode) di **DoNotEnforce** per evitare l'effetto di una valutazione non riuscita su risorse nuove o aggiornate durante il test e la convalida di una nuova definizione dei criteri.

#### <a name="value-examples"></a>Esempi d'uso di value

Questa regola dei criteri usa **value** per confrontare il risultato della funzione `resourceGroup()` e la proprietà restituita **name** rispetto a una condizione **like** di `*netrg`. La regola respinge qualunque risorsa non del **type** `Microsoft.Network/*` in qualunque gruppo di risorse il cui nome termina con `*netrg`.

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

Questa regola dei criteri usa **value** per verificare se il risultato di più funzioni annidate **equals** `true`. La regola respinge qualsiasi risorsa che non dispone di almeno tre tag.

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

#### <a name="avoiding-template-failures"></a>Evitare gli errori dei modelli

L'uso di _funzioni modello_ in **value** consente numerose funzioni annidate complesse. Se il risultato di una _funzione modello_ è un errore, la valutazione dei criteri non riesce. Una valutazione non riuscita è un risultato **deny** implicito. Un esempio di **value** che ha esito negativo in determinati scenari:

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

La regola dei criteri di esempio precedente usa [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) per confrontare i primi tre caratteri di **name** con **abc**. Se **name** è più breve di tre caratteri, la funzione `substring()` genera un errore. Questo errore fa sì che il criterio diventi un effetto **deny**.

Usare invece la funzione [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) per verificare se i primi tre caratteri di **name** sono uguali ad **abc** senza consentire che un **name** più breve di tre caratteri generi un errore:

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

Con la regola dei criteri modificata, `if()` controlla la lunghezza di **name** prima di provare a ottenere un `substring()` su un valore con meno di tre caratteri. Se **name** è troppo breve, viene invece restituito il valore "not starting with abc" e viene confrontato con **abc**. Una risorsa con un nome breve che non inizia con **abc** non soddisfa ancora la regola dei criteri, ma non causa più un errore durante la valutazione.

### <a name="count"></a>Conteggio

Le condizioni che contano il numero di membri di una matrice nel payload di risorse che soddisfano un'espressione di condizione possono essere create usando l'espressione **count**. Gli scenari comuni controllano se "almeno uno", "esattamente uno", "tutti" o "nessuno" dei membri della matrice soddisfano la condizione. **count** valuta ogni [alias \[\*\]](#understanding-the--alias) membro della matrice per un'espressione della condizione e somma i risultati _true_, che vengono quindi confrontati con l'operatore dell'espressione. Le espressioni **count** possono essere aggiunte fino a tre volte a una singola definizione **policyRule** .

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

Le proprietà seguenti vengono usate con **count**:

- **count.field** (obbligatorio): Contiene il percorso della matrice e deve essere un alias di matrice. Se la matrice non è presente, l'espressione viene valutata come _false_ senza considerare l'espressione della condizione.
- **count.where** (facoltativo): L'espressione della condizione per valutare individualmente ogni membro della matrice [alias \[\*\]](#understanding-the--alias) di **count.field**. Se questa proprietà non viene specificata, tutti i membri della matrice con il percorso ' Field ' verranno valutati come _true_. All'interno di questa proprietà è possibile usare qualunque [condizione](../concepts/definition-structure.md#conditions).
  [Gli operatori logici](#logical-operators) possono essere usati all'interno di questa proprietà per creare requisiti di valutazione complessi.
- **\<condition\>** (obbligatorio): il valore viene confrontato con il numero di elementi che soddisfano l'espressione della condizione **Count. Where** . È necessario usare una [condizione](../concepts/definition-structure.md#conditions) numerica.

#### <a name="count-examples"></a>Esempi di count

Esempio 1: Verificare se una matrice è vuota

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Esempio 2: Verificare che un solo membro della matrice soddisfi l'espressione della condizione

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

Esempio 3: Verificare che almeno un membro della matrice soddisfi l'espressione della condizione

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

Esempio 4: Verificare che tutti i membri della matrice di oggetti soddisfino l'espressione della condizione

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

Esempio 5: verificare che almeno un membro della matrice corrisponda a più proprietà nell'espressione della condizione

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

Criteri di Azure supporta i tipi di effetto seguenti:

- **Append**: aggiunge il set di campi definiti alla richiesta
- **Audit**: genera un evento di avviso nel log attività, ma non nega la richiesta
- **AuditIfNotExists**: genera un evento di avviso nel log attività se una risorsa correlata non esiste
- **Deny**: genera un evento nel log attività e nega la richiesta
- **DeployIfNotExists**: distribuisce una risorsa correlata se non esiste già
- **Disabled**: non valuta le risorse per garantire la conformità alla regola dei criteri
- **Modify**: aggiunge, aggiorna o rimuove i tag definiti da una risorsa
- **EnforceOPAConstraint** (deprecato): configura il controller di ammissione di agenti criteri aperti con gatekeeper V3 per i cluster Kubernetes autogestiti in Azure
- **EnforceRegoPolicy** (deprecato): configura il controller di ammissione di agenti criteri aperti con gatekeeper V2 nel servizio Azure Kubernetes

Per informazioni dettagliate su ogni effetto, ordine di valutazione, proprietà ed esempi, vedere [Informazioni sugli effetti di Criteri di Azure](effects.md).

### <a name="policy-functions"></a>Funzioni dei criteri

Tutte le [funzioni del modello di Resource Manager](../../../azure-resource-manager/templates/template-functions.md) sono disponibili per l'uso all'interno di una regola dei criteri, ad eccezione delle seguenti funzioni e funzioni definite dall'utente:

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> Queste funzioni sono ancora disponibili all'interno della porzione `details.deployment.properties.template` della distribuzione del modello in una definizione dei criteri **deployIfNotExists**.

La funzione seguente è disponibile per l'uso in una regola dei criteri, ma è diversa da quella usata in un modello di Azure Resource Manager (modello ARM):

- `utcNow()` -Diversamente da un modello ARM, questa proprietà può essere usata all'esterno di _DefaultValue_.
  - Restituisce una stringa impostata sulla data e sull'ora correnti nel formato DateTime ISO 8601 universale `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Le funzioni seguenti sono disponibili solo nelle regole dei criteri:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [Required] stringa stringa nel formato DateTime universale ISO 8601 `yyyy-MM-ddTHH:mm:ss.fffffffZ` .
  - **numberOfDaysToAdd**: [Required] numero intero di giorni da aggiungere.
- `field(fieldName)`
  - **fieldName**: [Obbligatorio] stringa - Nome del [campo](#fields) da recuperare
  - Restituisce il valore di tale campo dalla risorsa valutata dalla condizione If.
  - `field` viene principalmente usata con **AuditIfNotExists** e **DeployIfNotExists** per fare riferimento ai campi sulla risorsa che viene valutata. Altre informazioni sono disponibili nell'esempio [DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Restituisce la versione dell'API della richiesta che ha attivato la valutazione del criterio, ad esempio: `2019-09-01`.
    Questo valore è la versione dell'API usata nella richiesta PUT/PATCH per le valutazioni per la creazione o l'aggiornamento di risorse. La versione più recente dell'API viene sempre usata durante la valutazione della conformità sulle risorse esistenti.
- `policy()`
  - Restituisce le informazioni seguenti relative ai criteri valutati. È possibile accedere alle proprietà dall'oggetto restituito, ad esempio: `[policy().assignmentId]` .
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```
  
  
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

- Estensione di Criteri di Azure per Visual Studio Code (scelta consigliata)

  Usare l'[estensione Criteri di Azure per Visual Studio Code](../how-to/extension-for-vscode.md) per visualizzare e individuare gli alias per le proprietà delle risorse.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Screenshot dell'estensione di criteri di Azure per Visual Studio Code il passaggio del mouse su una proprietà per visualizzare i nomi degli alias." border="false":::

- Diagramma delle risorse di Azure

  Usare l'operatore `project` per visualizzare l'**alias** di una risorsa.

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

  > [!NOTE]
  > Per trovare gli alias che possono essere usati con l'effetto di [modifica](./effects.md#modify) , usare il comando seguente in Azure PowerShell **4.6.0** o versione successiva:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

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
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informazioni sull'alias [*]

Molti degli alias disponibili hanno una versione che viene visualizzata come un nome "normale" e un'altra a cui viene aggiunto **\[\*\]** . Ad esempio:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

L'alias "normal" rappresenta il campo come valore singolo. Questo campo è per gli scenari di confronto con corrispondenza esatta quando l'intero set di valori deve essere esattamente come definito, non più e non meno.

L'alias **\[\*\]** rende possibile il confronto con il valore di ogni elemento nella matrice e proprietà specifiche di ogni elemento. Questo approccio consente di confrontare le proprietà degli elementi per gli scenari "se nessuno", "se ogni" o "se tutti". Per scenari più complessi, usare l'espressione della condizione [count](#count). Usando **ipRules\[\*\]** , un esempio sarebbe la convalida che ogni _azione_ è _Deny_, ma senza preoccuparsi del numero di regole esistenti o del _value_ IP.
Questa regola di esempio controlla la presenza di eventuali corrispondenze di **ipRules\[\*\].value** con **10.0.4.1** e applica il l'**effectType** solo se non trova almeno una corrispondenza:

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

Per altre informazioni, vedere [Valutazione dell'alias [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [struttura della definizione dell'iniziativa](./initiative-definition-structure.md)
- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
