---
title: Configurare avvisi di integrità risorse di Azure tramite i modelli di Gestione risorse | Microsoft Docs
description: Creare avvisi a livello di codice che inviano una notifica quando le risorse di Azure non sono disponibili.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 9/4/2018
ms.openlocfilehash: 7ccd84042d11b586d524d4eb76eba03111e0b3c5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099017"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Configurare avvisi di integrità risorse di Azure tramite modelli di Gestione risorse

Questo articolo illustrerà come creare avvisi del registro delle attività di integrità risorse a livello di codice tramite modelli di Azure Resource Manager e Azure PowerShell.

Integrità risorse di Azure comunica lo stato di integrità attuale e cronologico delle risorse di Azure. Integrità risorse di Azure invia una notifica quasi in tempo reale quando tali risorse subiscono una modifica al loro stato di integrità. Creare avvisi di Integrità risorse di Azure a livello di codice consente agli utenti di creare e personalizzare gli avvisi in blocco.

> [!NOTE]
> Gli avvisi Integrità risorse sono attualmente in anteprima.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per seguire le istruzioni riportate in questa pagina, è necessario configurare in anticipo alcuni aspetti:

1. È necessario installare il [modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. È necessario [creare o riutilizzare un gruppo di azioni](../azure-monitor/platform/action-groups.md) configurato per ricevere una notifica

## <a name="instructions"></a>Istruzioni
1. Tramite PowerShell, accedere ad Azure con il proprio account e selezionare la sottoscrizione con cui si desidera interagire

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > È possibile usare `Get-AzSubscription` per elencare le sottoscrizioni che è possibile utilizzare.

2. Individuare e salvare l'ID di Azure Resource Manager completo per il gruppo di azioni

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Creare e salvare un modello di Gestione risorse per gli avvisi di Integrità risorse come `resourcehealthalert.json` ([vedere i dettagli di seguito](#resource-manager-template-options-for-resource-health-alerts))

4. Creare una nuova distribuzione di Azure Resource Manager usando questo modello

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Verrà richiesto di digitare il nome dell'avviso e l'ID di risorsa gruppo di azioni copiato in precedenza:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Se tutto funziona correttamente, si riceverà un messaggio di conferma in PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Se si intende automatizzare completamente questo processo, è sufficiente modificare il modello di Gestione risorse per non richiedere i valori nel passaggio 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opzioni del modello di Gestione risorse per gli avvisi di Integrità risorse

È possibile usare questo modello di base come punto iniziale per la creazione di avvisi di Integrità risorse di Azure. Questo modello funziona come descritto in precedenza ed effettuerà l'iscrizione per ricevere avvisi per tutti gli eventi di integrità risorse appena attivati in ogni risorsa presente in una sottoscrizione.

> Nella parte inferiore di questo articolo è stato inoltre incluso un modello di avviso più complesso che, rispetto a questo, dovrebbe aumentare il rapporto segnale/rumore per gli avvisi di Integrità risorse di Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Tuttavia, un ampio avviso come questo è in genere sconsigliato. Di seguito, informazioni su come è possibile restringere questo avviso per porre l'attenzione su eventi importanti.

### <a name="adjusting-the-alert-scope"></a>Modificare l'ambito degli avvisi

Gli avvisi di Integrità risorse di Azure possono essere configurati per monitorare gli eventi in tre ambiti diversi:

 * Livello di sottoscrizione
 * Il livello Gruppo di risorse
 * Il livello Risorsa

Il modello di avviso è configurato a livello di sottoscrizione, ma se si desidera configurare l'avviso in modo che dia informazioni solo su determinate risorse o su risorse all'interno di un determinato gruppo di risorse, è sufficiente modificare la sezione `scopes` nel modello precedente.

Per un ambito a livello gruppo di risorse, la sezione ambiti dovrebbe essere simile a:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

E per un ambito a livello di risorse, la sezione ambiti dovrebbe essere simile a:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Ad esempio: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Per ottenere questa stringa, è possibile accedere al portale di Azure e osservare l'URL quando si visualizzano le risorse di Azure.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Modificare il tipo di risorsa che invia un avviso

Gli avvisi a livello di gruppo di risorse o sottoscrizione possono avere diversi tipi di risorse. Se si desidera limitare gli avvisi solo a quelli provenienti da un determinato subset di tipi di risorse, è possibile definire questa opzione nella sezione `condition` del modello come segue:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Qui viene usata la wrapper `anyOf` per consentire l'avviso di integrità risorse in modo che corrisponda a una delle condizioni specificate, consentendo di inviare avvisi destinati a tipi di risorse specifiche.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Modifica gli eventi di Integrità risorse di Azure che inviano un avviso
Se le risorse sono sottoposte a un evento di integrità, possono passare attraverso una serie di fasi che rappresentano lo stato dell'evento di integrità: `Active`, `InProgress`, `Updated`, e `Resolved`.

È possibile ricevere una notifica quando una risorsa diventa non integra, in questo caso si configura l'avviso per inviare notifiche solo quando `status` è `Active`. Tuttavia se si vuole essere avvisati anche su tutte le altre fasi, è possibile aggiungere dettagli come illustrato di seguito:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Se si desidera ricevere una notifica per tutti i quattro passaggi degli eventi di integrità, è possibile rimuovere del tutto questa condizione in modo che l'avviso notifichi indipendentemente dalla proprietà dello`status`.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Modificare gli avvisi di Integrità risorse di Azure per evitare gli eventi "Sconosciuti"

Integrità risorse di Azure può segnalare all'utente l'integrità delle risorse più recente grazie al monitoraggio continuo effettuato usando gli strumenti di esecuzione di test. Gli stati di integrità segnalati pertinenti sono: "Disponibile", "Non disponibile" e "Danneggiato". Tuttavia, nelle situazioni in cui la risorsa di Azure e lo strumento di esecuzione non consentono la comunicazione, viene segnalato per la risorsa uno stato di integrità "Sconosciuto", considerato come evento di integrità "Attivo".

Tuttavia, quando una risorsa segnala lo stato "Sconosciuto", è probabile che lo stato di integrità non sia stato modificato dopo l'ultimo report accurato. Se si desidera eliminare gli avvisi sugli eventi "Sconosciuti", è possibile specificare questa logica nel modello:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

In questo esempio, si sta solo inviando una notifica sugli eventi in cui lo stato di integrità corrente e quello precedente non sono "Sconosciuti". Questa modifica può costituire un'aggiunta utile se gli avvisi sono inviati direttamente al telefono cellulare o tramite messaggio di posta elettronica. 

Si noti che è possibile che le proprietà currentHealthStatus e previousHealthStatus siano null in alcuni eventi. Ad esempio, quando si verifica un evento aggiornato è probabile che lo stato di integrità della risorsa non sia stato modificato dopo l'ultimo report, ma solo che siano disponibili informazioni aggiuntive sull'evento, ad esempio causa. Pertanto, l'utilizzo della clausola precedente può causare l'attivazione di alcuni avvisi, perché i valori Properties. currentHealthStatus e Properties. previousHealthStatus verranno impostati su null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Modifica dell'avviso per evitare gli eventi avviati dall'utente

Gli eventi Integrità risorse possono essere attivati da eventi avviati dalla piattaforma e avviati dall'utente. Potrebbe essere senato inviare solo una notifica quando l'evento di integrità è provocato dalla piattaforma di Azure.

Configurare l'avviso per filtrare solo questi tipi di eventi è semplice:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Si noti che è possibile che il campo della cause sia null in alcuni eventi. In altre termini, viene effettuata una transizione di integrità (ad esempio, disponibile per non disponibile) e l'evento viene registrato immediatamente per evitare ritardi di notifica. Pertanto, l'utilizzo della clausola precedente può comportare l'attivazione di un avviso, perché il valore della proprietà Properties. clause verrà impostato su null.

## <a name="complete-resource-health-alert-template"></a>Modello di avviso completo Integrità risorse

Utilizzando le diverse regolazioni descritte nella sezione precedente, di seguito è riportato un modello di esempio configurato per massimizzare il rapporto tra segnale e rumore. Tenere presente le avvertenze indicate sopra in cui i valori delle proprietà currentHealthStatus, previousHealthStatus e cause possono essere null in alcuni eventi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Tuttavia, ogni utente è consapevole di quali configurazioni sono più efficaci per il proprio utilizzo, quindi è possibile usare gli strumenti descritti in questa documentazione per adattarli alle proprio esigenze.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Integrità risorse:
-  [Panoramica su Integrità risorse di Azure](Resource-health-overview.md)
-  [Tipi di risorse e controlli integrità disponibili in Integrità risorse di Azure](resource-health-checks-resource-types.md)


Creare avviso di integrità dei servizi di Azure:
-  [Configurare gli avvisi per Integrità dei servizi di Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Schema degli eventi del log attività di Azure](../azure-monitor/platform/activity-log-schema.md)
