---
title: Creare un'unità organizzativa (OU) in Servizi di dominio Azure AD . Documenti Microsoft'
description: Informazioni su come creare e gestire un'unità organizzativa personalizzata in un dominio gestito di Servizi di dominio Azure AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 61f951c0dd6561fc8d5a5de6b80e3759fd42eb78
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655550"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Creare un'unità organizzativa (OU) in un dominio gestito di Servizi di dominio Azure ADCreate an Organizational Unit (OU) in an Azure AD Domain Services managed domain

Le unità organizzative in Servizi di dominio Active Directory consentono di raggruppare logicamente oggetti quali account utente, account di servizio o account computer. È quindi possibile assegnare gli amministratori a specifiche aree di gestione delle com/o e applicare criteri di gruppo per applicare le impostazioni di configurazione di destinazione.

Azure AD DS managed domains include the following two built-in OUs:

* *Computer AADDC:* contiene oggetti computer per tutti i computer aggiunti al dominio gestito.
* *Utenti AADDC:* include utenti e gruppi sincronizzati dal tenant di Azure AD.

Quando si creano ed eseguono carichi di lavoro che usano Servizi di dominio Active Directory di Azure, potrebbe essere necessario creare account di servizio per le applicazioni per autenticarsi. Per organizzare questi account di servizio, spesso si crea un'unità organizzativa personalizzata nel dominio gestito di Servizi di dominio Active Directory di Azure e quindi si creano account di servizio all'interno di tale unità organizzativa.

In a hybrid environment, OUs created in an on-premises AD DS environment aren't synchronized to Azure AD DS. I domini gestiti di Servizi di dominio Active Directory di Azure usano una struttura di unità organizzative semplice. Tutti gli account utente e i gruppi vengono archiviati nel contenitore *Utenti AADDC,* nonostante siano stati sincronizzati da domini o foreste locali diversi, anche se è stata configurata una struttura gerarchica di unità organizzative.

Questo articolo illustra come creare un'unità organizzativa nel dominio gestito di Servizi di dominio Active Directory di Azure.This article shows you how to create an OU in your Azure AD DS managed domain.

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

## <a name="custom-ou-considerations-and-limitations"></a>Considerazioni e limitazioni relative alle unità organizzative personalizzate

Quando si creano opzioni utente personalizzate in un dominio gestito di Azure AD DS, si ottiene una maggiore flessibilità di gestione per la gestione degli utenti e l'applicazione dei criteri di gruppo. Rispetto a un ambiente di Servizi di dominio Active Directory locale, esistono alcune limitazioni e considerazioni durante la creazione e la gestione di una struttura di unità organizzative personalizzata in Servizi di dominio Active Directory di Azure:Compared to an on-premises AD DS environment, there are some limitations and considerations when creating and managing a custom OU structure in Azure AD DS:

* Per creare un'ora di rete personalizzata, gli utenti devono essere membri del gruppo *AAD DC Administrators.*
* A un utente che crea un'unità organizzativa personalizzata vengono concessi privilegi amministrativi (controllo completo) su tale unità organizzativa ed è il proprietario della risorsa.
    * Per impostazione predefinita, il gruppo *AAD DC Administrators* ha anche il controllo completo dell'unità organizzativa personalizzata.
* Viene creata un'unità organizzativa predefinita per *gli utenti AADDC* che contiene tutti gli account utente sincronizzati dal tenant di Azure AD.
    * Non è possibile spostare utenti o gruppi dall'unità organizzativa *Utenti AADDC* a unità organizzative personalizzate create. Solo gli account utente o le risorse create nel dominio gestito di Azure AD DS possono essere spostati in unità di dominio personalizzate.
* Gli account utente, i gruppi, gli account di servizio e gli oggetti computer creati nelle unità di rete personalizzate non sono disponibili nel tenant di Azure AD.
    * Questi oggetti non vengono visualizzati tramite l'API Microsoft Graph o nell'interfaccia utente di Azure AD. sono disponibili solo nel dominio gestito di Servizi di dominio Active Directory di Azure.They're only available in your Azure AD DS managed domain.

## <a name="create-a-custom-ou"></a>Creare un'unità organizzativa personalizzataCreate a custom OU

Per creare un'unità organizzativa personalizzata, utilizzare gli strumenti di amministrazione di Active Directory da una macchina virtuale aggiunta a un dominio. Il Centro di amministrazione di Active Directory consente di visualizzare, modificare e creare risorse in un dominio gestito di Azure AD DS, incluse le unità unità radio.

> [!NOTE]
> Per creare un'unità organizzativa personalizzata in un dominio gestito di Servizi di dominio Active Directory di Azure, è necessario accedere a un account utente membro del gruppo *AAD DC Administrators.*

1. Accedere alla macchina virtuale di gestione. Per la procedura di connessione tramite il portale di Azure, vedere [Connettersi a una macchina virtuale][connect-windows-server-vm]di Windows Server.For steps on how to connect using the Azure portal, see Connect to a Windows Server VM .
1. Nella schermata Start selezionare **Strumenti di amministrazione.** Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per creare una macchina virtuale di [gestione.][tutorial-create-management-vm]
1. Per creare e gestire unità utente, selezionare **Centro di amministrazione** di Active Directory dall'elenco degli strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito di Azure AD DS, ad esempio *aaddscontoso.com*. Viene visualizzato un elenco di unità unità ora e risorse esistenti:

    ![Selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel Centro di amministrazione di Active DirectorySelect your Azure AD DS managed domain in the Active Directory Administrative Center](./media/create-ou/create-ou-adac-overview.png)

1. Il riquadro **Attività** viene visualizzato sul lato destro del Centro di amministrazione di Active Directory. Nel dominio, ad esempio *aaddscontoso.com*, selezionare **Nuova unità organizzativa >**.

    ![Selezionare l'opzione per creare una nuova unità organizzativa nel Centro di amministrazione di Active Directory](./media/create-ou/create-ou-adac-new-ou.png)

1. Nella finestra di dialogo **Crea unità organizzativa** specificare un **Nome** per la nuova unità organizzativa, ad esempio *MyCustomOu*. Fornire una breve descrizione per l'unità organizzativa, ad esempio *Unità organizzativa personalizzata per gli account di servizio*. Se lo si desidera, è anche possibile impostare il campo **Gestito da** per l'unità organizzativa. Per creare l'unità organizzativa personalizzata, selezionare **OK**.

    ![Creare un'unità organizzativa personalizzata dal Centro di amministrazione di Active Directory](./media/create-ou/create-ou-dialog.png)

1. Tornando al Centro di amministrazione di Active Directory, l'unità organizzativa personalizzata è ora elencata ed è disponibile per l'utilizzo:

    ![OU personalizzata disponibile per l'utilizzo nel Centro di amministrazione di Active Directory](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso degli strumenti di amministrazione o sulla creazione e l'uso degli account di servizio, vedere gli articoli seguenti:For more information on using the administrative tools or creating and using service accounts, see the following articles:

* [Centro di amministrazione di Active Directory: Introduzione](https://technet.microsoft.com/library/dd560651.aspx)
* [Guida dettagliata agli account del servizio gestiti](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
