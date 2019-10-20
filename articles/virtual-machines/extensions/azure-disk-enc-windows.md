---
title: Crittografia dischi di Azure per Windows | Microsoft Docs
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
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 00891122015bb3e6adb500b6f6c30fa031161b92
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598007"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Crittografia dischi di Azure per Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta BitLocker per fornire la crittografia completa del disco nelle macchine virtuali Azure con Windows.  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. 

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere [crittografia dischi di Azure per macchine virtuali Linux](../linux/disk-encryption-overview.md), in particolare le sezioni seguenti:

- [Crittografia dischi di Azure per macchine virtuali Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti di rete](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Estensione schemi

Sono disponibili due schemi per crittografia dischi di Azure: v 1.1, uno schema più recente e consigliato che non usa le proprietà Azure Active Directory (AAD) e v 0.1, uno schema precedente che richiede proprietà di AAD. È necessario usare la versione dello schema corrispondente all'estensione in uso: Schema v 1.1 per l'estensione AzureDiskEncryption versione 1,1, schema v 0.1 per la versione dell'estensione AzureDiskEncryption 0,1.

### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: nessun AAD (consigliato)

Lo schema v 1.1 è consigliato e non richiede proprietà Azure Active Directory.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v 0.1: con AAD 

Lo schema 0,1 richiede `aadClientID` e `aadClientSecret` o `AADClientCertificate`.

Utilizzo di `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Utilizzo di `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Valori delle proprietà

| name | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (schema 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (schema 0,1) AADClientSecret | password | string |
| (schema 0,1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dizionario JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| opzionale Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Sistema operativo, dati, tutti | string |

## <a name="template-deployment"></a>Distribuzione del modello
Per un esempio di distribuzione del modello, vedere [ Creare una nuova macchina virtuale Windows crittografata dall'immagine della raccolta](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Le istruzioni sono disponibili nella versione più recente della [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Vedere la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).
