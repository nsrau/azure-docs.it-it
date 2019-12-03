---
title: Gestire DNS per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come installare gli strumenti server DNS per gestire DNS per un dominio gestito Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f47f112991a4fe2e5b245920db98e5ae7617161a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704941"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Amministrare DNS in un dominio gestito Azure AD Domain Services

In Azure Active Directory Domain Services (Azure AD DS), un componente chiave è DNS (risoluzione del nome di dominio). Azure AD DS include un server DNS che fornisce la risoluzione dei nomi per il dominio gestito. Questo server DNS include i record DNS incorporati e gli aggiornamenti per i componenti chiave che consentono l'esecuzione del servizio.

Quando si eseguono applicazioni e servizi personalizzati, potrebbe essere necessario creare record DNS per computer che non fanno parte del dominio, configurare indirizzi IP virtuali per i servizi di bilanciamento del carico o configurare server d'avvio DNS esterni. Agli utenti che appartengono al gruppo di *amministratori di AAD DC* vengono concessi privilegi di amministrazione DNS nel dominio gestito Azure AD DS e possono creare e modificare i record DNS personalizzati.

In un ambiente ibrido, le zone e i record DNS configurati in un ambiente di servizi di dominio Active Directory locale non vengono sincronizzati con Azure AD DS. Per definire e usare le proprie voci DNS, creare i record nel server DNS di Azure AD DS o usare i server d'inoltri condizionali che fanno riferimento a server DNS esistenti nell'ambiente.

Questo articolo illustra come installare gli strumenti server DNS e quindi usare la console DNS per gestire i record in Azure AD DS.

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

## <a name="install-dns-server-tools"></a>Installare gli strumenti server DNS

Per creare e modificare i record DNS in Azure AD DS, è necessario installare gli strumenti server DNS. Questi strumenti possono essere installati come funzionalità di Windows Server. Per ulteriori informazioni su come installare gli strumenti di amministrazione in un client Windows, vedere Install [strumenti di amministrazione remota del server (amministrazione remota][install-rsat]del server).

1. Accedere alla macchina virtuale di gestione. Per i passaggi relativi alla modalità di connessione tramite la portale di Azure, vedere [connettersi a una macchina virtuale Windows Server][connect-windows-server-vm].
1. Se **Server Manager** non viene aperto per impostazione predefinita quando si accede alla macchina virtuale, selezionare il menu **Start** e quindi scegliere **Server Manager**.
1. Fare clic su *Aggiungi ruoli e funzionalità* nel riquadro **Dashboard** della finestra **Server Manager**.
1. Nella pagina **Prima di iniziare** dell'*aggiunta guidata ruoli e funzionalità* selezionare **Avanti**.
1. Per *Tipo di installazione* lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e selezionare **Avanti**.
1. Nella pagina **Selezione server** scegliere la macchina virtuale corrente dal pool di server, ad esempio *MyVM.aadds.contoso.com*, quindi fare clic su **Avanti**.
1. Nella pagina **Ruoli del server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità** espandere il nodo **Strumenti di amministrazione remota del server**, quindi il nodo **Strumenti di amministrazione ruoli**. Selezionare la funzionalità **Strumenti per server DNS** dall'elenco di strumenti di amministrazione ruoli.

    ![Scegliere di installare gli strumenti server DNS dall'elenco di strumenti di amministrazione ruoli disponibili](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti di gestione Criteri di gruppo potrebbe richiedere un minuto o due.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Aprire la console di gestione DNS per amministrare DNS

Con gli strumenti server DNS installati, è possibile amministrare i record DNS nel dominio gestito Azure AD DS.

> [!NOTE]
> Per amministrare DNS in un dominio gestito di Azure AD DS, è necessario aver eseguito l'accesso a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco degli strumenti di gestione disponibili, tra cui **DNS** installato nella sezione precedente. Selezionare **DNS** per avviare la console di gestione DNS.
1. Nella finestra di dialogo **Connetti al server DNS** selezionare **il computer seguente**, quindi immettere il nome di dominio DNS del dominio gestito, ad esempio *aadds.contoso.com*:

    ![Connettersi al dominio gestito di Azure AD DS nella console DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. La console DNS si connette al dominio gestito Azure AD DS specificato. Espandere le zone di **ricerca diretta** o le **zone di ricerca inversa** per creare le voci DNS necessarie o modificare i record esistenti in base alle esigenze.

    ![Console DNS - amministrare il dominio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Quando si gestiscono i record usando gli strumenti server DNS, assicurarsi di non eliminare o modificare i record DNS incorporati usati da Azure AD DS. I record DNS predefiniti includono record DNS di dominio, record del server dei nomi e altri record usati per l'individuazione dei controller di dominio. Se si modificano questi record, i servizi di dominio verranno interrotti nella rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione DNS, vedere l' [articolo sugli strumenti DNS in Technet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
