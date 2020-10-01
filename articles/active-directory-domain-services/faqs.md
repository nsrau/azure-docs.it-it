---
title: Domande frequenti su Azure AD Domain Services | Microsoft Docs
description: Leggere e comprendere alcune delle domande frequenti relative alla configurazione, all'amministrazione e alla disponibilità per Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/30/2020
ms.author: iainfou
ms.openlocfilehash: 6e2daa60e99eb7aab34b11f240a2e2fb03c98582
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612405"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Domande frequenti sui servizi di dominio Azure Active Directory (AD)

In questa pagina vengono fornite le risposte alle domande frequenti su Azure Active Directory Domain Services.

## <a name="configuration"></a>Configurazione

* [È possibile creare più domini gestiti per una singola directory di Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [È possibile abilitare Azure AD Domain Services in una rete virtuale classica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale Gestione risorse?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [È possibile abilitare Azure AD Domain Services in una directory di Azure AD federata? Non sincronizzare gli hash delle password con Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [È possibile abilitare Servizi di dominio Azure AD con PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [È possibile abilitare Azure AD Domain Services usando un modello di Gestione risorse?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Gli utenti guest possono essere invitati alla directory usando Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [È possibile spostare un dominio gestito Azure AD Domain Services esistente in una sottoscrizione, un gruppo di risorse, un'area o una rete virtuale diversi?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Azure AD Domain Services includono opzioni di disponibilità elevata?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>È possibile creare più domini gestiti per una singola directory di Azure AD?
No. È possibile creare solo un singolo dominio gestito da Active Directory Domain Services per una singola directory di Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>È possibile abilitare Azure AD Domain Services in una rete virtuale classica?
Le reti virtuali classiche non sono supportate per le nuove distribuzioni. I domini gestiti esistenti distribuiti nelle reti virtuali classiche continuano a essere supportati fino a quando non sono stati ritirati il 1 ° marzo 2023. Per le distribuzioni esistenti, è possibile [eseguire la migrazione Azure ad Domain Services dal modello di rete virtuale classica a gestione risorse](migrate-from-classic-vnet.md).

Per ulteriori informazioni, vedere l' [avviso ufficiale di deprecazione](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?
Sì. Azure AD Domain Services può essere abilitato in una rete virtuale di Azure Resource Manager. Le reti virtuali di Azure classiche non sono più disponibili quando si crea un dominio gestito.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale Gestione risorse?
Sì. Per altre informazioni, vedere [eseguire la migrazione Azure ad Domain Services dal modello di rete virtuale classica a gestione risorse](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?
Sì. Per altre informazioni, vedere [How to enable Azure ad Domain Services in Azure CSP subscriptions](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>È possibile abilitare Azure AD Domain Services in una directory di Azure AD federata? Gli hash delle password non vengono sincronizzati in Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?
No. Per autenticare gli utenti tramite NTLM o Kerberos, Azure AD Domain Services necessario accedere agli hash delle password degli account utente. In una directory federata gli hash delle password non vengono archiviati nella directory Azure AD. Pertanto, Azure AD Domain Services non funziona con tali directory di Azure AD.

Tuttavia, se si usa Azure AD Connect per la sincronizzazione dell'hash delle password, è possibile usare Azure AD Domain Services perché i valori hash della password sono archiviati in Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Il dominio gestito è disponibile in una sola rete virtuale alla volta. Tuttavia, è possibile configurare la connettività tra più reti virtuali per esporre Azure AD Domain Services ad altre reti virtuali. Per altre informazioni, vedere [come connettere reti virtuali in Azure tramite gateway VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) o [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>È possibile abilitare Servizi di dominio Azure AD con PowerShell?
Sì. Per altre informazioni, vedere [How to enable Azure ad Domain Services using PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>È possibile abilitare Azure Active Directory Domain Services con un modello di Gestione risorse?
Sì, è possibile creare un Azure AD Domain Services dominio gestito usando un modello di Gestione risorse. Prima di distribuire il modello, è necessario creare un'entità servizio e un gruppo di Azure AD per l'amministrazione utilizzando portale di Azure o Azure PowerShell. Per altre informazioni, vedere [creare un dominio gestito Azure AD DS usando un modello di Azure Resource Manager](template-create-instance.md). Quando si crea un Azure AD Domain Services dominio gestito nella portale di Azure, è disponibile anche un'opzione per esportare il modello da usare con distribuzioni aggiuntive.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?
No. Il dominio fornito da Servizi di dominio Azure Active Directory è un dominio gestito. Non è necessario eseguire il provisioning, configurare o gestire in altro modo i controller di dominio per questo dominio. Queste attività di gestione sono fornite come servizio da Microsoft. Pertanto, non è possibile aggiungere altri controller di dominio (lettura/scrittura o sola lettura) per il dominio gestito.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Gli utenti guest possono essere invitati alla directory usando Azure AD Domain Services?
No. Gli utenti guest invitati a una directory di Azure AD tramite la procedura di invito [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) vengono sincronizzati nel dominio gestito di Azure AD Domain Services. Tuttavia, le password per questi utenti non vengono archiviate nella directory Azure AD. Pertanto, Azure AD Domain Services non è in grado di sincronizzare gli hash NTLM e Kerberos per questi utenti nel dominio gestito. Tali utenti non possono accedere o aggiungere computer al dominio gestito.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>È possibile spostare un dominio gestito Azure AD Domain Services esistente in una sottoscrizione, un gruppo di risorse, un'area o una rete virtuale diversi?
No. Dopo aver creato un Azure AD Domain Services dominio gestito, non è possibile spostare il dominio gestito in un gruppo di risorse, una rete virtuale, una sottoscrizione e così via. Quando si distribuisce il dominio gestito, prestare attenzione a selezionare la sottoscrizione, il gruppo di risorse, l'area e la rete virtuale più appropriati.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Azure AD Domain Services includono opzioni di disponibilità elevata?

Sì. Ogni dominio gestito Azure AD Domain Services include due controller di dominio. Non è possibile gestire o connettersi a questi controller di dominio, che fanno parte del servizio gestito. Se si distribuisce Azure AD Domain Services in un'area che supporta zone di disponibilità, i controller di dominio vengono distribuiti tra le zone. In aree che non supportano zone di disponibilità, i controller di dominio vengono distribuiti tra set di disponibilità. Non sono disponibili opzioni di configurazione o controllo di gestione per questa distribuzione. Per altre informazioni, vedere [Opzioni di disponibilità per le macchine virtuali in Azure](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Amministrazione e operazioni

* [È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ho abilitato Azure AD Domain Services. Quale account utente è possibile usare per aggiungere computer al dominio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [È possibile modificare o aggiungere record DNS nel domino gestito?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Quali sono i criteri di durata delle password in un dominio gestito?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [È possibile configurare file system distribuito (DFS) e la replica all'interno di Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [In che modo vengono applicati gli aggiornamenti di Windows in Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?
No. Non si dispone delle autorizzazioni necessarie per connettersi ai controller di dominio per il dominio gestito utilizzando Desktop remoto. I membri del gruppo *AAD DC Administrators* possono amministrare il dominio gestito usando gli strumenti di amministrazione di ad, ad esempio Active Directory Administration Center (ADAC) o ad PowerShell. Questi strumenti vengono installati utilizzando la funzionalità *strumenti di amministrazione remota del server* in un server Windows aggiunto al dominio gestito. Per altre informazioni, vedere [creare una macchina virtuale di gestione per configurare e amministrare un Azure ad Domain Services dominio gestito](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ho abilitato Azure AD Domain Services. quale account utente è necessario usare per aggiungere i computer a questo dominio?
Tutti gli account utente che fanno parte del dominio gestito possono aggiungere una macchina virtuale. Ai membri del gruppo *AAD DC Administrators* viene concesso l'accesso desktop remoto ai computer che sono stati aggiunti al dominio gestito.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?
No. Non sono concessi privilegi amministrativi per il dominio gestito. I privilegi di amministratore di *dominio* e di *amministratore dell'organizzazione* non sono disponibili per l'uso all'interno del dominio. Ai membri dei gruppi di amministratori di dominio o di amministratori dell'organizzazione nell'Active Directory locale non sono inoltre concessi privilegi di amministratore di dominio o dell'organizzazione per il dominio gestito.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?
Non è possibile modificare utenti e gruppi sincronizzati da Azure Active Directory a Azure AD Domain Services perché la relativa origine di origine è Azure Active Directory. Qualsiasi utente o gruppo che ha origine nel dominio gestito può essere modificato.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?
Le modifiche apportate alla directory Azure AD usando l'interfaccia utente di Azure AD o PowerShell vengono automaticamente sincronizzate con il dominio gestito. Il processo di sincronizzazione avviene in background. Non esiste un periodo di tempo definito per completare la sincronizzazione di tutte le modifiche apportate all'oggetto.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?
No. Lo schema è amministrato da Microsoft per il dominio gestito. Le estensioni dello schema non sono supportate da Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>È possibile modificare o aggiungere record DNS nel domino gestito?
Sì. Ai membri del gruppo *AAD DC Administrators* vengono concessi privilegi di *amministratore DNS* per modificare i record DNS nel dominio gestito. Questi utenti possono usare la console Gestore DNS in un computer che esegue Windows Server aggiunto al dominio gestito per gestire il DNS. Per utilizzare la console Gestore DNS, installare *gli strumenti server DNS*, che fanno parte del *strumenti di amministrazione remota del server* funzionalità facoltativa del server. Per altre informazioni, vedere [amministrare DNS in un Azure ad Domain Services dominio gestito](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Quali sono i criteri di durata delle password in un dominio gestito?
La durata predefinita di una password in un dominio gestito di Azure AD Domain Services è 90 giorni. Questa durata non è sincronizzata con la durata delle password configurata in Azure AD. Pertanto, possono verificarsi situazioni in cui le password degli utenti scadono nel dominio gestito, ma sono ancora valide in Azure AD. In scenari di questo tipo gli utenti devono cambiare la password in Azure AD e la nuova password verrà sincronizzata nel dominio gestito. Se si desidera modificare la durata predefinita delle password in un dominio gestito, è possibile [creare e configurare criteri password personalizzati.](password-policy.md)

Inoltre, il criterio Azure AD password per *DisablePasswordExpiration* viene sincronizzato con un dominio gestito. Quando *DisablePasswordExpiration* viene applicato a un utente in Azure AD, *DONT_EXPIRE_PASSWORD* applicato il valore di *userAccountControl* per l'utente sincronizzato nel dominio gestito.

Quando gli utenti reimpostano la password in Azure AD, viene applicato l'attributo *forceChangePasswordNextSignIn = true* . Un dominio gestito Sincronizza questo attributo da Azure AD. Quando il dominio gestito rileva che *forceChangePasswordNextSignIn* è impostato per un utente sincronizzato da Azure ad, l'attributo *pwdLastSet* nel dominio gestito viene impostato su *0*, che invalida la password attualmente impostata.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?
Sì. Dopo 5 tentativi di inserimento di password non valide in 2 minuti per il dominio gestito, l'account utente viene bloccato per 30 minuti. Dopo 30 minuti l'account utente viene sbloccato automaticamente. I tentativi di password non validi nel dominio gestito non bloccano l'account utente in Azure AD. L'account utente viene bloccato solo all'interno del dominio gestito di Azure AD Domain Services. Per ulteriori informazioni, vedere [criteri di blocco di password e account nei domini gestiti](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>È possibile configurare file system distribuito e la replica all'interno di Azure AD Domain Services?
No. File system distribuito (DFS) e la replica non sono disponibili quando si usa Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>In che modo vengono applicati gli aggiornamenti di Windows in Azure AD Domain Services?
I controller di dominio in un dominio gestito applicano automaticamente gli aggiornamenti di Windows necessari. Non c'è niente da configurare o amministrare qui. Assicurarsi di non creare regole del gruppo di sicurezza di rete che bloccano il traffico in uscita verso gli aggiornamenti di Windows. Per le macchine virtuali associate al dominio gestito, l'utente è responsabile della configurazione e dell'applicazione degli aggiornamenti necessari del sistema operativo e dell'applicazione.

## <a name="billing-and-availability"></a>Fatturazione e disponibilità

* [Servizi di dominio Azure AD è a pagamento?](#is-azure-ad-domain-services-a-paid-service)
* [È disponibile una versione di valutazione gratuita per il servizio?](#is-there-a-free-trial-for-the-service)
* [È possibile sospendere un dominio gestito da Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento di ripristino di emergenza?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [È possibile ottenere Azure AD Domain Services come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium utilizzare Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In quale aree di Azure è disponibile il servizio?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Servizi di dominio Azure AD è a pagamento?
Sì. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>È disponibile una versione di valutazione gratuita per il servizio?
Azure AD Domain Services è incluso nella versione di valutazione gratuita di Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>È possibile sospendere un dominio gestito da Azure AD Domain Services?
No. Una volta abilitato un Azure AD Domain Services dominio gestito, il servizio è disponibile nella rete virtuale selezionata fino a quando non si elimina il dominio gestito. Non è possibile sospendere il servizio. La fatturazione continua su base oraria finché non viene eliminato il dominio gestito.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento di ripristino di emergenza?
No. Azure AD Domain Services attualmente non fornisce un modello di distribuzione con ridondanza geografica. È limitata a una singola rete virtuale in un'area di Azure. Se si vogliono usare più aree di Azure, è necessario eseguire i controller di Dominio di Active Directory nelle macchine virtuali IaaS di Azure. Per informazioni aggiuntive sull'architettura, vedere [estendere il dominio di Active Directory locale ad Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium per usare Azure Active Directory Domain Services?
No. Azure AD Domain Services è un servizio di Azure con pagamento in base al consumo e non fa parte di EMS. Azure AD Domain Services può essere utilizzato con tutte le edizioni di Azure AD (gratuito e Premium). L'addebito viene addebitato su base oraria, a seconda dell'utilizzo.

### <a name="what-azure-regions-is-the-service-available-in"></a>In quale aree di Azure è disponibile il servizio?
Per l'elenco delle aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per soluzioni relative ai problemi comuni di configurazione o amministrazione di Azure AD Domain Services, vedere la [Guida alla risoluzione dei problemi](troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure AD Domain Services, vedere [che cos'è Azure Active Directory Domain Services?](overview.md).

Per iniziare, vedere [creare e configurare un Azure Active Directory Domain Services dominio gestito](tutorial-create-instance.md).
