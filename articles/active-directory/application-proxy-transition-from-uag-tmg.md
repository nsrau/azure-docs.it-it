---
title: Transizione al proxy applicazione Azure AD da Microsoft Forefront | Documentazione Microsoft
description: Tratta i fondamenti dei connettori del proxy applicazione Azure AD.
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
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Transizione al proxy applicazione Azure AD da Microsoft Forefront

Questo articolo descrive come passare da soluzioni Microsoft Forefront Threat Management Gateway (TMG) e United Access Gateway (UAG) a questi proxy applicazione Azure AD: Proxy applicazione Web (WAP) e Proxy applicazione Azure Active Directory (AADAP). 

> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 
 
Molti clienti ci chiedono come passare da Forefront UAG e TMG ai nuovi proxy applicazione. Per informazioni, vedere questo [white paper](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx) che descrive in dettaglio la transizione. 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>Conversione di TMG/UAG nella tabella del proxy applicazione Azure AD
 
|**Funzionalità TMG/UAG**|**Proxy applicazione Web (WAP)/Proxy applicazione Azure Active Directory (AADAP)**|
|:-----|:-----|
|Pubblicazione HTTP selettiva per app basate su browser|Disponibile in WAP in Windows Server 2012 R2 (disponibile in AADAP oggi)|
|Integrazione ADFS|Disponibile in WAP in Windows Server 2012 R2 (disponibile in AADAP oggi)|
|Pubblicazione con protocolli ricchi (ad esempio Citrix, Lync, RDG)|Disponibile in WAP in Windows Server 2012 R2 (disponibile in AADAP oggi)|
|Preautenticazione per ActiveSync (HTTP Basic) e RDG|Sarà disponibile in WAP con Windows Server vNext (presto disponibile in AADAP)|
|di Microsoft Azure|Usare Intune/System Center per WAP (usare il pannello di accesso di AAD o Utilità di avvio app di Office 365 per AADAP)|
|Rilevamento dell'integrità degli endpoint|Usare Intune o System Center|
|Tunneling SSL|Usare la capacità SSL/VPN di Windows|
|Firewall livello 2/3|Usare le capacità di Windows Server|
|Web application firewall|Nessuna soluzione disponibile attualmente da Microsoft|
|Gateway Web sicuro (proxy di inoltro)|Nessuna soluzione disponibile attualmente da Microsoft|


## <a name="next-steps"></a>Passaggi successivi

[Blog: proxy applicazione Web](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog: proxy applicazione](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->


