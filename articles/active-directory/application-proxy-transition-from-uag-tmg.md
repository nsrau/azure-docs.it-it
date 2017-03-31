---
title: Eseguire la transizione ai proxy dell&quot;applicazione Azure AD da Microsoft Forefront | Documentazione Microsoft
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Eseguire la transizione ai proxy dell'applicazione Azure AD da Microsoft Forefront

Questo articolo descrive come passare da soluzioni Microsoft Forefront Threat Management Gateway (TMG) e United Access Gateway (UAG) a questi proxy di applicazione di Azure Active Directory (Azure AD): Proxy applicazione Web e Proxy di applicazione di Azure Active Directory.

> [!NOTE]
> Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento alla Premium Edition o alla Basic Edition di Azure AD. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).


Per informazioni dettagliate sulla transizione da Forefront TMG e Forefront UAG ai nuovi proxy di applicazione, è possibile [scaricare un white paper correlato di Microsoft](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx).

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

