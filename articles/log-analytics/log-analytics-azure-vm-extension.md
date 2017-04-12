---
title: Connettere macchine virtuali di Azure a Log Analytics | Microsoft Docs
description: "Nelle macchine virtuali Windows e Linux in esecuzione in Azure, per la raccolta di log e metriche è consigliabile installare l&quot;estensione macchina virtuale di Azure di Log Analytics. Per installare l&quot;estensione macchina virtuale di Log Analytics nelle VM di Azure è possibile usare il portale di Azure o PowerShell."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: richrund
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 87e888bf3d7355b36c42e8787abe9bf1cb191fcd
ms.lasthandoff: 04/03/2017


---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Connettere macchine virtuali di Azure a Log Analytics con un agente Log Analytics

Nei computer Windows e Linux, è consigliabile raccogliere log e metriche installando l'agente di Log Analytics.

Il modo più semplice per installare l'agente di Log Analytics nelle VM di Azure è tramite l'estensione macchina virtuale di Log Analytics.  L'uso dell'estensione macchina virtuale consente di semplificare il processo di installazione e di configurare automaticamente l'agente per l'invio di dati all'area di lavoro di Log Analytics specificata. L'agente viene anche aggiornato automaticamente in modo da garantire la presenza delle funzionalità e delle correzioni più recenti.

Per le macchine virtuali Windows si abilita l'estensione macchina virtuale *Microsoft Monitoring Agent*.
Per le macchine virtuali Linux si abilita l'estensione macchina virtuale *dell'agente OMS per Linux*.

