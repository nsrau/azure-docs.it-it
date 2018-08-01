---
title: Informazioni sugli effetti di Criteri di Azure
description: La definizione di Criteri di Azure ha diversi effetti che determinano in che modo viene gestita e segnalata la conformità.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 17ad631e2441e4b8d6314557c17be143fd2f3de0
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248727"
---
# <a name="understanding-policy-effects"></a>Informazioni sugli effetti di Criteri

Ogni definizione dei criteri in Criteri di Azure ha uno specifico effetto che determina cosa accade durante l'analisi quando il segmento **if** della regola dei criteri viene valutato corrispondente alla risorsa analizzata. Gli effetti possono avere anche un comportamento diverso se riguardano una nuova risorsa, una risorsa aggiornata o una risorsa esistente.

Attualmente sono disponibili cinque effetti supportati in una definizione dei criteri:

- Append
- Audit
- AuditIfNotExists
- Nega
- DeployIfNotExists (disponibile solo per criteri **integrati**)

## <a name="order-of-evaluation"></a>Ordine di valutazione

Quando viene effettuata una richiesta per la creazione o l'aggiornamento di una risorsa tramite Azure Resource Manager, Criteri elabora alcuni degli effetti prima di trasmettere la richiesta al provider di risorse appropriato.
Questa operazione impedisce l'elaborazione non necessaria da parte di un provider di risorse quando una risorsa non soddisfa i controlli di governance progettati di Criteri. Criteri crea un elenco di tutte le definizioni dei criteri assegnate, da un'assegnazione di un criterio o di un'iniziativa, applicabili dall'ambito (meno le esclusioni) alla risorsa e si prepara a valutare la risorsa in base a ogni definizione.

- **Append** viene valutato per primo. Dal momento che Append può alterare la richiesta, una modifica apportata da Append potrebbe impedire l'attivazione di un effetto Audit o Deny.
- Successivamente viene valutato **Deny**. La valutazione di Deny prima di Audit impedisce la doppia registrazione di una risorsa non desiderata.
- Viene quindi valutato **Audit** prima che la richiesta passi al provider di risorse.

Dopo che la richiesta viene fornita al provider di risorse e quest'ultimo restituisce un codice di stato con esito positivo, vengono valutati **AuditIfNotExists** e **DeployIfNotExists** per determinare se è richiesta un'azione o una registrazione di conformità come completamento della procedura.

## <a name="append"></a>Append

Append viene usato per aggiungere altri campi alla risorsa richiesta durante la creazione o l'aggiornamento. Può essere utile per aggiungere tag alle risorse come costCenter o specificare gli indirizzi IP consentiti per una risorsa di archiviazione.

### <a name="append-evaluation"></a>Valutazione di Append

Come accennato, Append viene valutato prima che la richiesta sia elaborata da un provider di risorse durante la creazione o l'aggiornamento di una risorsa. Append aggiunge uno o più campi alla risorsa quando viene soddisfatta la condizione **if** della regola dei criteri. Se l'effetto Append sostituisce un valore nella richiesta originale con un valore diverso, agisce come effetto Deny e la richiesta viene rifiutata.

Quando una definizione dei criteri che usa l'effetto Append viene eseguita come parte di un ciclo di valutazione, non apporta modifiche alle risorse già esistenti. Al contrario, contrassegna qualsiasi risorsa che soddisfi la condizione **if** come non conforme.

### <a name="append-properties"></a>Proprietà di Append

