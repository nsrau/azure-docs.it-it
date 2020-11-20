---
title: Abilitare l'estensione macchina virtuale usando l'interfaccia della riga di comando
description: Questo articolo descrive come distribuire le estensioni delle macchine virtuali nei server abilitati per Azure Arc in esecuzione in ambienti cloud ibridi usando l'interfaccia della riga di comando di Azure.
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3fa8273b15518c182aefa038e67d85773d500b30
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991452"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Abilitare le estensioni di VM di Azure usando l'interfaccia della riga di comando

Questo articolo illustra come distribuire e disinstallare le estensioni di macchina virtuale, supportate da server abilitati per Azure Arc, in un computer ibrido Linux o Windows usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

I comandi ConnectedMachine non vengono spediti come parte dell'interfaccia della riga di comando di Azure. Prima di usare l'interfaccia della riga di comando di Azure per gestire le estensioni di macchina virtuale nel server ibrido gestito da server abilitati per Arc, è necessario caricare l'estensione ConnectedMachine. Eseguire il comando seguente per ottenerlo:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Abilita estensione

Per abilitare un'estensione della macchina virtuale nel server abilitato per Arc, usare [AZ connectedmachine Extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) con i `--machine-name` `--extension-name` parametri,, `--location` , `--type` , `settings` e `--publisher` .

L'esempio seguente abilita l'estensione della macchina virtuale Log Analytics in un server Linux abilitato per Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Nell'esempio seguente viene abilitata l'estensione di script personalizzato in un server abilitato per Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Nell'esempio seguente viene abilitata l'estensione della macchina virtuale Key Vault (anteprima) in un server abilitato per Arc:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Elenca estensioni installate

Per ottenere un elenco delle estensioni di macchina virtuale nel server abilitato per Arc, usare [AZ connectedmachine Extension List](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) con `--machine-name` i `--resource-group` parametri e.

Esempio:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Per impostazione predefinita, l'output del comando dell'interfaccia della riga di comando di Azure è in formato JSON, ovvero JavaScript Object Notation. Per trasformare l'output predefinito in un elenco o in una tabella, ad esempio, usare [az configure --output](/cli/azure/reference-index). È possibile anche aggiungere `--output` a qualsiasi comando per apportare una modifica una sola volta nel formato di output.

L'esempio seguente illustra l'output JSON parziale del `az connectedmachine extension -list` comando:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Rimuovere un'estensione installata

Per rimuovere un'estensione VM installata nel server abilitato per Arc, usare [AZ connectedmachine Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) con i `--extension-name` `--machine-name` parametri, e `--resource-group` .

Ad esempio, per rimuovere l'estensione Log Analytics VM per Linux, eseguire il comando seguente:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere le estensioni della macchina virtuale usando il [Azure PowerShell](manage-vm-extensions-powershell.md), dal [portale di Azure](manage-vm-extensions-portal.md)o dai [modelli di Azure Resource Manager](manage-vm-extensions-template.md).

- Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida Risoluzione dei problemi delle estensioni VM](troubleshoot-vm-extensions.md).

- Per altre informazioni sui comandi, vedere l'articolo [Panoramica](/cli/azure/ext/connectedmachine/connectedmachine/extension) dell'estensione VM dell'interfaccia della riga di comando di Azure.
