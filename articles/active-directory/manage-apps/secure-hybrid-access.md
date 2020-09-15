---
title: Accesso ibrido sicuro di Azure AD s| Microsoft Docs
description: Questo articolo descrive le soluzioni dei partner per l'integrazione di applicazioni legacy locali, nel cloud pubblico o nel cloud privato con Azure AD. Proteggi le app legacy connettendo le reti e i controller di distribuzione delle app ad Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087087"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Accesso ibrido sicuro: Proteggi le app legacy con Azure Active Directory

È ora possibile proteggere le applicazioni locali e di autenticazione legacy del cloud connettendosi a Azure Active Directory (AD) con:

- [Proxy applicazione Azure AD](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Controller e reti per la distribuzione di applicazioni esistenti](#sha-through-networking-and-delivery-controllers)

- [Applicazioni di rete privata virtuale (VPN) e software-defined perimetrale (SDP)](#sha-through-vpn-and-sdp-applications)

È possibile colmare il gap e rafforzare il comportamento di sicurezza in tutte le applicazioni con funzionalità Azure AD come Azure AD [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e Azure ad [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>SHA (Secure Hybrid Access) tramite il proxy di applicazione Azure AD
  
Con il [proxy di applicazione](https://aka.ms/whyappproxy) è possibile fornire [l'accesso remoto sicuro](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) alle applicazioni Web locali. Gli utenti non richiedono l'uso di una VPN. Gli utenti traggono vantaggio grazie alla connessione semplificata alle applicazioni da qualsiasi dispositivo dopo un [Single Sign-on](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). Il proxy di applicazione fornisce accesso remoto come servizio e consente di [pubblicare facilmente le applicazioni locali](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) a utenti esterni alla rete aziendale. Consente di ridimensionare la gestione dell'accesso al cloud senza che sia necessario modificare le applicazioni locali. [Pianificare un Azure ad distribuzione del proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) come passaggio successivo.

## <a name="azure-ad-partner-integrations"></a>Integrazioni di partner Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA tramite i controller di rete e di recapito

Oltre a [Azure ad proxy di applicazione](https://aka.ms/whyappproxy), per consentire all'utente di utilizzare il [Framework zero Trust](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), partner Microsoft con provider di terze parti. È possibile utilizzare i controller di rete e di distribuzione esistenti e proteggere con facilità le applicazioni legacy cruciali per i processi aziendali, ma che non è stato possibile proteggere prima con Azure AD. È probabile che siano già presenti tutti gli elementi necessari per iniziare a proteggere le applicazioni.

![Immagine mostra l'accesso ibrido sicuro con i partner di rete e il proxy applicazione](./media/secure-hybrid-access/secure-hybrid-access.png)

I fornitori di reti seguenti offrono soluzioni predefinite e linee guida dettagliate per l'integrazione con Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA tramite applicazioni VPN e SDP

Grazie alle soluzioni VPN e SDP è possibile fornire l'accesso sicuro alla rete aziendale da qualsiasi dispositivo, in qualsiasi momento, in qualsiasi posizione, proteggendo al tempo stesso i dati dell'organizzazione. Con Azure AD come provider di identità (IDP), è possibile usare metodi di autenticazione e autorizzazione moderni come Azure AD [Single Sign-on](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) e [l'autenticazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) a più fattori per proteggere le applicazioni legacy locali.  

![Immagine mostra l'accesso ibrido sicuro con i partner VPN e il proxy app ](./media/secure-hybrid-access/app-proxy-vpn.png)

I fornitori VPN e SDP seguenti offrono soluzioni predefinite e linee guida dettagliate per l'integrazione con Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Protezione globale Palo Alto Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Accesso privato zScaler (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
