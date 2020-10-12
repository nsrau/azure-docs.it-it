---
title: Crittografia dischi di Azure per Linux
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80066911"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Crittografia dischi di Azure per Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta il sottosistema di dm-crypt di Linux per fornire la crittografia completa del disco nelle [distribuzioni di Linux Azure selezionate](https://aka.ms/adelinux).  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi.

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere [crittografia dischi di Azure per macchine virtuali Linux](../linux/disk-encryption-overview.md), in particolare le sezioni seguenti:

- [Macchine virtuali e sistemi operativi supportati](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti aggiuntivi delle macchine virtuali](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisiti di rete](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisiti di archiviazione delle chiavi di crittografia](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schema dell'estensione

Esistono due versioni dello schema di estensione per crittografia dischi di Azure (ADE):
- v 1.1: uno schema consigliato più recente che non usa le proprietà di Azure Active Directory (AAD).
- v 0.1: schema meno recente che richiede proprietà di Azure Active Directory (AAD). 

Per selezionare uno schema di destinazione, `typeHandlerVersion` è necessario impostare la proprietà sulla stessa versione dello schema che si desidera utilizzare.

### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: nessun AAD (consigliato)

Lo schema v 1.1 è consigliato e non richiede proprietà di Azure Active Directory (AAD).

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


### <a name="schema-v01-with-aad"></a>Schema v 0.1: con AAD 

Lo schema 0,1 richiede `AADClientID` e `AADClientSecret` o `AADClientCertificate` .

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
| typeHandlerVersion | 1,1, 0,1 | int |
| (schema 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schema 0,1) AADClientSecret | password | string |
| (schema 0,1) AADClientCertificate | thumbprint | string |
| opzionale (schema 0,1) Passphrase | password | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dizionario JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (facoltativo-predefinito RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| opzionale KeyEncryptionKeyURL | url | string |
| opzionale KekVaultResourceId | url | string |
| opzionale SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operativo, dati, tutti | string |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione dei modelli basata sullo schema v 1.1, vedere il modello di avvio rapido di Azure [201-Encrypt-running-Linux-VM-without-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Per un esempio di distribuzione dei modelli basata sullo schema v 0.1, vedere il modello di avvio rapido di Azure [201-Encrypt-running-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Se in precedenza è stato usato il servizio Crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la VM.
> - Durante la crittografia dei volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. È consigliabile evitare accessi SSH mentre è in corso la crittografia per evitare che eventuali file aperti a cui è necessario accedere durante il processo di crittografia risultino bloccati. Per controllare lo stato di avanzamento, usare il cmdlet di PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) o il comando [VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) cli. Questo processo può richiedere alcune ore per un volume di sistema operativo da 30 GB, più un tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo per la crittografia del volume di dati è proporzionale alla dimensione e quantità dei volumi di dati a meno che non venga usata l'opzione "encrypt format all". 
> - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato. 

>[!NOTE]
> Inoltre, se il `VolumeType` parametro è impostato su All, i dischi dati verranno crittografati solo se montati correttamente.

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). 

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [domande frequenti sul supporto Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle estensioni della macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).
* Per altre informazioni su crittografia dischi di Azure per Linux, vedere [macchine virtuali Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
