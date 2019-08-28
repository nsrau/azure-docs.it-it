---
title: Esercitazione - Creare una VM di gestione per Azure Active Directory Domain Services | Microsoft Docs
description: Questa esercitazione illustra come creare e configurare una macchina virtuale Windows da usare per amministrare un'istanza di Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e7c3ccb553010b84a30ccdad875ea0362112d830
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69618791"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Esercitazione: Creare una VM di gestione per configurare e amministrare un dominio gestito di Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. Il dominio gestito viene amministrato usando gli stessi Strumenti di amministrazione remota del server di un dominio di Active Directory Domain Services locale. Poiché Azure AD DS è un servizio gestito, alcune attività amministrative non possono essere eseguite, ad esempio l'uso del protocollo RDP (Remote Desktop Protocol) per connettersi ai controller di dominio.

Questa esercitazione illustra come creare una VM Windows Server in Azure e installare gli strumenti necessari per amministrare un dominio gestito di Azure AD DS.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Identificare le attività amministrative disponibili in un dominio gestito di Azure AD DS
> * Installare gli strumenti di amministrazione di Active Directory in una VM Windows Server
> * Usare il Centro di amministrazione di Active Directory per eseguire attività comun

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una VM Windows Server aggiunta al dominio gestito di Azure AD DS.
    * Se necessario, seguire l'esercitazione precedente per [creare una VM Windows Server e aggiungerla a un dominio gestito][create-join-windows-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione viene creata e configurata una VM di gestione tramite il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Attività amministrative disponibili in Azure AD DS

Azure AD DS fornisce un dominio gestito per l'utilizzo da parte di utenti, applicazioni e servizi. Questo approccio comporta modifiche per alcune delle attività di gestione disponibili che è possibile eseguire e per i privilegi presenti all'interno del dominio gestito. Queste attività e autorizzazioni possono essere diverse rispetto a quelle riscontrate con un normale ambiente di Active Directory Domain Services locale. Non è inoltre possibile connettersi ai controller di dominio nel dominio gestito usando Desktop remoto.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Attività amministrative che è possibile eseguire in un dominio gestito di Azure AD DS

Ai membri del gruppo *Amministratori di AAD DC* vengono concessi privilegi nel dominio gestito di Azure AD DS che consentono di eseguire attività come:

* Aggiungere computer al dominio gestito.
* Configurare l'oggetto Criteri di gruppo predefinito per i contenitori *AADDC Computers* e *AADDC Users* nel dominio gestito.
* Amministrare DNS nel dominio gestito.
* Creare e amministrare unità organizzative (OU) personalizzate nel dominio gestito.
* Ottenere l'accesso amministrativo ai computer aggiunti al dominio gestito.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Privilegi amministrativi non disponibili in un dominio gestito di Azure AD DS

Il dominio gestito di Azure AD DS è bloccato, quindi non si hanno privilegi per eseguire determinate attività amministrative al suo interno. Di seguito sono riportati alcuni esempi di attività che non è possibile eseguire:

* Estendere lo schema del dominio gestito.
* Connettersi ai controller di dominio per il dominio gestito usando Desktop remoto.
* Aggiungere controller di dominio al dominio gestito.
* Per il dominio gestito, non vengono concessi privilegi di *amministratore di dominio* o *amministratore dell'organizzazione*.

## <a name="sign-in-to-the-windows-server-vm"></a>Accedere alla VM Windows Server

Nell'esercitazione precedente è stata creata una macchina virtuale Windows Server, che è stata aggiunta al dominio gestito di Azure AD DS. Usare questa VM per installare gli strumenti di gestione. Se necessario, seguire i passaggi dell'esercitazione per [creare una VM Windows Server e aggiungerla a un dominio gestito][create-join-windows-vm].

