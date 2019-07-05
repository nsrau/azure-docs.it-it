---
title: Domande frequenti su Azure Active Directory Domain Services | Microsoft Docs
description: Domande frequenti su Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 10131ad306a8a24cb5835e55a02f1b502b20bea4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473377"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Domande frequenti (FAQ)
Questa pagina include le risposte a domande frequenti relative a Servizi di dominio Azure AD. Controllarla costantemente per eventuali aggiornamenti.

## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi
Per soluzioni relative ai problemi comuni di configurazione o amministrazione di Azure AD Domain Services, vedere la [Guida alla risoluzione dei problemi](troubleshoot.md).

## <a name="configuration"></a>Configurazione
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>È possibile creare più domini gestiti per una singola directory di Azure AD?
No. È possibile creare solo un singolo dominio gestito da Active Directory Domain Services per una singola directory di Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?
Sì. Azure AD Domain Services può essere abilitato in una rete virtuale di Azure Resource Manager. Le reti virtuali classiche di Azure non sono più supportate per la creazione di nuovi domini gestiti.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale di Resource Manager?
No, per il momento. In futuro verrà distribuito da Microsoft un meccanismo per eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale di Gestione risorse.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?
Sì. Per informazioni su come è possibile abilitare [Azure AD Domain Services nelle sottoscrizioni CSP di Azure](csp.md), vedere il relativo articolo.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>È possibile abilitare Azure AD Domain Services in una directory di Azure AD federata? Gli hash delle password non vengono sincronizzati in Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?
No. Azure AD Domain Services richiede l'accesso agli hash delle password degli account utente per autenticare gli utenti tramite NTLM o Kerberos. In una directory federata gli hash delle password non vengono memorizzati nella directory di Azure AD. Azure AD Domain Services, di conseguenza, non funziona con questi tipi di directory di Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Il dominio gestito è disponibile in una sola rete virtuale alla volta. È comunque possibile configurare la connettività tra più reti virtuali per esporre Servizi di dominio Azure AD ad altre reti virtuali. Vedere come [connettere più reti virtuali in Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>È possibile abilitare Servizi di dominio Azure AD con PowerShell?
Sì. Per informazioni su [come abilitare Azure AD Domain Services con PowerShell](powershell-create-instance.md), vedere il relativo articolo.

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>È possibile abilitare Azure Active Directory Domain Services con un modello di Gestione risorse?
No, attualmente non è possibile abilitare Azure AD Domain Services usando un modello. Anziché usare PowerShell, vedere [come abilitare Azure AD Domain Services con PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?
No. Il dominio fornito da Servizi di dominio Azure Active Directory è un dominio gestito. Non è necessario eseguire il provisioning, configurare o gestire in altro modo i controller di dominio per questo dominio. Queste attività di gestione vengono fornite come servizio da Microsoft. Non sarà quindi possibile aggiungere altri controller di dominio, di lettura-scrittura o sola lettura, per il dominio gestito.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Gli utenti guest invitati a una directory possono usare Azure AD Domain Services?
No. Gli utenti guest invitati a una directory di Azure AD tramite la procedura di invito [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) vengono sincronizzati nel dominio gestito di Azure AD Domain Services. Tuttavia, le password di questi utenti non vengono archiviate nella directory di Azure AD. Azure AD Domain Service non ha pertanto alcuna possibilità di sincronizzare gli hash NTLM e Kerberos per questi utenti nel dominio gestito. Di conseguenza, tali utenti non possono eseguire l'accesso né aggiungere computer al dominio gestito.

## <a name="administration-and-operations"></a>Amministrazione e gestione
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?
No. Non si dispone delle autorizzazioni necessarie per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. I membri del gruppo "AAD DC Administrators" possono occuparsi del dominio gestito usando gli strumenti di amministrazione di Active Directory, ad esempio Centro amministrativo di Active Directory e Active Directory PowerShell. Tramite la funzionalità Strumenti di amministrazione remota del server, questi strumenti vengono installati su un server Windows aggiunto al dominio gestito.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Dopo aver abilitato Servizi di dominio Azure AD, quale account utente è necessario usare per aggiungere i computer a questo dominio?
Gli utenti del gruppo di amministratori "AAD DC Administrators" sono autorizzati ad aggiungere computer a questo dominio. Gli utenti di questo gruppo sono inoltre autorizzati ad accedere da desktop remoti ai computer aggiunti al dominio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?
No. Non si ottengono privilegi amministrativi nel dominio gestito. Non è possibile avere né privilegi di amministratore di dominio né di amministratore dell'organizzazione per il dominio. I membri dei gruppi degli amministratori di dominio o dell'organizzazione in Active Directory locale non avranno privilegi di amministratore di dominio/dell'organizzazione per il dominio gestito.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?
No. Le appartenenze ai gruppi non possono essere modificate nei domini gestiti da Servizi di dominio Azure AD. Lo stesso vale per gli attributi utente. È tuttavia possibile modificare le appartenenze ai gruppi o gli attributi utente in Azure AD o nel dominio locale. Tali modifiche verranno sincronizzate automaticamente in Servizi di dominio Azure AD.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?
Le modifiche apportate nella directory di Azure AD tramite l'interfaccia utente di Azure Active Directory o PowerShell vengono sincronizzate con il dominio gestito. Il processo di sincronizzazione avviene in background. Dopo che la sincronizzazione iniziale è stata completata, occorrono in genere circa 20 minuti perché le modifiche apportate in Azure AD diventino visibili nel dominio gestito.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?
No. Lo schema è amministrato da Microsoft per il dominio gestito. Le estensioni dello schema non sono supportate da Servizi di dominio Azure Active Directory.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>È possibile modificare o aggiungere record DNS nel domino gestito?
Sì. Gli utenti del gruppo "AAD DC Administrators" possiedono privilegi di "amministratore DNS" che consentono loro di modificare i record DNS nel dominio gestito. Per gestire il DNS, questi utenti possono usare la console Gestore DNS in un computer con Windows Server aggiunto al dominio gestito. Per usare questa console, installare sul server Strumenti server DNS, che fa parte della funzionalità facoltativa Strumenti di amministrazione remota del server. Altre informazioni sulle [utilità di amministrazione, monitoraggio e risoluzione dei problemi DNS](https://technet.microsoft.com/library/cc753579.aspx) sono disponibili su TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Quali sono i criteri di durata delle password in un dominio gestito?
La durata predefinita di una password in un dominio gestito di Azure AD Domain Services è 90 giorni. Questa durata non è sincronizzata con la durata delle password configurata in Azure AD. Pertanto, possono verificarsi situazioni in cui le password degli utenti scadono nel dominio gestito, ma sono ancora valide in Azure AD. In scenari di questo tipo gli utenti devono cambiare la password in Azure AD e la nuova password verrà sincronizzata nel dominio gestito. Inoltre, gli attributi password-does-not-expire e user-must-change-password-at-next-logon degli account utente non vengono sincronizzati nel dominio gestito.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?
Sì. Dopo 5 tentativi di inserimento di password non valide in 2 minuti per il dominio gestito, l'account utente viene bloccato per 30 minuti. Dopo 30 minuti l'account utente viene sbloccato automaticamente. I tentativi di inserimento di password non valide per il dominio gestito non bloccano l'account utente in Azure AD. L'account utente viene bloccato solo all'interno del dominio gestito di Azure AD Domain Services.

## <a name="billing-and-availability"></a>Fatturazione e disponibilità
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Servizi di dominio Azure AD è a pagamento?
Sì. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>È disponibile una versione di valutazione gratuita per il servizio?
Questo servizio è incluso nella versione di valutazione gratuita di Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>È possibile sospendere un dominio gestito da Azure AD Domain Services? 
No. Dopo aver abilitato un dominio gestito da Azure AD Domain Services, il servizio rimane disponibile nella rete virtuale selezionata fino a quando il dominio gestito non viene disabilitato/eliminato e pertanto non può essere sospeso. La fatturazione continua su base oraria finché non viene eliminato il dominio gestito.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento DR?
No.  Attualmente Azure Active Directory Domain Services non fornisce un modello di distribuzione con ridondanza geografica. È limitato a una singola rete virtuale in un'area di Azure. Se si vogliono usare più aree di Azure, è necessario eseguire i controller di Dominio di Active Directory nelle macchine virtuali IaaS di Azure.  È disponibile il materiale sussidiario sull'architettura [qui](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium per usare Azure Active Directory Domain Services?
No. Azure Active Directory Domain Services è un servizio di Azure con pagamento in base al consumo e non fa parte di EMS. Servizi di dominio AD Azure può essere usato con tutte le edizioni di Azure AD (gratuito, Basic e Premium). La fatturazione avviene su base oraria, a seconda dell'uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>In quale aree di Azure è disponibile il servizio?
Per l'elenco delle aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).
