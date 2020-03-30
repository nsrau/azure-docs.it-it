---
title: Azure Disk Encryption for Linux
description: Consente la distribuzione di Crittografia dischi di Azure per Linux in una macchina virtuale usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066911"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Crittografia dischi di Azure per Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta il sottosistema di dm-crypt di Linux per fornire la crittografia completa del disco nelle [distribuzioni di Linux Azure selezionate](https://aka.ms/adelinux).  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi.

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere Crittografia disco di [Azure per macchine virtuali Linux,](../linux/disk-encryption-overview.md)in particolare nelle sezioni seguenti:

- [Macchine virtuali e sistemi operativi supportati](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti aggiuntivi per le macchine virtuali](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisiti di rete](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schema dell'estensione

Esistono due versioni dello schema di estensione per Azure Disk Encryption (ADE):
- v1.1 - Uno schema consigliato più recente che non usa le proprietà di Azure Active Directory (AAD).
- v0.1 - Uno schema precedente che richiede proprietà di Azure Active Directory (AAD). 

Per selezionare uno `typeHandlerVersion` schema di destinazione, la proprietà deve essere impostata uguale alla versione dello schema che si desidera utilizzare.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Nessun AAD (consigliato)

Lo schema v1.1 è consigliato e non richiede proprietà di Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v0.1: con AAD 

Lo schema 0.1 richiede `AADClientID` e o `AADClientSecret` o `AADClientCertificate`.

Utilizzo di `AADClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1, 0.1 | INT |
| (0,1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1 schema) AADClientSecret | password | string |
| (0,1 schema) AADClientCertificate | thumbprint | string |
| (opzionale) (0,1 schema) Passphrase | password | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dizionario JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opzionale - predefinito RSA-OAEP ) KeyEncryptionAlgorithm (Algoritme di Crittografia chiave) | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| KeyVaultURL | url | string |
| IdVaultKeyVaultResourceId | url | string |
| (opzionale) KeyEncryptionKeyURL | url | string |
| (opzionale) IdKekVaultResource | url | string |
| (opzionale) Versione Di sequenza | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operativo, dati, tutti | string |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione di modelli basata sullo schema v1.1, vedere Azure Quickstart Template [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Per un esempio di distribuzione di modelli basata sullo schema v0.1, vedere Azure Quickstart Template [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Se in precedenza è stato usato Crittografia disco di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale.
> - Quando si crittografano i volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. Si consiglia vivamente di evitare gli accessi SSH mentre la crittografia è in corso per evitare problemi che bloccano i file aperti che dovranno essere accessibili durante il processo di crittografia. Per controllare lo stato di avanzamento, utilizzare il cmdlet [PowerShell Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) o il comando dell'interfaccia della riga di comando di [crittografia della macchina virtuale.](/cli/azure/vm/encryption#az-vm-encryption-show) Questo processo può richiedere alcune ore per un volume di sistema operativo da 30 GB, più un tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo per la crittografia del volume di dati è proporzionale alla dimensione e quantità dei volumi di dati a meno che non venga usata l'opzione "encrypt format all". 
> - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato. 

>[!NOTE]
> Inoltre, `VolumeType` se il parametro è impostato su Tutti, i dischi dati verranno crittografati solo se sono montati correttamente.

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Supporto

Se è necessaria ulteriore assistenza in qualsiasi momento di questo articolo, è possibile contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni supporto. Per informazioni sull'uso del supporto di Azure, leggere le [domande frequenti sul supporto di Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle estensioni della macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).
* Per altre informazioni su Crittografia disco di Azure per Linux, vedere [Macchine virtuali Linux.For](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)more information about Azure Disk Encryption for Linux, see Linux virtual machines .
