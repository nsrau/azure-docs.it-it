---
title: Panoramica su gestione e monitoraggio della sicurezza di Azure | Documentazione Microsoft
description: " Azure offre meccanismi di sicurezza utili per la gestione e il monitoraggio dei servizi cloud e delle macchine virtuali di Azure.  Questo articolo offre una panoramica dei servizi e delle funzionalità di sicurezza di base. "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 6787877deabafd0b7308e190cb45b4036049b05b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-management-and-monitoring-overview"></a>Panoramica su gestione e monitoraggio della sicurezza di Azure
Azure offre meccanismi di sicurezza utili per la gestione e il monitoraggio dei servizi cloud e delle macchine virtuali di Azure. Questo articolo offre una panoramica dei servizi e delle funzionalità di sicurezza di base. Per ottenere maggiori informazioni, sono disponibili collegamenti ad articoli contenenti informazioni dettagliate su ogni funzionalità.

La sicurezza dei servizi cloud Microsoft è una responsabilità condivisa tra l'utente e Microsoft. Responsabilità condivisa significa che Microsoft è responsabile di Microsoft Azure e della sicurezza fisica dei propri data center, tramite l'uso di meccanismi di sicurezza come porte con ingresso tramite badge, recinzioni e agenti di sicurezza. Azure offre anche elevati livelli di protezione cloud a livello di software che soddisfano le complesse esigenze di sicurezza, privacy e conformità dei clienti.

L'utente è proprietario dei dati e delle identità ed è responsabile della protezione di tali elementi, della sicurezza delle risorse locali, nonché della sicurezza dei componenti cloud di cui ha il controllo. Microsoft offre funzionalità e controlli di sicurezza che facilitano la protezione di dati e applicazioni. Il grado di responsabilità dell'utente per la sicurezza dipende dal tipo di servizio cloud.

Nel grafico seguente vengono riepilogate le responsabilità di Microsoft e del cliente.

![Responsabilità condivisa][1]

Per un approfondimento sulla gestione della sicurezza, vedere [Gestione della sicurezza in Azure](azure-security-management.md).

Qui di seguito sono elencati gli argomenti trattati in questo articolo:

* Controllo degli accessi in base al ruolo
* Antimalware
* Autenticazione a più fattori
* ExpressRoute
* Gateway di rete virtuale
* Privileged Identity Management
* Identity Protection
* Centro sicurezza

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo consente la gestione specifica degli accessi per le risorse di Azure. L'uso del controllo degli accessi in base al ruolo consente di concedere agli utenti solo il livello di accesso necessario per le attività che devono svolgere.  Il controllo degli accessi in base al ruolo consente anche di assicurarsi che gli utenti non abbiano più accesso alle risorse nel cloud quando lasciano l'organizzazione.

Altre informazioni:

