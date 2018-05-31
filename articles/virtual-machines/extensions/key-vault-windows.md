---
title: Estensione di macchina virtuale Key Vault per Windows | Microsoft Docs
description: Distribuire un agente che esegue l'aggiornamento automatico dei segreti di un insieme di credenziali delle chiavi in macchine virtuali tramite un'estensione di macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944753"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Estensione di macchina virtuale Key Vault per Windows

## <a name="overview"></a>Panoramica

L'estensione di macchina virtuale Key Vault offre l'aggiornamento automatico dei segreti archiviati in un insieme di credenziali delle chiavi di Azure. In particolare, l'estensione monitora un elenco di certificati osservati archiviati in insiemi di credenziali delle chiavi e, quando rileva una modifica, recupera e installa i certificati corrispondenti. L'estensione di macchina virtuale Key Vault viene pubblicata e supportata da Microsoft, attualmente su macchine virtuali Windows. Questo documento descrive dettagliatamente le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione di macchina virtuale Key Vault per Windows. 

## <a name="prerequisites"></a>prerequisiti

L'estensione di macchina virtuale Key Vault dipende dall'estensione di macchina virtuale Identità del servizio gestita per poter autenticare se stessa nel servizio Key Vault. Per altre informazioni, fare riferimento alla documentazione dell'estensione di macchina virtuale Identità del servizio gestita.

### <a name="operating-system"></a>Sistema operativo

Attualmente l'estensione di macchina virtuale Key Vault supporta tutte le versioni di Windows.

| Distribuzione | Version |
|---|---|
| Windows | Core |

### <a name="internet-connectivity"></a>Connettività Internet

Per l'estensione di macchina virtuale Key Vault per Windows è necessario che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema per l'estensione di macchina virtuale Key Vault. L'estensione non richiede impostazioni protette e tutte le impostazioni sono considerate informazioni che non hanno impatto sulla sicurezza. L'estensione richiede un elenco di segreti monitorati, la frequenza di polling e l'archivio dei certificati di destinazione. In particolare:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | date |
| publisher | Microsoft.Azure.KeyVault.Edp | stringa |
| type | KeyVaultForWindows | stringa |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | stringa |
| certificateStoreLocation  | LOCAL_MACHINE | stringa |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | Matrice di stringhe


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono uno strumento ideale per distribuire una o più macchine virtuali per cui è necessario l'aggiornamento dei certificati successivamente alla distribuzione. L'estensione può essere distribuita in singole macchine virtuali o in set di scalabilità di macchine virtuali. Lo schema e la configurazione sono comuni a entrambi i tipi di modello. 

La configurazione JSON per un'estensione di macchina virtuale deve essere annidata all'interno del frammento della risorsa della macchina virtuale del modello, in particolare nell'oggetto `"resources": []` della macchina virtuale.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
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
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Per distribuire l'estensione in un set di scalabilità di macchine virtuali, ad esempio nell'ambito di un cluster di Service Fabric:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Tenere presenti le restrizioni e i requisiti seguenti:
- La distribuzione deve essere eseguita nell'area Stati Uniti centro-meridionali
- Restrizioni relative all'insieme di credenziali:
    - Deve essere già presente al momento della distribuzione 
    - Deve trovarsi nella stessa area e nello stesso gruppo di risorse della distribuzione
    - Deve essere abilitato per la distribuzione e per la distribuzione di modelli

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Alcuni esempi per la distribuzione dell'estensione di macchina virtuale Key Vault nell'interfaccia della riga di comando di Azure saranno disponibili a breve. 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una macchina virtuale specifica, eseguire il comando seguente tramite Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
