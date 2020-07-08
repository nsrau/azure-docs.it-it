---
title: Aggiornare la password dell'account di archiviazione di Active Directory Domain Services
description: Informazioni su come aggiornare la password dell'account di Active Directory Domain Services che rappresenta l'account di archiviazione. In questo modo si impedisce la pulizia dell'account di archiviazione alla scadenza della password, evitando errori di autenticazione.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510255"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Aggiornare la password dell'identità dell'account di archiviazione in servizi di dominio Active Directory

Se è stata registrata l'identità o l'account Active Directory Domain Services (AD DS) che rappresenta l'account di archiviazione in un'unità organizzativa o in un dominio che impone l'ora di scadenza della password, è necessario modificare la password prima della validità massima della password. L'organizzazione può eseguire script di pulizia automatici che eliminano gli account dopo la scadenza della password. Per questo motivo, se non si modifica la password prima della scadenza, è possibile che l'account venga eliminato. in questo modo si perderà l'accesso alle condivisioni file di Azure.

Per attivare la rotazione della password, è possibile eseguire il `Update-AzStorageAccountADObjectPassword` comando dal [modulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Questo comando deve essere eseguito in un ambiente Active Directory Domain Services locale usando un utente ibrido con autorizzazione Owner per l'account di archiviazione e le autorizzazioni di Active Directory Domain Services per modificare la password dell'identità che rappresenta l'account di archiviazione. Il comando esegue azioni simili alla rotazione della chiave dell'account di archiviazione. In particolare, ottiene la seconda chiave Kerberos dell'account di archiviazione e la usa per aggiornare la password dell'account registrato in servizi di dominio Active Directory. Quindi, rigenera la chiave Kerberos di destinazione dell'account di archiviazione e aggiorna la password dell'account registrato in servizi di dominio Active Directory. È necessario eseguire questo comando in un ambiente locale di Active Directory Domain Services.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
