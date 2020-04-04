---
title: Abilitare il servizio profili utente di SharePoint con Servizi di dominio Active Directory di Azure. Documenti Microsoft
description: Informazioni su come configurare un dominio gestito di Servizi di dominio Azure Active Directory per supportare la sincronizzazione dei profili per SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: a684a669c491e35b5c6b62dd318b4fe61edeb52b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655391"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Configurare Servizi di dominio Azure Active Directory per supportare la sincronizzazione dei profili utente per SharePoint ServerConfigure Azure Active Directory Domain Services to support user profile synchronization for SharePoint Server

SharePoint Server include un servizio per sincronizzare i profili utente. Questa caratteristica consente di archiviare i profili utente in una posizione centrale e accessibile tra più siti e farm di SharePoint. Per configurare il servizio profili utente di SharePoint Server, è necessario concedere le autorizzazioni appropriate in un dominio gestito di Servizi di dominio Azure Active Directory.To configure the SharePoint Server user profile service, the appropriate permissions must be granted in an Azure Active Directory Domain Services (Azure AD DS) managed domain. Per ulteriori informazioni, vedere [Sincronizzazione dei profili utente in SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

Questo articolo illustra come configurare Servizi di dominio Active Directory di Azure per consentire il servizio di sincronizzazione dei profili utente di SharePoint Server.This article shows you how to configure Azure AD DS to allow the SharePoint Server user profile sync service.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.A Windows Server management VM that is joined to the Azure AD DS managed domain.
    * Se necessario, completare l'esercitazione per creare una macchina virtuale di [gestione.][tutorial-create-management-vm]
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.
* Un account del servizio SharePoint per il servizio di sincronizzazione dei profili utente.
    * Se necessario, vedere [Pianificare gli account amministrativi e][sharepoint-service-account]di servizio in SharePoint Server .

## <a name="service-accounts-overview"></a>Panoramica degli account di servizioService accounts overview

In un dominio gestito di Servizi di dominio Active Directory di Azure esiste un gruppo di sicurezza denominato Account di **servizio controller di dominio AAD** come parte dell'unità organizzativa *Utenti.* Ai membri di questo gruppo di sicurezza vengono delegati i privilegi seguenti:

- **Privilegio Replica modifiche directory** nel DSE radice.
- **Privilegio Replica modifiche directory** nel`cn=configuration` contesto dei nomi di *configurazione* (contenitore).

Il gruppo di protezione **AAD DC Service Accounts** è anche un membro del gruppo predefinito **Accesso compatibile precedente a Windows 2000**.

Quando viene aggiunto a questo gruppo di sicurezza, all'account del servizio per il servizio di sincronizzazione dei profili utente di SharePoint Server vengono concessi i privilegi necessari per il corretto funzionamento.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Abilitare il supporto per la sincronizzazione dei profili utente di SharePoint ServerEnable support for SharePoint Server user profile sync

L'account del servizio per SharePoint Server necessita di privilegi adeguati per replicare le modifiche nella directory e consentire il corretto funzionamento della sincronizzazione dei profili utente di SharePoint Server. Per fornire questi privilegi, aggiungere l'account di servizio utilizzato per la sincronizzazione dei profili utente di SharePoint al gruppo Account di **servizio controller di dominio di AAD.**

Dalla macchina virtuale di gestione di Azure AD DS completare i passaggi seguenti:From your Azure AD DS management VM, complete the following steps:

> [!NOTE]
> Per modificare l'appartenenza al gruppo in un dominio gestito di Servizi di dominio Active Directory di Azure, è necessario accedere a un account utente membro del gruppo *AAD DC Administrators.*

1. Nella schermata Start selezionare **Strumenti di amministrazione.** Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per creare una macchina virtuale di [gestione.][tutorial-create-management-vm]
1. Per gestire l'appartenenza ai gruppi, selezionare **Centro di amministrazione** di Active Directory dall'elenco degli strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito di Azure AD DS, ad esempio *aaddscontoso.com*. Viene visualizzato un elenco di unità unità di gestione e risorse esistenti.
1. Selezionare l'unità organizzativa **Utenti,** quindi scegliere il gruppo di protezione *AAD DC Service Accounts.*
1. Selezionare **Membri**, quindi scegliere **Aggiungi...**.
1. Immettere il nome dell'account del servizio SharePoint, quindi scegliere **OK**. Nell'esempio seguente l'account del servizio SharePoint è denominato *spadmin*:

    ![Aggiungere l'account del servizio SharePoint al gruppo di sicurezza AAD DC Service Accounts](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere Concedere le autorizzazioni di Servizi di dominio Active Directory per la [sincronizzazione dei profili in SharePoint ServerFor more information, see Grant Active Directory Domain Services permissions for profile synchronization in SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
