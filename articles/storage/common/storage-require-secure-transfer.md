---
title: Richiedi trasferimento sicuro per garantire connessioni sicure
titleSuffix: Azure Storage
description: Informazioni su come richiedere il trasferimento sicuro per le richieste ad archiviazione di Azure. Quando è necessario un trasferimento sicuro per un account di archiviazione, le richieste provenienti da una connessione non protetta vengono rifiutate.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 668b8d714c4d6c146d9b279d9e6a49599c04775c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423508"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Richiedi trasferimento sicuro per garantire connessioni sicure

È possibile configurare l'account di archiviazione in modo da accettare le richieste dalle connessioni protette solo impostando la proprietà **trasferimento sicuro obbligatorio** per l'account di archiviazione. Quando è necessario il trasferimento sicuro, le richieste provenienti da una connessione non protetta vengono rifiutate. Microsoft consiglia di richiedere sempre il trasferimento sicuro per tutti gli account di archiviazione.

Quando è necessario il trasferimento sicuro, è necessario effettuare una chiamata a un'operazione dell'API REST di archiviazione di Azure tramite HTTPS. Qualsiasi richiesta effettuata su HTTP viene rifiutata.

La connessione a una condivisione file di Azure tramite SMB senza crittografia ha esito negativo quando è necessario un trasferimento sicuro per l'account di archiviazione. Esempi di connessioni non sicure includono quelle effettuate su SMB 2,1, SMB 3,0 senza crittografia o alcune versioni del client SMB Linux.

Per impostazione predefinita, la proprietà **trasferimento sicuro obbligatorio** è abilitata quando si crea un account di archiviazione.

> [!NOTE]
> Poiché Archiviazione di Azure non supporta HTTPS per i nomi di dominio personalizzati, l'opzione non è applicabile quando si usa un nome di dominio personalizzato. Gli account di archiviazione di tipo classico non sono supportati.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Richiedi il trasferimento sicuro nella portale di Azure

È possibile attivare la proprietà **trasferimento sicuro obbligatorio** quando si crea un account di archiviazione nell' [portale di Azure](https://portal.azure.com). È possibile anche abilitarla per un account di archiviazione esistente.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Richiedere il trasferimento sicuro per un nuovo account di archiviazione

1. Aprire il riquadro **Crea account di archiviazione** nel portale di Azure.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

   ![Riquadro Crea account di archiviazione](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Richiedere il trasferimento sicuro per un account di archiviazione esistente

1. Selezionare un account di archiviazione esistente nel portale di Azure.
1. Selezionare **Configurazione** in **IMPOSTAZIONI** nel riquadro del menu dell'account di archiviazione.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

   ![Riquadro del menu Account di archiviazione](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Richiedi trasferimento sicuro dal codice

Per richiedere il trasferimento sicuro a livello di codice, impostare la proprietà _enableHttpsTrafficOnly_ su _true_ nell'account di archiviazione. È possibile impostare questa proprietà tramite l'API REST del provider di risorse di archiviazione, le librerie client o gli strumenti:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Richiedi il trasferimento sicuro con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per questo esempio, è necessario il modulo Azure PowerShell Az 0.7 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

Eseguire `Connect-AzAccount` per creare una connessione con Azure.

 Per verificare l'impostazione, usare la riga di comando seguente:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Per abilitare l'impostazione, usare la riga di comando seguente:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Richiedi trasferimento sicuro con l'interfaccia della riga di comando di Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Usare il comando seguente per controllare l'impostazione:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Usare il comando seguente per abilitare l'impostazione:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Passaggi successivi

[Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
