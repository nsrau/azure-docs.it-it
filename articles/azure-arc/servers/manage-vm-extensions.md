---
title: Gestione delle estensioni VM con i server abilitati per Azure Arc (anteprima)
description: I server abilitati per Azure Arc (anteprima) possono gestire la distribuzione delle estensioni delle macchine virtuali che forniscono attività di configurazione e automazione post-distribuzione con macchine virtuali non di Azure.
ms.date: 06/17/2020
ms.topic: conceptual
ms.openlocfilehash: 1b27172a14896041cb4217b12af41d6a04118721
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213120"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers-preview"></a>Gestione delle estensioni delle macchine virtuali con i server abilitati per Azure Arc (anteprima)

Le estensioni delle macchine virtuali sono piccole applicazioni che forniscono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione di software, la protezione antivirus o l'esecuzione di uno script al suo interno, è possibile usare un'estensione macchina virtuale.

Azure Arc Enabled Servers (anteprima) consente di distribuire le estensioni di VM di Azure in macchine virtuali Windows e Linux non di Azure, semplificando la gestione del computer ibrido in locale, perimetrale e altri ambienti cloud attraverso il ciclo di vita.

## <a name="key-benefits"></a>Vantaggi principali

Il supporto per l'estensione di VM per i server abilitati per Azure Arc (anteprima) offre i vantaggi principali seguenti:

* Usare la [configurazione dello stato di automazione di Azure](../../automation/automation-dsc-overview.md) per archiviare centralmente le configurazioni e mantenere lo stato desiderato dei computer connessi ibridi abilitati tramite l'estensione VM DSC.

* Raccogliere i dati di log per l'analisi con i [log in monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md) abilitati tramite l'estensione della macchina virtuale agente log Analytics. Questa operazione è utile per eseguire analisi complesse tra i dati da diverse origini.

* Con [monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md), analizza le prestazioni delle macchine virtuali Windows e Linux e monitora i processi e le dipendenze da altre risorse e processi esterni. Questa operazione viene eseguita tramite l'abilitazione dell'agente Log Analytics e delle estensioni della macchina virtuale dell'agente di dipendenza.

* Scaricare ed eseguire script su computer connessi ibridi usando l'estensione script personalizzata. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione.

## <a name="availability"></a>Disponibilità

La funzionalità di estensione della macchina virtuale è disponibile solo nell'elenco delle [aree supportate](overview.md#supported-regions). Assicurarsi di caricare il computer in una di queste aree.

## <a name="extensions"></a>Estensioni

In questa versione di anteprima, sono supportate le estensioni di macchina virtuale seguenti nei computer Windows e Linux.

|Estensione |Sistema operativo |Publisher |Informazioni aggiuntive |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Estensione script personalizzato Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Estensione DSC di Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente di Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics estensione VM per Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Estensione macchina virtuale di Dependency Agent per Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Estensione script personalizzato Linux versione 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Estensione DSC PowerShell per Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente di Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics estensione VM per Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Estensione macchina virtuale dell'agente di dipendenza per Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Le estensioni di macchina virtuale possono essere eseguite con Azure Resource Manager modelli, dal portale di Azure o Azure PowerShell nei server ibridi gestiti da server abilitati per Arc (anteprima).

Per informazioni sul pacchetto dell'agente di computer connesso di Azure e per informazioni dettagliate sul componente agente di estensione, vedere [Panoramica degli agenti](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità dipende dai provider di risorse di Azure seguenti nella sottoscrizione:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se non sono già registrati, seguire la procedura descritta in [registrare i provider di risorse di Azure](agent-overview.md#register-azure-resource-providers).

Per l'estensione della macchina virtuale dell'agente Log Analytics per Linux è necessario che Python 2. x sia installato nel computer di destinazione.

### <a name="connected-machine-agent"></a>Agente Connected Machine

