---
title: Accesso protetto alle VM remote in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come proteggere l'accesso remoto alle macchine virtuali usando server dei criteri di rete e Azure Multi-Factor Authentication con una distribuzione di Servizi Desktop remoto in un dominio gestito da Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 462e0ff8ef8baadf40ba81ed2ae137d8904fbf7a
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722518"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Proteggere l'accesso remoto alle macchine virtuali in Azure Active Directory Domain Services

Per proteggere l'accesso remoto alle macchine virtuali (VM) in esecuzione in un dominio gestito Azure Active Directory Domain Services (Azure AD DS), è possibile utilizzare Servizi Desktop remoto (RDS) e server dei criteri di rete. Azure AD DS autentica gli utenti durante la richiesta di accesso tramite l'ambiente RDS. Per una maggiore sicurezza, è possibile integrare Azure Multi-Factor Authentication per fornire una richiesta di autenticazione aggiuntiva durante gli eventi di accesso. Azure Multi-Factor Authentication usa un'estensione per NPS per fornire questa funzionalità.

> [!IMPORTANT]
> Per connettersi in modo sicuro alle VM in un dominio gestito di Azure AD DS, è consigliabile usare Azure Bastion, un servizio PaaS completamente gestito dalla piattaforma di cui si esegue il provisioning all'interno della rete virtuale. Un host Bastion fornisce connettività di Remote Desktop Protocol (RDP) protetta e senza problemi alle VM direttamente nella portale di Azure tramite SSL. Quando ci si connette tramite un host Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico e non è necessario usare i gruppi di sicurezza di rete per esporre l'accesso a RDP sulla porta TCP 3389.
>
> Si consiglia vivamente di usare Azure Bastion in tutte le aree in cui è supportato. In aree senza disponibilità di Azure Bastion, seguire i passaggi descritti in questo articolo fino a quando non è disponibile Azure Bastion. Prestare attenzione all'assegnazione di indirizzi IP pubblici alle macchine virtuali Unite a Azure AD DS in cui è consentito tutto il traffico RDP in ingresso.
>
> Per altre informazioni, vedere [che cos'è Azure Bastion?][bastion-overview].

Questo articolo illustra come configurare Servizi Desktop remoto in Azure AD DS e, facoltativamente, come usare l'estensione NPS Multi-Factor Authentication Azure.

![Panoramica di Servizi Desktop remoto (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un dominio gestito di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Subnet dei *carichi di lavoro* creata nella rete virtuale Azure Active Directory Domain Services.
    * Se necessario, [configurare la rete virtuale per un Azure Active Directory Domain Services dominio gestito][configure-azureadds-vnet].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Distribuire e configurare l'ambiente di Desktop remoto

Per iniziare, creare almeno due macchine virtuali di Azure che eseguono Windows Server 2016 o Windows Server 2019. Per la ridondanza e la disponibilità elevata dell'ambiente Desktop remoto (desktop remoto), è possibile aggiungere e bilanciare il carico di altri host in un secondo momento.

Una distribuzione di Servizi Desktop remoto suggerita include le due macchine virtuali seguenti:

* *RDGVM01* : esegue il server Gestore connessione Desktop remoto, il server Accesso Web Desktop remoto e il server Gateway Desktop remoto.
* *RDSHVM01* : esegue il server Host sessione Desktop remoto.

Assicurarsi che le macchine virtuali vengano distribuite in una subnet dei *carichi di lavoro* della rete virtuale Azure AD DS, quindi aggiungere le VM al dominio gestito. Per ulteriori informazioni, vedere come [creare e aggiungere una macchina virtuale Windows Server a un dominio gestito][tutorial-create-join-vm].

La distribuzione dell'ambiente desktop remoto contiene una serie di passaggi. La guida alla distribuzione di desktop remoto esistente può essere usata senza modifiche specifiche da usare in un dominio gestito:

1. Accedere alle macchine virtuali create per l'ambiente desktop remoto con un account che fa parte del gruppo *amministratori di Azure ad DC* , ad esempio *ContosoAdmin*.
1. Per creare e configurare Servizi Desktop remoto, utilizzare la [Guida alla distribuzione dell'ambiente desktop remoto][deploy-remote-desktop]esistente. Distribuire i componenti server desktop remoto nelle macchine virtuali di Azure nel modo desiderato.
    * Specifico per Azure AD DS: quando si configura il servizio licenze Desktop remoto, impostarlo su modalità **per dispositivo** , non **per utente** come indicato nella Guida alla distribuzione.
1. Se si vuole fornire l'accesso tramite un Web browser, [configurare la desktop remoto client Web per gli utenti][rd-web-client].

Con il servizio desktop remoto distribuito nel dominio gestito, è possibile gestire e usare il servizio come si farebbe con un dominio di servizi di dominio Active Directory locale.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Distribuire e configurare NPS e l'estensione NPS di Azure per l'autenticazione a più fattori

Se si vuole aumentare la sicurezza dell'esperienza di accesso degli utenti, è possibile integrare facoltativamente l'ambiente desktop remoto con Azure Multi-Factor Authentication. Con questa configurazione, gli utenti ricevono un messaggio di richiesta aggiuntivo durante l'accesso per confermare la propria identità.

Per fornire questa funzionalità, nel proprio ambiente viene installato un server dei criteri di rete (NPS) aggiuntivo con l'estensione server dei criteri di rete Multi-Factor Authentication di Azure. Questa estensione si integra con Azure AD per richiedere e restituire lo stato dei prompt di autenticazione a più fattori.

Gli utenti devono essere [registrati per usare Azure multi-factor authentication][user-mfa-registration], che potrebbe richiedere licenze Azure ad aggiuntive.

Per integrare Multi-Factor Authentication di Azure nell'ambiente di Desktop remoto di Azure AD DS, creare un server NPS e installare l'estensione:

1. Creare una VM Windows Server 2016 o 2019 aggiuntiva, ad esempio *NPSVM01*, connessa a una subnet dei *carichi di lavoro* nella rete virtuale Azure AD DS. Aggiungere la macchina virtuale al dominio gestito.
1. Accedere alla macchina virtuale NPS come account che fa parte del gruppo *amministratori di Azure ad DC* , ad esempio *ContosoAdmin*.
1. Da **Server Manager**selezionare **Aggiungi ruoli e funzionalità**, quindi installare il ruolo *servizi di accesso e criteri di rete* .
1. Usare l'articolo sulle procedure per [installare e configurare l'estensione server dei criteri][nps-extension]di autenticazione a più fattori di Azure.

Con il server NPS e l'estensione NPS di Azure Multi-Factor Authentication installata, completare la sezione successiva per configurarla per l'uso con l'ambiente desktop remoto.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integrare Desktop remoto Gateway e Azure Multi-Factor Authentication

Per integrare l'estensione server dei criteri di rete Multi-Factor Authentication di Azure, usare l'articolo sulle procedure esistenti per [integrare l'infrastruttura di desktop remoto Gateway usando l'estensione NPS (Network Policy Server) e Azure ad][azure-mfa-nps-integration].

Per l'integrazione con un dominio gestito sono necessarie le seguenti opzioni di configurazione aggiuntive:

1. Non [registrare il server NPS in Active Directory][register-nps-ad]. Questo passaggio ha esito negativo in un dominio gestito.
1. Nel [passaggio 4 per configurare i criteri di rete][create-nps-policy], selezionare anche la casella per **ignorare le proprietà di connessione remota dell'account utente**.
1. Se si usa Windows Server 2019 per il server dei criteri di rete e Azure Multi-Factor Authentication Server dei criteri di rete, eseguire il comando seguente per aggiornare il canale sicuro per consentire al server dei criteri di rete di comunicare correttamente:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Agli utenti viene ora richiesto un fattore di autenticazione aggiuntivo al momento dell'accesso, ad esempio un messaggio di testo o una richiesta nell'app Microsoft Authenticator.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul miglioramento della resilienza della distribuzione, vedere [disponibilità elevata Servizi Desktop remoto][rds-high-availability].

Per altre informazioni sulla protezione dell'accesso utente, vedere [come funziona: Azure multi-factor authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability