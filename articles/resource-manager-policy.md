<properties
	pageTitle="Criteri di Gestione risorse di Azure | Microsoft Azure"
	description="Viene descritto come usare i criteri di Gestione risorse di Azure per impedire violazioni in ambiti diversi come sottoscrizione, gruppi di risorse o singole risorse."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="07/12/2016"
	ms.author="gauravbh;tomfitz"/>

# Usare i criteri per gestire le risorse e controllare l'accesso

Gestione risorse di Azure consente ora di controllare l'accesso tramite criteri personalizzati. Con i criteri è possibile impedire agli utenti dell'organizzazione di violare le convenzioni necessarie per gestire le risorse dell'organizzazione.

È possibile creare definizioni dei criteri che descrivono le azioni o le risorse specificamente negate. Le definizioni dei criteri vengono assegnate all'ambito desiderato, ad esempio la sottoscrizione, un gruppo di risorse o una singola risorsa.

Questo articolo illustra la struttura di base del linguaggio di definizione dei criteri che è possibile usare per creare i criteri. Descrive quindi le modalità per l'applicazione di tali criteri in ambiti diversi e infine illustra alcuni esempi di come sia possibile ottenere questi risultati tramite l'API REST.

## Quali sono le differenze rispetto al controllo degli accessi in base al ruolo?

Vi sono alcune differenze sostanziali tra il controllo degli accessi in base al ruolo e i criteri, ma il primo aspetto da considerare è che i criteri e il controllo degli accessi in base al ruolo funzionano insieme. Per poter usare i criteri, l'utente deve essere autenticato tramite il controllo degli accessi in base al ruolo. A differenza del controllo degli accessi in base al ruolo, i criteri rappresentano un sistema con autorizzazioni predefinite e negazione esplicita.

Il controllo degli accessi in base al ruolo è incentrato sulle azioni che un **utente** può eseguire in ambiti diversi. Ad esempio, un determinato utente viene aggiunto al ruolo di collaboratore per un gruppo di risorse nell'ambito desiderato, in modo che possa apportare modifiche a tale gruppo di risorse.

Il criterio è incentrato sulle azioni delle **risorse** nei vari ambiti. Tramite i criteri, ad esempio, è possibile controllare i tipi di risorse di cui è possibile eseguire il provisioning o limitare le posizioni in cui è possibile eseguire il provisioning delle risorse.

## Scenari comuni

Uno scenario comune prevede la richiesta di tag di reparto a scopo di chargeback. È possibile che un'organizzazione desideri consentire determinate operazioni solo quando viene associato il centro di costo appropriato; in caso contrario, rifiuterà la richiesta. Ciò sarebbe utile per gestire l'addebito sul centro di costo appropriato per le operazioni eseguite.

Un altro scenario comune è rappresentato dai casi in un cui un'organizzazione ha l'esigenza di controllare la posizione di creazione delle risorse oppure di controllare l'accesso alle risorse consentendo il provisioning solo di determinati tipi di risorse.

In modo analogo, un'organizzazione può controllare il catalogo dei servizi o applicare le convenzioni di denominazione desiderate per le risorse.

Questi scenari possono essere facilmente realizzati con i criteri, come descritto di seguito.

## Struttura delle definizioni di criteri

La definizione dei criteri viene creata tramite JSON. È costituita da uno o più condizioni/operatori logici che definiscono le azioni e un effetto che indica cosa succede quando sono soddisfatte le condizioni. Lo schema è pubblicato in [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json).

I criteri includono fondamentalmente gli elementi seguenti:

**Condizioni/operatori logici:** contiene un set di condizioni che possono essere modificate tramite un set di operatori logici.

**Effetto:** descrive l'effetto quando la condizione viene soddisfatta, ovvero negazione o controllo. Per un effetto di controllo verrà generato un log del servizio eventi di avviso. Ad esempio, un amministratore può creare un criterio che determina un controllo, se tutti gli utenti creano una macchina virtuale di grandi dimensioni, e poi esaminano i registri in un secondo momento.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }
    
## Valutazione dei criteri

I criteri vengono valutati quando si verifica la creazione di risorse o la distribuzione del modello utilizzando una richiesta PUT HTTP. In caso di distribuzione del modello, i criteri sono valutati durante la creazione di ogni risorsa nel modello.

> [AZURE.NOTE] I tipi di risorsa che non supportano tag, tipologia, percorso non vengono attualmente valutati dal criterio, come ad esempio il tipo di risorsa Microsoft.Resources/deployments. Il supporto di questo tipo di risorsa verrà aggiunto in futuro. Per evitare problemi di compatibilità con le versioni precedenti, è consigliabile specificare in modo esplicito il tipo durante la creazione di criteri. Ad esempio, a tutti i tipi viene applicato un criterio per i tag che non specifica i tipi, in modo che la distribuzione del modello non sarà completata in presenza di una risorsa annidata che non supporta il tag, mentre il tipo di risorsa della distribuzione sarà aggiunto alla valutazione criteri.

