---
title: Protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante Servizio app di Azure | Microsoft Docs
description: " Informazioni sulle procedure consigliate della sicurezza di Servizio app di Azure per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili. "
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
ms.openlocfilehash: f1f08133b4601c7260687a4b281b461dbf4d611e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Protezione delle applicazioni Web e per dispositivi mobili in PaaS mediante Servizio app di Azure

In questo articolo viene illustrato un insieme di procedure consigliate di [Servizio App di Azure](https://azure.microsoft.com/services/app-service/) per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con Azure.

## <a name="azure-app-service"></a>Servizio app di Azure
[Servizio app di Azure](../app-service/app-service-web-overview.md) è una soluzione PaaS che consente di creare applicazioni Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo e di connettersi ai dati ovunque, nel cloud o in locale. Il servizio app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud. Il servizio app è un singolo servizio integrato che offre un set completo di funzionalità per scenari Web, per dispositivi mobili e di integrazione.

## <a name="best-practices"></a>Procedure consigliate

Quando si usa il servizio app, seguire queste procedure consigliate:

- [Eseguire l'autenticazione tramite Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). Il servizio app fornisce un servizio OAuth 2.0 per il provider di identità. OAuth 2.0 è incentrato sulla semplicità di sviluppo client fornendo i flussi di autorizzazione specifici per le applicazioni Web, applicazioni desktop e telefoni cellulari. Azure AD usa OAuth 2.0 per consentire all'utente di autorizzare l'accesso alle applicazioni per dispositivi mobili e alle applicazioni Web.
- Limitare l'accesso in base ai principio di necessità e al principio dei privilegi minimi in materia di sicurezza. La limitazione degli accessi è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati. Il controllo degli accessi in base al ruolo (RBAC) consente di assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. Per altre informazioni sulla concessione agli utenti dell'accesso alle applicazioni, vedere [Introduzione alla gestione degli accessi](../active-directory/role-based-access-control-what-is.md).
- Proteggere le chiavi. Non importa quanto è valida la sicurezza impostata se si perdono le chiavi di sottoscrizione. L'insieme di credenziali chiave di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Con l'insieme di credenziali delle chiavi è possibile crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dati, file PFX e password) usando chiavi protette da moduli di protezione hardware (HSM). Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware. Per ulteriori informazioni, vedere [Azure Key Vault](../key-vault/key-vault-whatis.md). È anche possibile utilizzare Azure Key Vault per gestire i certificati TLS con il rinnovo automatico.
- Limitare gli indirizzi IP di origine in ingresso. [Ambiente del servizio app](../app-service/environment/intro.md) ha una funzionalità di integrazione di rete virtuale che consente di limitare gli indirizzi IP di origine in ingresso tramite gruppi di sicurezza di rete (NSG). Se non si ha familiarità con le reti virtuali di Azure (VNET), si tratta di una funzionalità che consente di posizionare molte delle risorse di Azure in una rete instradabile non Internet di cui si controlla l'accesso. Per altre informazioni, vedere [Integrare un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive una serie di procedure consigliate per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante il servizio app. Per ulteriori informazioni sulla protezione delle distribuzioni PaaS, vedere:

- [Securing PaaS deployments (Proteggere le distribuzioni PaaS)](security-paas-deployments.md)
- [Protezione di applicazioni Web PaaS Eeb e di applicazioni per dispositivi mobili utilizzando il database SQL Azure e SQL Data Warehouse](security-paas-applications-using-sql.md)
