---
title: Onboarding delle risorse con provider personalizzati di Azure
description: L'onboarding delle risorse tramite provider personalizzati consente di modificare ed estendere le risorse di Azure esistenti.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 45086987d5ba3a619028ced798712f2870c6d487
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826792"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Esercitazione: Onboarding delle risorse con provider personalizzati di Azure

In questa esercitazione verrà distribuito in Azure un provider di risorse personalizzato che estende l'API Azure Resource Manager con il tipo di risorsa Microsoft. CustomProviders/associations. L'esercitazione illustra come estendere le risorse esistenti che si trovano all'esterno del gruppo di risorse in cui si trova l'istanza del provider personalizzato. In questa esercitazione il provider di risorse personalizzato è fornito da un'app per la logica di Azure, ma è possibile usare qualsiasi endpoint API pubblico.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario sapere come:

* Funzionalità dei [provider personalizzati di Azure](custom-providers-overview.md).
* Informazioni di base sull'[onboarding delle risorse con provider personalizzati](concepts-custom-providers-resourceonboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Introduzione all'onboarding delle risorse

In questa esercitazione sono disponibili due elementi che devono essere distribuiti: il provider personalizzato e l'associazione. Per semplificare il processo, è possibile usare un unico modello che distribuisce entrambi gli elementi.

Il modello userà queste risorse:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Distribuire l'infrastruttura del provider personalizzato

La prima parte del modello distribuisce l'infrastruttura del provider personalizzato. Questa infrastruttura definisce l'effetto della risorsa delle associazioni. Se non si ha familiarità con i provider personalizzati, vedere [Cenni preliminari sui provider personalizzati](custom-providers-overview.md).

Distribuire l'infrastruttura del provider personalizzato. Copiare, salvare e distribuire il modello precedente oppure proseguire e distribuire l'infrastruttura usando il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare i **modelli** in **Tutti i servizi** o usando la casella di ricerca principale:

   ![Cercare i modelli](media/custom-providers-resource-onboarding/templates.png)

3. Selezionare **Aggiungi** nel riquadro **Modelli**:

   ![Selezionare Aggiungi](media/custom-providers-resource-onboarding/templatesadd.png)

4. In **Generale** immettere un **Nome** e una **Descrizione** per il nuovo modello:

   ![Nome e descrizione del modello](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Creare il modello di Resource Manager copiando nel modello JSON dalla sezione "Introduzione all'onboarding delle risorse" di questo articolo:

   ![Creare un modello di Resource Manager](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Selezionare **Aggiungi** per creare il modello. Se il nuovo modello non viene visualizzato, selezionare **Aggiorna**.

7. Selezionare il modello appena creato, quindi selezionare **Distribuisci**:

   ![Selezionare il nuovo modello e quindi selezionare Distribuisci](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Immettere le impostazioni per i campi obbligatori e quindi selezionare la sottoscrizione e il gruppo di risorse. È possibile lasciare vuota la casella **Custom Resource Provider Id** (ID provider di risorse personalizzato).

   | Nome impostazione | Obbligatorio? | DESCRIZIONE |
   | ------------ | -------- | ----------- |
   | Location | Sì | Posizione delle risorse nel modello. |
   | Nome dell'app per la logica | No | Nome dell'app per la logica. |
   | Custom Resource Provider Name (Nome provider di risorse personalizzato) | No | Nome del provider di risorse personalizzato. |
   | Custom Resource Provider Id (ID provider di risorse personalizzato) | No | Provider di risorse personalizzato esistente che supporta la risorsa di associazione. Se si specifica un valore, l'app per la logica e la distribuzione del provider personalizzato verranno ignorate. |
   | Nome associazione | No | Nome della risorsa di associazione. |

   Parametri di esempio:

   ![Immettere i parametri del modello](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Passare alla distribuzione e attendere il completamento. Verrà visualizzata una schermata simile allo screenshot seguente. Verrà visualizzata la nuova risorsa di associazione come output:

   ![Distribuzione completata](media/custom-providers-resource-onboarding/customproviderdeployment.png)

   Di seguito è riportato il gruppo di risorse con l'opzione **Mostra tipi nascosti** selezionata:

   ![Distribuzione del provider personalizzato](media/custom-providers-resource-onboarding/showhidden.png)

10. Esplorare la scheda **Cronologia esecuzioni** dell'app per la logica per visualizzare le chiamate per l'associazione CREATE:

    ![Cronologia esecuzioni dell'app per la logica](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Distribuire associazioni aggiuntive

Dopo aver configurato l'infrastruttura del provider personalizzato, è possibile distribuire facilmente altre associazioni. Il gruppo di risorse per le associazioni aggiuntive non deve corrispondere al gruppo di risorse in cui è stata distribuita l'infrastruttura del provider personalizzato. Per creare un'associazione, è necessario avere le autorizzazioni Microsoft.CustomProviders/resourceproviders/write per l'ID del provider di risorse personalizzato specificato.

1. Passare alla risorsa **Microsoft.CustomProviders/resourceProviders** del provider personalizzato nel gruppo di risorse della distribuzione precedente. È necessario selezionare la casella di controllo **Mostra tipi nascosti**:

   ![Passare alla risorsa](media/custom-providers-resource-onboarding/showhidden.png)

2. Copiare la proprietà ID risorsa del provider personalizzato.

3. Cercare i **modelli** in **Tutti i servizi** o usando la casella di ricerca principale:

   ![Cercare i modelli](media/custom-providers-resource-onboarding/templates.png)

4. Selezionare il modello creato in precedenza, quindi selezionare **Distribuisci**:

   ![Selezionare il modello creato in precedenza, quindi selezionare Distribuisci](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Immettere le impostazioni per i campi obbligatori e quindi selezionare la sottoscrizione e un gruppo di risorse diverso. Per l'impostazione **Custom Resource Provider Id** (ID provider di risorse personalizzato), immettere l'ID risorsa copiato dal provider personalizzato distribuito in precedenza.

6. Passare alla distribuzione e attendere il completamento. A questo punto verrà distribuita solo la nuova risorsa associations:

   ![Nuova risorsa associations](media/custom-providers-resource-onboarding/createdassociationresource.png)

Se si vuole, è possibile tornare alla **Cronologia esecuzioni** dell'app per la logica e verificare che sia stata effettuata un'altra chiamata all'app per la logica. È possibile aggiornare l'app per la logica per aumentare la funzionalità aggiuntiva per ogni associazione creata.

## <a name="getting-help"></a>Risorse della Guida

In caso di domande sui provider personalizzati di Azure, provare a inviarle a [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di inserirle. Aggiungere il tag `azure-custom-providers` per ottenere una risposta rapida.

