---
title: Dettagli della struttura della definizione dell'iniziativa
description: Viene descritto come vengono usate le definizioni delle iniziative di criteri per raggruppare le definizioni di criteri per la distribuzione nelle risorse di Azure dell'organizzazione.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 80fa90765caa25d6995220134b9a5b4225133219
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84205872"
---
# <a name="azure-policy-initiative-definition-structure"></a>Struttura di definizione di Azure Policy Initiative

Le iniziative consentono di raggruppare più definizioni di criteri correlati per semplificare le assegnazioni e la gestione e quindi di usare un gruppo come se fosse un unico elemento. È possibile ad esempio raggruppare tutte le definizioni di criteri relativi ai tag in una singola iniziativa. Invece di assegnare ciascun criterio singolarmente, si applica l'iniziativa.

Si usa JSON per creare una definizione di iniziativa di criteri. La definizione di iniziativa relativa ai criteri contiene elementi per:

- nome visualizzato
- description
- metadata
- parametri
- definizioni dei criteri
- gruppi di criteri (questa proprietà fa parte della [funzionalità di conformità alle normative (anteprima)](./regulatory-compliance.md))

L'esempio seguente illustra come creare un'iniziativa per la gestione di due tag: `costCenter` e `productName`. Usa due criteri predefiniti per applicare il valore di tag predefinito.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
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

I criteri e i modelli predefiniti di criteri di Azure sono disponibili in [esempi di criteri di Azure](../samples/index.md).

## <a name="metadata"></a>Metadati

La proprietà facoltativa `metadata` archivia le informazioni sulla definizione di iniziativa dei criteri.
I clienti possono definire le proprietà e i valori utili per la propria organizzazione in `metadata` . Tuttavia, esistono alcune proprietà _comuni_ utilizzate dai criteri di Azure e in quelli predefiniti.

### <a name="common-metadata-properties"></a>Proprietà dei metadati comuni

- `version`(String): tiene traccia dei dettagli sulla versione del contenuto di una definizione di iniziativa di criteri.
- `category`(String): determina in quale categoria portale di Azure viene visualizzata la definizione dei criteri.

  > [!NOTE]
  > Per un'iniziativa di [conformità alle normative](./regulatory-compliance.md) , `category` deve essere una **conformità normativa**.

- `preview`(booleano): true o false flag per se la definizione di iniziativa Policy è _Preview_.
- `deprecated`(booleano): true o false flag per se la definizione di iniziativa Policy è stata contrassegnata come _deprecata_.