* [Blog del team di Active Directory sul controllo degli accessi in base al ruolo](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware
Con Azure è possibile usare software antimalware a cura dei principali fornitori di soluzioni di sicurezza, come Microsoft, Symantec, Trend Micro, McAfee e Kaspersky, per proteggere le macchine virtuali da file dannosi, adware e altre minacce.

Microsoft Antimalware offre la possibilità di installare un agente antimalware per le macchine virtuali e i ruoli PaaS. Questa funzionalità, basata su System Center Endpoint Protection, applica al cloud una comprovata tecnologia di sicurezza locale.

Viene anche garantita una stretta integrazione dei prodotti Trend [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ e [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ nella piattaforma Azure. DeepSecurity è una soluzione antivirus e SecureCloud è una soluzione di crittografia. La soluzione DeepSecurity viene distribuita nelle VM con un modello di estensione. Usando l'interfaccia utente del portale e PowerShell, si può scegliere di usare DeepSecurity all'interno delle nuove VM attivate o di VM esistenti già distribuite.

Anche Symantec Endpoint Protection (SEP) è supportato in Azure. Tramite l'integrazione nel portale, i clienti possono specificare che intendono usare SEP all'interno di una VM. È possibile installare SEP in una nuova VM tramite il portale di Azure oppure in una VM esistente usando PowerShell.

Altre informazioni:

* [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](azure-security-antimalware.md)
* [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Windows](../virtual-machines/windows/classic/install-trend.md)
* [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nuove opzioni antimalware per la protezione di macchine virtuali di Azure: McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori
Azure Multi-Factor Authentication (MFA) è un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. MFA consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre autenticazione avanzata tramite diverse opzioni di verifica, ad esempio una telefonata, un SMS, una notifica dell'app per dispositivi mobili o un codice di verifica e token OATH di terze parti.

Altre informazioni:

* [Autenticazione a più fattori](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Come funziona Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata dedicata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e CRM Online. La connettività può essere stabilita da una rete (IP VPN) any-to-any, da una rete Ethernet punto a punto o da una Cross Connection virtuale tramite un provider di connettività presso una struttura di condivisione del percorso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

Altre informazioni:

* [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateway di rete virtuale
I gateway VPN, anche detti gateway di rete virtuale di Azure, vengono usati per inviare traffico di rete tra reti virtuali e percorsi locali. Vengono inoltre usati per inviare il traffico tra più reti virtuali in Azure.  I gateway VPN offrono connettività cross-premise sicura tra Azure e l'infrastruttura locale.

Altre informazioni:

* [Informazioni sui gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Panoramica della sicurezza di rete di Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
In alcuni casi gli utenti hanno la necessità di eseguire operazioni con privilegi nelle risorse di Azure o in altre applicazioni SaaS. Per questa ragione, è spesso necessario che le organizzazioni concedano agli utenti l'accesso con privilegi permanente in Azure Active Directory (Azure AD). Questo rappresenta un rischio di sicurezza crescente per le risorse ospitate nel cloud poiché le organizzazioni non sono in grado di monitorare completamente le operazioni eseguite dagli utenti con l'accesso con privilegi.
Se un account utente con accesso privilegiato è compromesso, tale singola violazione può inoltre compromettere la sicurezza dell'intero cloud. Azure AD Privileged Identity Management consente di evitare questo rischio riducendo il tempo di esposizione dei privilegi e aumentando la visibilità sull'utilizzo.  

Privileged Identity Management introduce il concetto di amministratore temporaneo per un ruolo, detto anche accesso amministrativo "Just-In-Time", ovvero un utente che deve completare un processo di attivazione per il ruolo assegnato. Il processo di attivazione cambia l'assegnazione dell'utente a un ruolo in Azure AD da inattiva ad attiva per un periodo di tempo specificato, ad esempio otto ore.

Altre informazioni:

* [Gestione identità con privilegi di Azure AD](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Introduzione ad Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure Active Directory (AD) Identity Protection offre una visualizzazione consolidata delle attività di accesso sospette e delle potenziali vulnerabilità per garantire la protezione dell'organizzazione. Identity Protection rileva le attività sospette per le identità utente e con privilegi (amministratore), in base a segnali come attacchi di forza bruta, perdita di credenziali e accessi da località ignote e dispositivi infetti.

Tramite l'invio di notifiche e la visualizzazione delle correzioni consigliate, Identity Protection consente di ridurre i rischi in tempo reale. Identity Protection calcola la gravità dei rischi per l'utente e consente di configurare criteri basati sul rischio per proteggere automaticamente l'accesso all'applicazione da future minacce.

Altre informazioni:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro sicurezza
Il Centro sicurezza di Azure aiuta a impedire, rilevare e rispondere alle minacce offrendo visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza di Azure consente di ottimizzare e monitorare la sicurezza di delle risorse di Azure tramite le funzionalità seguenti:

* La possibilità di definire i criteri per le risorse delle sottoscrizioni di Azure in base alle esigenze di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ogni sottoscrizione.
* Il monitoraggio dello stato della rete, delle applicazioni e delle macchine virtuali di Azure.
* La visualizzazione di un elenco degli avvisi di sicurezza con priorità, inclusi gli avvisi relativi alle soluzioni dei partner integrate, nonché le informazioni che è necessario esaminare rapidamente e le indicazioni su come risolvere un attacco.

Altre informazioni:

* [Introduzione al Centro sicurezza di Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
