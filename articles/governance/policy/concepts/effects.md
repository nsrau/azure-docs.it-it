---
title: Comprendere il funzionamento degli effetti
description: Le definizioni dei criteri di Azure hanno vari effetti che determinano il modo in cui la conformità viene gestita e segnalata.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 631c941173a500a4159a37c7c31107b9a6eab872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239978"
---
# <a name="understand-azure-policy-effects"></a>Informazioni sugli effetti di Criteri di Azure

Ogni definizione di criteri in Criteri di Azure ha un effetto. Questo effetto determina cosa accade quando viene valutata la corrispondenza della regola dei criteri. Il comportamento degli effetti varia a seconda che riguardino una nuova risorsa, una risorsa aggiornata o una risorsa esistente.

Questi effetti sono attualmente supportati in una definizione di criteri:These effects are currently supported in a policy definition:

- [Aggiungere](#append)
- [Controllo](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Negare](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabili](#disabled)
- [EnforceOPAConstraint (anteprima)EnforceOPAConstraint](#enforceopaconstraint) (preview)
- [EnforceRegoPolicy (anteprima)EnforceRegoPolicy](#enforceregopolicy) (preview)
- [Modificare](#modify)

## <a name="order-of-evaluation"></a>Ordine di valutazione

Le richieste di creare o aggiornare una risorsa tramite Azure Resource Manager vengono valutate per prima cosa da Criteri di Azure. Criteri di Azure crea un elenco di tutte le assegnazioni che si applicano alla risorsa e quindi valuta la risorsa rispetto a ogni definizione. Criteri di Azure elabora diversi degli effetti prima di consegnare la richiesta al provider di risorse appropriato. In questo modo si impedisce l'elaborazione non necessaria da parte di un provider di risorse quando una risorsa non soddisfa i controlli di governance progettati di Criteri di Azure.Doing so prevents unnecessary processing by a Resource Provider when a resource doesn't meet the designed governance controls of Azure Policy.

- **Disabled** viene verificato per primo, per determinare se valutare la regola dei criteri.
- **Append** e **Modify** vengono quindi valutati. Poiché uno dei due potrebbe alterare la richiesta, una modifica apportata potrebbe impedire l'attivazione di un controllo o di negare l'attivazione dell'effetto.
- Successivamente viene valutato **Deny**. La valutazione di Deny prima di Audit impedisce la doppia registrazione di una risorsa non desiderata.
- **Audit** viene quindi valutato prima che la richiesta venga passata al provider di risorse.

Dopo che il provider di risorse restituisce un codice di riuscita, vengono valutati **AuditIfNotExists** e **DeployIfNotExists** per determinare se è necessaria un'ulteriore registrazione della conformità o un'altra azione.

Attualmente non è disponibile alcun ordine di valutazione per gli effetti **EnforceOPAConstraint** o **EnforceRegoPolicy.**

## <a name="disabled"></a>Disabled

Questo effetto è utile per gli scenari di test o quando la definizione dei criteri ha parametrizzato l'effetto. Grazie a questa flessibilità è possibile disabilitare una singola assegnazione invece di disabilitare tutte le assegnazioni di quei criteri.

Un'alternativa all'effetto Disabled è **enforcementMode** impostato sull'assegnazione dei criteri.
Quando **enforcementMode** è _Disabled_, le risorse vengono comunque valutate. La registrazione, ad esempio i registri attività, e l'effetto dei criteri non si verificano. Per ulteriori informazioni, vedere [assegnazione dei criteri - modalità di imposizione](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Accoda

Append viene usato per aggiungere altri campi alla risorsa richiesta durante la creazione o l'aggiornamento. Un esempio comune è la specifica di indirizzi IP consentiti per una risorsa di archiviazione.

> [!IMPORTANT]
> Append è destinato all'uso con le proprietà non tag. Sebbene Append possa aggiungere tag a una risorsa durante una richiesta di creazione o aggiornamento, è consigliabile usare l'effetto [Modifica](#modify) per i tag.

### <a name="append-evaluation"></a>Valutazione di Append

Append viene valutato prima che la richiesta venga elaborata da un provider di risorse durante la creazione o l'aggiornamento di una risorsa. Append aggiunge campi alla risorsa quando viene soddisfatta la condizione **if** della regola dei criteri. Se l'effetto Append sostituisce un valore nella richiesta originale con un valore diverso, agisce come effetto Deny e rifiuta la richiesta. Per aggiungere un nuovo valore a una matrice esistente, utilizzare la versione **[\*]** dell'alias.

Quando una definizione dei criteri che usa l'effetto Append viene eseguita come parte di un ciclo di valutazione, non apporta modifiche alle risorse già esistenti. Al contrario, contrassegna qualsiasi risorsa che soddisfi la condizione **if** come non conforme.

### <a name="append-properties"></a>Proprietà di Append

Un effetto Append ha solo una matrice **details** obbligatoria. Essendo una matrice, **details** può richiedere una coppia **campo/valore** singola o coppie multiple. Fare riferimento alla [struttura di definizione](definition-structure.md#fields) per l'elenco dei campi accettabili.

### <a name="append-examples"></a>Esempi di Append

Esempio 1: coppia **campo singolo/valore** che usa un [alias](definition-structure.md#aliases) non**\*[ ]** con un **valore** di matrice per impostare le regole IP in un account di archiviazione. Quando l'alias non **-\*[ ]** è una matrice, l'effetto aggiunge il **valore** come matrice completa. Se la matrice esiste già, si verifica un evento Deny per effetto del conflitto.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Esempio 2: coppia **campo singolo/valore** che usa un [alias](definition-structure.md#aliases) **\*[ ]** con un **valore** di matrice per impostare le regole IP in un account di archiviazione. Utilizzando l'alias **[\*],** l'effetto aggiunge il **valore** a una matrice potenzialmente preesistente. Se la matrice non esiste ancora, verrà creata.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>Modifica

Modifica viene utilizzato per aggiungere, aggiornare o rimuovere tag in una risorsa durante la creazione o l'aggiornamento. Un esempio comune è l'aggiornamento dei tag su risorse come costCenter.A common example is updating tags on resources such as costCenter. Un criterio di `mode` modifica deve sempre essere impostato su _Indicizzato,_ a meno che la risorsa di destinazione non sia un gruppo di risorse. Le risorse non conformi esistenti possono essere aggiornate con [un'attività](../how-to/remediate-resources.md)di correzione. Una singola regola di modifica può avere un numero qualsiasi di operazioni.

> [!IMPORTANT]
> Modifica è attualmente solo per l'uso con i tag. Se si gestiscono i tag, è consigliabile usare Modifica anziché Aggiungi come modifica offre tipi di operazione aggiuntivi e la possibilità di correggere le risorse esistenti. Tuttavia, l'opzione Aggiungi è consigliata se non è possibile creare un'identità gestita.

### <a name="modify-evaluation"></a>Modificare la valutazione

Modify valuta prima che la richiesta venga elaborata da un provider di risorse durante la creazione o l'aggiornamento di una risorsa. Modifica aggiunge o aggiorna i tag su una risorsa quando viene soddisfatta la condizione **if** della regola dei criteri.

Quando una definizione di criteri che usa l'effetto Modifica viene eseguita come parte di un ciclo di valutazione, non apporta modifiche alle risorse già esistenti. Al contrario, contrassegna qualsiasi risorsa che soddisfi la condizione **if** come non conforme.

### <a name="modify-properties"></a>Modifica di proprietà

La proprietà **details** dell'effetto Modifica dispone di tutte le sottoproprietà che definiscono le autorizzazioni necessarie per la correzione e le **operazioni** utilizzate per aggiungere, aggiornare o rimuovere i valori dei tag.

- **roleDefinitionIds** [required]
  - Questa proprietà deve contenere una matrice di stringhe che corrispondono all'ID ruolo di controllo degli accessi in base al ruolo accessibile dalla sottoscrizione. Per ulteriori informazioni, vedere [correzione - configurare la definizione dei criteri](../how-to/remediate-resources.md#configure-policy-definition).
  - Il ruolo definito deve includere tutte le operazioni concesse al ruolo [Collaboratore.](../../../role-based-access-control/built-in-roles.md#contributor)
- **operazioni** [obbligatorio]
  - Matrice di tutte le operazioni di tag da completare sulle risorse corrispondenti.
  - Proprietà:
    - **operazione** [obbligatorio]
      - Definisce l'azione da eseguire su una risorsa corrispondente. Le opzioni sono: _addOrReplace_, _Add_, _Remove_. _Aggiungete_ si comporta in modo simile all'effetto [Aggiungi.](#append)
    - **campo** [obbligatorio]
      - Tag da aggiungere, sostituire o rimuovere. I nomi dei tag devono rispettare la stessa convenzione di denominazione per altri [campi.](./definition-structure.md#fields)
    - **valore** (opzionale)
      - Valore su cui impostare il tag.
      - Questa proprietà è obbligatoria se **l'operazione** è _addOrReplace_ o _Add_.

### <a name="modify-operations"></a>Modifica delle operazioni

La matrice di proprietà **operations** consente di modificare diversi tag in modi diversi da una singola definizione di criteri. Ogni operazione è costituita da **proprietà operation**, **field**e **value** . Operazione determina le operazioni eseguite dall'attività di correzione per i tag, il campo determina il tag modificato e il valore definisce la nuova impostazione per tale tag. L'esempio seguente apporta le seguenti modifiche ai tag:

- Imposta `environment` il tag su "Test", anche se esiste già con un valore diverso.
- Rimuove il `TempResource`tag .
- Imposta `Dept` il tag sul parametro di criteri _DeptName_ configurato per l'assegnazione dei criteri.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

La proprietà **operation** ha le seguenti opzioni:

|Operazione |Descrizione |
|-|-|
|addOrReplace |Aggiunge il tag e il valore definiti alla risorsa, anche se il tag esiste già con un valore diverso. |
|Add |Aggiunge il tag e il valore definiti alla risorsa. |
|Rimuovere |Rimuove il tag definito dalla risorsa. |

### <a name="modify-examples"></a>Modificare gli esempi

Esempio 1: `environment` aggiungere il `environment` tag e sostituire i tag esistenti con "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Esempio 2: `env` rimuovere il `environment` tag e `environment` aggiungere il tag o sostituire i tag esistenti con un valore con parametri:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Nega

Deny viene usato per impedire una richiesta di risorse che non corrisponde agli standard definiti tramite una definizione dei criteri e che genera un errore della richiesta.

### <a name="deny-evaluation"></a>Valutazione di Deny

Quando si crea o si aggiorna una risorsa corrispondente, Deny impedisce la richiesta prima che sia inviata al provider di risorse. La richiesta viene restituita come `403 (Forbidden)`. Nel portale l'accesso negato può essere visualizzato come stato della distribuzione impedita dall'assegnazione dei criteri.

Durante la valutazione di risorse esistenti, le risorse che corrispondono a una definizione di criteri Deny vengono contrassegnate come non conformi.

### <a name="deny-properties"></a>Proprietà di Deny

L'effetto Deny non ha proprietà aggiuntive da usare nella condizione **then** della definizione dei criteri.

### <a name="deny-example"></a>Esempio di Deny

Esempio: uso dell'effetto Deny.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

Audit viene usato per creare un evento di avviso nel log attività quando viene valutata una risorsa non conforme, ma non arresta la richiesta.

### <a name="audit-evaluation"></a>Valutazione di Audit

Il controllo è l'ultimo effetto controllato da Criteri di Azure durante la creazione o l'aggiornamento di una risorsa. Criteri di Azure invia quindi la risorsa al provider di risorse. Audit funziona allo stesso modo per una richiesta di risorse e un ciclo di valutazione. Criteri di `Microsoft.Authorization/policies/audit/action` Azure aggiunge un'operazione al log attività e contrassegna la risorsa come non conforme.

### <a name="audit-properties"></a>Proprietà di Audit

L'effetto Audit non ha proprietà aggiuntive da usare nella condizione **then** della definizione dei criteri.

### <a name="audit-example"></a>Esempio di Audit

Esempio: uso dell'effetto Audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists consente il controllo sulle risorse che corrispondono alla condizione **if**, ma non ha i componenti specificati in **details** nella condizione **then**.

### <a name="auditifnotexists-evaluation"></a>Valutazione di AuditIfNotExists

AuditIfNotExists viene eseguito dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento di risorse e ha restituito un codice di stato con esito positivo. L'effetto Audit si verifica se non ci sono risorse correlate o se le risorse definite da **ExistenceCondition** non restituiscono true. Criteri di `Microsoft.Authorization/policies/audit/action` Azure aggiunge un'operazione al log attività nello stesso modo dell'effetto di controllo. Quando è attivato, la risorsa che ha soddisfatto la condizione **if** è la risorsa contrassegnata come non conforme.

### <a name="auditifnotexists-properties"></a>Proprietà di AuditIfNotExists

La proprietà **details** degli effetti AuditIfNotExists ha tutte le sottoproprietà che definiscono le risorse correlate a cui corrispondere.

- **Type** [obbligatorio]
  - Specifica il tipo della risorsa correlata a cui corrispondere.
  - Se **details.type** è un tipo di risorsa sotto la risorsa condizione **if,** i criteri eseguono una query per le risorse di questo **tipo** all'interno dell'ambito della risorsa valutata. In caso contrario, le query dei criteri all'interno dello stesso gruppo di risorse della risorsa valutata.
- **Name** (facoltativo)
  - Specifica il nome esatto della risorsa a cui corrispondere e fa sì che il criterio recuperi una risorsa specifica invece di tutte le risorse del tipo specificato.
  - Quando i valori della condizione per **if.field.type** e **then.details.type** corrispondono, **Name** diventa _obbligatorio_ e deve essere `[field('name')]`. Tuttavia, un effetto di [audit](#audit) dovrebbe essere considerato invece.
- **ResourceGroupName** (facoltativo)
  - Consente che la corrispondenza della risorsa correlata provenga da un gruppo di risorse diverso.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - L'impostazione predefinita è il gruppo di risorse della risorsa della condizione **if**.
- **ExistenceScope** (facoltativo)
  - I valori consentiti sono _Subscription_ e _ResourceGroup_.
  - Imposta l'ambito dove recuperare la risorsa correlata a cui corrispondere.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - Per _ResourceGroup_, si limita al gruppo di risorse della risorsa della condizione **if** o al gruppo di risorse specificato in **ResourceGroupName**.
  - Per _Subscription_, esegue una query nell'intera sottoscrizione per la risorsa correlata.
  - L'impostazione predefinita è _ResourceGroup_.
- **ExistenceCondition** (facoltativo)
  - Se non specificato, qualsiasi risorsa correlata di **type** soddisfa l'effetto e non attiva il controllo.
  - Usa lo stesso linguaggio della regola dei criteri per la condizione **if**, ma viene valutato singolarmente rispetto a ogni risorsa correlata.
  - Se qualsiasi risorsa correlata corrispondente restituisce true, l'effetto è soddisfatto e non attiva il controllo.
  - Può usare [field()] per controllare l'equivalenza con i valori nella condizione **if**.
  - Ad esempio, può essere usato per convalidare che la risorsa padre (nella condizione **if**) sia nello stesso percorso della risorsa della risorsa correlata corrispondente.

### <a name="auditifnotexists-example"></a>Esempio di AuditIfNotExists

Esempio: valuta le macchine virtuali per determinare se esiste l'estensione antimalware, quindi consente di controllare quando manca.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Analogamente a AuditIfNotExists, una definizione di criteri DeployIfNotExists esegue una distribuzione del modello quando viene soddisfatta la condizione.

> [!NOTE]
> I [Modelli nidificati](../../../azure-resource-manager/templates/linked-templates.md#nested-template) sono supportati con **deployIfNotExists** ma i [Modelli collegati](../../../azure-resource-manager/templates/linked-templates.md#linked-template) non sono attualmente supportati.

### <a name="deployifnotexists-evaluation"></a>Valutazione di DeployIfNotExists

DeployIfNotExists viene eseguito circa 15 minuti dopo che un provider di risorse ha gestito una richiesta di risorsa di creazione o aggiornamento e ha restituito un codice di stato di esito positivo. Una distribuzione di modello si verifica se non ci sono risorse correlate o se le risorse definite da **ExistenceCondition** non restituiscono true.
La durata della distribuzione dipende dalla complessità delle risorse incluse nel modello.

Durante un ciclo di valutazione, le definizioni dei criteri con un effetto DeployIfNotExists che corrispondono alle risorse vengono contrassegnate come non conformi, ma non vengono eseguite azioni sulla risorsa stessa. Le risorse non conformi esistenti possono essere aggiornate con [un'attività](../how-to/remediate-resources.md)di correzione.

### <a name="deployifnotexists-properties"></a>Proprietà di DeployIfNotExists

La proprietà **details** dell'effetto DeployIfNotExists dispone di tutte le sottoproprietà che definiscono le risorse correlate da confrontare e della distribuzione del modello da eseguire.

- **Type** [obbligatorio]
  - Specifica il tipo della risorsa correlata a cui corrispondere.
  - Inizia cercando di recuperare una risorsa sottostante la risorsa della condizione **if**, quindi esegue una query all'interno dello stesso gruppo di risorse come risorsa della condizione **if**.
- **Name** (facoltativo)
  - Specifica il nome esatto della risorsa a cui corrispondere e fa sì che il criterio recuperi una risorsa specifica invece di tutte le risorse del tipo specificato.
  - Quando i valori della condizione per **if.field.type** e **then.details.type** corrispondono, **Name** diventa _obbligatorio_ e deve essere `[field('name')]`.
- **ResourceGroupName** (facoltativo)
  - Consente che la corrispondenza della risorsa correlata provenga da un gruppo di risorse diverso.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - L'impostazione predefinita è il gruppo di risorse della risorsa della condizione **if**.
  - Se viene eseguita una distribuzione di modelli, la distribuzione avviene nel gruppo di risorse di questo valore.
- **ExistenceScope** (facoltativo)
  - I valori consentiti sono _Subscription_ e _ResourceGroup_.
  - Imposta l'ambito dove recuperare la risorsa correlata a cui corrispondere.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - Per _ResourceGroup_, si limita al gruppo di risorse della risorsa della condizione **if** o al gruppo di risorse specificato in **ResourceGroupName**.
  - Per _Subscription_, esegue una query nell'intera sottoscrizione per la risorsa correlata.
  - L'impostazione predefinita è _ResourceGroup_.
- **ExistenceCondition** (facoltativo)
  - Se non specificato, qualsiasi risorsa correlata di **type** soddisfa l'effetto e non attiva la distribuzione.
  - Usa lo stesso linguaggio della regola dei criteri per la condizione **if**, ma viene valutato singolarmente rispetto a ogni risorsa correlata.
  - Se qualsiasi risorsa correlata corrispondente restituisce true, l'effetto è soddisfatto e non attiva la distribuzione.
  - Può usare [field()] per controllare l'equivalenza con i valori nella condizione **if**.
  - Ad esempio, può essere usato per convalidare che la risorsa padre (nella condizione **if**) sia nello stesso percorso della risorsa della risorsa correlata corrispondente.
- **roleDefinitionIds** [required]
  - Questa proprietà deve contenere una matrice di stringhe che corrispondono all'ID ruolo di controllo degli accessi in base al ruolo accessibile dalla sottoscrizione. Per ulteriori informazioni, vedere [correzione - configurare la definizione dei criteri](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (facoltativo)
  - I valori consentiti sono _Subscription_ e _ResourceGroup_.
  - Imposta il tipo di distribuzione da attivare. _Subscription_ indica una [distribuzione a livello di sottoscrizione](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ indica una distribuzione a un gruppo di risorse.
  - Una proprietà _location_ deve essere specificata in _Deployment_ quando si usano distribuzioni a livello di sottoscrizione.
  - L'impostazione predefinita è _ResourceGroup_.
- **Deployment** [obbligatorio]
  - Questa proprietà deve contenere la distribuzione di modelli completa passata all'API PUT `Microsoft.Resources/deployments`. Per altre informazioni, vedere l'[API REST per le distribuzioni](/rest/api/resources/deployments).

  > [!NOTE]
  > Tutte le funzioni all'interno della proprietà **Deployment** vengono valutate come componenti del modello, non del criterio. L'eccezione è costituita dalla proprietà **parameters** che passa i valori dai criteri al modello. **value** in questa sezione sotto un nome di parametro del modello viene usato per eseguire il passaggio del valore (vedere _fullDbName_ nell'esempio di DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Esempio di DeployIfNotExists

Esempio: valuta i database SQL Server per determinare se transparentDataEncryption è abilitato. In caso contrario, viene eseguita una distribuzione per abilitarlo.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Questo effetto viene utilizzato con `Microsoft.Kubernetes.Data`una *modalità* di definizione dei criteri di . Viene usato per passare le regole di controllo di ammissione Gatekeeper v3 definite con OPA Constraint Framework per [aprire l'agente criteri](https://www.openpolicyagent.org/) (OPA) ai cluster Kubernetes in Azure.It's used to pass Gatekeeper v3 admission control rules defined with [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) to Open Policy Agent (OPA) to Kubernetes clusters on Azure.

> [!NOTE]
> Criteri di [Azure per Kubernetes](aks-engine.md) è in anteprima e supporta solo le definizioni dei criteri predefinite.

### <a name="enforceopaconstraint-evaluation"></a>Valutazione di EnforceOPAConstraint

Il controller di ammissione dell'Agente Criteri aperti valuta qualsiasi nuova richiesta nel cluster in tempo reale.
Ogni 15 minuti viene completata un'analisi completa del cluster e i risultati vengono segnalati aCriteri di Azure.Every 15 minutes, a full scan of the cluster is completed and the results reported to Azure Policy.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint proprietà

La proprietà **details** dell'effetto EnforceOPAConstraint dispone delle sottoproprietà che descrivono la regola di controllo di ammissione gatekeeper v3.

- **constraintTemplate** [obbligatorio]
  - Modello di vincolo CustomResourceDefinition (CRD) che definisce nuovi vincoli. Il modello definisce la logica Rego, lo schema del vincolo e i parametri del vincolo che vengono passati tramite **i valori** dei criteri di Azure.The template defines the Rego logic, the Constraint schema, and the Constraint parameters which are passed via values from Azure Policy.
- **vincolo** [obbligatorio]
  - Implementazione CRD del modello Constraint. Utilizza i parametri `{{ .Values.<valuename> }}`passati tramite i **valori** come . Nell'esempio seguente, questo `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}`sarebbe e .
- **valori** [facoltativo]
  - Definisce i parametri e i valori da passare al vincolo. Ogni valore deve essere presente nel modello di vincolo CRD.

### <a name="enforceopaconstraint-example"></a>Esempio EnforceOPAConstraint

Esempio: regola di controllo di ammissione gatekeeper v3 per impostare i limiti delle risorse di memoria e CPU del contenitore in Kubernetes.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Questo effetto viene utilizzato con `Microsoft.ContainerService.Data`una *modalità* di definizione dei criteri di . Viene utilizzato per passare le regole di controllo di ammissione Gatekeeper v2 definite con [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) a [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) nel servizio Azure [Kubernetes](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> Criteri di [Azure per Kubernetes](rego-for-aks.md) è in anteprima e supporta solo le definizioni dei criteri predefinite. I criteri predefiniti rientrano nella categoria **Kubernetes.** L'effetto **EnforceRegoPolicy** e i relativi criteri di categoria **Kubernetes Service** sono _deprecati._ Utilizzare invece l'effetto [EnforceOPAConstraint](#enforceopaconstraint) aggiornato.

### <a name="enforceregopolicy-evaluation"></a>Valutazione di EnforceRegoPolicyEnforceRegoPolicy evaluation

Il controller di ammissione dell'Agente Criteri aperti valuta qualsiasi nuova richiesta nel cluster in tempo reale.
Ogni 5 minuti viene completata un'analisi completa del cluster e i risultati vengono segnalati aCriteri di Azure.Every 5 minutes, a full scan of the cluster is completed and the results reported to Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy proprietà

La proprietà **details** dell'effetto EnforceRegoPolicy dispone delle sottoproprietà che descrivono la regola di controllo di ammissione gatekeeper v2.

- **policyId** [obbligatorio]
  - Nome univoco passato come parametro alla regola di controllo di ammissione Rego.
- **criterio** [obbligatorio]
  - Specifica l'URI della regola di controllo di ammissione rego.
- **policyParameters** [facoltativo]
  - Definisce i parametri e i valori da passare ai criteri di rigo.

### <a name="enforceregopolicy-example"></a>Esempio EnforceRegoPolicy

Esempio: regola di controllo di ammissione gatekeeper v2 per consentire solo le immagini del contenitore specificate in AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Livelli dei criteri

Una risorsa potrebbe essere interessata da diverse assegnazioni. Queste assegnazioni possono essere nello stesso ambito o in ambiti diversi. È anche probabile che ognuna di queste assegnazioni abbia un effetto diverso definito. La condizione e l'effetto per ogni criterio vengono valutati in modo indipendente. Ad esempio:

- Criterio 1
  - Limita la posizione delle risorse a "westus"
  - Assegnato alla sottoscrizione A
  - Effetto Deny
- Criterio 2
  - Limita la posizione delle risorse a "eastus"
  - Assegnato al gruppo di risorse B nella sottoscrizione A
  - Effetto Audit
  
Questa configurazione produrrebbe il risultato seguente:

- Qualsiasi risorsa già presente nel gruppo di risorse B in "eastus" è conforme al criterio 2 e non conforme al criterio 1
- Qualsiasi risorsa già presente nel gruppo di risorse B non in "eastus" è conforme al criterio 2 e non conforme al criterio 1 se non in "westus"
- Qualsiasi nuova risorsa nella sottoscrizione A non in "westus" viene rifiutata dal criterio 1
- Qualsiasi nuova risorsa nella sottoscrizione A e nel gruppo di risorse B in "westus" è creata e non conforme al criterio 2

Se entrambi i criteri 1 e 2 avessero l'effetto Deny, la situazione diventerebbe la seguente:

- Qualsiasi risorsa già presente nel gruppo di risorse B non in "eastus" non è conforme al criterio 2
- Qualsiasi risorsa già presente nel gruppo di risorse B non in "westus" non è conforme al criterio 1
- Qualsiasi nuova risorsa nella sottoscrizione A non in "westus" viene rifiutata dal criterio 1
- Qualsiasi nuova risorsa nel gruppo di risorse B della sottoscrizione A viene negata

Ogni assegnazione viene valutata singolarmente. Di conseguenza, non c'è alcuna possibilità di inserire per sbaglio una risorsa a causa delle differenze nell'ambito. Il risultato netto della disposizione dei criteri su più livelli o della sovrapposizione dei criteri è considerato **cumulativo più restrittivo**. Ad esempio, se entrambi i criteri 1 e 2 avessero un effetto Deny, una risorsa verrebbe bloccata dai criteri sovrapposti e in conflitto. Se è ancora necessario che la risorsa venga creata nell'ambito di destinazione, rivedere le esclusioni per ogni assegnazione per verificare che i criteri corretti interessino gli ambiti corretti.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
