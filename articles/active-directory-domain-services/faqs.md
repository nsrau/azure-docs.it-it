---
title: Domande frequenti su Servizi di dominio Azure AD Documenti Microsoft
description: Leggere e comprendere alcune delle domande frequenti sulla configurazione, l'amministrazione e la disponibilità per Servizi di dominio Azure Active DirectoryRead and understand some of the frequently asked questions around configuration, administration, and availability for Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 86b68b794928900717bea25623e7eb833c23e86c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655344"
---
# <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

In questa pagina vengono fornite le risposte alle domande frequenti su Servizi di dominio Azure Active Directory.This page answers frequently asked questions about Azure Active Directory Domain Services.

## <a name="configuration"></a>Configurazione

* [È possibile creare più domini gestiti per una singola directory di Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [È possibile abilitare Servizi di dominio Azure AD in una rete virtuale classica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale di Resource Manager?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [È possibile abilitare Servizi di dominio Azure AD in una directory federata di Azure AD? Non sincronizzare gli hashe delle password con Azure AD. È possibile abilitare Servizi di dominio Azure AD per questa directory?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [È possibile abilitare Servizi di dominio Azure AD con PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [È possibile abilitare Servizi di dominio Azure AD usando un modello di Resource Manager?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Gli utenti guest invitati a una directory possono usare Azure AD Domain Services?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [È possibile spostare un dominio gestito di Servizi di dominio Azure AD esistente in una sottoscrizione, un gruppo di risorse, un'area o una rete virtuale diversa?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Servizi di dominio Azure AD include opzioni di disponibilità elevata?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>È possibile creare più domini gestiti per una singola directory di Azure AD?
No. È possibile creare solo un singolo dominio gestito da Active Directory Domain Services per una singola directory di Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>È possibile abilitare Servizi di dominio Azure AD in una rete virtuale classica?
Le reti virtuali classiche non sono supportate per le nuove distribuzioni. I domini gestiti esistenti distribuiti nelle reti virtuali classiche continuano a essere supportati fino al ritiro il 1 marzo 2023. Per le distribuzioni esistenti, è possibile eseguire la migrazione di Servizi di dominio Azure AD dal modello di [rete virtuale classica a Gestione risorse.](migrate-from-classic-vnet.md)

Per ulteriori informazioni, vedere l'avviso ufficiale di [deprecazione](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?
Sì. Azure AD Domain Services può essere abilitato in una rete virtuale di Azure Resource Manager. Le reti virtuali di Azure classiche non sono più disponibili quando si crea un dominio gestito.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una rete virtuale di Resource Manager?
Sì. Per altre informazioni, vedere Eseguire la migrazione di Servizi di dominio Azure AD dal modello di rete virtuale classica a Resource Manager.For more information, see [Migrate Azure AD Domain Services from the Classic virtual network model to Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?
Sì. Per altre informazioni, vedere [come abilitare Servizi di dominio Azure AD nelle sottoscrizioni Di Azure CSP.](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>È possibile abilitare Azure AD Domain Services in una directory di Azure AD federata? Gli hash delle password non vengono sincronizzati in Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?
No. Per autenticare gli utenti tramite NTLM o Kerberos, Servizi di dominio Azure AD deve accedere agli accessi delle password degli account utente. In una directory federata, gli ishe delle password non vengono archiviati nella directory di Azure AD. Pertanto, Servizi di dominio Azure AD non funziona con tali directory di Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Il dominio gestito è disponibile in una sola rete virtuale alla volta. Tuttavia, è possibile configurare la connettività tra più reti virtuali per esporre Servizi di dominio Azure AD ad altre reti virtuali. Per altre informazioni, vedere [come connettere reti virtuali in Azure usando gateway VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) o [peering di rete virtuale.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>È possibile abilitare Servizi di dominio Azure AD con PowerShell?
Sì. Per altre informazioni, vedere [come abilitare Servizi di dominio Azure AD tramite PowerShell.For](powershell-create-instance.md)more information, see how to enable Azure AD Domain Services using PowerShell .

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>È possibile abilitare Azure Active Directory Domain Services con un modello di Gestione risorse?
Sì, è possibile creare un dominio gestito di Servizi di dominio Azure AD usando un modello di Resource Manager.Yes, you can create an Azure AD Domain Services managed domain using a Resource Manager template. Un'entità servizio e un gruppo di Azure AD per l'amministrazione devono essere creati usando il portale di Azure o Azure PowerShell prima della distribuzione del modello. Per altre informazioni, vedere Creare un dominio gestito di Servizi di dominio Active Directory di Azure usando un modello di Azure Resource Manager.For more information, see [Create an Azure AD DS managed domain using an Azure Resource Manager template.](template-create-instance.md) Quando si crea un dominio gestito di Servizi di dominio Azure AD nel portale di Azure, è disponibile anche un'opzione per esportare il modello per l'uso con distribuzioni aggiuntive.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?
No. Il dominio fornito da Servizi di dominio Azure Active Directory è un dominio gestito. Non è necessario eseguire il provisioning, configurare o gestire in altro modo i controller di dominio per questo dominio. Queste attività di gestione vengono fornite come servizio da Microsoft.These management activities are provided as a service by Microsoft. Pertanto, è possibile aggiungere ulteriori controller di dominio (lettura-scrittura o sola lettura) per il dominio gestito.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Gli utenti guest invitati a una directory possono usare Azure AD Domain Services?
No. Gli utenti guest invitati a una directory di Azure AD tramite la procedura di invito [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) vengono sincronizzati nel dominio gestito di Azure AD Domain Services. Tuttavia, le password per questi utenti non vengono archiviate nella directory di Azure AD. Pertanto, Servizi di dominio Azure AD non è in grado di sincronizzare gli abissi NTLM e Kerberos per questi utenti nel dominio gestito. Tali utenti non possono accedere o aggiungere computer al dominio gestito.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>È possibile spostare un dominio gestito di Servizi di dominio Azure AD esistente in una sottoscrizione, un gruppo di risorse, un'area o una rete virtuale diversa?
No. Dopo aver creato un dominio gestito di Servizi di dominio Azure AD, non è possibile spostare l'istanza in un gruppo di risorse diverso, in una rete virtuale, in una sottoscrizione e così via. Quando si distribuisce l'istanza di Servizi di dominio Active Directory di Azure, è necessario selezionare la sottoscrizione, il gruppo di risorse, l'area e la rete virtuale più appropriati.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Servizi di dominio Azure AD include opzioni di disponibilità elevata?

Sì. Ogni dominio gestito di Servizi di dominio Azure AD include due controller di dominio. Non si gestisce o si connette a questi controller di dominio, fanno parte del servizio gestito. Se si distribuisce Servizi di dominio Azure AD in un'area che supporta le zone di disponibilità, i controller di dominio vengono distribuiti tra le zone. Nelle aree che non supportano le zone di disponibilità, i controller di dominio vengono distribuiti tra i set di disponibilità. Non sono disponibili opzioni di configurazione o controllo di gestione su questa distribuzione. Per altre informazioni, vedere Opzioni di disponibilità per le macchine virtuali in Azure.For more information, see [Availability options for virtual machines in Azure.](../virtual-machines/windows/availability.md)

## <a name="administration-and-operations"></a>Amministrazione e operazioni

* [È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ho abilitato Servizi di dominio Azure AD. Quale account utente si utilizza per aggiungere computer a questo dominio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [È possibile modificare o aggiungere record DNS nel domino gestito?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Quali sono i criteri di durata delle password in un dominio gestito?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?
No. Non si dispone delle autorizzazioni per connettersi ai controller di dominio per il dominio gestito tramite Desktop remoto. I membri del gruppo *AAD DC Administrators* possono amministrare il dominio gestito utilizzando gli strumenti di amministrazione di Active Directory, ad esempio l'interfaccia di amministrazione di Active Directory (ADAC) o AD PowerShell. Questi strumenti vengono installati utilizzando la funzionalità Strumenti di *amministrazione remota del server* in un server Windows aggiunto al dominio gestito. Per altre informazioni, vedere Creare una macchina virtuale di [gestione per configurare e amministrare un dominio gestito](tutorial-create-management-vm.md)di Servizi di dominio Azure AD.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ho abilitato Servizi di dominio Azure AD. quale account utente è necessario usare per aggiungere i computer a questo dominio?
Qualsiasi account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure può aggiungere una macchina virtuale. Ai membri del gruppo *AAD DC Administrators* viene concesso l'accesso desktop remoto ai computer aggiunti al dominio gestito.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?
No. Non sono stati concessi privilegi amministrativi per il dominio gestito. *I* privilegi di amministratore di dominio e *amministratore dell'organizzazione* non sono disponibili per l'utilizzo all'interno del dominio. Ai membri dei gruppi di amministratori di dominio o di amministratori dell'organizzazione in Active Directory locale non vengono inoltre concessi privilegi di amministratore di dominio/organizzazione nel dominio gestito.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?
Gli utenti e i gruppi sincronizzati da Azure Active Directory a Servizi di dominio Azure AD non possono essere modificati perché l'origine di origine è Azure Active Directory.Users and groups that are synchronized from Azure Active Directory to Azure AD Domain Services cannot be modified because their source of origin is Azure Active Directory. Qualsiasi utente o gruppo che ha origine nel dominio gestito può essere modificato.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?
Le modifiche apportate nella directory di Azure AD tramite l'interfaccia utente di Azure AD o PowerShell vengono sincronizzate automaticamente con il dominio gestito. Il processo di sincronizzazione avviene in background. Non esiste un periodo di tempo definito per questa sincronizzazione per completare tutte le modifiche agli oggetti.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?
No. Lo schema è amministrato da Microsoft per il dominio gestito. Le estensioni dello schema non sono supportate da Servizi di dominio Azure AD.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>È possibile modificare o aggiungere record DNS nel domino gestito?
Sì. Ai membri del gruppo *AAD DC Administrators* vengono concessi privilegi di *amministratore DNS* per modificare i record DNS nel dominio gestito. Tali utenti possono utilizzare la console di gestione DNS in un computer che esegue Windows Server aggiunto al dominio gestito per gestire DNS. Per utilizzare la console di gestione DNS, installare *Strumenti server DNS*, che fa parte della funzionalità facoltativa Strumenti di amministrazione remota *del* server nel server. Per altre informazioni, vedere [Amministrare DNS in un dominio gestito](manage-dns.md)di Servizi di dominio Azure AD.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Quali sono i criteri di durata delle password in un dominio gestito?
La durata predefinita di una password in un dominio gestito di Azure AD Domain Services è 90 giorni. Questa durata non è sincronizzata con la durata delle password configurata in Azure AD. Pertanto, possono verificarsi situazioni in cui le password degli utenti scadono nel dominio gestito, ma sono ancora valide in Azure AD. In scenari di questo tipo gli utenti devono cambiare la password in Azure AD e la nuova password verrà sincronizzata nel dominio gestito. Inoltre, gli attributi *password-does-not-expire* e *user-must-change-password-at-next-logon* per gli account utente non vengono sincronizzati con il dominio gestito.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?
Sì. Dopo 5 tentativi di inserimento di password non valide in 2 minuti per il dominio gestito, l'account utente viene bloccato per 30 minuti. Dopo 30 minuti l'account utente viene sbloccato automaticamente. I tentativi con password non valida nel dominio gestito non bloccano l'account utente in Azure AD. L'account utente viene bloccato solo all'interno del dominio gestito di Azure AD Domain Services. Per ulteriori informazioni, vedere Criteri di [blocco di password e account nei domini gestiti](password-policy.md).

## <a name="billing-and-availability"></a>Fatturazione e disponibilità

* [Servizi di dominio Azure AD è a pagamento?](#is-azure-ad-domain-services-a-paid-service)
* [È disponibile una versione di valutazione gratuita per il servizio?](#is-there-a-free-trial-for-the-service)
* [È possibile sospendere un dominio gestito da Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento DR?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium per usare Servizi di dominio Azure AD?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In quale aree di Azure è disponibile il servizio?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Servizi di dominio Azure AD è a pagamento?
Sì. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>È disponibile una versione di valutazione gratuita per il servizio?
Azure AD Domain Services is included in the free trial for Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>È possibile sospendere un dominio gestito da Azure AD Domain Services?
No. Dopo aver abilitato un dominio gestito di Servizi di dominio Azure AD, il servizio è disponibile all'interno della rete virtuale selezionata fino a quando non si elimina il dominio gestito. Non c'è modo di mettere in pausa il servizio. La fatturazione continua su base oraria finché non viene eliminato il dominio gestito.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento DR?
No. Servizi di dominio Azure AD non fornisce attualmente un modello di distribuzione con ridondanza geografica. È limitato a una singola rete virtuale in un'area di Azure.It's limited to a single virtual network in an Azure region. Se si vogliono usare più aree di Azure, è necessario eseguire i controller di Dominio di Active Directory nelle macchine virtuali IaaS di Azure. Per istruzioni sull'architettura, vedere Estendere il dominio di Active Directory locale ad Azure.For architecture guidance, see [Extend your on-premises Active Directory domain to Azure.](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium per usare Azure Active Directory Domain Services?
No. Servizi di dominio Azure AD è un servizio di Azure con pagamento in base al pagamento e non fa parte di Servizi di gestione emergenze. Servizi di dominio Azure AD può essere usato con tutte le edizioni di Azure AD (gratuito e Premium). La fatturazione viene esitante su base oraria, a seconda dell'utilizzo.

### <a name="what-azure-regions-is-the-service-available-in"></a>In quale aree di Azure è disponibile il servizio?
Per l'elenco delle aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per soluzioni relative ai problemi comuni di configurazione o amministrazione di Azure AD Domain Services, vedere la [Guida alla risoluzione dei problemi](troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Servizi di dominio Azure AD, vedere [Che cos'è Servizi](overview.md)di dominio Azure Active Directory?

Per iniziare, vedere [Creare e configurare un'istanza](tutorial-create-instance.md)di Servizi di dominio Azure Active Directory.
