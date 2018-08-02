---
title: Crittografia dischi di Azure per Windows | Microsoft Docs
description: Consente la distribuzione di Crittografia dischi di Azure in una macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 11ec26729b2239279dddc8cd62f6b658a4f7ed20
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413791"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Crittografia dischi di Azure per Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Panoramica

Crittografia dischi di Azure sfrutta Bitlocker per fornire la crittografia completa del disco nelle macchine virtuali Azure con Windows.  Questa soluzione è integrata con Azure Key Vault per gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. 

## <a name="prerequisites"></a>Prerequisiti

Per un elenco completo dei prerequisiti, vedere [Prerequisiti di Crittografia dischi di Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Sistema operativo

Per un elenco delle versioni di Windows attuali, vedere [Prerequisiti di Crittografia dischi di Azure](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Connettività Internet

Crittografia dischi di Azure richiede una connessione Internet per accedere a Active Directory, Key Vault, Archiviazione e agli endpoint di gestione pacchetti.  Per altre informazioni sulle impostazioni di sicurezza di rete, vedere [Prerequisiti di Crittografia dischi di Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

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
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | stringa |
| type | AzureDiskEncryptionForWindows| stringa |
| typeHandlerVersion | 1.0, 2.2 (VMSS) | int |
| (facoltativo) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (facoltativo) AADClientSecret | password | stringa |
| (facoltativo) AADClientCertificate | thumbprint | stringa |
| EncryptionOperation | EnableEncryption | stringa | 
| KeyEncryptionAlgorithm | RSA-OAEP | stringa |
| KeyEncryptionKeyURL | URL | stringa |
| KeyVaultURL | URL | stringa |
| SequenceVersion | uniqueidentifier | stringa |
| VolumeType | Sistema operativo, dati, tutti | stringa |

## <a name="template-deployment"></a>Distribuzione del modello
Per un esempio di distribuzione del modello, vedere [ Creare una nuova macchina virtuale Windows crittografata dall'immagine della raccolta](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Le istruzioni sono disponibili nella versione più recente della [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Vedere la [guida alla risoluzione dei problemi di Crittografia dischi di Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).