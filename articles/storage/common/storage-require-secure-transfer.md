---
title: Richiedere il trasferimento sicuro in Archiviazione di Azure | Microsoft Docs
description: "Informazioni sulla funzionalità \"Trasferimento sicuro obbligatorio\" per Archiviazione di Azure e su come abilitarla."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 5ec50ca23d9f7c92365492dfab42dc14a38699e2
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---
# <a name="require-secure-transfer-in-azure-storage"></a>Richiedere il trasferimento sicuro in Archiviazione di Azure

L'opzione "Trasferimento sicuro obbligatorio" aumenta la sicurezza dell'account di archiviazione perché consente le richieste all'account solo tramite connessioni sicure. Ad esempio, quando si chiamano le API REST per accedere all'account di archiviazione, è necessario connettersi usando HTTPS. "Trasferimento sicuro obbligatorio" rifiuta le richieste che usano HTTP.

Quando si usa il servizio File di Azure, se è abilitata l'opzione "Trasferimento sicuro obbligatorio" le connessioni senza crittografia hanno esito negativo. Questo include scenari in cui si usano SMB 2.1, SMB 3.0 senza crittografia e alcune versioni del client SMB Linux. 

Per impostazione predefinita, l'opzione "Trasferimento sicuro obbligatorio" è disattivata.

> [!NOTE]
> Poiché Archiviazione di Azure non supporta HTTPS per i nomi di dominio personalizzati, l'opzione non è applicabile quando si usa un nome di dominio personalizzato.

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

* [API REST](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) (versione: 12-01-2016)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (versione: 4.1.0)
* [Interfaccia della riga di comando](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (versione: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (versione: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (versione: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (versione: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (versione: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>Abilitare l'impostazione "Trasferimento sicuro obbligatorio" tramite l'API REST

Per semplificare il test con l'API REST, usare [ArmClient](https://github.com/projectkudu/ARMClient) per eseguire la chiamata dalla riga di comando.

 Per verificare l'impostazione con l'API REST, usare la riga di comando seguente:

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

Nella risposta trovare l'impostazione _supportsHttpsTrafficOnly_. ad esempio:

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

Per abilitare l'impostazione con l'API REST, usare la riga di comando seguente:

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

Di seguito è riportato un esempio di Input.json:
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>Passaggi successivi
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Per altre informazioni, vedere la [Guida alla sicurezza delle risorse di archiviazione](storage-security-guide.md).

