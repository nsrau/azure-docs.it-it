---
title: Eseguire la transizione ai proxy dell&quot;applicazione Azure AD da Microsoft Forefront | Microsoft Docs
description: Vengono illustrate le nozioni di base sulla transizione dalle soluzioni Microsoft Forefront TMG e Microsoft Forefront UAG ai proxy di applicazione di Azure Active Directory.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: kgremban
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 6e95e9abac988ae54a401927a92bdb397dd63eed
ms.lasthandoff: 04/21/2017

---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Eseguire la transizione ai proxy dell'applicazione Azure AD da Microsoft Forefront

Questo articolo descrive come spostarsi da soluzioni TMG (Microsoft Forefront Threat Management Gateway) e UAG (Unified Access Gateway) a un proxy di applicazione di Azure AD.

Per informazioni dettagliate sulla transizione da Forefront TMG e UAG al proxy di applicazione, è possibile [leggere un white paper di Microsoft correlato](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Dettagli sulle funzionalità per la conversione

|**Funzionalità TMG/UAG**|**Proxy applicazione Web/Proxy di applicazione di Azure AD**|
|:-----|:-----|
|Pubblicazione HTTP selettiva per app basate su browser|Disponibile nel Proxy applicazione Web di Windows Server 2012 R2. Disponibile nel proxy di applicazione di Azure AD.|
|Integrazione di Active Directory Federation Services (AD FS)|Disponibile nel Proxy applicazione Web di Windows Server 2012 R2. Disponibile nel proxy di applicazione di Azure AD.|
|Pubblicazione con protocolli ricchi, ad esempio Citrix, Lync e RDG|Disponibile nel Proxy applicazione Web di Windows Server 2012 R2. Disponibile nel proxy di applicazione di Azure AD.|
|Preautenticazione per ActiveSync (HTTP Basic) e RDG|Attualmente non disponibile nel Proxy applicazione Web o nel proxy di applicazione di Azure AD.|
|di Microsoft Azure|Usare Intune o System Center per il Proxy applicazione Web. Usare il pannello di accesso di Azure AD o l'icona di avvio delle app di Office 365 per il proxy di applicazione di Azure AD.|
|Rilevamento dell'integrità degli endpoint|Usare Intune o System Center.|
|Tunneling SSL|Usare la funzionalità SSL o VPN di Windows.|
|Firewall di livello 2/3|Usare le funzionalità di Windows Server.|
|Web application firewall|Nessuna soluzione Microsoft disponibile attualmente.|
|Gateway Web sicuro (proxy di inoltro)|Nessuna soluzione Microsoft disponibile attualmente.|


## <a name="next-steps"></a>Passaggi successivi

[Blog: proxy applicazione Web](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog: proxy di applicazione di Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

