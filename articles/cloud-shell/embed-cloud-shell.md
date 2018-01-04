---
title: Incorporare una Shell di Cloud di Azure | Documenti Microsoft
description: Viene illustrato come incorporare Shell di Cloud di Azure.
services: cloud-shell
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 78b539136971aa282e5447d7882ecb02f73f346b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="embed-azure-cloud-shell"></a>Incorporare una Shell di Cloud di Azure

Incorporamento di Shell Cloud consente agli sviluppatori e agli autori dei contenuti aprire direttamente Shell Cloud da un URL dedicato, [shell.azure.com](https://shell.azure.com). Verrà visualizzata immediatamente le potenzialità di autenticazione della Shell del Cloud, strumenti, e aggiornato Azure CLI/Azure PowerShell degli strumenti per gli utenti.

[![](https://shell.azure.com/images/launchcloudshell.png "Avviare Shell di Cloud di Azure")](https://shell.azure.com)

## <a name="how-to"></a>Procedura

Integrazione di pulsante di avvio della Shell del Cloud nei file markdown copiando le operazioni seguenti:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Il codice HTML per incorporare un menu a comparsa A che cloud Shell è inferiore:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Personalizzare l'esperienza

Impostare un'esperienza di shell specifica potenziando l'URL.
|Esperienza   |URL   |
|---|---|
|Shell utilizzati più di recente   |Shell.Azure.com           |
|Bash                       |Shell.Azure.com/bash       |
|PowerShell                 |Shell.Azure.com/PowerShell |

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md)<br>
[Guida introduttiva a Cloud Shell PowerShell](quickstart-powershell.md)
