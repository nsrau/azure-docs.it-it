---
title: Abilitare l'estensione macchina virtuale usando l'interfaccia della riga di comando
description: Questo articolo descrive come distribuire le estensioni delle macchine virtuali nei server abilitati per Azure Arc in esecuzione in ambienti cloud ibridi usando l'interfaccia della riga di comando di Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462960"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Abilitare le estensioni di VM di Azure usando l'interfaccia della riga di comando

Questo articolo illustra come distribuire e disinstallare le estensioni delle macchine virtuali di Azure, supportate da server abilitati per Azure Arc, in un computer ibrido Linux o Windows usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>Prerequisiti

[Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

Prima di usare l'interfaccia della riga di comando di Azure per gestire le estensioni di macchina virtuale nel server ibrido gestito da server abilitati per Arc, è necessario installare l' `ConnectedMachine` estensione cli. Eseguire il comando seguente nel server abilitato per Arc:

```azurecli
az extension add connectedmachine
```

Al termine dell'installazione, viene restituito il messaggio seguente:

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Abilita estensione

Per abilitare un'estensione della macchina virtuale nel server abilitato per Arc, usare [AZ connectedmachine Machine-Extension create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) con i `--machine-name` `--extension-name` parametri,, `--location` ,, `--type` `settings` e `--publisher` .

L'esempio seguente abilita l'estensione della macchina virtuale Log Analytics in un server Linux abilitato per Arc:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Nell'esempio seguente viene abilitata l'estensione di script personalizzato in un server abilitato per Arc:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>Elenca estensioni installate

Per ottenere un elenco delle estensioni di macchina virtuale nel server abilitato per Arc, usare [AZ connectedmachine Machine-Extension List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) con `--machine-name` i `--resource-group` parametri e.

Esempio:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Per impostazione predefinita, l'output del comando dell'interfaccia della riga di comando di Azure è in formato JSON, ovvero JavaScript Object Notation. Per trasformare l'output predefinito in un elenco o in una tabella, ad esempio, usare [az configure --output](/cli/azure/reference-index). È possibile anche aggiungere `--output` a qualsiasi comando per apportare una modifica una sola volta nel formato di output.

L'esempio seguente illustra l'output JSON parziale del `az connectedmachine machine-extension -list` comando:

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

Per rimuovere un'estensione VM installata nel server abilitato per Arc, usare [AZ connectedmachine Machine-Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) con i `--extension-name` `--machine-name` parametri, e `--resource-group` .

Ad esempio, per rimuovere l'estensione Log Analytics VM per Linux, eseguire il comando seguente:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere estensioni di VM tramite [PowerShell](manage-vm-extensions-powershell.md), dal [portale di Azure](manage-vm-extensions-portal.md)o da [modelli di Azure Resource Manager](manage-vm-extensions-template.md).

- Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida Risoluzione dei problemi delle estensioni VM](troubleshoot-vm-extensions.md).
