---
title: Crittografia dischi di Azure per Linux | Microsoft Docs
description: Consente la distribuzione di Crittografia dischi di Azure per Linux in una macchina virtuale usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: e7926fa488ad38784a3db96035055a80f841aed8
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449018"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Crittografia dischi di Azure per Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta il sottosistema di dm-crypt di Linux per fornire la crittografia completa del disco nelle [distribuzioni di Linux Azure selezionate](https://aka.ms/adelinux).  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi.

## <a name="prerequisites"></a>prerequisiti

Per un elenco completo dei prerequisiti, vedere [Prerequisiti di Crittografia dischi di Azure](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Sistema operativo

Crittografia dischi di Azure è attualmente supportata nelle distribuzioni e versioni selezionate.  Vedere le [domande frequenti su Crittografia dischi di Azure](../../security/azure-security-disk-encryption-faq.md#what-linux-distributions-does-azure-disk-encryption-support) per un elenco delle distribuzioni di Linux supportate.

### <a name="internet-connectivity"></a>Connettività Internet

Crittografia dischi di Azure per Linux richiede una connessione Internet per accedere a Active Directory, Key Vault, Archiviazione e agli endpoint di gestione pacchetti.  Per altre informazioni, vedere [Prerequisiti Crittografia di Crittografia dischi di Azure](
../../security/azure-security-disk-encryption.md#prerequisites).

## <a name="extension-schema"></a>Schema dell'estensione

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

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | stringa |
| type | AzureDiskEncryptionForLinux | stringa |
| typeHandlerVersion | 0.1, 1.1 (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | password | stringa |
| AADClientCertificate | thumbprint | stringa |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dizionario JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | stringa | 
| KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | stringa |
| KeyEncryptionKeyURL | URL | stringa |
| KeyVaultURL | URL | stringa |
| Passphrase | password | stringa | 
| SequenceVersion | uniqueidentifier | stringa |
| VolumeType | Sistema operativo, dati, tutti | stringa |

## <a name="template-deployment"></a>Distribuzione del modello

Per un esempio di distribuzione del modello, vedere [Abilitare la crittografia in una macchina virtuale Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Le istruzioni sono disponibili nella versione più recente della [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Per la risoluzione di problemi, consultare la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle estensioni della macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).