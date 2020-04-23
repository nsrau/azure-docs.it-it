---
title: Richiedere il trasferimento sicuro per garantire connessioni sicure
titleSuffix: Azure Storage
description: Informazioni su come richiedere il trasferimento sicuro per le richieste in Archiviazione di Azure.Learn how to require secure transfer for requests to Azure Storage. Quando è necessario un trasferimento sicuro per un account di archiviazione, tutte le richieste provenienti da una connessione non sicura vengono rifiutate.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870523"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Richiedere il trasferimento sicuro per garantire connessioni sicure

È possibile configurare l'account di archiviazione per accettare richieste da connessioni protette solo impostando la proprietà **Trasferimento sicuro richiesto** per l'account di archiviazione. Quando è necessario un trasferimento sicuro, tutte le richieste provenienti da una connessione non sicura vengono rifiutate. Microsoft consiglia di richiedere sempre il trasferimento sicuro per tutti gli account di archiviazione.

Quando è necessario un trasferimento sicuro, è necessario effettuare una chiamata a un'operazione dell'API REST di Archiviazione di Azure tramite HTTPS. Qualsiasi richiesta effettuata tramite HTTP viene rifiutata.

La connessione a una condivisione file di Azure tramite SMB senza crittografia ha esito negativo quando è necessario il trasferimento sicuro per l'account di archiviazione. Esempi di connessioni non sicure includono quelli effettuati su SMB 2.1, SMB 3.0 senza crittografia o alcune versioni del client Linux SMB.

Per impostazione predefinita, la proprietà **Trasferimento sicuro richiesto** è abilitata quando si crea un account di archiviazione.

> [!NOTE]
> Poiché Archiviazione di Azure non supporta HTTPS per i nomi di dominio personalizzati, l'opzione non è applicabile quando si usa un nome di dominio personalizzato. Gli account di archiviazione di tipo classico non sono supportati.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Richiedere il trasferimento sicuro nel portale di AzureRequire secure transfer in the Azure portal

È possibile attivare la proprietà **Richiesto trasferimento sicuro** quando si crea un account di archiviazione nel portale di [Azure.](https://portal.azure.com) È possibile anche abilitarla per un account di archiviazione esistente.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Richiedere il trasferimento sicuro per un nuovo account di archiviazione

1. Aprire il riquadro **Crea account di archiviazione** nel portale di Azure.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

   ![Riquadro Crea account di archiviazione](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Richiedere il trasferimento sicuro per un account di archiviazione esistente

1. Selezionare un account di archiviazione esistente nel portale di Azure.
1. Selezionare **Configurazione** in **IMPOSTAZIONI** nel riquadro del menu dell'account di archiviazione.
1. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

   ![Riquadro del menu Account di archiviazione](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Richiedere il trasferimento sicuro dal codiceRequire secure transfer from code

Per richiedere il trasferimento sicuro a livello di codice, impostare la proprietà _supportsHttpsTrafficOnly_ nell'account di archiviazione. È possibile impostare questa proprietà usando l'API REST del provider di risorse di archiviazione, le librerie client o gli strumenti:You can set this property by using the Storage Resource Provider REST API, client libraries, or tools:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodoJS](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Richiedere il trasferimento sicuro con PowerShellRequire secure transfer with PowerShell

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

## <a name="require-secure-transfer-with-azure-cli"></a>Richiedere il trasferimento sicuro con l'interfaccia della riga di comando di AzureRequire secure transfer with Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Utilizzare il comando seguente per controllare l'impostazione:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Utilizzare il comando seguente per abilitare l'impostazione:

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

[Consigli sulla sicurezza per l'archiviazione BLOBSecurity recommendations for Blob storage](../blobs/security-recommendations.md)
