---
title: Criteri di Azure Resource Manager | Microsoft Docs
description: Viene descritto come usare i criteri di Gestione risorse di Azure per impedire violazioni in ambiti diversi come sottoscrizione, gruppi di risorse o singole risorse.
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: bdc759341e1f9707ddf688512249c3297d85c29b


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>Usare i criteri per gestire le risorse e controllare l'accesso
Gestione risorse di Azure consente ora di controllare l'accesso tramite criteri personalizzati. Con i criteri è possibile impedire agli utenti dell'organizzazione di violare le convenzioni necessarie per gestire le risorse dell'organizzazione. 

È possibile creare definizioni dei criteri che descrivono le azioni o le risorse specificamente negate. Le definizioni dei criteri vengono assegnate all'ambito desiderato, ad esempio la sottoscrizione, un gruppo di risorse o una singola risorsa. I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo.

Questo articolo illustra la struttura di base del linguaggio di definizione dei criteri che è possibile usare per creare i criteri. Descrive quindi come applicare questi criteri ad ambiti diversi.

## <a name="how-is-it-different-from-rbac"></a>Quali sono le differenze rispetto al controllo degli accessi in base al ruolo?
Vi sono alcune differenze sostanziali tra il controllo degli accessi in base al ruolo e i criteri, ma il primo aspetto da considerare è che i criteri e il controllo degli accessi in base al ruolo funzionano insieme. Per usare i criteri, l'utente deve essere autenticato tramite il controllo degli accessi in base al ruolo. A differenza del controllo degli accessi in base al ruolo, i criteri rappresentano un sistema con autorizzazioni predefinite e negazione esplicita. 

Il controllo degli accessi in base al ruolo è incentrato sulle azioni che un **utente** può eseguire in ambiti diversi. Ad esempio, un determinato utente viene aggiunto al ruolo di collaboratore per un gruppo di risorse nell'ambito desiderato, in modo che possa apportare modifiche a tale gruppo di risorse. 

Il criterio è incentrato sulle azioni delle **risorse** nei vari ambiti. Tramite i criteri, ad esempio, è possibile controllare i tipi di risorse di cui è possibile eseguire il provisioning o limitare le posizioni in cui è possibile eseguire il provisioning delle risorse.

## <a name="common-scenarios"></a>Scenari comuni
Uno scenario comune prevede la richiesta di tag di reparto a scopo di chargeback. È possibile che un'organizzazione voglia consentire determinate operazioni solo quando viene associato il centro di costo appropriato. In caso contrario, rifiuta la richiesta. Questo criterio è utile per gestire l'addebito al centro di costo appropriato per le operazioni eseguite.

Un altro scenario comune è rappresentato dai casi in un cui un'organizzazione ha l'esigenza di controllare la posizione di creazione delle risorse oppure di controllare l'accesso alle risorse consentendo il provisioning solo di determinati tipi di risorse.

In modo analogo, un'organizzazione può controllare il catalogo dei servizi o applicare le convenzioni di denominazione desiderate per le risorse.

Questi scenari possono essere facilmente realizzati con i criteri.