> [!NOTE]
> Il servizio Criteri di Azure usa le proprietà `version`, `preview` e `deprecated` per fornire il livello di modifica a una definizione o un'iniziativa di criteri predefinita e uno stato. Il formato di `version` è: `{Major}.{Minor}.{Patch}`. Gli stati specifici, ad esempio _deprecato_ o _anteprima_, vengono aggiunti alla proprietà `version` o a un'altra proprietà come **booleano**. Per altre informazioni sul modo in cui le versioni di criteri di Azure sono predefinite, vedere controllo delle versioni [predefinito](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametri

I parametri consentono di semplificare la gestione dei criteri, riducendone il numero di definizioni. I parametri possono essere paragonati ai campi di un modulo: `name`, `address`, `city`, `state`. Questi parametri rimangono sempre invariati, ma i loro valori cambiano a seconda dei dati immessi durante la compilazione del modulo da parte dei singoli utenti.
I parametri funzionano nello stesso modo quando si compilano le iniziative dei criteri. Includendo parametri in una definizione di iniziativa di criteri, è possibile riutilizzare tale parametro nei criteri inclusi.

> [!NOTE]
> Una volta assegnata un'iniziativa, non è possibile modificare i parametri del livello iniziativa. Per questo motivo, è consigliabile impostare un **defaultValue** quando si definisce il parametro.

### <a name="parameter-properties"></a>Proprietà parametro

Un parametro presenta le proprietà seguenti che vengono usate nella definizione di iniziativa Policy:

- `name`: Nome del parametro. Usato dalla funzione di distribuzione `parameters` all'interno della regola dei criteri. Per altre informazioni, vedere [Usare un valore di parametro](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Determina se il parametro è una **stringa**, **matrice**, **oggetto**, **booleano**, **intero**, **float** o **datetime**.
- `metadata`: definisce le sottoproprietà usate principalmente dal portale di Azure per visualizzare informazioni di tipo descrittivo:
  - `description`: la spiegazione di ciò per cui viene usato il parametro. Può essere usata per fornire esempi di valori accettabili.
  - `displayName`: il nome descrittivo visualizzato per il parametro nel portale.
  - `strongType`: (facoltativa) usata quando si assegna la definizione di criteri tramite portale. Fornisce un elenco con riconoscimento del contesto. Per altre informazioni, vedere [strongType](#strongtype).
- `defaultValue`: (facoltativa) imposta il valore del parametro in un'assegnazione se non viene specificato alcun valore.
- `allowedValues`: (Facoltativo) Fornisce una matrice di valori accettati dal parametro durante l'assegnazione.

Ad esempio, è possibile definire una definizione di iniziativa di criteri per limitare i percorsi delle risorse nelle varie definizioni di criteri incluse. Un parametro per la definizione di iniziativa di criteri potrebbe essere **allowedLocations**. Il parametro viene quindi fornito a ogni definizione di criteri inclusa e definito durante l'assegnazione dell'iniziativa di criteri.

```json
"parameters": {
    "init_allowedLocations": {
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

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Passaggio di un valore di parametro a una definizione di criteri

Si dichiarano i parametri Initiative passati a che includono le definizioni dei criteri nella matrice [PolicyDefinitions](#policy-definitions) della definizione Initiative. Sebbene il nome del parametro possa essere lo stesso, l'uso di nomi diversi nelle iniziative che nelle definizioni dei criteri semplifica la leggibilità del codice.

Il parametro **init_allowedLocations** Initiative definito in precedenza, ad esempio, può essere passato a diverse definizioni di criteri incluse e ai relativi parametri, **sql_locations** e **vm_locations**, come segue:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Questo esempio fa riferimento al parametro **init_allowedLocations** dimostrato nelle [proprietà dei parametri](#parameter-properties).

### <a name="strongtype"></a>strongType

Nella proprietà `metadata` è possibile usare **strongType** per fornire un elenco di opzioni di selezione multipla nel portale di Azure. **strongType** può essere un _tipo di risorsa_ supportato o un valore consentito. Per determinare se un _tipo di risorsa_ è valido per **strongType**, usare [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Alcuni tipi di risorse non restituiti da **Get-AzResourceProvider** sono supportati. Questi tipi di risorse sono:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

I valori non consentiti per il tipo di risorsa per **strongType** sono:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Definizioni dei criteri

La `policyDefinitions` parte della definizione Initiative è una _matrice_ in cui sono incluse le definizioni dei criteri esistenti nell'iniziativa. Come indicato nel [passaggio di un valore di parametro alla definizione di un criterio](#passing-a-parameter-value-to-a-policy-definition), questa proprietà è la posizione in cui i [parametri di iniziativa](#parameters) vengono passati alla definizione dei criteri.

### <a name="policy-definition-properties"></a>Proprietà definizione criteri

Ogni elemento della _matrice_ che rappresenta una definizione di criteri presenta le proprietà seguenti:

- `policyDefinitionId`(String): ID della definizione di criteri personalizzata o predefinita da includere.
- `policyDefinitionReferenceId`(String): nome breve per la definizione dei criteri inclusa.
- `parameters`: (Facoltativo) coppie nome/valore per il passaggio di un parametro Initiative alla definizione di criteri inclusa come proprietà nella definizione dei criteri. Per altre informazioni, vedere [Parametri](#parameters).
- `groupNames`(matrice di stringhe): (facoltativo) gruppo di cui è membro la definizione dei criteri. Per ulteriori informazioni, vedere [gruppi di criteri](#policy-definition-groups).

Di seguito è riportato un esempio di `policyDefinitions` che include due definizioni di criteri incluse ognuna delle quali ha superato lo stesso parametro Initiative:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definitions-groups-preview"></a><a name="policy-definition-groups"></a>Gruppi di definizioni dei criteri (anteprima)

Come parte della funzionalità di [conformità normativa](./regulatory-compliance.md) di criteri di Azure (anteprima), le definizioni dei criteri in una definizione di iniziativa possono essere raggruppate. Queste informazioni sono definite nella proprietà `policyDefinitionGroups` _Array_ . In questi raggruppamenti sono disponibili ulteriori dettagli, ad esempio il dominio di **controllo** e di **conformità** che la definizione dei criteri fornisce per la copertura.
È possibile trovare ulteriori dettagli sul raggruppamento in un oggetto **policyMetadata** creato da Microsoft. Per informazioni, vedere [oggetti di metadati](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Parametri dei gruppi di definizioni dei criteri

Ogni elemento della _matrice_ in `policyDefinitionGroups` deve avere entrambe le proprietà seguenti:

- `name`(stringa) \[ Required \] : nome breve per il **controllo**. Il valore di questa proprietà viene utilizzato da `groupNames` in `policyDefinitions` .
- `category`(String): **dominio di conformità** del controllo.
- `displayName`(String): nome descrittivo per il **controllo**. Usato dal portale.
- `description`(String): Descrizione della funzione del **controllo** .
- `additionalMetadataId`(String): la posizione dell'oggetto [policyMetadata](#metadata-objects) con ulteriori dettagli sul dominio di **controllo** e **conformità**.

  > [!NOTE]
  > I clienti possono puntare a un oggetto [policyMetadata](#metadata-objects) esistente. Tuttavia, questi oggetti sono di sola _lettura_ e vengono creati solo da Microsoft.

Un esempio della `policyDefinitionGroups` proprietà della definizione di iniziativa predefinita NIST è simile al seguente:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Oggetti metadati

Le informazioni aggiuntive sulla conformità alle normative create da Microsoft includono informazioni aggiuntive su ogni controllo.
Queste informazioni sono:

- Visualizzato nella portale di Azure Panoramica di un **controllo** in un'iniziativa di conformità normativa.
- Disponibile tramite l'API REST. Vedere il `Microsoft.PolicyInsights` provider di risorse e il [gruppo di operazioni policyMetadata](/rest/api/policy-insights/policymetadata/getresource).
- Disponibile tramite l'interfaccia della riga di comando di Azure. Vedere il comando [AZ Policy Metadata](/cli/azure/policy/metadata?view=azure-cli-latest) .

> [!IMPORTANT]
> Gli oggetti di metadati per la conformità alle normative sono di sola _lettura_ e non possono essere creati dai clienti.

I metadati per un raggruppamento di criteri hanno le informazioni seguenti nel `properties` nodo:

- `metadataId`: **ID del controllo** a cui si riferisce il raggruppamento.
- `category`(obbligatorio): il **dominio di conformità** a cui appartiene il **controllo** .
- `title`(obbligatorio): nome descrittivo dell' **ID del controllo**.
- `owner`(obbligatorio): identifica chi ha la responsabilità del controllo in Azure: _Customer_, _Microsoft_, _Shared_.
- `description`: Informazioni aggiuntive sul controllo.
- `requirements`: Informazioni dettagliate sulla responsabilità dell'implementazione del controllo.
- `additionalContentUrl`: Un collegamento ad altre informazioni sul controllo. Questa proprietà è in genere un collegamento alla sezione della documentazione che copre questo controllo nello standard di conformità.

Di seguito è riportato un esempio dell'oggetto **policyMetadata** . Questo esempio di metadati appartiene al controllo _NIST SP 800-53 R4 AC-1_ .

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [struttura della definizione](./definition-structure.md)
- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
