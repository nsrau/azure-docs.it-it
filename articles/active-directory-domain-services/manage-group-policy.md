---
title: Creare e gestire criteri di gruppo in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come modificare gli oggetti Criteri di gruppo (GPO) incorporati e creare criteri personalizzati in un Azure Active Directory Domain Services dominio gestito.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 32020341c7ac3bf33082ba365f053a613dcdffc2
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705090"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Amministrare Criteri di gruppo in un dominio gestito Azure AD Domain Services

Le impostazioni per gli oggetti utente e computer in Azure Active Directory Domain Services (Azure AD DS) vengono spesso gestite utilizzando oggetti Criteri di gruppo (GPO). Azure AD DS include oggetti Criteri di gruppo predefiniti per i contenitori *utenti aaddc Computers* e *computer aaddc Computers* . È possibile personalizzare questi oggetti Criteri di gruppo predefiniti per configurare Criteri di gruppo in base alle esigenze dell'ambiente in uso. I membri del gruppo *Azure ad DC Administrators* dispongono di criteri di gruppo privilegi di amministrazione nel dominio di Azure AD DS e possono inoltre creare oggetti Criteri di gruppo e unità organizzative (OU) personalizzati. Per altre informazioni sulle Criteri di gruppo e sul suo funzionamento, vedere Panoramica di [criteri di gruppo][group-policy-overview].

In un ambiente ibrido, i criteri di gruppo configurati in un ambiente di servizi di dominio Active Directory locale non vengono sincronizzati con Azure AD DS. Per definire le impostazioni di configurazione per utenti o computer in Azure AD DS, modificare uno degli oggetti Criteri di gruppo predefiniti o creare un oggetto Criteri di gruppo personalizzato.

