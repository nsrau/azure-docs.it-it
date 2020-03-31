---
title: Azure Key Vault VM Extension for Windows
description: Distribuire un agente che esegue l'aggiornamento automatico dei segreti di un insieme di credenziali delle chiavi in macchine virtuali tramite un'estensione di macchina virtuale.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 22a4177d85cb9dbbaa9ed75e063306484c7b48a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298972"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Estensione di macchina virtuale Key Vault per Windows

L'estensione della macchina virtuale dell'insieme di credenziali delle chiavi fornisce l'aggiornamento automatico dei certificati archiviati in un insieme di credenziali delle chiavi di Azure.The Key Vault VM extension provides automatic refresh of certificates stored in an Azure key vault. In particolare, l'estensione monitora un elenco di certificati osservati archiviati in insiemi di credenziali delle chiavi e, quando rileva una modifica, recupera e installa i certificati corrispondenti. Questo documento descrive dettagliatamente le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione di macchina virtuale Key Vault per Windows. 

### <a name="operating-system"></a>Sistema operativo

L'estensione della macchina virtuale dell'insieme di credenziali delle chiavi supporta le versioni seguenti di Windows:The Key Vault VM extension supports below versions of Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Tipi di contenuto dei certificati supportati

- #12 PKCS

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema per l'estensione di macchina virtuale Key Vault. L'estensione non richiede impostazioni protette - tutte le sue impostazioni sono considerate informazioni pubbliche. L'estensione richiede un elenco di certificati monitorati, la frequenza di polling e l'archivio certificati di destinazione. In particolare:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Gli URL dei certificati osservati devono `https://myVaultName.vault.azure.net/secrets/myCertName`essere nel formato .
> 
> Questo perché `/secrets` il percorso restituisce il certificato completo, `/certificates` inclusa la chiave privata, mentre il percorso non lo fa. Ulteriori informazioni sui certificati sono disponibili qui: [Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)

### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Data |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal (rinnovo collegamento) | false | boolean |
| certificateStoreLocation  | LocalMachine | string |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | Matrice di stringhe


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono uno strumento ideale per distribuire una o più macchine virtuali per cui è necessario l'aggiornamento dei certificati successivamente alla distribuzione. L'estensione può essere distribuita in singole macchine virtuali o set di scalabilità di macchine virtuali. Lo schema e la configurazione sono comuni a entrambi i tipi di modello. 

La configurazione JSON per un'estensione della macchina virtuale deve essere `"resources": []` annidata all'interno del frammento di risorsa `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` macchina virtuale del modello, in particolare l'oggetto per il modello di macchina virtuale e nel caso di set di scalabilità della macchina virtuale nell'oggetto.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Distribuzione con Azure PowerShell

È possibile usare Azure PowerShell per distribuire l'estensione di macchina virtuale Key Vault in una macchina virtuale o un set di scalabilità di macchine virtuali esistente. 

* Per distribuire l'estensione in una macchina virtuale:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Per distribuire l'estensione in un set di scalabilità di macchine virtuali:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione della macchina dell'insieme di credenziali delle chiavi in una macchina virtuale o in un set di scalabilità di macchine virtuali esistente. 
 
* Per distribuire l'estensione in una macchina virtuale:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Per distribuire l'estensione in un set di scalabilità di macchine virtuali:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Tenere presenti le restrizioni e i requisiti seguenti:
- Restrizioni relative all'insieme di credenziali:
  - Deve essere già presente al momento della distribuzione 
  - I criteri di accesso dell'insieme di credenziali delle chiavi sono impostati per l'identità VM/VMSS tramite MSIKey Vault Access Policy is set for VM/VMSS Identity using MSI


## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una macchina virtuale specifica, eseguire il comando seguente tramite Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Supporto

Se è necessaria ulteriore assistenza in qualsiasi momento di questo articolo, è possibile contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
