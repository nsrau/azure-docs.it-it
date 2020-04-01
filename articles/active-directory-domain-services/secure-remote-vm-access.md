---
title: Accesso sicuro alle macchine virtuali remote in Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come proteggere l'accesso remoto alle macchine virtuali usando Server dei criteri di rete e Azure Multi-Factor Authentication con una distribuzione di Servizi Desktop remoto in un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to secure remote access to VMs using Network Policy Server (NPS) and Azure Multi-Factor Authentication with a Remote Desktop Services deployment in an Azure Active Directory Domain Services managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: f2a222c6a382fa2c316211e293547780a8778177
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423145"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Proteggere l'accesso remoto alle macchine virtuali in Servizi di dominio Azure Active DirectorySecure remote access to virtual machines in Azure Active Directory Domain Services

Per proteggere l'accesso remoto alle macchine virtuali (VM) eseguite in un dominio gestito di Servizi di dominio Azure Active Directory, è possibile usare Servizi Desktop remoto e Server dei criteri di rete. Servizi di dominio Active Directory di Azure autentica gli utenti quando richiedono l'accesso tramite l'ambiente RDS. Per una maggiore sicurezza, è possibile integrare Azure Multi-Factor Authentication per fornire una richiesta di autenticazione aggiuntiva durante gli eventi di accesso. Azure Multi-Factor Authentication usa un'estensione per Server dei criteri di rete per fornire questa funzionalità.

