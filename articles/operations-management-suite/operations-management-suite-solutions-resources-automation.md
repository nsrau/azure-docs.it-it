---
title: Risorse di automazione nelle soluzioni OMS | Microsoft Docs
description: Le soluzioni in OMS contengono in genere runbook in Automazione di Azure per automatizzare i processi, ad esempio la raccolta e l&quot;elaborazione dei dati di monitoraggio.  Questo articolo descrive come includere i runbook e le risorse correlate in una soluzione.
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>Risorse di automazione nelle soluzioni OMS (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.   
> 
> 

Le [soluzioni di gestione in OMS](operations-management-suite-solutions.md) contengono in genere runbook in Automazione di Azure per automatizzare i processi, ad esempio la raccolta e l'elaborazione dei dati di monitoraggio.  Oltre ai runbook, gli account di Automazione includono asset come le variabili e le pianificazioni che supportano i runbook usati nella soluzione.  Questo articolo descrive come includere i runbook e le risorse correlate in una soluzione.

> [!NOTE]
> Gli esempi in questo articolo usano parametri e variabili che sono richiesti o comuni nelle soluzioni di gestione e che sono descritti in [Creazione di soluzioni di gestione in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) 
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si abbia già familiarità con le modalità di [creazione di una soluzione di gestione](operations-management-suite-solutions-creating.md) e la struttura di un file di soluzione.

## <a name="samples"></a>Esempi
È possibile ottenere modelli di esempio di Resource Manager per le risorse di Automazione dai [modelli di avvio rapido in GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Account di Automazione
Tutte le risorse di Automazione di Azure sono contenute in un [account di Automazione](../automation/automation-security-overview.md#automation-account-overview).  Come descritto in [Area di lavoro OMS e account di Automazione](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account), l'account di Automazione non è incluso nella soluzione di gestione, ma deve essere presente prima dell'installazione della soluzione.  Se non è disponibile, l'installazione della soluzione non riuscirà.

Il nome dell'account di Automazione corrisponde al nome di ogni risorsa di Automazione.  Questa operazione viene eseguita nella soluzione con il parametro **accountName** come nell'esempio seguente di una risorsa runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
Le risorse [runbook di Automazione di Azure](../automation/automation-runbook-types.md) sono di tipo **Microsoft.Automation/automationAccounts/runbooks** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| runbookType |Specifica il tipo del runbook. <br><br> Script - Script di PowerShell <br>PowerShell - Flusso di lavoro di PowerShell <br> GraphPowerShell - Runbook di script di PowerShell grafico <br> GraphPowerShellWorkflow - Runbook di flusso di lavoro di PowerShell grafico |
| logProgress |Specifica se devono essere generati [record di avanzamento](../automation/automation-runbook-output-and-messages.md) per il runbook. |
| logVerbose |Specifica se devono essere generati [record dettagliati](../automation/automation-runbook-output-and-messages.md) per il runbook. |
| description |Descrizione facoltativa per il runbook. |
| publishContentLink |Specifica il contenuto del runbook. <br><br>uri - URI del contenuto del runbook.  Si tratterà di un file con estensione ps1 per i runbook di PowerShell e di script e di un file di runbook grafico esportato per un runbook di Graph.  <br> version - Versione del runbook per il monitoraggio. |

Di seguito è riportato un esempio di risorsa runbook.  In questo caso, il runbook viene recuperato da [PowerShell Gallery](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Avvio di un runbook
Ci sono due modi per avviare un runbook in una soluzione di gestione.

* Per avviare il runbook quando si installa la soluzione, creare una [risorsa processo](#automation-jobs) come descritto di seguito.
* Per avviare il runbook in base a una pianificazione, creare una [risorsa pianificazione](#schedules) come descritto di seguito. 

## <a name="automation-jobs"></a>Processi di Automazione
Per avviare un runbook quando si installa la soluzione di gestione, è necessario creare una risorsa **processo**.  Le risorse processo sono di tipo **Microsoft.Automation/automationAccounts/jobs** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| runbook |Entità **name** singola con il nome del runbook. |
| parameters |Entità per ogni parametro richiesto dal runbook. |

Il processo include il nome del runbook e i valori dei parametri da inviare al runbook.  Il processo deve [dipendere](operations-management-suite-solutions-creating.md#resources) dall'avvio del runbook, perché il runbook deve essere creato prima del processo.  È anche possibile creare dipendenze da altri processi per i runbook, che devono essere completati prima di quello corrente.

Il nome di una risorsa processo deve contenere un GUID che viene in genere assegnato da un parametro.  Altre informazioni sui parametri GUID sono disponibili in [Creazione di soluzioni di gestione in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Di seguito è disponibile un esempio di risorsa processo che avvia un runbook quando si installa la soluzione di gestione.  Prima dell'avvio di questo runbook ne deve essere completato un altro, quindi sono presenti dipendenze dai processi per tale runbook.  I dettagli del processo vengono forniti tramite parametri e variabili invece che essere specificati direttamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificati
I [certificati di Automazione di Azure](../automation/automation-certificates.md) sono di tipo **Microsoft.Automation/automationAccounts/certificates** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| base64Value |Valore Base 64 per il certificato. |
| thumbprint |Identificazione personale del certificato. |

Di seguito è riportato un esempio di risorsa certificato.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenziali
Le [credenziali di Automazione di Azure](../automation/automation-credentials.md) sono di tipo **Microsoft.Automation/automationAccounts/credentials** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| userName |Nome utente per la credenziale. |
| password |Password per la credenziale. |

Di seguito è riportato un esempio di risorsa credenziale.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Pianificazioni
Le [pianificazioni di Automazione di Azure](../automation/automation-schedules.md) sono di tipo **Microsoft.Automation/automationAccounts/schedules** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| description |Descrizione facoltativa per la pianificazione. |
| startTime |Specifica l'ora di inizio di una pianificazione come oggetto DateTime. È possibile fornire una stringa, se può essere convertita in un oggetto DateTime valido. |
| isEnabled |Specifica se la pianificazione è abilitata. |
| interval |Tipo di intervallo per la pianificazione.<br><br>day<br>hour |
| frequency |Frequenza con cui la pianificazione deve essere attivata, in numero di ore o giorni. |

Di seguito è riportato un esempio di risorsa pianificazione.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variabili
Le [variabili di Automazione di Azure](../automation/automation-variables.md) sono di tipo **Microsoft.Automation/automationAccounts/variables** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| description |Descrizione facoltativa per la variabile. |
| isEncrypted |Specifica se la variabile deve essere crittografata. |
| type |Tipo di dati per la variabile. |
| value |Valore per la variabile. |

Di seguito è riportato un esempio di risorsa variabile.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Moduli
La soluzione di gestione non deve necessariamente definire i [moduli globali](../automation/automation-integration-modules.md) usati dai runbook, perché saranno sempre disponibili.  È necessario includere una risorsa per qualsiasi altro modulo usato dai runbook e il runbook deve dipendere dalla risorsa modulo, per assicurare che tale risorsa venga creata prima del runbook.

I [moduli di integrazione](../automation/automation-integration-modules.md) sono di tipo **Microsoft.Automation/automationAccounts/modules** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| contentLink |Specifica il contenuto del modulo. <br><br>uri - URI del contenuto del runbook.  Si tratterà di un file con estensione ps1 per i runbook di PowerShell e di script e di un file di runbook grafico esportato per un runbook di Graph.  <br> version - Versione del runbook per il monitoraggio. |

Di seguito è riportato un esempio di risorsa modulo.

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Aggiornamento dei moduli
Se si aggiorna una soluzione di gestione che include un runbook che usa una pianificazione e la nuova versione della soluzione ha un nuovo modulo usato dal runbook, il runbook potrebbe usare la versione precedente del modulo.  È necessario includere i runbook seguenti nella soluzione e creare un processo per eseguirli prima di qualsiasi altro runbook.  In questo modo, tutti i moduli verranno aggiornati come necessario prima del caricamento del runbook.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantisce che tutti i moduli usati dal runbook nella soluzione siano della versione più recente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) esegue nuovamente la registrazione di tutte le risorse pianificazione per garantire che i runbook collegati useranno i moduli più recenti.

Di seguito è riportato un esempio degli elementi necessari di una soluzione per supportare l'aggiornamento dei moduli.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere una vista alla soluzione](operations-management-suite-solutions-resources-views.md) per visualizzare i dati raccolti.




<!--HONumber=Nov16_HO3-->


