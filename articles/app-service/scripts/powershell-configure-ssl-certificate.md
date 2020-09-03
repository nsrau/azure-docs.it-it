---
title: 'PowerShell: Caricare e associare TLS/SSL'
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come associare un certificato TLS/SSL personalizzato a un'app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 74bec2e8793331019a6eca986446880c10565aeb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079949"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Associare un certificato TLS/SSL personalizzato a un'app Web usando PowerShell

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate, quindi associa ad essa il certificato TLS/SSL di un nome di dominio personalizzato. 

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure. Verificare inoltre se:

- È stata creata una connessione con Azure usando il comando `az login`.
- Si disponga dell'accesso alla pagina di configurazione DNS del registrar.
- Si disponga di un file con estensione PFX valido e della relativa password per il certificato TLS/SSL da caricare e per cui si vuole eseguire l'associazione.

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modifica un piano di servizio app per cambiarne il piano tariffario. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica la configurazione di un'app Web. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Crea un'associazione al certificato TLS/SSL per un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
