---
title: Eseguire l&quot;aggiornamento al proxy di applicazione di Azure AD | Microsoft Docs
description: Scegliere la soluzione del proxy migliore se si esegue l&quot;aggiornamento da Microsoft Forefront o Unified Access Gateway.
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Eseguire l'aggiornamento ai proxy di AD Azure da Microsoft Forefront o Unified Access Gateway

Questo articolo descrive come spostarsi da soluzioni TMG (Microsoft Forefront Threat Management Gateway) e UAG (Unified Access Gateway) a un proxy di applicazione di Azure AD.

Per informazioni dettagliate sulla transizione da Forefront TMG e UAG al proxy di applicazione, è possibile [leggere un white paper di Microsoft correlato](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Dettagli sulle funzionalità per la conversione

|**Funzionalità TMG/UAG**|**Soluzione moderna**|
|:-----|:-----|
|Pubblicazione HTTP selettiva per app basate su browser|Proxy dell'applicazione di Azure AD|
|Integrazione di Active Directory Federation Services (AD FS)|Proxy dell'applicazione di Azure AD|
|Pubblicazione con protocolli ricchi, ad esempio Citrix, Lync e RDG|Proxy dell'applicazione di Azure AD|
|di Microsoft Azure|Pannello di accesso di Azure AD o l'icona di avvio delle app di Office 365 per il proxy di applicazione di Azure AD|
|Rilevamento dell'integrità degli endpoint|Intune o System Center|
|Tunneling SSL|Windows SSL o VPN|
|Firewall di livello 2/3|Windows Server|
|Preautenticazione per ActiveSync (HTTP Basic) e RDG|Nessuna soluzione disponibile attualmente da Microsoft|
|Web application firewall|Nessuna soluzione Microsoft disponibile attualmente.|
|Gateway Web sicuro (proxy di inoltro)|Nessuna soluzione Microsoft disponibile attualmente.|


## <a name="next-steps"></a>Passaggi successivi

[Blog: proxy applicazione Web](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog: proxy di applicazione di Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

