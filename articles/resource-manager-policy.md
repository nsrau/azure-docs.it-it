<properties
	pageTitle="Criteri di Gestione risorse di Azure | Microsoft Azure"
	description="Viene descritto come usare i criteri di Gestione risorse di Azure per impedire violazioni in ambiti diversi come sottoscrizione, gruppi di risorse o singole risorse."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="09/29/2015"
	ms.author="gauravbh;tomfitz"/>

# Usare i criteri per gestire le risorse e controllare l'accesso

Gestione risorse di Azure consente ora di controllare l'accesso tramite criteri personalizzati. Un criterio rappresenta una o più violazioni che è possibile impedire per l'ambito desiderato. L'ambito, in questo caso, può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

I criteri sono un sistema per consentire determinate condizioni per impostazione predefinita. I criteri vengono definiti tramite una definizione di criteri e applicati tramite un'assegnazione di criteri. Le assegnazioni di criteri consentono di controllare l'ambito in cui è possibile applicare un criterio.

In questo articolo verrà illustrata la struttura di base del linguaggio di definizione dei criteri che è possibile usare per creare i criteri. Verranno quindi descritte le modalità per l'applicazione dei criteri in ambiti diversi e infine verranno illustrati alcuni esempi di come sia possibile ottenere questi risultati tramite l'API REST. È prevista a breve anche l'aggiunta di informazioni sul supporto di PowerShell.

## Scenari comuni

Uno scenario comune prevede la richiesta di tag di reparto a scopo di chargeback. È possibile che un'organizzazione desideri consentire determinate operazioni solo quando viene associato il centro di costo appropriato. In caso contrario la richiesta deve essere negata. Ciò sarebbe utile per gestire l'addebito sul centro di costo appropriato per le operazioni eseguite.

Un altro scenario comune è rappresentato dai casi in un cui un'organizzazione ha l'esigenza di controllare la posizione di creazione delle risorse oppure di controllare l'accesso alle risorse consentendo il provisioning solo di determinati tipi di risorse.

In modo analogo, un'organizzazione può controllare il catalogo dei servizi o applicare le convenzioni di denominazione desiderate per le risorse.

Questi scenari sono facilmente realizzabili con i criteri, come descritto di seguito.

## Struttura delle definizioni di criteri

La definizione dei criteri viene creata tramite JSON. È costituita da uno o più condizioni/operatori logici che definiscono le azioni e un effetto che indica cosa succede quando sono soddisfatte le condizioni.

I criteri includono fondamentalmente gli elementi seguenti:

**Condizioni/operatori logici:** contiene un set di condizioni che possono essere modificate tramite un set di operatori logici.

**Effetto:** descrive l'effetto quando la condizione viene soddisfatta, ovvero negazione o controllo. Per un effetto di controllo verrà generato un log del servizio eventi di avviso. Ad esempio, un amministratore può creare un criterio per attivare il controllo nel caso chiunque crei una macchina virtuale di grandi dimensioni. L'amministratore potrà poi esaminare questi log in un secondo momento.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Operatori logici

Gli operatori logici supportati con la relativa sintassi sono elencati di seguito:

| Nome operatore | Sintassi |
| :------------- | :------------- |
| Not | "not" : {&lt;condizione&gt;} |
| E | "allOf" : [ {&lt;condizione1&gt;},{&lt;condizione2&gt;}] |
| Or | "anyOf" : [ {&lt;condizione1&gt;},{&lt;condizione2&gt;}] |


## Condizioni

Le condizioni supportate con la relativa sintassi sono elencate di seguito:

| Nome condizione | Sintassi |
| :------------- | :------------- |
| Uguale a | "equals" : "&lt;valore&gt;" |
| Come | "like" : "&lt;valore&gt;" |
| Contiene | "contains" : "&lt;valore&gt;"|
| In | "in" : [ "&lt;valore1&gt;","&lt;valore2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;nomeChiave&gt;" |


## Campi e origini

Le condizioni vengono create usando campi e origini. Sono supportati i campi e le origini seguenti:

Campi: **name**, **kind**, **type**, **location**, **tags**, **tags.***.

Origini: **action**

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


### Conformità geografica: verifica della posizione delle risorse

L'esempio seguente illustra un criterio che negherà tutte le richieste con posizione diversa da Europa settentrionale o Europa occidentale.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["north europe" , "west europe"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Controllo dei servizi: selezione del catalogo dei servizi

L'esempio seguente illustra l'uso dell'origine. Mostra che sono consentite solo le azioni sui servizi di tipo Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/*, Microsoft.Network/*. Qualsiasi altra azione verrà negata.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
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

## Assegnazione dei criteri

I criteri possono essere applicati in ambiti diversi come sottoscrizione, gruppi di risorse e singole risorse. I criteri vengono ereditati da tutte le risorse figlio. Pertanto, se un criterio viene applicato a un gruppo di risorse, sarà applicabile a tutte le risorse in tale gruppo.

## Creazione dei criteri

Questa sezione include informazioni dettagliate sulla procedura per la creazione dei criteri con l'API REST e PowerShell.

### Creare una definizione di criteri con l'API REST

È possibile creare criteri con l'API REST per i criteri. L'API REST consente di creare ed eliminare i criteri e recuperare informazioni sui criteri esistenti.

Per creare un nuovo criterio, eseguire:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Il corpo della richiesta avrà un aspetto simile a quello riportato nell'esempio seguente:

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
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


La definizione dei criteri può essere definita come uno degli esempi illustrati in precedenza. Per api-version usare *2015-10-01-preview*. Per esempi e altri dettagli, vedere API REST per i criteri.

## Applicazione dei criteri

### Assegnazione dei criteri con l'API REST

È possibile applicare la definizione di criteri nell'ambito desiderato tramite l'API REST per l'assegnazione dei criteri. L'API REST consente di creare ed eliminare le assegnazioni dei criteri, nonché di ottenere informazioni sulle assegnazioni esistenti.

Per creare una nuova assegnazione di criteri, eseguire:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} è il nome dell'assegnazione di criteri. Per api-version usare *2015-10-01-preview*. Per esempi e altri dettagli, vedere API REST per l'assegnazione dei criteri.

Il corpo della richiesta avrà un aspetto simile a quello riportato nell'esempio seguente:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

<!---HONumber=Oct15_HO1-->