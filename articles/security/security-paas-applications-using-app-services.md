---
title: Protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante i Servizi app di Azure | Microsoft Docs
description: " Informazioni sulle procedure consigliate della sicurezza di Servizi app di Azure per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0738522250f1863c9584936a2d2b2c7a0a823c8c
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-services"></a>Protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante i Servizi app di Azure

In questo articolo viene illustrato un insieme di procedure consigliate dei [Servizi App di Azure](https://azure.microsoft.com/services/app-service/) per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con Azure.

## <a name="azure-app-services"></a>Servizi app di Azure
[Servizi app di Azure](../app-service/app-service-value-prop-what-is.md) è una soluzione PaaS che consente di creare applicazioni Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo e di connettersi ai dati ovunque, nel cloud o in locale. Servizi app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud. I Servizi app sono un singolo servizio integrato che offre un set completo di funzionalità per scenari Web, mobili e di integrazione.

Per altre informazioni, vedere le panoramiche su [app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md) e [app Web](../app-service-web/app-service-web-overview.md).

## <a name="best-practices"></a>Procedure consigliate

Quando si utilizza Servizi app, seguire queste procedure consigliate:

- [Eseguire l'autenticazione tramite Azure Active Directory (AD)](../app-service-web/web-sites-authentication-authorization.md#authenticate-through-azure-active-directory). Servizi app fornisce un servizio OAuth 2.0 per il provider di identità. OAuth 2.0 è incentrato sulla semplicità di sviluppo client fornendo i flussi di autorizzazione specifici per le applicazioni Web, applicazioni desktop e telefoni cellulari. Azure AD usa OAuth 2.0 per consentire all'utente di autorizzare l'accesso alle applicazioni per dispositivi mobili e alle applicazioni Web.
- Limitare l'accesso in base ai principio di necessità e al principio dei privilegi minimi in materia di sicurezza. La limitazione degli accessi è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati. Il controllo degli accessi in base al ruolo (RBAC) consente di assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. Per altre informazioni sulla concessione agli utenti dell'accesso alle applicazioni, vedere [Introduzione alla gestione degli accessi](../active-directory/role-based-access-control-what-is.md).
- Proteggere le chiavi. Non importa quanto è valida la sicurezza impostata se si perdono le chiavi di sottoscrizione. L'insieme di credenziali chiave di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Con l'insieme di credenziali delle chiavi è possibile crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dati, file PFX e password) usando chiavi protette da moduli di protezione hardware (HSM). Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware. Per ulteriori informazioni, vedere [Azure Key Vault](../key-vault/key-vault-whatis.md). È anche possibile utilizzare Azure Key Vault per gestire i certificati TLS con il rinnovo automatico.
- Limitare gli indirizzi IP di origine in ingresso. [Ambiente del servizio app](../app-service-web/app-service-app-service-environment-intro.md) dispone di una funzionalità di integrazione di rete virtuale che consente di limitare gli indirizzi origine IP di origine in ingresso tramite gruppi di sicurezza di rete (NSG). Se non si ha familiarità con le reti virtuali di Azure (VNET), si tratta di una funzionalità che consente di posizionare molte delle risorse di Azure in una rete instradabile non Internet di cui si controlla l'accesso. Per altre informazioni, vedere [Integrare un'app in una rete virtuale di Azure](../app-service-web/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive una serie di procedure consigliate per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante Servizi app di Azure. Per ulteriori informazioni sulla protezione delle distribuzioni PaaS, vedere:

- [Securing PaaS deployments (Proteggere le distribuzioni PaaS)](security-paas-deployments.md)
- [Protezione di applicazioni Web PaaS Eeb e di applicazioni per dispositivi mobili utilizzando il database SQL Azure e SQL Data Warehouse](security-paas-applications-using-sql.md)