Questo articolo illustra come installare gli strumenti di gestione di Criteri di gruppo, quindi modificare gli oggetti Criteri di gruppo predefiniti e creare oggetti Criteri di gruppo personalizzati.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito][create-join-windows-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

> [!NOTE]
> Poiché non è possibile [accedere ai controller di dominio in Azure AD DS](faqs.md#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop), non è possibile creare e usare un archivio centrale per i modelli amministrativi di criteri di gruppo in un dominio gestito. [SYSVOL non è incluso nella sincronizzazione dei Azure ad Connect locali](synchronization.md#what-isnt-synchronized-to-azure-ad-ds), quindi non è possibile creare un archivio centrale locale e sincronizzarlo per Azure AD DS tramite Azure ad.

## <a name="install-group-policy-management-tools"></a>Installare gli strumenti di gestione di Criteri di gruppo

Per creare e configurare Criteri di gruppo oggetto (GPO), è necessario installare gli strumenti di gestione di Criteri di gruppo. Questi strumenti possono essere installati come funzionalità di Windows Server. Per ulteriori informazioni su come installare gli strumenti di amministrazione in un client Windows, vedere Install [strumenti di amministrazione remota del server (amministrazione remota][install-rsat]del server).

1. Accedere alla macchina virtuale di gestione. Per i passaggi relativi alla modalità di connessione tramite la portale di Azure, vedere [connettersi a una macchina virtuale Windows Server][connect-windows-server-vm].
1. Quando si accede alla macchina virtuale, per impostazione predefinita dovrebbe essere visualizzata la finestra di **Server Manager**. In caso contrario, scegliere **Server Manager** dal menu **Start**.
1. Fare clic su *Aggiungi ruoli e funzionalità* nel riquadro **Dashboard** della finestra **Server Manager**.
1. Nella pagina **Prima di iniziare** dell'*aggiunta guidata ruoli e funzionalità* selezionare **Avanti**.
1. Per *Tipo di installazione* lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e selezionare **Avanti**.
1. Nella pagina **Selezione server** scegliere la macchina virtuale corrente dal pool di server, ad esempio *MyVM.aadds.contoso.com*, quindi fare clic su **Avanti**.
1. Nella pagina **Ruoli del server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità**, selezionare **Gestione Criteri di gruppo**.

    ![Installare ' Criteri di gruppo Management ' dalla pagina funzionalità](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti di gestione Criteri di gruppo potrebbe richiedere un minuto o due.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Aprire il Console Gestione Criteri di gruppo e modificare un oggetto

Gli oggetti Criteri di gruppo (GPO) predefiniti sono disponibili per utenti e computer in un dominio gestito Azure AD DS. Con la funzionalità di gestione Criteri di gruppo installata nella sezione precedente, è ora di visualizzare e modificare un oggetto Criteri di gruppo esistente. Nella sezione successiva viene creato un oggetto Criteri di gruppo personalizzato.

> [!NOTE]
> Per amministrare criteri di gruppo in un dominio gestito di Azure AD DS, è necessario essere connessi a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco degli strumenti di gestione disponibili, tra cui **criteri di gruppo Management** installato nella sezione precedente.
1. Per aprire la Console Gestione Criteri di gruppo (GPMC), scegliere **gestione criteri di gruppo**.

    ![Il Console Gestione Criteri di gruppo viene aperto pronto per la modifica degli oggetti Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Esistono due oggetti Criteri di gruppo predefiniti (GPO) in un dominio gestito Azure AD DS, uno per il contenitore *computer aaddc Computers* e uno per il contenitore *utenti aaddc Computers* . È possibile personalizzare questi oggetti Criteri di gruppo per configurare i criteri di gruppo in base alle esigenze all'interno del dominio gestito di Azure AD DS.

1. Nella console di **gestione di criteri di gruppo** espandere il nodo **foresta: aadds.contoso.com** . Successivamente, espandere i nodi **domini** .

    Per i *computer aaddc Computers* e *gli utenti aaddc Computers*sono disponibili due contenitori predefiniti. A ognuno di questi contenitori è applicato un oggetto Criteri di gruppo predefinito.

    ![Oggetti Criteri di gruppo predefiniti applicati ai contenitori predefiniti ' AADDC computers Computers ' è AADDC computers users '](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Questi oggetti Criteri di gruppo predefiniti possono essere personalizzati per configurare criteri di gruppo specifici nel dominio gestito di Azure AD DS. Fare clic con il pulsante destro del mouse su uno degli oggetti Criteri di gruppo, ad esempio *Aaddc computers Computers*, quindi scegliere **modifica.**

    ![Scegliere l'opzione per modificare uno degli oggetti Criteri di gruppo predefiniti](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Lo strumento Editor Gestione Criteri di gruppo si apre per consentire la personalizzazione dell'oggetto Criteri di gruppo, ad esempio i *criteri account*:

    ![Personalizzare l'oggetto Criteri di gruppo per configurare le impostazioni come richiesto](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Al termine, scegliere **File > Salva** per salvare il criterio. I computer aggiornano Criteri di gruppo per impostazione predefinita ogni 90 minuti e applicano le modifiche apportate.

## <a name="create-a-custom-group-policy-object"></a>Creare un oggetto Criteri di gruppo personalizzato

Per raggruppare impostazioni di criteri simili, è spesso necessario creare oggetti Criteri di gruppo aggiuntivi anziché applicare tutte le impostazioni necessarie nel singolo oggetto Criteri di gruppo predefinito. Con Azure AD DS, è possibile creare o importare oggetti Criteri di gruppo personalizzati e collegarli a un'unità organizzativa personalizzata. Se è necessario creare prima un'unità organizzativa personalizzata, vedere [creare un'unità organizzativa personalizzata in un dominio gestito Azure AD DS](create-ou.md).

1. Nella console di **gestione criteri di gruppo** selezionare l'unità organizzativa (OU) personalizzata, ad esempio *mycustomou "* . Fare clic con il pulsante destro del mouse sull'unità organizzativa e scegliere **Crea un oggetto Criteri di gruppo in questo dominio e collegarlo qui...** :

    ![Creare un oggetto Criteri di gruppo personalizzato in Criteri di gruppo Management Console](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Specificare un nome per il nuovo oggetto Criteri di gruppo, ad esempio l' *oggetto Criteri*di gruppo personalizzato, quindi fare clic su **OK**. Facoltativamente, è possibile basare l'oggetto Criteri di gruppo personalizzato su un oggetto Criteri di gruppo esistente e su un set di opzioni.

    ![Specificare un nome per il nuovo oggetto Criteri di gruppo personalizzato](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. L'oggetto Criteri di gruppo personalizzato viene creato e collegato all'unità organizzativa personalizzata. Per configurare le impostazioni dei criteri, fare clic con il pulsante destro del mouse sull'oggetto Criteri di gruppo personalizzato e scegliere **modifica...** :

    ![Scegliere l'opzione per modificare l'oggetto Criteri di gruppo personalizzato](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Viene visualizzata la **Editor gestione criteri di gruppo** che consente di personalizzare l'oggetto Criteri di gruppo:

    ![Personalizzare l'oggetto Criteri di gruppo per configurare le impostazioni come richiesto](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Al termine, scegliere **File > Salva** per salvare il criterio. I computer aggiornano Criteri di gruppo per impostazione predefinita ogni 90 minuti e applicano le modifiche apportate.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle impostazioni di Criteri di gruppo disponibili che è possibile configurare usando il Console Gestione Criteri di gruppo, vedere usare gli [elementi preferenza criteri di gruppo][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
