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
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557820"
---
Per la procedura seguente è stata usata la configurazione seguente:

- Computer: Ubuntu server 18,04
- Dipendenze: strongSwan


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

Usare il comando seguente per installare l'interfaccia della riga di comando di Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Istruzioni aggiuntive su come installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)