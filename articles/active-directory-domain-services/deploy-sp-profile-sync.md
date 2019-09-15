---
title: Abilitare il servizio profili utente di SharePoint con Azure AD DS | Microsoft Docs
description: Informazioni su come configurare un dominio gestito Azure Active Directory Domain Services per supportare la sincronizzazione dei profili per SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: iainfou
ms.openlocfilehash: 90d728ceee0b9a4ed5e5e33805de9358aca6530c
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996317"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Configurare Azure Active Directory Domain Services per supportare la sincronizzazione dei profili utente per SharePoint Server

SharePoint Server include un servizio per sincronizzare i profili utente. Questa funzionalità consente di archiviare i profili utente in una posizione centrale e di accedervi tra più siti e farm di SharePoint. Per configurare il servizio profili utente di SharePoint Server, è necessario concedere le autorizzazioni appropriate in un dominio gestito di Azure Active Directory Domain Services (Azure AD DS). Per ulteriori informazioni, vedere [sincronizzazione dei profili utente in SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

Questo articolo illustra come configurare Azure AD DS per consentire il servizio di sincronizzazione dei profili utente di SharePoint Server.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.
* Un account del servizio SharePoint per il servizio di sincronizzazione dei profili utente.
    * Se necessario, vedere [pianificare gli account amministrativi e di servizio in SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Panoramica degli account di servizio

In un dominio gestito Azure AD DS, un gruppo di sicurezza denominato **account del servizio AAD DC** esiste come parte dell'unità organizzativa (OU) *degli utenti* . Ai membri di questo gruppo di sicurezza vengono delegati i privilegi seguenti:

- **Replicare i privilegi delle modifiche della directory** nella DSE radice.
- **Replicare i privilegi delle modifiche della directory** nel contesto`cn=configuration` dei nomi di *configurazione* (contenitore).

Il gruppo di sicurezza **account del servizio DC di AAD** è anche un membro del gruppo predefinito **Accesso compatibile precedente a Windows 2000**.

Quando viene aggiunto a questo gruppo di sicurezza, all'account del servizio per il servizio di sincronizzazione dei profili utente del server SharePoint vengono concessi i privilegi necessari per il corretto funzionamento.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Abilita il supporto per la sincronizzazione dei profili utente di SharePoint Server

L'account del servizio per SharePoint Server necessita di privilegi adeguati per replicare le modifiche apportate alla directory e consentire il corretto funzionamento della sincronizzazione dei profili utente di SharePoint Server. Per fornire questi privilegi, aggiungere l'account del servizio usato per la sincronizzazione dei profili utente di SharePoint al gruppo **AAD DC Service Accounts** .

Dalla macchina virtuale di gestione di Azure AD DS completare i passaggi seguenti:

> [!NOTE]
> Per modificare l'appartenenza a un gruppo in un dominio gestito di Azure AD DS, è necessario avere eseguito l'accesso a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
1. Per gestire l'appartenenza a un gruppo, selezionare **centro di amministrazione di Active Directory** dall'elenco di strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito di Azure AD DS, ad esempio *contoso.com*. Viene visualizzato un elenco delle unità organizzative e delle risorse esistenti.
1. Selezionare la ou **utenti** , quindi scegliere il gruppo di sicurezza *account del servizio DC di AAD* .
1. Selezionare **membri**, quindi scegliere **Aggiungi.**
1. Immettere il nome dell'account del servizio SharePoint, quindi fare clic su **OK**. Nell'esempio seguente, l'account del servizio SharePoint è denominato *SPAdmin*:

    ![Aggiungere l'account del servizio SharePoint al gruppo di sicurezza account del servizio DC di AAD](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [concedere autorizzazioni Active Directory Domain Services per la sincronizzazione dei profili in SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
