---
title: Incorporare Azure Cloud Shell | Microsoft Docs
description: Informazioni su come incorporare Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614317"
---
# <a name="embed-azure-cloud-shell"></a>Incorporare Azure Cloud Shell

L'incorporamento di Cloud Shell consente a sviluppatori e autori di contenuti di aprire Cloud Shell direttamente da un URL dedicato, [shell.azure.com](https://shell.azure.com). Da qui gli utenti possono accedere immediatamente alla gamma completa di funzioni di autenticazione e strumenti di Cloud Shell, nonché agli strumenti aggiornati di Azure PowerShell e dell'interfaccia della riga di comando di Azure.

Pulsante di dimensioni normali

[![Avvio di dimensioni normali](https://shell.azure.com/images/launchcloudshell.png "Avvio di Azure Cloud Shell")](https://shell.azure.com)

Pulsante di grandi dimensioni

[![Avvio di dimensioni grandi](https://shell.azure.com/images/launchcloudshell@2x.png "Avvio di Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Procedura

Integrare il pulsante di avvio di Cloud Shell nei file markdown copiando il codice seguente:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Di seguito è riportato il codice HTML per incorporare Cloud Shell come elemento popup:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Personalizzare l'esperienza

Impostare un'esperienza shell specifica aumentando l'URL.

|Esperienza   |URL   |
|---|---|
|Ultima shell usata   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md)<br>
[Guida introduttiva a PowerShell in Cloud Shell](quickstart-powershell.md)