> [!NOTE]
> In questa esercitazione viene usata una VM Windows Server in Azure aggiunta al dominio gestito di Azure AD DS. È anche possibile usare un client Windows, ad esempio Windows 10, aggiunto al dominio gestito.
>
> Per altre informazioni su come installare gli strumenti di amministrazione in un client Windows, vedere [Installare gli Strumenti di amministrazione remota del server](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Per iniziare, connettersi alla VM Windows Server come illustrato di seguito:

1. Nel riquadro di spostamento sinistro del portale di Azure selezionare **Gruppi di risorse**. Scegliere il gruppo di risorse in cui è stata creata la VM, ad esempio *myResourceGroup*, quindi selezionare la VM, ad esempio *myVM*.
1. Nella finestra **Panoramica** della VM selezionare **Connetti**.

    ![Connettersi alla macchina virtuale Windows nel portale di Azure](./media/tutorial-create-management-vm/connect-vm.png)

    È anche possibile [creare e usare un host di Azure Bastion (attualmente in anteprima)][azure-bastion] per consentire l'accesso solo dal portale di Azure tramite SSL.

1. Selezionare l'opzione *Scarica file RDP*. Salvare il file RDP nel Web browser.
1. Per connettersi alla VM, aprire il file RDP scaricato. Quando richiesto, selezionare **Connetti**.
1. Immettere le credenziali di un utente appartenente al gruppo di *amministratori di controller di dominio Azure AD*, ad esempio *contoso\dee*
1. Se viene visualizzato un avviso relativo al certificato durante la fase di accesso, selezionare **Sì** o **Continua** per connettersi.

## <a name="install-active-directory-administrative-tools"></a>Installare gli strumenti di amministrazione di Active Directory

I domini gestiti di Azure AD DS vengono gestiti con gli stessi strumenti di amministrazione degli ambienti AD DS locali, ad esempio il Centro di amministrazione di Active Directory o AD PowerShell. Questi strumenti possono essere installati come parte della funzionalità facoltativa Strumenti di amministrazione remota del server in Windows Server e nei computer client. I membri del gruppo *Amministratori di AAD DC* possono quindi amministrare i domini gestiti di Azure AD DS in remoto usando questi strumenti di amministrazione di AD in un computer client aggiunto al dominio gestito.

Per installare gli strumenti di amministrazione di Active Directory in una VM aggiunta al dominio, seguire questa procedura:

1. Quando si accede alla macchina virtuale, per impostazione predefinita dovrebbe essere visualizzata la finestra di **Server Manager**. In caso contrario, scegliere **Server Manager** dal menu **Start**.
1. Fare clic su *Aggiungi ruoli e funzionalità* nel riquadro **Dashboard** della finestra **Server Manager**.
1. Nella pagina **Prima di iniziare** dell'*aggiunta guidata ruoli e funzionalità* selezionare **Avanti**.
1. Per *Tipo di installazione* lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e selezionare **Avanti**.
1. Nella pagina **Selezione server** scegliere la VM corrente dal pool di server, ad esempio *myvm.contoso.com*, quindi selezionare **Avanti**.
1. Nella pagina **Ruoli del server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità** espandere il nodo **Strumenti di amministrazione remota del server**, quindi il nodo **Strumenti di amministrazione ruoli**.

    Scegliere la funzionalità **Strumenti per Servizi di dominio Active Directory e AD LDS** nell'elenco di strumenti di amministrazione ruoli, quindi selezionare **Avanti**.

    ![Installare 'Strumenti per Servizi di dominio Active Directory e AD LDS' nella pagina Funzionalità](./media/tutorial-create-management-vm/install-features.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti di amministrazione potrebbe richiedere uno o due minuti.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="use-active-directory-administrative-tools"></a>Usare gli strumenti di amministrazione di Active Directory

Una volta installati gli strumenti di amministrazione, ecco come usarli per amministrare il dominio gestito di Azure AD DS. Assicurarsi di aver eseguito l'accesso alla VM con un account utente membro del gruppo *Amministratori di AAD DC*.

1. Nel menu **Start** selezionare **Strumenti di amministrazione Windows**. Sono elencati gli strumenti di amministrazione di AD installati nel passaggio precedente.

    ![Elenco di strumenti di amministrazione installati nel server](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selezionare **Centro di amministrazione di Active Directory**.
1. Per esplorare il dominio gestito di Azure AD DS, scegliere il nome di dominio nel riquadro sinistro, ad esempio *contoso.com*. All'inizio dell'elenco sono presenti due contenitori denominati *AADDC Computers* e *AADDC Users*.

    ![Visualizzare l'elenco dei contenitori disponibili appartenenti al dominio gestito di Azure AD DS](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Per visualizzare gli utenti e i gruppi che appartengono al dominio gestito di Azure AD DS, selezionare il contenitore **AADDC Users**. In questo contenitore vengono visualizzati gli account utente e gruppi del tenant di Azure AD.

    Nell'output di esempio seguente in questo contenitore vengono visualizzati un account utente denominato *contosoadmin* e un gruppo per gli *Amministratori di AAD DC*.

    ![Visualizzare l'elenco degli utenti del dominio di Azure AD DS nel Centro di amministrazione di Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Per visualizzare i computer aggiunti al dominio gestito di Azure AD DS, selezionare il contenitore **AADDC Computers**. Viene visualizzata una voce per la macchina virtuale corrente, ad esempio *myVM*. In questo contenitore *AADDC Computers* sono archiviati gli account di tutti i computer aggiunti al dominio gestito di Azure AD DS.

Sono disponibili azioni comuni del Centro di amministrazione di Active Directory, ad esempio la reimpostazione della password di un account utente o la gestione dell'appartenenza ai gruppi. È anche possibile usare il *modulo di Active Directory per Windows PowerShell*, installato come parte degli strumenti di amministrazione, per gestire le azioni comuni nel dominio gestito di Azure AD DS.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Identificare le attività amministrative disponibili in un dominio gestito di Azure AD DS
> * Installare gli strumenti di amministrazione di Active Directory in una VM Windows Server
> * Usare il Centro di amministrazione di Active Directory per eseguire attività comun

Per interagire in sicurezza con il dominio gestito di Azure AD DS, abilitare il protocollo LDAP sicuro (LDAPS).

> [!div class="nextstepaction"]
> [Configurare LDAP sicuro per un dominio gestito](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