Un effetto Append ha solo una matrice **details** obbligatoria. Essendo una matrice, **details** può richiedere una coppia **campo/valore** singola o coppie multiple. Fare riferimento alla [definizione dei criteri](policy-definition.md#fields) per l'elenco dei campi accettabili.

### <a name="append-examples"></a>Esempi di Append

Esempio 1: coppia **campo/valore** singola per accodare un tag.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Esempio 2: coppie **campo/valore** multiple per accodare un set di tag.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Esempio 3: coppia **campo/valore** singola che usa un [alias](policy-definition.md#aliases) con **value** di matrice per impostare le regole IP in un account di archiviazione.

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

## <a name="deny"></a>Nega

Deny viene usato per impedire una richiesta di risorse che non corrisponde agli standard desiderati tramite una definizione dei criteri e che genera un errore della richiesta.

### <a name="deny-evaluation"></a>Valutazione di Deny

Quando si crea o si aggiorna una risorsa, Deny impedisce la richiesta prima che sia inviata al provider di risorse. La richiesta viene restituita come 403 (Accesso negato). Nel portale l'accesso negato può essere visualizzato come stato della distribuzione impedita a causa dell'assegnazione dei criteri.

Durante un ciclo di valutazione, le definizioni dei criteri con un effetto Deny che corrispondono alle risorse sono contrassegnate come non conformi, ma non vengono eseguite azioni sulla risorsa stessa.

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

L'effetto Audit viene usato per creare un evento di avviso in un log attività quando viene valutata una risorsa non conforme, ma la richiesta non viene arrestata.

### <a name="audit-evaluation"></a>Valutazione di Audit

L'effetto Audit è l'ultimo a essere eseguito durante la creazione o l'aggiornamento di una risorsa prima che la risorsa venga inviata al provider di risorse. L'effetto Audit ha lo stesso funzionamento per una richiesta di risorsa e un ciclo di valutazione ed esegue un'operazione `Microsoft.Authorization/policies/audit/action` per il log attività. In entrambi i casi la risorsa è contrassegnata come non conforme.

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

AuditIfNotExists consente il controllo su una risorsa che corrisponde alla condizione **if**, ma non ha i componenti specificati in **details** della condizione **then**.

### <a name="auditifnotexists-evaluation"></a>Valutazione di AuditIfNotExists

AuditIfNotExists viene eseguito dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento per una risorsa e ha restituito un codice di stato con esito positivo. L'effetto viene attivato se non ci sono risorse correlate o se le risorse definite da **ExistenceCondition** non restituiscono true. Quando l'effetto viene attivato, un'operazione `Microsoft.Authorization/policies/audit/action` per il log attività viene eseguita allo stesso modo dell'effetto Audit. Quando è attivato, la risorsa che ha soddisfatto la condizione **if** è la risorsa contrassegnata come non conforme.

### <a name="auditifnotexists-properties"></a>Proprietà di AuditIfNotExists

La proprietà **details** degli effetti AuditIfNotExists ha tutte le sottoproprietà che definiscono le risorse correlate a cui corrispondere.

- **Type** [obbligatorio]
  - Specifica il tipo della risorsa correlata a cui corrispondere.
  - Inizia cercando di recuperare una risorsa sottostante la risorsa della condizione **if**, quindi esegue una query all'interno dello stesso gruppo di risorse come risorsa della condizione **if**.
- **Name** (facoltativo)
  - Specifica il nome esatto della risorsa a cui corrispondere e fa sì che il criterio recuperi una risorsa specifica invece di tutte le risorse del tipo specificato.
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

Analogamente ad AuditIfNotExists, DeployIfNotExists esegue una distribuzione di modelli quando la condizione viene soddisfatta.

> [!WARNING]
> DeployIfNotExists è disponibile solo per criteri **integrati**.

### <a name="deployifnotexists-evaluation"></a>Valutazione di DeployIfNotExists

Anche DeployIfNotExists viene eseguito dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento per una risorsa e ha restituito un codice di stato con esito positivo. L'effetto viene attivato se non ci sono risorse correlate o se le risorse definite da **ExistenceCondition** non restituiscono true. Quando viene attivato l'effetto, viene eseguita una distribuzione di modelli.

Durante un ciclo di valutazione, le definizioni dei criteri con un effetto DeployIfNotExists che corrispondono alle risorse sono contrassegnate come non conformi, ma non vengono eseguite azioni sulla risorsa stessa.

### <a name="deployifnotexists-properties"></a>Proprietà di DeployIfNotExists

La proprietà **details** degli effetti DeployIfNotExists ha tutte le sottoproprietà che definiscono le risorse correlate a cui corrispondere e la distribuzione di modelli da eseguire.

- **Type** [obbligatorio]
  - Specifica il tipo della risorsa correlata a cui corrispondere.
  - Inizia cercando di recuperare una risorsa sottostante la risorsa della condizione **if**, quindi esegue una query all'interno dello stesso gruppo di risorse come risorsa della condizione **if**.
- **Name** (facoltativo)
  - Specifica il nome esatto della risorsa a cui corrispondere e fa sì che il criterio recuperi una risorsa specifica invece di tutte le risorse del tipo specificato.
- **ResourceGroupName** (facoltativo)
  - Consente che la corrispondenza della risorsa correlata provenga da un gruppo di risorse diverso.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - L'impostazione predefinita è il gruppo di risorse della risorsa della condizione **if**.
  - Se viene eseguita una distribuzione di modelli, viene distribuita nel gruppo di risorse di questo valore.
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
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="layering-policies"></a>Livelli dei criteri

Una risorsa potrebbe essere interessata da assegnazioni multiple. Queste assegnazioni possono essere nello stesso ambito (risorsa, gruppo di risorse, sottoscrizione o gruppo di gestione specifico) o in ambiti diversi. È anche probabile che ognuna di queste assegnazioni abbia un effetto diverso definito. Indipendentemente da ciò, la condizione e l'effetto per ogni criterio (assegnato direttamente o come parte di un'iniziativa) vengono valutati in modo indipendente. Ad esempio, se il criterio 1 ha una condizione che limita la creazione del percorso risorsa per la sottoscrizione A solo in 'westus' con l'effetto Deny e il criterio 2 ha una condizione che limita la creazione del percorso risorsa nel gruppo di risorse B (che si trova nella sottoscrizione A) solo in 'eastus' con l'effetto Audit e sono entrambi assegnati, il risultato finale potrebbe essere il seguente:

- Qualsiasi risorsa già presente nel gruppo di risorse B in 'eastus' è conforme al criterio 2, ma è contrassegnata come non conforme al criterio 1.
- Qualsiasi risorsa già presente nel gruppo di risorse B non in 'eastus' verrà contrassegnata come non conforme al criterio 2 e potrebbe anche essere contrassegnata come non conforme al criterio 1 se non in 'westus'.
- Qualsiasi nuova risorsa nella sottoscrizione A non in 'westus' verrebbe rifiutata dal criterio 1.
- Qualsiasi nuova risorsa nella sottoscrizione A/gruppo di risorse B in 'westus' verrebbe contrassegnata come non conforme al criterio 2, ma sarebbe creata (in caso di conformità al criterio 1 e al criterio 2 è applicabile Audit e non Deny).

Se entrambi i criteri 1 e 2 avessero l'effetto Deny, la situazione diventerebbe la seguente:

- Qualsiasi risorsa già presente nel gruppo di risorse B non in 'eastus' verrà contrassegnata come non conforme al criterio 2.
- Qualsiasi risorsa già presente nel gruppo di risorse B non in 'westus' verrà contrassegnata come non conforme al criterio 1.
- Qualsiasi nuova risorsa nella sottoscrizione A non in 'westus' verrebbe rifiutata dal criterio 1.
- Qualsiasi nuova risorsa nella sottoscrizione A/gruppo di risorse B verrebbe rifiutata (dal momento che il percorso non potrebbe mai soddisfare entrambi i percorsi 1 e 2).

Dal momento che ogni assegnazione viene valutata singolarmente, non c'è la possibilità di inserire per sbaglio una risorsa a causa delle differenze nell'ambito. Pertanto, il risultato netto dei livelli dei criteri o della sovrapposizione dei criteri è considerato **cumulativo più restrittivo**. In altre parole, una risorsa che si vuole creare potrebbe essere bloccata a causa di criteri sovrapposti e in conflitto, come nell'esempio precedente se i criteri 1 e 2 hanno avuto un effetto Deny. Se è ancora necessario che la risorsa venga creata nell'ambito di destinazione, rivedere le esclusioni per ogni assegnazione per verificare che i criteri corretti interessino gli ambiti corretti.

## <a name="next-steps"></a>Passaggi successivi

Ora che gli effetti della definizione dei criteri sono stati meglio compresi, esaminare gli esempi dei criteri:

- Vedere altri esempi in [Esempi di Criteri di Azure](json-samples.md).
