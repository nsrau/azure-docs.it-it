---
title: Domande frequenti su Azure Active Directory Domain Services | Microsoft Docs
description: Domande frequenti su Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: maheshu
ms.openlocfilehash: 1963931f30808e861445c9555a04f933514239c3
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Servizi di dominio Azure Active Directory: domande frequenti
Questa pagina include le risposte a domande frequenti relative a Servizi di dominio Azure AD. Controllarla costantemente per eventuali aggiornamenti.

## <a name="troubleshooting-guide"></a>Guida alla risoluzione dei problemi
Fare riferimento alla [Guida alla risoluzione dei problemi](active-directory-ds-troubleshooting.md) per soluzioni relative ai problemi comuni riscontrati durante la configurazione o l'amministrazione di Azure AD Domain Services.

## <a name="configuration"></a>Configurazione
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>È possibile creare più domini gestiti per una singola directory di Azure AD?
di serie È possibile creare solo un singolo dominio gestito da Active Directory Domain Services per una singola directory di Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?
Sì. Azure AD Domain Services può essere abilitato in una rete virtuale di Azure Resource Manager. Le reti virtuali classiche di Azure non sono più supportate per la creazione di nuovi domini gestiti.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale di Resource Manager?
No, per il momento. In futuro verrà distribuito da Microsoft un meccanismo per eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale di Gestione risorse.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?
Sì. Per informazioni su come è possibile abilitare [Azure AD Domain Services nelle sottoscrizioni CSP di Azure](active-directory-ds-csp.md), vedere il relativo articolo.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-use-adfs-to-authenticate-users-for-access-to-office-365-and-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>È possibile abilitare Azure AD Domain Services in una directory di Azure AD federata? Si usa AD FS per autenticare gli utenti per l'accesso a Office 365 e non si sincronizzano gli hash delle password con Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?
di serie Azure AD Domain Services richiede l'accesso agli hash delle password degli account utente per autenticare gli utenti tramite NTLM o Kerberos. In una directory federata gli hash delle password non vengono memorizzati nella directory di Azure AD. Azure AD Domain Services, di conseguenza, non funziona con questi tipi di directory di Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Il dominio gestito è disponibile in una sola rete virtuale alla volta. È comunque possibile configurare la connettività tra più reti virtuali per esporre Servizi di dominio Azure AD ad altre reti virtuali. Vedere come [connettere più reti virtuali in Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>È possibile abilitare Servizi di dominio Azure AD con PowerShell?
Sì. Per informazioni su [come abilitare Azure AD Domain Services con PowerShell](active-directory-ds-enable-using-powershell.md), vedere il relativo articolo.

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>È possibile abilitare Azure Active Directory Domain Services con un modello di Resource Manager?
Sì. Per informazioni su [come abilitare Azure AD Domain Services con PowerShell](active-directory-ds-enable-using-powershell.md), vedere il relativo articolo.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?
di serie Il dominio fornito da Servizi di dominio Azure Active Directory è un dominio gestito. Non è necessario eseguire il provisioning, configurare o gestire in altro modo i controller di dominio per questo dominio. Queste attività di gestione vengono fornite come servizio da Microsoft. Non sarà quindi possibile aggiungere altri controller di dominio, di lettura-scrittura o sola lettura, per il dominio gestito.

## <a name="administration-and-operations"></a>Amministrazione e gestione
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?
di serie Non si dispone delle autorizzazioni necessarie per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. I membri del gruppo "AAD DC Administrators" possono occuparsi del dominio gestito usando gli strumenti di amministrazione di Active Directory, ad esempio Centro amministrativo di Active Directory e Active Directory PowerShell. Tramite la funzionalità Strumenti di amministrazione remota del server, questi strumenti vengono installati su un server Windows aggiunto al dominio gestito.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Dopo aver abilitato Servizi di dominio Azure AD, quale account utente è necessario usare per aggiungere i computer a questo dominio?
Gli utenti del gruppo di amministratori "AAD DC Administrators" sono autorizzati ad aggiungere computer a questo dominio. Gli utenti di questo gruppo sono inoltre autorizzati ad accedere da desktop remoti ai computer aggiunti al dominio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?
di serie Non si ottengono privilegi amministrativi nel dominio gestito. Non è possibile avere né privilegi di amministratore di dominio né di amministratore dell'organizzazione per il dominio. Anche i gruppi di amministratori di dominio o dell'organizzazione esistenti nella directory di Azure AD non avranno privilegi di amministratore di dominio/dell'organizzazione per questo dominio.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?
di serie Le appartenenze ai gruppi non possono essere modificate nei domini gestiti da Servizi di dominio Azure AD. Lo stesso vale per gli attributi utente. È tuttavia possibile modificare le appartenenze ai gruppi o gli attributi utente in Azure AD o nel dominio locale. Tali modifiche verranno sincronizzate automaticamente in Servizi di dominio Azure AD.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?
Le modifiche apportate nella directory di Azure AD tramite l'interfaccia utente di Azure Active Directory o PowerShell vengono sincronizzate con il dominio gestito. Il processo di sincronizzazione avviene in background. Dopo che la sincronizzazione iniziale occasionale della directory è stata completata, occorrono in genere circa 20 minuti perché le modifiche apportate in Azure AD diventino visibili nel dominio gestito.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?
di serie Lo schema è amministrato da Microsoft per il dominio gestito. Le estensioni dello schema non sono supportate da Servizi di dominio Azure Active Directory.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>È possibile modificare o aggiungere record DNS nel domino gestito?
Sì. Gli utenti del gruppo "AAD DC Administrators" possiedono privilegi di "amministratore DNS" che consentono loro di modificare i record DNS nel dominio gestito. Per gestire il DNS, questi utenti possono usare la console Gestore DNS in un computer con Windows Server aggiunto al dominio gestito. Per usare questa console, installare sul server Strumenti server DNS, che fa parte della funzionalità facoltativa Strumenti di amministrazione remota del server. Altre informazioni sulle [utilità di amministrazione, monitoraggio e risoluzione dei problemi DNS](https://technet.microsoft.com/library/cc753579.aspx) sono disponibili su TechNet.

## <a name="billing-and-availability"></a>Fatturazione e disponibilità
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Servizi di dominio Azure AD è a pagamento?
Sì. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>È disponibile una versione di valutazione gratuita per il servizio?
Questo servizio è incluso nella versione di valutazione gratuita di Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>È possibile sospendere un dominio gestito da Azure AD Domain Services? 
di serie Dopo aver abilitato un dominio gestito da Azure AD Domain Services, il servizio rimane disponibile nella rete virtuale selezionata fino a quando il dominio gestito non viene disabilitato/eliminato e pertanto non può essere sospeso. La fatturazione continua su base oraria finché non viene eliminato il dominio gestito.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium per usare Azure Active Directory Domain Services?
di serie Azure Active Directory Domain Services è un servizio di Azure con pagamento in base al consumo e non fa parte di EMS. Servizi di dominio AD Azure può essere usato con tutte le edizioni di Azure AD (gratuito, Basic e Premium). La fatturazione avviene su base oraria, a seconda dell'uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>In quale aree di Azure è disponibile il servizio?
Per l'elenco delle aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).
