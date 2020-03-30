---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520851"
---
La seguente configurazione è stata utilizzata per la procedura seguente:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Dependencies| strongSwan |


Per installare la configurazione strongSwan richiesta, eseguire i comandi seguenti:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Usare il comando seguente per installare l'interfaccia della riga di comando di Azure:Use the following command to install the Azure command-line interface:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Istruzioni aggiuntive su come installare l'interfaccia della riga di comando di AzureAdditional instructions on how to install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
