---
title: Richiedere il trasferimento sicuro in Archiviazione di Azure | Microsoft Docs
description: Informazioni sulla funzionalità "Trasferimento sicuro obbligatorio" per Archiviazione di Azure e su come abilitarla.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: ec5949438ef179ecef17c65e91138ed4dcde9906
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467085"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Richiedere il trasferimento sicuro in Archiviazione di Azure

L'opzione "Trasferimento sicuro obbligatorio" aumenta la sicurezza dell'account di archiviazione perché consente le richieste all'account solo tramite connessioni sicure. Ad esempio, quando si chiamano le API REST per accedere all'account di archiviazione, è necessario connettersi usando HTTPS. "Trasferimento sicuro obbligatorio" rifiuta le richieste che usano HTTP.

Quando si usa il servizio File di Azure, se è abilitata l'opzione "Trasferimento sicuro obbligatorio" le connessioni senza crittografia hanno esito negativo. Questo include scenari in cui si usano SMB 2.1, SMB 3.0 senza crittografia e alcune versioni del client SMB Linux. 

Per impostazione predefinita, l'opzione "Trasferimento sicuro obbligatorio" è disabilitata quando si crea un account di archiviazione con l'SDK ed è abilitata per impostazione predefinita quando si crea un account di archiviazione nel portale di Azure.

> [!NOTE]
> Poiché Archiviazione di Azure non supporta HTTPS per i nomi di dominio personalizzati, l'opzione non è applicabile quando si usa un nome di dominio personalizzato. Gli account di archiviazione di tipo classico non sono supportati.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Abilitare "Trasferimento sicuro obbligatorio" nel portale di Azure

È possibile abilitare l'impostazione "Trasferimento sicuro obbligatorio" quando si crea un account di archiviazione nel [portale di Azure](https://portal.azure.com). È possibile anche abilitarla per un account di archiviazione esistente.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Richiedere il trasferimento sicuro per un nuovo account di archiviazione

1. Aprire il riquadro **Crea account di archiviazione** nel portale di Azure.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

  ![Riquadro Crea account di archiviazione](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Richiedere il trasferimento sicuro per un account di archiviazione esistente

1. Selezionare un account di archiviazione esistente nel portale di Azure.
1. Selezionare **Configurazione** in **IMPOSTAZIONI** nel riquadro del menu dell'account di archiviazione.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

  ![Riquadro del menu Account di archiviazione](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Abilitare "Trasferimento sicuro obbligatorio" a livello di codice

Per richiedere il trasferimento sicuro a livello di codice, usare l'impostazione _supportsHttpsTrafficOnly_ nelle proprietà dell'account di archiviazione con l'API REST, gli strumenti o le raccolte:

* [API REST](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (versione: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) (versione: 0.7)
* [Interfaccia della riga di comando](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (versione: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (versione: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (versione: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (versione: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (versione: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Abilitare l'impostazione "Trasferimento sicuro obbligatorio" con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per questo esempio, è necessario il modulo Azure PowerShell Az 0.7 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

Eseguire `Connect-AzAccount` per creare una connessione con Azure.

 Per verificare l'impostazione, usare la riga di comando seguente:

```powershell
> Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Per abilitare l'impostazione, usare la riga di comando seguente:

```powershell
> Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Abilitare l'impostazione "Trasferimento sicuro obbligatorio" con l'interfaccia della riga di comando

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Per verificare l'impostazione, usare la riga di comando seguente:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Per abilitare l'impostazione, usare la riga di comando seguente:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Passaggi successivi
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](storage-security-guide.md).
