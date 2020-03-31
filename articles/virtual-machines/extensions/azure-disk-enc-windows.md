---
title: Crittografia dischi di Azure per Windows
description: Consente la distribuzione di Crittografia dischi di Azure in una macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066874"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Crittografia dischi di Azure per Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta BitLocker per fornire la crittografia completa del disco nelle macchine virtuali Azure con Windows.  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. 

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere Crittografia disco di [Azure per macchine virtuali Windows,](../windows/disk-encryption-overview.md)in particolare nelle sezioni seguenti:

- [Macchine virtuali e sistemi operativi supportati](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti di rete](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schema dell'estensione

Esistono due versioni dello schema di estensione per Azure Disk Encryption (ADE):
- v2.2 - Uno schema consigliato più recente che non usa le proprietà di Azure Active Directory (AAD).
- v1.1 - Uno schema precedente che richiede proprietà di Azure Active Directory (AAD). 

Per selezionare uno `typeHandlerVersion` schema di destinazione, la proprietà deve essere impostata uguale alla versione dello schema che si desidera utilizzare.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Nessun AAD (consigliato)

Lo schema v2.2 è consigliato per tutte le nuove macchine virtuali e non richiede proprietà di Azure Active Directory.The v2.2 schema is recommended for all new VMs and does not require Azure Active Directory properties.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.1: con AAD 

Lo schema 1.1 richiede `aadClientID` e o `aadClientSecret` o `AADClientCertificate` non è consigliato per le nuove macchine virtuali.

Utilizzo di `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Utilizzo di `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Data |
| publisher | Microsoft.Azure.Security | string |
| type | Crittografia di AzureDiskAzureDiskEncryption | string |
| typeHandlerVersion | 2.2, 1.1 | string |
| (1.1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 schema) AADClientSecret | password | string |
| (1.1 schema) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opzionale - predefinito RSA-OAEP ) KeyEncryptionAlgorithm (Algoritme di Crittografia chiave) | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| KeyVaultURL | url | string |
| IdVaultKeyVaultResourceId | url | string |
| (opzionale) KeyEncryptionKeyURL | url | string |
| (opzionale) IdKekVaultResource | url | string |
| (opzionale) Versione Di sequenza | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operativo, dati, tutti | string |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione di modelli basata sullo schema v2.2, vedere Modello di Guida rapida di Azure [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Per un esempio di distribuzione di modelli basata sullo schema v1.1, vedere Modello di Guida rapida di Azure [201-crittografia-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Inoltre, `VolumeType` se il parametro è impostato su Tutti, i dischi dati verranno crittografati solo se sono formattati correttamente. 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Supporto

Se è necessaria ulteriore assistenza in qualsiasi momento di questo articolo, è possibile contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni supporto. Per informazioni sull'uso del supporto di Azure, leggere le [domande frequenti sul supporto di Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).
* Per altre informazioni su Crittografia disco di Azure per Windows, vedere [Macchine virtuali Windows.For](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)more information about Azure Disk Encryption for Windows, see Windows virtual machines .
