---
title: Risorse di automazione di Azure nelle soluzioni OMS | Microsoft Docs
description: Le soluzioni in OMS contengono in genere runbook in Automazione di Azure per automatizzare i processi, ad esempio la raccolta e l'elaborazione dei dati di monitoraggio.  Questo articolo descrive come includere i runbook e le risorse correlate in una soluzione.
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1909183a33ed03d8165671cff25cc8b83b77733
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-azure-automation-resources-to-an-oms-management-solution-preview"></a>Aggiunta di risorse di automazione di Azure a una soluzione di gestione OMS (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.   


Le [soluzioni di gestione in OMS](operations-management-suite-solutions.md) contengono in genere runbook in Automazione di Azure per automatizzare i processi, ad esempio la raccolta e l'elaborazione dei dati di monitoraggio.  Oltre ai runbook, gli account di Automazione includono asset come le variabili e le pianificazioni che supportano i runbook usati nella soluzione.  Questo articolo descrive come includere i runbook e le risorse correlate in una soluzione.

> [!NOTE]
> Gli esempi in questo articolo usano parametri e variabili che sono richiesti o comuni nelle soluzioni di gestione e che sono descritti in [Creazione di soluzioni di gestione in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Prerequisiti
Nell'articolo si presuppone che il lettore abbia già familiarità con le informazioni seguenti:

- Come [creare una soluzione di gestione](operations-management-suite-solutions-creating.md).
- La struttura di un [file di soluzione](operations-management-suite-solutions-solution-file.md).
- Come [creare modelli di Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Account di Automazione
Tutte le risorse di Automazione di Azure sono contenute in un [account di Automazione](../automation/automation-security-overview.md#automation-account-overview).  Come descritto in [Area di lavoro OMS e account di Automazione](operations-management-suite-solutions.md#oms-workspace-and-automation-account), l'account di Automazione non è incluso nella soluzione di gestione, ma deve essere presente prima dell'installazione della soluzione.  Se non è disponibile, l'installazione della soluzione non riuscirà.

Il nome di ogni risorsa di automazione include il nome del rispettivo account di automazione.  Questa operazione viene eseguita nella soluzione con il parametro **accountName** come nell'esempio seguente di una risorsa runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
È consigliabile includere nel file di soluzione eventuali runbook usati dalla soluzione, in modo che vengano creati nel momento in cui viene installata la soluzione.  Non è possibile, tuttavia, contenere il corpo del runbook nel modello ed è quindi necessario pubblicare il runbook in una posizione pubblica a cui può accedere qualsiasi utente che installa la soluzione.

Le risorse [runbook di automazione di Azure](../automation/automation-runbook-types.md) sono di tipo **Microsoft.Automation/automationAccounts/runbooks** e presentano la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Le proprietà dei runbook sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| runbookType |Specifica il tipo del runbook. <br><br> Script - Script di PowerShell <br>PowerShell - Flusso di lavoro di PowerShell <br> GraphPowerShell - Runbook di script di PowerShell grafico <br> GraphPowerShellWorkflow - Runbook di flusso di lavoro di PowerShell grafico |
| logProgress |Specifica se devono essere generati [record di avanzamento](../automation/automation-runbook-output-and-messages.md) per il runbook. |
| logVerbose |Specifica se devono essere generati [record dettagliati](../automation/automation-runbook-output-and-messages.md) per il runbook. |
| description |Descrizione facoltativa per il runbook. |
| publishContentLink |Specifica il contenuto del runbook. <br><br>uri - URI del contenuto del runbook.  Si tratterà di un file con estensione ps1 per i runbook di PowerShell e di script e di un file di runbook grafico esportato per un runbook di Graph.  <br> version - Versione del runbook per il monitoraggio. |


## <a name="automation-jobs"></a>Processi di Automazione
Quando si avvia un runbook in Automazione di Azure, viene creato un processo di automazione.  È possibile aggiungere una risorsa "processo di automazione" alla soluzione per consentire l'avvio automatico di un runbook nel momento in cui viene installata la soluzione di gestione.  Questo metodo, in genere, viene adottato per avviare i runbook usati per la configurazione iniziale della soluzione.  Per avviare un runbook a intervalli regolari, creare una [pianificazione](#schedules) e una [pianificazione processi](#job-schedules).

Le risorse "processo" sono di tipo **Microsoft.Automation/automationAccounts/jobs** e presentano la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Le proprietà dei processi di automazione sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| runbook |Entità name singola con il nome del runbook da avviare. |
| parameters |Entità relativa ad ogni valore di parametro richiesto dal runbook. |

Il processo include il nome del runbook e i valori dei parametri da inviare al runbook.  Il processo deve [dipendere](operations-management-suite-solutions-solution-file.md#resources) dal runbook in fase di avvio, poiché questo deve essere creato prima del processo.  Se sono presenti più runbook da avviare, è possibile definire l'ordine di avvio impostando un processo in modo che dipenda da un altro processo che deve essere eseguito prima.

Il nome di una risorsa processo deve contenere un GUID che viene in genere assegnato da un parametro.  Altre informazioni sui parametri GUID sono disponibili in [Creazione di soluzioni di gestione in Operations Management Suite (OMS)](operations-management-suite-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificati
I [certificati di automazione di Azure](../automation/automation-certificates.md) sono di tipo **Microsoft.Automation/automationAccounts/certificates** e presentano la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



Le proprietà delle risorse "certificati" sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| base64Value |Valore Base 64 per il certificato. |
| thumbprint |Identificazione personale del certificato. |



## <a name="credentials"></a>Credenziali
Le [credenziali di automazione di Azure](../automation/automation-credentials.md) sono di tipo **Microsoft.Automation/automationAccounts/credentials** e presentano la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

Le proprietà delle risorse "credenziali" sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| userName |Nome utente per la credenziale. |
| password |Password per la credenziale. |


## <a name="schedules"></a>Pianificazioni
Le [pianificazioni di automazione di Azure](../automation/automation-schedules.md) sono di tipo **Microsoft.Automation/automationAccounts/schedules** e presentano la struttura seguente. Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Le proprietà delle risorse pianificazione sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| description |Descrizione facoltativa per la pianificazione. |
| startTime |Specifica l'ora di inizio di una pianificazione come oggetto DateTime. È possibile fornire una stringa, se può essere convertita in un oggetto DateTime valido. |
| isEnabled |Specifica se la pianificazione è abilitata. |
| interval |Tipo di intervallo per la pianificazione.<br><br>day<br>hour |
| frequency |Frequenza con cui la pianificazione deve essere attivata, in numero di ore o giorni. |

Per le pianificazioni deve essere definita un'ora di avvio con un valore successivo all'ora corrente.  Non è possibile specificare questo valore con una variabile poiché non è possibile sapere quando verrà installata la soluzione.

Applicare una delle due strategie seguenti quando si usano risorse di pianificazione in una soluzione.

- Usare un parametro per l'ora di avvio della pianificazione:  all'utente verrà richiesto di specificare un valore durante l'installazione della soluzione.  In caso di più pianificazioni, è possibile usare un unico valore di parametro anche per più di esse.
- Creare le pianificazioni usando un runbook che viene avviato al momento dell'installazione della soluzione.  In questo modo viene eliminata la necessità per l'utente di specificare un'ora, ma la pianificazione non potrà essere contenuta nella soluzione e verrà quindi rimossa con l'eliminazione della soluzione.


### <a name="job-schedules"></a>Pianificazioni dei processi
Le risorse "pianificazione dei processi" collegano un runbook a una pianificazione.  Sono di tipo **Microsoft.Automation/automationAccounts/jobSchedules** e presentano la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


Le proprietà delle pianificazioni dei processi sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| schedule name |Entità **name** singola con il nome della pianificazione. |
| runbook name  |Entità **name** singola con il nome del runbook.  |



## <a name="variables"></a>Variabili
Le [variabili di automazione di Azure](../automation/automation-variables.md) sono di tipo **Microsoft.Automation/automationAccounts/variables** e presentano la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

Le proprietà delle risorse "variabile" sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| description | Descrizione facoltativa per la variabile. |
| isEncrypted | Specifica se la variabile deve essere crittografata. |
| type | Questa proprietà attualmente non ha alcun effetto.  Il tipo di dati della variabile verrà determinato dal valore iniziale. |
| value | Valore per la variabile. |

> [!NOTE]
> La proprietà **type** attualmente non ha alcun effetto sulla variabile che viene creata.  Il tipo di dati per la variabile verrà determinato dal valore.  

Se si imposta il valore iniziale per la variabile, è necessario configurarla come tipo di dati corretto.  La tabella seguente elenca i diversi tipi di dati disponibili e la rispettiva sintassi.  Si noti che i valori in JSON devono essere sempre racchiusi tra virgolette con qualsiasi carattere speciale tra virgolette.  Un valore di stringa, ad esempio, verrà specificato dalle virgolette all'inizio e alla fine della stringa, usando il carattere di escape (\\), mentre un valore numerico verrà specificato con un set di virgolette.

| Tipo di dati | Descrizione | Esempio | Risoluzione |
|:--|:--|:--|:--|
| string   | Racchiude il valore tra virgolette doppie.  | "\"Hello world\"" | "Hello world" |
| numeric  | Valore numerico con virgolette singole.| "64" | 64 |
| boolean  | **true** o **false** tra virgolette.  Si noti che questo valore deve essere minuscolo. | "true" | true |
| datetime | Valore di data serializzato.<br>È possibile usare il cmdlet ConvertTo-Json in PowerShell per generare questo valore per una particolare data.<br>Esempio: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduli
La soluzione di gestione non deve necessariamente definire i [moduli globali](../automation/automation-integration-modules.md) usati dai runbook, poiché saranno sempre disponibili nel proprio account di automazione.  È tuttavia necessario includere una risorsa per qualsiasi altro modulo usato dai runbook.

I [moduli di integrazione](../automation/automation-integration-modules.md) sono di tipo **Microsoft.Automation/automationAccounts/modules** e presentano la struttura seguente.  Nella struttura sono inclusi parametri e variabili comuni ed è quindi possibile copiare e incollare questo frammento di codice nel file della soluzione e, se necessario, modificare i nomi dei parametri.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


Le proprietà delle risorse "modulo" sono descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| contentLink |Specifica il contenuto del modulo. <br><br>uri - URI del contenuto del modulo.  Si tratterà di un file con estensione ps1 per i runbook di PowerShell e di script e di un file di runbook grafico esportato per un runbook di Graph.  <br> version - Versione del modulo per il monitoraggio. |

Il runbook deve dipendere dalla risorsa "modulo" affinché la risorsa venga creata prima del runbook.

### <a name="updating-modules"></a>Aggiornamento dei moduli
Se si aggiorna una soluzione di gestione che include un runbook che usa una pianificazione e la nuova versione della soluzione ha un nuovo modulo usato dal runbook, il runbook potrebbe usare la versione precedente del modulo.  È necessario includere i runbook seguenti nella soluzione e creare un processo per eseguirli prima di qualsiasi altro runbook.  In questo modo, tutti i moduli verranno aggiornati come necessario prima del caricamento del runbook.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantisce che tutti i moduli usati dal runbook nella soluzione siano della versione più recente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) esegue nuovamente la registrazione di tutte le risorse pianificazione per garantire che i runbook collegati useranno i moduli più recenti.




## <a name="sample"></a>Esempio
Di seguito è riportato un esempio di soluzione che include le risorse seguenti:

- Runbook.  Si tratta di un runbook di esempio archiviato in un repository GitHub pubblico.
- Processo di automazione che avvia il runbook quando viene installata la soluzione.
- Pianificazione e pianificazione dei processi per avviare il runbook a intervalli regolari.
- Certificato.
- Credenziali.
- Variabile.
- Modulo.  Si tratta del [modulo OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) per la scrittura di dati in Log Analytics. 

L'esempio usa variabili dei [parametri di soluzione standard](operations-management-suite-solutions-solution-file.md#parameters) comunemente usate in una soluzione, anziché impostare i valori come hardcoded nelle definizioni delle risorse.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere una vista alla soluzione](operations-management-suite-solutions-resources-views.md) per visualizzare i dati raccolti.
