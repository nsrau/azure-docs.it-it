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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80066874"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Crittografia dischi di Azure per Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta BitLocker per fornire la crittografia completa del disco nelle macchine virtuali Azure con Windows.  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. 

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere [crittografia dischi di Azure per macchine virtuali Windows](../windows/disk-encryption-overview.md), in particolare le sezioni seguenti:

- [Macchine virtuali e sistemi operativi supportati](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti di rete](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schema dell'estensione

Esistono due versioni dello schema di estensione per crittografia dischi di Azure (ADE):
- v 2.2: schema consigliato più recente che non usa le proprietà di Azure Active Directory (AAD).
- v 1.1: schema meno recente che richiede proprietà di Azure Active Directory (AAD). 

Per selezionare uno schema di destinazione, `typeHandlerVersion` è necessario impostare la proprietà sulla stessa versione dello schema che si desidera utilizzare.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: nessun AAD (consigliato)

Lo schema v 2.2 è consigliato per tutte le nuove macchine virtuali e non richiede proprietà Azure Active Directory.

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


### <a name="schema-v11-with-aad"></a>Schema v 1.1: con AAD 

Lo schema 1,1 richiede `aadClientID` e `aadClientSecret` `AADClientCertificate` non è consigliato per le nuove macchine virtuali.

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
| type | AzureDiskEncryption | string |
| typeHandlerVersion | 2,2, 1,1 | string |
| (schema 1,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schema 1,1) AADClientSecret | password | string |
| (schema 1,1) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (facoltativo-predefinito RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| opzionale KeyEncryptionKeyURL | url | string |
| opzionale KekVaultResourceId | url | string |
| opzionale SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operativo, dati, tutti | string |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione dei modelli basato sullo schema v 2.2, vedere il modello di avvio rapido di Azure [201-Encrypt-running-Windows-VM-without-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Per un esempio di distribuzione dei modelli basata sullo schema v 1.1, vedere il modello di avvio rapido di Azure [201-Encrypt-running-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Inoltre, se il `VolumeType` parametro è impostato su All, i dischi dati verranno crittografati solo se sono formattati correttamente. 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). 

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [domande frequenti sul supporto Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).
* Per altre informazioni su crittografia dischi di Azure per Windows, vedere [macchine virtuali Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