## Operatori logici

Gli operatori logici supportati con la relativa sintassi sono elencati di seguito:

| Nome operatore | Sintassi |
| :------------- | :------------- |
| Not | "not": {&lt;condizione o operatore &gt;} |
| E | "allOf": [{&lt; condizione o operatore &gt;}, {&lt; condizione o operatore &gt;}] |
| Oppure | "anyOf": [{&lt; condizione o operatore &gt;}, {&lt; condizione o operatore &gt;}] |

Gestione risorse consente di specificare una logica complessa nel criterio tramite operatori nidificati. Ad esempio, è possibile impedire la creazione di risorse in un percorso specifico per un tipo di risorsa specificato. Di seguito è riportato un esempio di operatori nidificati.

## Condizioni

Una condizione valuta se un **campo** o un'**origine** soddisfa determinati criteri. I nomi e la sintassi delle condizioni supportate sono elencati di seguito:

| Nome condizione | Sintassi |
| :------------- | :------------- |
| Uguale a | "equals" : "&lt;valore&gt;" |
| Come | "like" : "&lt;valore&gt;" |
| Contiene | "contains" : "&lt;valore&gt;"|
| In | "in" : [ "&lt;valore1&gt;","&lt;valore2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;nomeChiave&gt;" |

### Fields

Le condizioni vengono create usando campi e origini. Un campo rappresenta le proprietà nel payload delle richieste di risorse usato per descrivere lo stato della risorsa. Un'origine rappresenta le caratteristiche della richiesta stessa.

Sono supportati i campi e le origini seguenti:

Campi: **name**, **kind**, **type**, **location**, **tags**, **tags.*** e **property alias**.

### Alias delle proprietà 
L'alias delle proprietà è un nome che può essere usato nella definizione di criteri per accedere alle proprietà specifiche del tipo di risorsa, ad esempio impostazioni e SKU. Funziona in tutte le versioni di API in cui la proprietà esiste. È possibile recuperare gli alias tramite l'API REST sottostante. Il supporto di PowerShell verrà aggiunto in futuro:

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
	
Di seguito è illustrata la definizione di un alias. È possibile notare che un alias definisce percorsi in diverse versioni di API, anche in caso di una modifica del nome di una proprietà.

	"aliases": [
	    {
	      "name": "Microsoft.Storage/storageAccounts/sku.name",
	      "paths": [
	        {
	          "path": "properties.accountType",
	          "apiVersions": [
	            "2015-06-15",
	            "2015-05-01-preview"
	          ]
	        },
	        {
	          "path": "sku.name",
	          "apiVersions": [
	            "2016-01-01"
	          ]
	        }
	      ]
	    }
	]

Attualmente, gli alias supportati sono:

| Nome alias | Descrizione |
| ---------- | ----------- |
| {resourceType}/sku.name | I tipi di risorse supportati sono: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft..CDN/profiles |
| {resourceType}/sku.family | Il tipo di risorsa supportato è Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | Il tipo di risorsa supportato è Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

Attualmente, il criterio funziona solo su richieste PUT.

## Effetto
Il criterio supporta tre tipi di effetto: **negazione**, **controllo** e **aggiunta**.

- La negazione genera un evento nel log di controllo e nega la richiesta
- Il controllo genera un evento nel log di controllo, ma non nega la richiesta
- L'aggiunta aggiunge il set di campi definiti alla richiesta

In caso di **aggiunta** è necessario specificare i dettagli illustrati di seguito:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

Il valore può essere una stringa o un oggetto formato JSON.

## Esempi di definizioni di criteri

Vedremo ora come definire i criteri per le esigenze degli scenari descritti in precedenza.

### Chargeback: requisito dei tag di reparto

Il criterio seguente nega tutte le richieste senza un tag con la chiave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

Il criterio seguente aggiunge il tag costCenter con un valore predefinito se non esistono tag.

	{
	  "if": {
	    "field": "tags",
	    "exists": "false"
	  },
	  "then": {
	    "effect": "append",
	    "details": [
	      {
	        "field": "tags",
	        "value": {"costCenter":"myDepartment" }
	      }
	    ]
	  }
	}
	
Il criterio seguente aggiunge il tag costCenter con un valore predefinito se esistono altri tag.

	{
	  "if": {
	    "allOf": [
	      {
	        "field": "tags",
	        "exists": "true"
	      },
	      {
	        "field": "tags.costCenter",
	        "exists": "false"
	      }
	    ]
	
	  },
	  "then": {
	    "effect": "append",
	    "details": [
	      {
	        "field": "tags.costCenter",
	        "value": "myDepartment"
	      }
	    ]
	  }
	}


### Conformità geografica: verifica della posizione delle risorse

L'esempio seguente illustra un criterio che negherà tutte le richieste con posizione diversa da Europa settentrionale o Europa occidentale.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Controllo dei servizi: selezione del catalogo dei servizi

L'esempio seguente illustra l'uso dell'origine. Mostra che sono consentite solo le azioni sui servizi di tipo Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\*. Qualsiasi altra azione verrà negata.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Usare SKU approvate

L'esempio seguente illustra l'uso di alias di proprietà alias per limitare le SKU. Nell'esempio seguente, solo l'uso di Standard\_LRS e Standard\_GRS è approvato per gli account di archiviazione.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### Convenzione di denominazione

L'esempio seguente illustra l'uso dei caratteri jolly supportato dalla condizione "like". La condizione dichiara che la richiesta deve essere negata se il nome non corrisponde al modello indicato (namePrefix*nameSuffix).

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### Requisito di tag solo per le risorse di archiviazione

L'esempio seguente illustra come annidare gli operatori logici per richiedere un tag dell'applicazione solo per le risorse di archiviazione.

    {
        "if": {
            "allOf": [
              {
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
        "then": {
            "effect": "audit"
        }
    }

## Assegnazione dei criteri

I criteri possono essere applicati in ambiti diversi come sottoscrizione, gruppi di risorse e singole risorse. I criteri vengono ereditati da tutte le risorse figlio. Pertanto, se un criterio viene applicato a un gruppo di risorse, sarà applicabile a tutte le risorse in tale gruppo.

## Creazione dei criteri

Questa sezione include informazioni dettagliate sulla procedura per la creazione dei criteri con l'API REST.

### Creare una definizione di criteri con l'API REST

È possibile creare un criterio con l'[API REST per le definizioni dei criteri](https://msdn.microsoft.com/library/azure/mt588471.aspx). L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti.

Per creare un nuovo criterio, eseguire:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Con un corpo della richiesta simile al seguente:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


La definizione dei criteri può essere definita come uno degli esempi illustrati in precedenza. Per api-version, usare *2016-04-01*. Per esempi e altre informazioni dettagliate, vedere [API REST per le definizioni dei criteri](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### Creare una definizione di criteri tramite PowerShell

È possibile creare una nuova definizione di criteri usando il cmdlet New-AzureRmPolicyDefinition, come illustrato di seguito. Negli esempi seguenti viene creato un criterio per consentire solo le risorse in Europa settentrionale ed Europa occidentale.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{	
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
    	}
      },
      "then" : {
        "effect" : "deny"
      }
    }'    		

L'output dell'esecuzione viene archiviato nell'oggetto $policy, in modo da poterlo usare in seguito durante l'assegnazione dei criteri. Per il parametro dei criteri, è anche possibile specificare il percorso di un file con estensione JSON contenente i criteri invece di specificare criteri inline come illustrato di seguito.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Applicazione dei criteri

### Assegnazione dei criteri con l'API REST

È possibile applicare la definizione dei criteri all'ambito desiderato tramite l'[API REST per le assegnazioni dei criteri](https://msdn.microsoft.com/library/azure/mt588466.aspx). L'API REST consente di creare ed eliminare le assegnazioni dei criteri e ottenere informazioni sulle assegnazioni esistenti.

Per creare una nuova assegnazione di criteri, eseguire:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} è il nome dell'assegnazione di criteri. Per api-version, usare *2016-04-01*.

Con un corpo della richiesta simile al seguente:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

Per esempi e altre informazioni dettagliate, vedere [API REST per le assegnazioni dei criteri](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### Assegnazione di criteri tramite PowerShell

È possibile applicare il criterio creato in precedenza tramite PowerShell all'ambito desiderato usando il cmdlet New-AzureRmPolicyAssignment, come illustrato di seguito:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
$policy è l'oggetto criteri restituito come risultato dell'esecuzione del cmdlet New-AzureRmPolicyDefinition, come illustrato in precedenza. L'ambito è il nome del gruppo di risorse specificato.

Se si desidera rimuovere l'assegnazione dei criteri precedente, è possibile procedere come segue:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

È possibile ottenere, modificare o rimuovere le definizioni dei criteri tramite i cmdlet Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition e Remove-AzureRmPolicyDefinition, rispettivamente.

Analogamente, è possibile ottenere, modificare o rimuovere le assegnazioni dei criteri tramite i cmdlet Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment e Remove-AzureRmPolicyAssignment, rispettivamente.

##Eventi di controllo dei criteri

Dopo aver applicato il criterio, si inizierà a visualizzare gli eventi correlati ai criteri. È possibile accedere al portale o utilizzare PowerShell per ottenere questi dati.

Per visualizzare tutti gli eventi correlati all'effetto di negazione, è possibile utilizzare il comando seguente.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Per visualizzare tutti gli eventi correlati all'effetto di controllo, è possibile utilizzare il comando seguente.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 
    

<!---HONumber=AcomDC_0713_2016-->