Altre informazioni sulle [estensioni delle macchine virtuali](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e sull'[agente Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Quando si usa la raccolta basata su agenti per i dati di log, è necessario configurare le [origini dati in Log Analytics](log-analytics-data-sources.md) per specificare i log e le metriche da raccogliere.

> [!IMPORTANT]
> Se si configura Log Analytics per indicizzare i dati di log usando [Diagnostica di Azure](log-analytics-azure-storage.md) e si configura l'agente per raccogliere gli stessi log, i log verranno raccolti due volte e verranno addebitati costi per entrambe le origini dati. Se è installato l'agente, è consigliabile raccogliere i dati di log usando solo l'agente e non configurare Log Analytics per raccogliere i dati di log da Diagnostica di Azure.
>
>

È possibile abilitare l'estensione macchina virtuale di Log Analytics in tre semplici modi:

* Usando il portale di Azure
* Usando Azure PowerShell
* Usando un modello di Azure Resource Manager.

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Abilitare l'estensione macchina virtuale nel portale di Azure
È possibile installare l'agente per Log Analytics e connettere la macchina virtuale di Azure in cui viene eseguito usando il [portale di Azure](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Per installare l'agente di Log Analytics e connettere la macchina virtuale a un'area di lavoro di Log Analytics
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Selezionare **Esplora** sul lato sinistro del portale e quindi individuare e selezionare **Log Analytics (OMS)**.
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare quella da usare con la VM di Azure.  
   ![Aree di lavoro di OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. In **Log analytics management** (Gestione di Log Analytics) selezionare **Macchine virtuali**.  
   ![Macchine virtuali](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. Nell'elenco di **Macchine virtuali** selezionare la macchina virtuale in cui si vuole installare l'agente. Lo **stato della connessione OMS** per la VM sarà **Non connesso**.  
   ![VM non connessa](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. Nei dettagli relativi alla macchina virtuale selezionare **Connetti**. L'agente viene automaticamente installato e configurato per l'area di lavoro di Log Analytics. Il processo richiede alcuni minuti, durante i quali lo stato della connessione OMS visualizzato è *Connessione*.  
   ![Connessione della VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. Al termine dell'installazione e della connessione dell'agente, lo stato della **connessione OMS** verrà aggiornato in **Questa area di lavoro**.  
   ![Connessione effettuata](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>Abilitare l'estensione macchina virtuale con PowerShell
Quando si configura la macchina virtuale con PowerShell, è necessario specificare il **workspaceId**e la **workspaceKey**. Si noti che i nomi di proprietà nella configurazione json fanno distinzione **tra maiuscole e minuscole**.

L'ID e la chiave sono riportati nella pagina **Impostazioni** del portale di OMS o sono reperibili usando PowerShell come illustrato nell'esempio precedente.

![ID area di lavoro e chiave primaria](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

I comandi per le macchine virtuali classiche di Azure e per le macchine virtuali di Resource Manager sono diversi. Di seguito sono riportati esempi sia per le macchine virtuali classiche che per quelle di Resource Manager.

Per le macchine virtuali classiche usare l'esempio di PowerShell seguente:

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Per le macchine virtuali di Resource Manager usare l'esempio di PowerShell seguente:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```

## <a name="deploy-the-vm-extension-using-a-template"></a>Distribuire l'estensione macchina virtuale usando un modello
Azure Resource Manager consente di creare un modello semplice (in formato JSON) che definisce la distribuzione e la configurazione dell'applicazione. Questo modello è noto come modello di Resource Manager e permette di definire la distribuzione in modo dichiarativo. Usando un modello, è possibile distribuire ripetutamente l'applicazione in tutto il ciclo di vita dell'app avendo la certezza che le risorse verranno distribuite in uno stato coerente.

Includendo l'agente di Log Analytics nel modello di Resource Manager è possibile accertarsi che ogni macchina virtuale sia preconfigurata per la segnalazione di dati all'area di lavoro di Log Analytics.

Per altre informazioni sui modelli di Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Di seguito è riportato un modello di Resource Manager di esempio usato per distribuire una macchina virtuale che esegue Windows e in cui è installata l'estensione Microsoft Monitoring Agent. Si tratta di un modello tipico di macchina virtuale con le aggiunte seguenti:

* Parametri workspaceId e workspaceName
* Sezione di estensione di risorsa Microsoft.EnterpriseCloud.Monitoring
* Output per la ricerca di workspaceId e workspaceSharedKey

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

È possibile distribuire un modello con il comando di PowerShell seguente:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Risoluzione dei problemi relativi all'estensione della VM di Log Analytics
In genere si riceverà un messaggio quando si verifica un malfunzionamento dal portale Azure o da Azure PowerShell.

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Individuare la VM e aprire i relativi dettagli.
3. Fare clic su **Estensioni** per verificare se l'estensione OMS è abilitata o disabilitata.

   ![Visualizzazione dell'estensione della VM](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. Fare clic sull'estensione *MicrosoftMonitoringAgent*(Windows) o *OmsAgentForLinux* (Linux) e visualizzare i dettagli. 

   ![Dettagli dell'estensione VM](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Risoluzione dei problemi delle macchine virtuali Windows
Se l'estensione dell'agente di macchine virtuali *Microsoft Monitoring Agent* non viene installata o non segnala dati, è possibile seguire queste procedure per risolvere il problema.

1. Controllare che l'agente di macchine virtuali di Azure sia installato e funzioni correttamente seguendo la procedura descritta nell'articolo [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * È anche possibile esaminare il file di log dell'agente di macchine virtuali `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se il log non esiste, l'agente di macchine virtuali non è installato
     * [Installare l'agente di macchine virtuali di Azure in VM classiche](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. Verificare che l'attività dell'heartbeat dell'estensione Microsoft Monitoring Agent sia in esecuzione seguendo questa procedura:
   * Accedere alla macchina virtuale
   * Aprire Utilità di pianificazione e trovare l'attività `update_azureoperationalinsight_agent_heartbeat`
   * Verificare che l'attività sia abilitata e venga eseguita ogni minuto
   * Controllare il file di log dell'heartbeat in `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Esaminare i file di log dell'estensione macchina virtuale Microsoft Monitoring Agent in `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Verificare che la macchina virtuale possa eseguire script di PowerShell
5. Verificare che le autorizzazioni per C:\Windows\temp non siano state modificate
6. Visualizzare lo stato di Microsoft Monitoring Agent digitando quanto riportato di seguito in una finestra di PowerShell con privilegi elevati nella macchina virtuale: `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Esaminare i file di log dell'installazione di Microsoft Monitoring Agent in `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi delle estensioni Windows](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="troubleshooting-linux-virtual-machines"></a>Risoluzione dei problemi delle macchine virtuali Linux
Se l'estensione dell'agente di macchine virtuali per l'*agente OMS per Linux* non viene installata o non segnala dati, è possibile seguire queste procedure per risolvere il problema.

1. Se lo stato dell'estensione è *Sconosciuto*, controllare che l'agente di macchine virtuali di Azure sia installato e funzioni correttamente esaminando il relativo file di log `/var/log/waagent.log`
   * Se il log non esiste, l'agente di macchine virtuali non è installato
   * [Installare l'agente di macchine virtuali di Azure nelle VM Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. Per altri stati non integri, esaminare i file di log dell'estensione macchina virtuale dell'agente OMS per Linux in `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se lo stato dell'estensione è integro ma i dati non vengono caricati, esaminare i file di log dell'agente OMS per Linux in `/var/opt/microsoft/omsagent/log/omsagent.log`

Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi delle estensioni Linux](../virtual-machines/linux/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
* Configurare [origini dati in Log Analytics](log-analytics-data-sources.md) per specificare i log e le metriche da raccogliere.
* Per raccogliere dati dalle macchine virtuali, [aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
* [Raccogliere dati con Diagnostica di Azure](log-analytics-azure-storage.md) per altre risorse in esecuzione in Azure.

Per i computer non inclusi in Azure è possibile installare l'agente di Log Analytics usando i metodi descritti negli articoli seguenti:

* [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md)
* [Connettere computer Linux a Log Analytics](log-analytics-linux-agents.md)

