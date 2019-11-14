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
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073801"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Crittografia dischi di Azure per Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overview

Crittografia dischi di Azure sfrutta il sottosistema di dm-crypt di Linux per fornire la crittografia completa del disco nelle [distribuzioni di Linux Azure selezionate](https://aka.ms/adelinux).  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi.

## <a name="prerequisites"></a>prerequisiti

Per un elenco completo dei prerequisiti, vedere [crittografia dischi di Azure per macchine virtuali Linux](../linux/disk-encryption-overview.md), in particolare le sezioni seguenti:

- [Crittografia dischi di Azure per macchine virtuali Linux](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti aggiuntivi per le macchine virtuali](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisiti di rete](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Estensione schemi

Sono disponibili due schemi per crittografia dischi di Azure: v 1.1, uno schema più recente e consigliato che non usa le proprietà Azure Active Directory (AAD) e v 0.1, uno schema precedente che richiede proprietà di AAD. È necessario usare la versione dello schema corrispondente all'estensione in uso: Schema v 1.1 per l'estensione AzureDiskEncryptionForLinux versione 1,1, schema v 0.1 per la versione dell'estensione AzureDiskEncryptionForLinux 0,1.
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
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
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
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | stringa |
| type | AzureDiskEncryptionForLinux | stringa |
| typeHandlerVersion | 0,1, 1,1 | int |
| (schema 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schema 0.1) AADClientSecret | Password | stringa |
| (schema 0.1) AADClientCertificate | thumbprint | stringa |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dizionario JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | stringa | 
| KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | stringa |
| KeyEncryptionKeyURL | URL | stringa |
| opzionale KeyVaultURL | URL | stringa |
| Passphrase | Password | stringa | 
| SequenceVersion | uniqueidentifier | stringa |
| VolumeType | Sistema operativo, dati, tutti | stringa |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione del modello, vedere [Abilitare la crittografia in una macchina virtuale Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Le istruzioni sono disponibili nella versione più recente della [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle estensioni della macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).