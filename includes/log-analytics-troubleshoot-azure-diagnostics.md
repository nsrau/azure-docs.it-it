---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 44febf95c660601df78047fc473f61e0d3169890
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270681"
---
### <a name="troubleshoot-azure-diagnostics"></a>Risolvere i problemi relativi a Diagnostica di Azure

Se viene visualizzato il messaggio di errore seguente, il provider di risorse Microsoft.insights non è registrato:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Per registrare il provider di risorse, seguire questa procedura nel portale di Azure:

1.  Nel pannello di navigazione a sinistra fare clic su *Sottoscrizioni*.
2.  Selezionare la sottoscrizione identificata nel messaggio di errore.
3.  Fare clic su *Provider di risorse*.
4.  Trovare il provider *Microsoft.insights*.
5.  Fare clic sul collegamento *Registra*.

![Registrare il provider di risorse Microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Dopo aver registrato il provider di risorse *Microsoft.insights*, provare di nuovo a configurare la diagnostica.


Se in PowerShell viene visualizzato il messaggio di errore seguente è necessario aggiornare la versione di PowerShell:

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Aggiornare PowerShell alla versione di novembre 2016 (v2.3.0) o successiva usando le istruzioni disponibili nell'articolo [Get started with Azure PowerShell cmdlets](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (Introduzione ai cmdlet di Azure PowerShell).
