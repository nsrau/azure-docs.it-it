---
title: Creare gruppi IP nel firewall di Azure
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394527"
---
# <a name="create-ip-groups"></a>Creare gruppi IP

I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure. Possono avere un solo indirizzo IP, più indirizzi IP o uno o più intervalli di indirizzi IP.

## <a name="create-an-ip-group---azure-portal"></a>Creare un gruppo IP-portale di Azure

Per creare un gruppo IP usando il portale di Azure:

1. Nella home page portale di Azure selezionare **Crea una risorsa**.
1. Nella casella di ricerca immettere **gruppi IP** , quindi selezionare gruppi di **indirizzi IP**.
1. Selezionare **Crea**.
1. Selezionare la propria sottoscrizione.
1. selezionare un gruppo di risorse o crearne uno nuovo.
1. Immettere un nome univoco per il gruppo IP, quindi selezionare un'area.
1. Selezionare **Avanti: Indirizzi IP**.
1. Digitare un indirizzo IP, più indirizzi IP o intervalli di indirizzi IP.

   Esistono due modi per immettere gli indirizzi IP:
   - È possibile immetterli manualmente
   - È possibile importarli da un file

   Per importare da un file, selezionare **Importa da un file**. È possibile trascinare il file nella casella oppure selezionare **Cerca file**. Se necessario, è possibile esaminare e modificare gli indirizzi IP caricati.

   Quando si digita un indirizzo IP, il portale lo convalida per verificare la sovrapposizione, i duplicati e i problemi di formattazione.

1. Al termine, selezionare **Verifica + crea**.
1. Selezionare **Crea**.

## <a name="create-an-ip-group---azure-powershell"></a>Creare un gruppo IP-Azure PowerShell

Questo esempio crea un gruppo IP con un prefisso di indirizzo e un indirizzo IP usando Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Creare un gruppo IP-interfaccia della riga di comando di Azure

Questo esempio Mostra come creare un gruppo IP con un prefisso di indirizzo e un indirizzo IP usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui gruppi di indirizzi IP](ip-groups.md)
