---
title: Azure Key Vault estensione VM per Windows
description: Distribuire un agente che esegue l'aggiornamento automatico dei segreti di un insieme di credenziali delle chiavi in macchine virtuali tramite un'estensione di macchina virtuale.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 0418c11d84fb82ac4a4b181289f032407a29f92e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500666"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Estensione di macchina virtuale Key Vault per Windows

L'estensione macchina virtuale di Key Vault offre l'aggiornamento automatico dei certificati archiviati in un insieme di credenziali delle chiavi di Azure. In particolare, l'estensione monitora un elenco di certificati osservati archiviati in insiemi di credenziali delle chiavi e, quando rileva una modifica, recupera e installa i certificati corrispondenti. Questo documento descrive dettagliatamente le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione di macchina virtuale Key Vault per Windows. 

### <a name="operating-system"></a>Sistema operativo

L'estensione della macchina virtuale Key Vault supporta le versioni seguenti di Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

L'estensione della macchina virtuale Key Vault è supportata anche in una macchina virtuale locale personalizzata che viene caricata e convertita in un'immagine specializzata da usare in Azure con l'installazione di Windows Server 2019 core.

### <a name="supported-certificate-content-types"></a>Tipi di contenuto del certificato supportati

- PKCS #12
- PEM

## <a name="prerequisities"></a>Prerequisiti
  - Key Vault istanza con certificato. Vedere [creare un Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS deve avere l' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) assegnata
  - I criteri di accesso Key Vault devono essere impostati con i segreti `get` e l' `list` autorizzazione per l'identità gestita VM/vmss per recuperare la parte del certificato di un segreto. Vedere [come eseguire l'autenticazione a Key Vault](../../key-vault/general/authentication.md) e [assegnare un criterio di accesso key Vault](../../key-vault/general/assign-access-policy-cli.md).

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
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
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
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | Matrice di stringhe
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono uno strumento ideale per distribuire una o più macchine virtuali per cui è necessario l'aggiornamento dei certificati successivamente alla distribuzione. L'estensione può essere distribuita in singole macchine virtuali o in set di scalabilità di macchine virtuali. Lo schema e la configurazione sono comuni a entrambi i tipi di modello. 

La configurazione JSON per un'estensione macchina virtuale deve essere annidata all'interno del frammento delle risorse della macchina virtuale del modello, in particolare nell'oggetto `"resources": []` per il modello di macchina virtuale e nell'oggetto `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` nel caso di un set di scalabilità di macchine virtuali.

 > [!NOTE]
> Per l'autenticazione nell'insieme di credenziali delle chiavi, l'estensione della macchina virtuale richiede l'assegnazione dell'identità gestita dal sistema o dall'utente.  Vedere [come eseguire l'autenticazione a Key Vault e assegnare un criterio di accesso key Vault.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

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
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Distribuzione con Azure PowerShell
> [!WARNING]
> I client PowerShell spesso aggiungono `\` a `"` nel settings.jssu cui causerà akvvm_service avrà esito negativo con errore: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

È possibile usare Azure PowerShell per distribuire l'estensione di macchina virtuale Key Vault in una macchina virtuale o un set di scalabilità di macchine virtuali esistente. 

* Per distribuire l'estensione in una macchina virtuale:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
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
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
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
         az vm extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Per distribuire l'estensione in un set di scalabilità di macchine virtuali:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Tenere presenti le restrizioni e i requisiti seguenti:
- Restrizioni relative all'insieme di credenziali:
  - Deve essere già presente al momento della distribuzione 
  - I criteri di accesso Key Vault devono essere impostati per l'identità VM/VMSS usando un'identità gestita. Vedere [come eseguire l'autenticazione a Key Vault](../../key-vault/general/authentication.md) e [assegnare un criterio di accesso key Vault](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="frequently-asked-questions"></a>Domande frequenti

* È previsto un limite per il numero di observedCertificates che è possibile configurare?
  No, Key Vault estensione della macchina virtuale non ha limiti per il numero di observedCertificates.

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una macchina virtuale specifica, eseguire il comando seguente tramite Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Interfaccia della riga di comando di Azure**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Log e configurazione

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).