## <a name="policy-definition-structure"></a>Struttura delle definizioni di criteri
La definizione dei criteri viene creata tramite JSON. È costituita da uno o più condizioni/operatori logici che definiscono le azioni e un effetto che indica cosa succede quando sono soddisfatte le condizioni. Lo schema è pubblicato in [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

I criteri includono fondamentalmente gli elementi seguenti:

**Condizioni/operatori logici:** set di condizioni che possono essere modificate tramite un set di operatori logici.

**Effetto:** conseguenza del rispetto della condizione, ovvero negazione o controllo. Per un effetto di controllo viene generato un log del servizio eventi di avviso. Un amministratore può ad esempio creare un criterio che determina un evento di controllo, se tutti gli utenti creano una VM di grandi dimensioni. L'amministratore può quindi esaminare i log in seguito.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>Valutazione dei criteri
I criteri vengono valutati quando vengono create le risorse. Per la distribuzione del modello, i criteri sono valutati durante la creazione di ogni risorsa nel modello. 

> [!NOTE]
> I tipi di risorsa che non supportano tag, tipologia, percorso non vengono attualmente valutati dal criterio, come ad esempio il tipo di risorsa Microsoft.Resources/deployments. Il supporto di questo tipo di risorsa verrà aggiunto in futuro. Per evitare problemi di compatibilità con le versioni precedenti, è consigliabile specificare in modo esplicito il tipo durante la creazione di criteri. A tutti i tipi viene ad esempio applicato un criterio per i tag che non specifica i tipi, in modo che la distribuzione del modello non venga completata in presenza di una risorsa annidata che non supporta il tag, mentre il tipo di risorsa della distribuzione sarà aggiunto alla valutazione criteri. 
> 
> 

## <a name="logical-operators"></a>Operatori logici
Ecco gli operatori logici supportati con la relativa sintassi:

| Nome operatore | Sintassi |
|:--- |:--- |
| not |"not" : {&lt; condizione oppure operatore &gt;} |
| e |"allOf" : [ {&lt; condizione oppure operatore &gt;},{&lt; condizione oppure operatore &gt;}] |
| oppure |"anyOf" : [ {&lt; condizione oppure operatore &gt;},{&lt; condizione oppure operatore &gt;}] |

Gestione risorse consente di specificare una logica complessa nel criterio tramite operatori nidificati. Ad esempio, è possibile impedire la creazione di risorse in un percorso specifico per un tipo di risorsa specificato. Un esempio di operatori nidificati è illustrato in questo argomento.

## <a name="conditions"></a>Condizioni
Una condizione valuta se un **campo** o un'**origine** soddisfa determinati criteri. Ecco i nomi e la sintassi delle condizioni supportate:

| Nome condizione | Sintassi |
|:--- |:--- |
| Uguale a |"equals" : "&lt;valore&gt;" |
| Come |"like" : "&lt;valore&gt;" |
| Contiene |"contains" : "&lt;valore&gt;" |
| In ingresso |"in" : [ "&lt;valore1&gt;","&lt;valore2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;NomeChiave&gt;" |
| Exists |"exists" : "&lt;booleano&gt;" |

### <a name="fields"></a>Fields
Le condizioni vengono formate usando campi e origini. Un campo rappresenta le proprietà nel payload delle richieste di risorse usato per descrivere lo stato della risorsa. Un'origine rappresenta le caratteristiche della richiesta stessa. 

Sono supportati i campi e le origini seguenti:

Campi: **name**, **kind**, **type**, **location**, **tags**, **tags.*** e **property alias**. 

### <a name="property-aliases"></a>Alias delle proprietà
L'alias delle proprietà è un nome che può essere usato nella definizione di criteri per accedere alle proprietà specifiche del tipo di risorsa, ad esempio impostazioni e SKU. Funziona in tutte le versioni di API in cui la proprietà esiste. Gli alias sono rintracciabili tramite l'API REST (il supporto per PowerShell verrà aggiunto in futuro):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

L'esempio seguente illustra la definizione di un alias. È possibile notare che un alias definisce percorsi in diverse versioni di API, anche in caso di una modifica del nome di una proprietà. 

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
| --- | --- |
| {resourceType}/sku.name |I tipi di risorse supportati sono: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |Il tipo di risorsa supportato è Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |Il tipo di risorsa supportato è Microsoft.Cache/Redis |
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

## <a name="effect"></a>Effetto
Il criterio supporta tre tipi di effetto: **negazione**, **controllo** e **aggiunta**. 

* La negazione genera un evento nel log di controllo e nega la richiesta
* Il controllo genera un evento nel log di controllo, ma non nega la richiesta
* L'aggiunta aggiunge il set di campi definiti alla richiesta 

In caso di **aggiunta**, è necessario specificare questi dettagli:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

Il valore può essere una stringa o un oggetto formato JSON. 

## <a name="policy-definition-examples"></a>Esempi di definizioni di criteri
Di seguito viene illustrato come definire i criteri per ottenere gli scenari precedenti.

### <a name="chargeback-require-departmental-tags"></a>Chargeback: requisito dei tag di reparto
Il criterio seguente nega le richieste senza un tag con la chiave "costCenter".

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

Il criterio seguente aggiunge il tag costCenter con un valore predefinito quando il tag costCenter non è presente, ma sono presenti altri tag. 

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


### <a name="geo-compliance-ensure-resource-locations"></a>Conformità geografica: verifica della posizione delle risorse
L'esempio seguente illustra un criterio che nega tutte le richieste con posizione diversa da Europa settentrionale o Europa occidentale.

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

### <a name="service-curation-select-the-service-catalog"></a>Controllo dei servizi: selezione del catalogo dei servizi
L'esempio seguente mostra un criterio che consente azioni solo nei servizi di tipo Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\*. Eventuali altre azioni vengono negate.

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

### <a name="use-approved-skus"></a>Usare SKU approvate
L'esempio seguente illustra l'uso di alias di proprietà per limitare le SKU. Nell'esempio solo l'uso di Standard_LRS e Standard_GRS è approvato per gli account di archiviazione.

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


### <a name="naming-convention"></a>Convenzione di denominazione
L'esempio seguente illustra l'uso dei caratteri jolly supportato dalla condizione "like". La condizione dichiara che la richiesta deve essere negata se il nome non corrisponde al modello indicato (namePrefix\*nameSuffix).

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

### <a name="tag-requirement-just-for-storage-resources"></a>Requisito di tag solo per le risorse di archiviazione
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

## <a name="create-and-assign-a-policy"></a>Creare e assegnare un criterio
Per applicare un criterio è necessario crearne una definizione e quindi applicarlo a un ambito. 

### <a name="rest-api"></a>API REST
È possibile creare un criterio con l' [API REST per le definizioni dei criteri](https://docs.microsoft.com/rest/api/resources/policydefinitions). L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti.

Per creare un criterio, eseguire:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Per api-version, usare *2016-04-01*. Includere un corpo della richiesta in modo simile all'esempio seguente:

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

È possibile applicare la definizione dei criteri all'ambito desiderato tramite l' [API REST per le assegnazioni dei criteri](https://docs.microsoft.com/rest/api/resources/policyassignments). L'API REST consente di creare ed eliminare le assegnazioni dei criteri e ottenere informazioni sulle assegnazioni esistenti.

Per creare un'assegnazione di criteri, eseguire:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} è il nome dell'assegnazione di criteri. Per api-version, usare *2016-04-01*. 

Con un corpo della richiesta simile all'esempio seguente:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

### <a name="powershell"></a>PowerShell
È possibile creare una definizione di criteri usando il cmdlet New-AzureRmPolicyDefinition. L'esempio seguente crea un criterio per consentire solo le risorse in Europa settentrionale ed Europa occidentale.

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

L'output dell'esecuzione viene archiviato nell'oggetto $policy e può essere usato in seguito durante l'assegnazione dei criteri. Per il parametro dei criteri, è anche possibile specificare il percorso di un file con estensione json contenente i criteri invece di specificare criteri inline.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

È possibile applicare il criterio all'ambito desiderato usando il cmdlet New-AzureRmPolicyAssignment:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

$policy è l'oggetto criteri restituito come risultato dell'esecuzione del cmdlet New-AzureRmPolicyDefinition. L'ambito è il nome del gruppo di risorse specificato.

Per rimuovere un'assegnazione di criteri, usare:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

È possibile ottenere, modificare o rimuovere le definizioni dei criteri tramite i cmdlet Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition e Remove-AzureRmPolicyDefinition, rispettivamente.

Analogamente, è possibile ottenere, modificare o rimuovere le assegnazioni dei criteri tramite i cmdlet Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment e Remove-AzureRmPolicyAssignment, rispettivamente.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure con il comando di definizione dei criteri. L'esempio seguente crea un criterio per consentire solo le risorse in Europa settentrionale ed Europa occidentale.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
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


È possibile specificare il percorso di un file con estensione JSON contenente i criteri invece di specificare criteri inline.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

È possibile applicare il criterio all'ambito desiderato usando il comando per l'assegnazione di criteri:

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

L'ambito è il nome del gruppo di risorse specificato. Se non si conosce il valore del parametro policy-definition-id, è possibile ottenerlo tramite l'interfaccia della riga di comando di Azure. 

    azure policy definition show <policy-name>

Per rimuovere un'assegnazione di criteri, usare:

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

È possibile ottenere, modificare o rimuovere le definizioni dei criteri tramite la visualizzazione delle definizioni dei criteri, rispettivamente con i comandi di impostazione ed eliminazione.

Analogamente, è possibile ottenere, modificare o rimuovere le assegnazioni dei criteri rispettivamente tramite la visualizzazione delle assegnazioni dei criteri e i comandi di eliminazione.

## <a name="policy-audit-events"></a>Eventi di controllo dei criteri
Dopo aver applicato il criterio, si inizierà a visualizzare gli eventi correlati ai criteri. Per ottenere questi dati, è possibile accedere al portale oppure usare PowerShell o l'interfaccia della riga di comando di Azure. 

### <a name="powershell"></a>PowerShell
Per visualizzare tutti gli eventi correlati all'effetto di negazione, è possibile usare il comando di PowerShell seguente:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Per visualizzare tutti gli eventi correlati all'effetto di controllo, è possibile usare il comando seguente:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Per visualizzare tutti gli eventi di un gruppo di risorse correlati all'effetto di negazione, è possibile usare il comando seguente dell'interfaccia della riga di comando di Azure:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Per visualizzare tutti gli eventi correlati all'effetto di controllo, è possibile usare il comando seguente dell'interfaccia della riga di comando di Azure:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>Visualizzare un criterio
I criteri possono essere visualizzati tramite PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Visualizzare i criteri potrebbe rivelarsi necessario per visualizzare la regola che ha negato una distribuzione non riuscita. Il messaggio di errore include l'ID della definizione dei criteri.

### <a name="powershell"></a>PowerShell
Per ottenere un criterio, usare il cmdlet seguente:

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

Questo cmdlet restituisce il JSON relativo alla definizione del criterio.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Per ottenere un criterio, usare il comando seguente:

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>API REST
Per ottenere un criterio, usare l'operazione [Ottieni definizione criteri](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

## <a name="next-steps"></a>Passaggi successivi
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).




<!--HONumber=Nov16_HO3-->


