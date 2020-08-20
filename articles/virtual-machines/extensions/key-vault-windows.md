---
title: Azure Key Vault estensione VM per Windows
description: Distribuire un agente che esegue l'aggiornamento automatico dei segreti di un insieme di credenziali delle chiavi in macchine virtuali tramite un'estensione di macchina virtuale.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: c41207bedbea96c76f0a9a6ffd45ea6efa09138b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641773"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Estensione di macchina virtuale Key Vault per Windows

L'estensione macchina virtuale di Key Vault offre l'aggiornamento automatico dei certificati archiviati in un insieme di credenziali delle chiavi di Azure. In particolare, l'estensione monitora un elenco di certificati osservati archiviati in insiemi di credenziali delle chiavi e, quando rileva una modifica, recupera e installa i certificati corrispondenti. Questo documento descrive dettagliatamente le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione di macchina virtuale Key Vault per Windows. 

### <a name="operating-system"></a>Sistema operativo

L'estensione della macchina virtuale Key Vault supporta le versioni seguenti di Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Tipi di contenuto del certificato supportati

- PKCS #12
- PEM

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema per l'estensione di macchina virtuale Key Vault. L'estensione non richiede impostazioni protette. tutte le impostazioni sono considerate informazioni pubbliche. L'estensione richiede un elenco dei certificati monitorati, la frequenza di polling e l'archivio certificati di destinazione. In particolare:  

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
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Gli URL dei certificati osservati devono essere nel formato `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Ciò è dovuto al fatto che il percorso `/secrets` restituisce il certificato completo, inclusa la chiave privata, mentre il percorso `/certificates` non lo restituisce. Altre informazioni sui certificati sono disponibili qui: [Certificati Key Vault](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> La proprietà' authenticationSettings ' è **obbligatoria** solo per le macchine virtuali con **identità assegnate dall'utente**.
> Specifica l'identità da usare per l'autenticazione Key Vault.


### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Data |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine o CurrentUser (maiuscole/minuscole) | string |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | Matrice di stringhe
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono uno strumento ideale per distribuire una o più macchine virtuali per cui è necessario l'aggiornamento dei certificati successivamente alla distribuzione. L'estensione può essere distribuita in singole macchine virtuali o in set di scalabilità di macchine virtuali. Lo schema e la configurazione sono comuni a entrambi i tipi di modello. 

La configurazione JSON per un'estensione macchina virtuale deve essere annidata all'interno del frammento delle risorse della macchina virtuale del modello, in particolare nell'oggetto `"resources": []` per il modello di macchina virtuale e nell'oggetto `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` nel caso di un set di scalabilità di macchine virtuali.

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

Per distribuire l'estensione macchina virtuale di Key Vault in una macchina virtuale o un set di scalabilità di macchine virtuali esistente è possibile usare l'interfaccia della riga di comando di Azure. 
 
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
  - Il criterio di accesso Key Vault è impostato per l'identità VM/VMSS con MSI


## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

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

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