Verificare che il computer corrisponda alle [versioni supportate](agent-overview.md#supported-operating-systems) del sistema operativo Windows e Linux per l'agente del computer connesso di Azure.

La versione minima dell'agente del computer connesso supportato con questa funzionalità è la seguente:

* Windows-0.7. x
* Linux-0.8. x

Per aggiornare il computer alla versione dell'agente richiesta, vedere [upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Abilitare le estensioni dal portale

Le estensioni di macchina virtuale possono essere applicate al computer gestito di Arc per server (anteprima) tramite la portale di Azure.

1. Nel browser passare al [portale di Azure](https://aka.ms/arcserver-preview).

2. Nel portale passare a **machines-Azure Arc** e selezionare il computer ibrido nell'elenco.

3. Scegliere **estensioni**, quindi selezionare **Aggiungi**. Scegliere l'estensione desiderata dall'elenco di quelle disponibili e quindi seguire le istruzioni della procedura guidata. In questo esempio verrà distribuita l'estensione della macchina virtuale Log Analytics. 

    ![Selezionare l'estensione della macchina virtuale per il computer selezionato](./media/manage-vm-extensions/add-vm-extensions.png)

    Nell'esempio seguente viene illustrata l'installazione dell'estensione della macchina virtuale Log Analytics dal portale di Azure:

    ![Installare Log Analytics estensione VM](./media/manage-vm-extensions/mma-extension-config.png)

    Per completare l'installazione, è necessario specificare l'ID e la chiave primaria dell'area di lavoro. Se non si ha familiarità con la ricerca di queste informazioni, vedere [ottenere l'ID e la chiave dell'area di lavoro](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key).

4. Dopo aver confermato le informazioni necessarie, selezionare **Crea**. Viene visualizzato un riepilogo della distribuzione ed è possibile esaminare lo stato della distribuzione.

>[!NOTE]
>Sebbene sia possibile raggruppare ed elaborare più estensioni, queste vengono installate in modo seriale. Al termine dell'installazione della prima estensione, viene tentata l'installazione dell'estensione successiva.

## <a name="azure-resource-manager-templates"></a>Modelli di Azure Resource Manager

Le estensioni macchina virtuale possono essere aggiunte a un modello di Azure Resource Manager ed eseguite con la distribuzione del modello. Con le estensioni VM supportate dai server abilitati per Arc (anteprima), è possibile distribuire l'estensione VM supportata in computer Linux o Windows usando Azure PowerShell. Ogni esempio seguente include un file modello e un file di parametri con valori di esempio da fornire al modello.

>[!NOTE]
>Sebbene sia possibile raggruppare ed elaborare più estensioni, queste vengono installate in modo seriale. Al termine dell'installazione della prima estensione, viene tentata l'installazione dell'estensione successiva.

### <a name="deploy-the-log-analytics-vm-extension"></a>Distribuire l'estensione della macchina virtuale Log Analytics

Per distribuire facilmente l'agente di Log Analytics, viene fornito l'esempio seguente per installare l'agente in Windows o Linux.

#### <a name="template-file-for-linux"></a>File modello per Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>File modello per Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>File di parametri

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Salvare il modello e i file dei parametri su disco e modificare il file dei parametri con i valori appropriati per la distribuzione. È quindi possibile installare l'estensione in tutti i computer connessi in un gruppo di risorse con il comando seguente. Il comando usa il parametro *TemplateFile* per specificare il modello e il parametro *TemplateParameterFile* per specificare un file che contiene parametri e valori di parametri.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Distribuire l'estensione script personalizzato

Per usare l'estensione di script personalizzata, viene fornito l'esempio seguente per l'esecuzione in Windows e Linux. Se non si ha familiarità con l'estensione script personalizzata, vedere [estensione script personalizzato per Windows](../../virtual-machines/extensions/custom-script-windows.md) o [estensione script personalizzata per Linux](../../virtual-machines/extensions/custom-script-linux.md). Quando si usa questa estensione con macchine ibride, è necessario comprendere un paio di caratteristiche diverse:

* L'elenco dei sistemi operativi supportati con l'estensione di script personalizzati della macchina virtuale di Azure non è applicabile ai server abilitati per Azure Arc. L'elenco di sistemi operativi supportati per i server abilitati per Arc è disponibile [qui](agent-overview.md#supported-operating-systems).

* I dettagli di configurazione relativi ai set di scalabilità di macchine virtuali di Azure o alle VM classiche non sono applicabili

* Se i computer devono scaricare uno script esternamente e possono comunicare solo tramite un server proxy, è necessario [configurare l'agente del computer connesso](manage-agent.md#update-or-remove-proxy-settings) per impostare la variabile di ambiente del server proxy.

La configurazione dell'estensione script personalizzata specifica informazioni come il percorso dello script e il comando da eseguire. Questa configurazione è specificata in un modello di Azure Resource Manager, disponibile di seguito per computer ibridi sia Linux che Windows.

#### <a name="template-file-for-linux"></a>File modello per Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>File modello per Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Distribuire l'estensione DSC di PowerShell

Per usare l'estensione DSC di PowerShell, viene fornito l'esempio seguente per l'esecuzione in Windows e Linux. Se non si ha familiarità con l'estensione DSC di PowerShell, vedere [Panoramica del gestore dell'estensione DSC](../../virtual-machines/extensions/dsc-overview.md). Quando si usa questa estensione con macchine ibride, è necessario comprendere un paio di caratteristiche diverse:

* L'elenco dei sistemi operativi supportati con l'estensione DSC PowerShell per macchine virtuali di Azure non è applicabile ai server abilitati per Azure Arc. L'elenco di sistemi operativi supportati per i server abilitati per Arc è disponibile [qui](agent-overview.md#supported-operating-systems).

* Se i computer devono scaricare uno script esternamente e possono comunicare solo tramite un server proxy, è necessario [configurare l'agente del computer connesso](manage-agent.md#update-or-remove-proxy-settings) per impostare la variabile di ambiente del server proxy.

#### <a name="template-file-for-linux"></a>File modello per Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>File modello per Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Dependency Agent

Per usare l'estensione dell'agente di dipendenza di monitoraggio di Azure, viene fornito l'esempio seguente per l'esecuzione in Windows e Linux. Se non si ha familiarità con Dependency Agent, vedere [Panoramica degli agenti di monitoraggio di Azure](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>File modello per Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>File modello per Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure.

I passaggi seguenti per la risoluzione dei problemi sono validi per tutte le estensioni macchina virtuale.

1. Per controllare il log dell'agente guest, esaminare l'attività quando è stato eseguito il provisioning dell'estensione in `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` per Windows e per Linux in `/var/lib/GuestConfig/ext_mgr_logs` .

2. Per altri dettagli in per Windows, controllare i log di estensione per l'estensione specifica `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . L'output dell'estensione viene registrato in un file per ogni estensione installata in Linux `/var/log/GuestConfig/extension_logs` .

3. Verificare le sezioni relative alla risoluzione dei problemi della documentazione specifica per i codici di errore, i problemi noti e così via Per ulteriori informazioni sulla risoluzione dei problemi per ogni estensione, vedere la sezione **risoluzione dei problemi e supporto** nella panoramica per l'estensione. Include la descrizione dei codici di errore scritti nel log. Gli articoli di estensione sono collegati nella [tabella Extensions](#extensions) disponibile in precedenza in questo articolo.

4. Esaminare i log di sistema. Controllare la presenza di altre operazioni che potrebbero aver interferito con l'estensione, ad esempio un'installazione a esecuzione prolungata di un'altra applicazione che ha richiesto l'accesso di gestione pacchetti esclusiva.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

- Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).