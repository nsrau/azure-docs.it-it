---
title: Elenchi di sicurezza di portale di Azure URL | Microsoft Docs
description: Aggiungere questi URL al bypass del server proxy per comunicare con il portale di Azure e i relativi servizi
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667484"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Attendibilità degli URL portale di Azure nel firewall o nel server proxy

Per prestazioni ottimali e connettività tra la rete locale o l'area estesa e il cloud di Azure, configurare i dispositivi di sicurezza locali per ignorare le restrizioni di sicurezza per gli URL portale di Azure. Gli amministratori di rete spesso distribuiscono server proxy, firewall o altri dispositivi per proteggere e controllare il modo in cui gli utenti accedono a Internet. Tuttavia, le regole progettate per proteggere gli utenti possono a volte bloccare o rallentare il traffico Internet basato sull'azienda, incluse le comunicazioni tra l'utente e Azure. Per ottimizzare la connettività tra la rete e il portale di Azure e i relativi servizi, è consigliabile aggiungere URL portale di Azure all'elenchi di sicurezza.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL di portale di Azure per il bypass del proxy

Aggiungere l'elenco di URL seguente al server o al firewall proxy per consentire al traffico di rete verso questi endpoint di ignorare le restrizioni:

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
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Il traffico verso questi endpoint usa le porte TCP standard per HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Passaggi successivi

* È necessario disporre di indirizzi IP attendibili? Scaricare l'elenco di [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653).
* Altri servizi Microsoft usano URL e indirizzi IP aggiuntivi per la connettività. Per ottimizzare la connettività di rete per Microsoft 365 Services, vedere [configurare la rete per Office 365](/office365/enterprise/set-up-network-for-office-365).
