---
title: Creare una zona DNS e un record per un nome di dominio - Interfaccia della riga di comando di Azure - DNS di Azure
description: Questo esempio di interfaccia della riga di comando di Azure mostra come creare una zona DNS e un record per un nome di dominio
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 348b7911930711a25c88595b6360341ef6e00468
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954427"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Esempio di interfaccia della riga di comando di Azure: creare una zona DNS e un record

Questo esempio di interfaccia della riga di comando di Azure crea una zona DNS e un record per un nome di dominio. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la zona DNS e tutte le risorse correlate.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Crea una zona DNS di Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Aggiunge un record *A* a una zona DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Elencare tutti i set di record *A* in una zona DNS. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).