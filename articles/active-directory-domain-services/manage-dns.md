---
title: Gestire DNS per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come installare gli strumenti del server DNS per gestire DNS e creare server d'inoltri condizionali per un Azure Active Directory Domain Services dominio gestito.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: af321f4e9ef9e340026852a759bb70b9f03c77f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722943"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Amministrare DNS e creare server d'inoltri condizionali in un Azure Active Directory Domain Services dominio gestito

In Azure Active Directory Domain Services (Azure AD DS), un componente chiave è DNS (risoluzione del nome di dominio). Azure AD DS include un server DNS che fornisce la risoluzione dei nomi per il dominio gestito. Questo server DNS include i record DNS incorporati e gli aggiornamenti per i componenti chiave che consentono l'esecuzione del servizio.

Quando si eseguono applicazioni e servizi personalizzati, potrebbe essere necessario creare record DNS per computer che non fanno parte del dominio, configurare indirizzi IP virtuali per i servizi di bilanciamento del carico o configurare server d'avvio DNS esterni. Agli utenti che appartengono al gruppo di *amministratori di AAD DC* vengono concessi privilegi di amministrazione DNS nel dominio gestito Azure AD DS e possono creare e modificare i record DNS personalizzati.

In un ambiente ibrido, le zone e i record DNS configurati in altri spazi dei nomi DNS, ad esempio un ambiente di servizi di dominio Active Directory locale, non vengono sincronizzati nel dominio gestito. Per risolvere le risorse denominate in altri spazi dei nomi DNS, creare e usare i server d'inoltri condizionali che fanno riferimento a server DNS esistenti nell'ambiente.