> [!IMPORTANT]
> Il modo consigliato per connettersi in modo sicuro alle macchine virtuali in un dominio gestito di Azure AD DS consiste nell'usare Azure Bastion, un servizio PaaS completamente gestito dalla piattaforma di cui si esegue il provisioning all'interno della rete virtuale. Un host bastion fornisce connettività Remote Desktop Protocol (RDP) sicura e senza soluzione di continuità alle macchine virtuali direttamente nel portale di Azure tramite SSL. Quando ci si connette tramite un host bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico e non è necessario usare i gruppi di sicurezza di rete per esporre l'accesso a RDP sulla porta TCP 3389.
>
> È consigliabile usare Azure Bastion in tutte le aree in cui è supportato. Nelle aree senza disponibilità di Bastione di Azure seguire i passaggi descritti in questo articolo finché non è disponibile Azure Bastion.In regions without Azure Bastion availability, follow the steps detailed in this article until Azure Bastion is available. Prestare attenzione all'assegnazione di indirizzi IP pubblici alle macchine virtuali aggiunte a Servizi di dominio Active Directory di Azure in cui è consentito tutto il traffico RDP in ingresso.
>
> Per altre informazioni, vedere [Che cos'è Azure Bastion?][bastion-overview].

Questo articolo illustra come configurare Servizi Desktop remoto in Azure AD DS e, facoltativamente, usare l'estensione Server dei criteri di rete di Azure Multi-Factor Authentication.This article shows you how to configure RDS in Azure AD DS and optionally use the Azure Multi-Factor Authentication NPS extension.

![Panoramica di Servizi Desktop remoto](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessarie le risorse seguenti:To complete this article, you need the following resources:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Subnet dei carichi di lavoro creata nella rete virtuale di Servizi di dominio Azure Active Directory.A *workloads* subnet created in your Azure Active Directory Domain Services virtual network.
    * Se necessario, [configurare la rete virtuale per un dominio gestito][configure-azureadds-vnet]di Servizi di dominio Azure Active Directory .
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Distribuire e configurare l'ambiente Desktop remoto

Per iniziare, creare un minimo di due macchine virtuali di Azure che eseguono Windows Server 2016 o Windows Server 2019.To get started, create a minimum of two Azure VMs that run Windows Server 2016 or Windows Server 2019. Per la ridondanza e la disponibilità elevata dell'ambiente Desktop remoto, è possibile aggiungere e bilanciare il carico di host aggiuntivi in un secondo momento.

Una distribuzione di SERVIZI Desktop remoto suggerita include le due macchine virtuali seguenti:A suggested RDS deployment includes the following two VMs:

* *RDGVM01* - Esegue il server Gestore connessione Desktop remoto, il server Accesso Web Desktop remoto e il server Gateway Desktop remoto.
* *RDSHVM01* - Esegue il server Host sessione Desktop remoto.

Assicurarsi che le macchine virtuali vengano distribuite in una subnet dei carichi di lavoro della rete virtuale di Azure AD DS, quindi aggiungere le macchine virtuali al dominio gestito di Servizi di dominio Active Directory di Azure.Make sure that VMs are deployed into a *workloads* subnet of your Azure AD DS virtual network, then join the VMs to Azure AD DS managed domain. Per altre informazioni, vedere come creare e aggiungere una macchina virtuale di Windows Server a un dominio gestito di Azure AD DS.For more information, see how to [create and join a Windows Server VM to an Azure AD DS managed domain][tutorial-create-join-vm].

La distribuzione dell'ambiente Desktop remoto contiene una serie di passaggi. La guida alla distribuzione di Servizi Desktop remoto esistente può essere usata senza modifiche specifiche da usare in un dominio gestito di Azure AD DS:The existing RD deployment guide can be used without any specific changes to use in an Azure AD DS managed domain:

1. Accedere alle macchine virtuali create per l'ambiente Desktop remoto con un account che fa parte del gruppo *Amministratori controller di dominio di Azure AD,* ad esempio *contosoadmin*.
1. Per creare e configurare Servizi Desktop remoto, utilizzare la [guida alla distribuzione dell'ambiente Desktop remoto][deploy-remote-desktop]esistente. Distribuire i componenti server Desktop remoto tra le macchine virtuali di Azure come desiderato.
1. Se si desidera fornire l'accesso tramite un Web browser, [configurare il client Web Desktop remoto per gli utenti.][rd-web-client]

Con Servizi desktop remoto distribuito nel dominio gestito di Servizi di dominio Active Directory di Azure, è possibile gestire e usare il servizio come per un dominio di Servizi di dominio Active Directory locale.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Distribuire e configurare Server dei criteri di rete e l'estensione Server dei criteri di rete di Azure MFADeploy and configure NPS and the Azure MFA NPS extension

Se si vuole aumentare la sicurezza dell'esperienza di accesso utente, è possibile integrare facoltativamente l'ambiente Di Desktop remoto con Azure Multi-Factor Authentication.If you want to increase the security of the user sign-in experience, you can optionally integrate the RD environment with Azure Multi-Factor Authentication. Con questa configurazione, gli utenti ricevono un prompt aggiuntivo durante l'accesso per confermare la propria identità.

Per fornire questa funzionalità, nell'ambiente dell'ambiente viene installato un server di criteri di rete aggiuntivo insieme all'estensione Server dei criteri di rete di Azure Multi-Factor Authentication.To provide this capability, an additional Network Policy Server (NPS) is installed in your environment along with the Azure Multi-Factor Authentication NPS extension. Questa estensione si integra con Azure AD per richiedere e restituire lo stato delle richieste di autenticazione a più fattori.

Gli utenti devono essere [registrati per usare Azure Multi-Factor Authentication][user-mfa-registration], che potrebbe richiedere licenze di Azure AD aggiuntive.

Per integrare Azure Multi-Factor Authentication nell'ambiente Desktop remoto di Servizi di dominio Active Directory di Azure AD, creare un server dei criteri di rete e installare l'estensione:To integrate Azure Multi-Factor Authentication into your Azure AD DS Remote Desktop environment, create an NPS Server and install the extension:

1. Creare una macchina virtuale di Windows Server 2016 o 2019 aggiuntiva, ad esempio *NPSVM01,* connessa a una subnet dei carichi di lavoro nella rete virtuale di Servizi di dominio Active Directory di Azure.Create an additional Windows Server 2016 or 2019 VM, such as NPSVM01 , that's connected to a *workloads* subnet in your Azure AD DS virtual network. Aggiungere la macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure.Join the VM to the Azure AD DS managed domain.
1. Accedere alla macchina virtuale server dei criteri di rete come account che fa parte del gruppo *Amministratori controller di dominio di Azure AD,* ad esempio *contosoadmin*.
1. In **Server Manager**selezionare Aggiungi ruoli e **funzionalità**, quindi installare il ruolo Servizi di accesso e criteri di *rete.*
1. Usare l'articolo delle procedure esistenti per [installare e configurare l'estensione Server dei criteri][nps-extension]di rete di Azure MFA.

Con il server dei criteri di rete e l'estensione dei criteri di rete di Azure Multi-Factor Authentication installati, completare la sezione successiva per configurarlo per l'uso con l'ambiente Desktop remoto.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integrare Gateway Desktop remoto e Azure Multi-Factor Authentication

Per integrare l'estensione Dei criteri di rete di Azure Multi-Factor Authentication, usare l'articolo delle procedure esistenti per [integrare l'infrastruttura di Gateway Desktop remoto usando l'estensione Server dei criteri di rete (NPS) e Azure AD.][azure-mfa-nps-integration]

Per l'integrazione con un dominio gestito di Servizi di dominio Active Directory di Azure sono necessarie le opzioni di configurazione aggiuntive seguenti:The following additional configuration options are needed to integrate with an Azure AD DS managed domain:

1. Non [registrare il server dei criteri di rete in Active Directory.][register-nps-ad] Questo passaggio ha esito negativo in un dominio gestito di Servizi di dominio Active Directory di Azure.This step fails in an Azure AD DS managed domain.
1. Nel [passaggio 4 per configurare][create-nps-policy]i criteri di rete selezionare anche la casella Ignora proprietà di **connessione accesso utenti**.
1. Se si usa Windows Server 2019 per il server dei criteri di rete e l'estensione Di MaeS di Azure Multi-Factor Authentication, eseguire il comando seguente per aggiornare il canale sicuro per consentire al server dei criteri di rete di comunicare correttamente:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Agli utenti viene ora richiesto un fattore di autenticazione aggiuntivo all'accesso, ad esempio un messaggio di testo o un prompt nell'app Microsoft Authenticator.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul miglioramento della resilienza della distribuzione, vedere [Servizi Desktop remoto - Disponibilità elevata][rds-high-availability].

Per altre informazioni sulla protezione dell'accesso utente, vedere Come funziona: Azure Multi-Factor Authentication.For more information about securing user sign-in, [see How it works: Azure Multi-Factor Authentication][concepts-mfa].

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
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
