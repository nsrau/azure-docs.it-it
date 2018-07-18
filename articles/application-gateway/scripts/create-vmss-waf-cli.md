---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Limitare il traffico Web | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un gateway applicazione con un web application firewall e un set di scalabilità di macchine virtuali che usa regole OWASP per limitare il traffico.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7b63da2e21735da78d2e6b3c244dc2e80a48aab5
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750256"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Limitare il traffico Web con l'interfaccia della riga di comando di Azure

Questo script crea un gateway applicazione con un web application firewall che usa un set di scalabilità di macchine virtuali per i server back-end. Il web application firewall consente di limitare il traffico Web in base alle regole OWASP. Dopo aver eseguito lo script, è possibile testare il gateway applicazione tramite il relativo indirizzo IP pubblico.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_net) | Crea una rete virtuale. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crea una subnet in una rete virtuale. |
| [az network public-ip create](https://docs.microsoft.com/en-us/cli/azure/network/public-ip?view=azure-cli-latest) | Crea l'indirizzo IP pubblico per il gateway applicazione. |
| [az network application-gateway create](https://docs.microsoft.com/en-us/cli/azure/network/application-gateway?view=azure-cli-latest) | Creare un gateway applicazione. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Crea un set di scalabilità di macchine virtuali. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Crea un account di archiviazione. |
| [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) | Crea un account di archiviazione. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_show) | Ottiene l'indirizzo IP pubblico del gateway applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per il gateway applicazione sono reperibili nella [documentazione sul gateway applicazione di Azure](../cli-samples.md).