Questo articolo illustra come installare gli strumenti server DNS, quindi usare la console DNS per gestire i record e creare server d'inoltri condizionali in Azure AD DS.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un Azure Active Directory Domain Services dominio gestito][create-azure-ad-ds-instance].
* Connettività dalla rete virtuale Azure AD DS alla posizione in cui sono ospitati gli altri spazi dei nomi DNS.
    * Questa connettività può essere fornita con una connessione di [Azure ExpressRoute][expressroute] o [gateway VPN di Azure][vpn-gateway] .
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito][create-join-windows-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="install-dns-server-tools"></a>Installare gli strumenti server DNS

Per creare e modificare i record DNS in un dominio gestito, è necessario installare gli strumenti server DNS. Questi strumenti possono essere installati come funzionalità di Windows Server. Per ulteriori informazioni su come installare gli strumenti di amministrazione in un client Windows, vedere Install [strumenti di amministrazione remota del server (amministrazione remota][install-rsat]del server).

1. Accedere alla macchina virtuale di gestione. Per i passaggi relativi alla modalità di connessione tramite la portale di Azure, vedere [connettersi a una macchina virtuale Windows Server][connect-windows-server-vm].
1. Se **Server Manager** non viene aperto per impostazione predefinita quando si accede alla macchina virtuale, selezionare il menu **Start** e quindi scegliere **Server Manager**.
1. Fare clic su *Aggiungi ruoli e funzionalità* nel riquadro **Dashboard** della finestra **Server Manager**.
1. Nella pagina **Prima di iniziare** dell'*aggiunta guidata ruoli e funzionalità* selezionare **Avanti**.
1. Per *Tipo di installazione* lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e selezionare **Avanti**.
1. Nella pagina **Selezione server** scegliere la macchina virtuale corrente dal pool di server, ad esempio *myvm.aaddscontoso.com* e quindi selezionare **Avanti**.
1. Nella pagina **Ruoli del server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità** espandere il nodo **Strumenti di amministrazione remota del server**, quindi il nodo **Strumenti di amministrazione ruoli**. Selezionare la funzionalità **Strumenti per server DNS** dall'elenco di strumenti di amministrazione ruoli.

    ![Scegliere di installare gli strumenti server DNS dall'elenco di strumenti di amministrazione ruoli disponibili](./media/manage-dns/install-dns-tools.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti server DNS potrebbe richiedere un minuto o due.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Aprire la console di gestione DNS per amministrare DNS

Con gli strumenti server DNS installati, è possibile amministrare i record DNS nel dominio gestito.

> [!NOTE]
> Per amministrare DNS in un dominio gestito, è necessario aver eseguito l'accesso a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco degli strumenti di gestione disponibili, tra cui **DNS** installato nella sezione precedente. Selezionare **DNS** per avviare la console di gestione DNS.
1. Nella finestra di dialogo **Connetti al server DNS** selezionare **il computer seguente**, quindi immettere il nome di dominio DNS del dominio gestito, ad esempio *aaddscontoso.com*:

    ![Connettersi al dominio gestito nella console DNS](./media/manage-dns/connect-dns-server.png)

1. La console DNS si connette al dominio gestito specificato. Espandere le zone di **ricerca diretta** o le **zone di ricerca inversa** per creare le voci DNS necessarie o modificare i record esistenti in base alle esigenze.

    ![Console DNS - amministrare il dominio](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Quando si gestiscono i record usando gli strumenti server DNS, assicurarsi di non eliminare o modificare i record DNS incorporati usati da Azure AD DS. I record DNS predefiniti includono record DNS di dominio, record del server dei nomi e altri record usati per l'individuazione dei controller di dominio. Se si modificano questi record, i servizi di dominio verranno interrotti nella rete virtuale.

## <a name="create-conditional-forwarders"></a>Creare server d'inoltri condizionali

Una zona DNS di Azure AD DS deve contenere solo la zona e i record per il dominio gestito stesso. Non creare zone aggiuntive nel dominio gestito per risolvere le risorse denominate in altri spazi dei nomi DNS. Usare invece i server d'inoltri condizionali nel dominio gestito per indicare al server DNS dove andare per risolvere gli indirizzi per tali risorse.

Un server d'avanzamento condizionale è un'opzione di configurazione in un server DNS che consente di definire un dominio DNS, ad esempio *contoso.com*, per l'invio di query a. Al posto del server DNS locale che tenta di risolvere le query per i record in tale dominio, le query DNS vengono trasmesse al DNS configurato per tale dominio. Questa configurazione assicura che vengano restituiti i record DNS corretti, perché non si crea una zona DNS locale con record duplicati nel dominio gestito per riflettere tali risorse.

Per creare un server d'avanzamento condizionale nel dominio gestito, completare i passaggi seguenti:

1. Selezionare la zona DNS, ad esempio *aaddscontoso.com*.
1. Selezionare **server d'inoltri condizionali**, quindi fare clic con il pulsante destro del mouse e scegliere **nuovo server d'istruzione condizionale.**
1. Immettere l'altro **dominio DNS**, ad esempio *contoso.com*, quindi immettere gli indirizzi IP dei server DNS per lo spazio dei nomi, come illustrato nell'esempio seguente:

    ![Aggiungere e configurare un server d'avanzamento condizionale per il server DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Selezionare la casella **Archivia questo server d'istruzione condizionale in Active Directory e replicarlo come indicato di seguito**, quindi selezionare l'opzione per *tutti i server DNS in questo dominio*, come illustrato nell'esempio seguente:

    ![Console DNS-selezionare tutti i server DNS in questo dominio](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Se il server d'avanzamento condizionale viene archiviato nella *foresta* anziché nel *dominio*, il server d'avanzamento condizionale ha esito negativo.

1. Per creare il server d'avanzamento condizionale, fare clic su **OK**.

La risoluzione dei nomi delle risorse in altri spazi dei nomi dalle VM connesse al dominio gestito dovrebbe ora risolversi correttamente. Le query per il dominio DNS configurato nel server di trasmissione condizionale vengono passate ai server DNS pertinenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione DNS, vedere l' [articolo sugli strumenti DNS in Technet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh