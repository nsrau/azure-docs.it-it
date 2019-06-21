---
title: Il portale di Azure dell'elenco indirizzi attendibili URL | Microsoft Docs
description: Aggiungere tali URL al bypass del server proxy per comunicare con il portale di Azure e i relativi servizi
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304934"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Il portale di Azure dell'elenco indirizzi attendibili gli URL nel server proxy o firewall

Configurare i dispositivi di sicurezza locali per ignorare le restrizioni di sicurezza per il portale di Azure per prestazioni ottimali e la connettività tra la rete LAN o wide e il cloud di Azure, gli URL. Gli amministratori di rete spesso distribuiscono i server proxy, firewall o altri dispositivi per proteggere e offrono controllo sul modo in cui gli utenti accedono a internet. Tuttavia, regole progettate per proteggere gli utenti possono a volte bloccare o rallentare legittimo aziendali il traffico internet, incluse le comunicazioni tra l'utente e Azure. Per ottimizzare la connettività tra la rete e il portale di Azure e i relativi servizi, si consiglia di aggiungere il portale di Azure gli URL per dell'elenco indirizzi attendibili.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL per il proxy di bypass del portale di Azure

Aggiungere il seguente elenco di URL per il server proxy o firewall per consentire il traffico di rete a questi endpoint per ignorare le restrizioni:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.exp.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.wpc.azureedge.net

> [!NOTE]
> Il traffico a questi endpoint Usa porte TCP standard per HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Passaggi successivi

* Necessario per gli indirizzi IP dell'elenco indirizzi attendibili? Scarica l'elenco degli [intervalli IP dei data center Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Altri servizi per uso Microsoft altri URL e indirizzi IP per la connettività. Per ottimizzare la connettività di rete per i servizi Microsoft 365, vedere [configurare la rete per Office 365](/office365/enterprise/set-up-network-for-office-365).
