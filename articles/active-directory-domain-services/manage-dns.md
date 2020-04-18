---
title: Gestire il DNS per Servizi di dominio Azure AD - Documenti Microsoft
description: Informazioni su come installare gli strumenti server DNS per gestire DNS e creare server di inoltro condizionale per un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to install the DNS Server Tools to manage DNS and create conditional forwarders for an Azure Active Directory Domain Services managed domain.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603541"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>Amministrare DNS e creare server d'inoltro condizionale in un dominio gestito di Servizi di dominio Azure ADAdminister DNS and create conditional forwarders in an Azure AD Domain Services managed domain

In Servizi di dominio Azure Active Directory (Azure AD DS) un componente chiave è DNS (Domain Name Resolution). Servizi di dominio Active Directory di Azure include un server DNS che fornisce la risoluzione dei nomi per il dominio gestito. Questo server DNS include i record DNS incorporati e gli aggiornamenti per i componenti chiave che consentono l'esecuzione del servizio.

Quando si eseguono le proprie applicazioni e servizi, potrebbe essere necessario creare record DNS per le macchine che non fanno parte del dominio, configurare indirizzi IP virtuali per i servizi di bilanciamento del carico o configurare server d'inoltro DNS esterni. Agli utenti che appartengono al gruppo *Amministratori controller di dominio AAD* vengono concessi privilegi di amministrazione DNS nel dominio gestito di Servizi di dominio Active Directory di Azure e possono creare e modificare record DNS personalizzati.

In un ambiente ibrido, le zone e i record DNS configurati in altri spazi dei nomi DNS, ad esempio un ambiente Servizi di dominio Active Directory locale, non vengono sincronizzati con Servizi di dominio Active Directory di Azure.In a hybrid environment, DNS zones and records configured in other DNS namespaces, such as an on-premises AD DS environment, arend aren's at synchronized to Azure AD DS. Per risolvere le risorse denominate in altri spazi dei nomi DNS, creare e utilizzare server d'inoltro condizionali che puntano ai server DNS esistenti nell'ambiente.

Questo articolo illustra come installare gli strumenti del server DNS, quindi usare la console DNS per gestire i record e creare server d'inoltro condizionali in Servizi di dominio Active Directory di Azure.This article shows you how to install the DNS Server tools then use the DNS console to manage records and create conditional forwarders in Azure AD DS.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
* Connettività dalla rete virtuale di Servizi di dominio Active Directory di Azure alla posizione in cui sono ospitati gli altri spazi dei nomi DNS.
    * Questa connettività può essere fornita con una connessione di Azure ExpressRoute o gateway VPN di [Azure.This][vpn-gateway] connectivity can be provided with an [Azure ExpressRoute][expressroute] or Azure VPN Gateway connection.
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.A Windows Server management VM that is joined to the Azure AD DS managed domain.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito.][create-join-windows-vm]
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="install-dns-server-tools"></a>Installare gli strumenti del server DNS

Per creare e modificare i record DNS in Servizi di dominio Active Directory di Azure, è necessario installare gli strumenti del server DNS. Questi strumenti possono essere installati come funzionalità in Windows Server.These tools can be installed as a feature in Windows Server. Per ulteriori informazioni su come installare gli strumenti di amministrazione in un client Windows, vedere installare Strumenti di [amministrazione remota del server (RSAT)][install-rsat].

1. Accedere alla macchina virtuale di gestione. Per la procedura di connessione tramite il portale di Azure, vedere [Connettersi a una macchina virtuale][connect-windows-server-vm]di Windows Server.For steps on how to connect using the Azure portal, see Connect to a Windows Server VM .
1. Se **Server Manager** non viene aperto per impostazione predefinita quando si accede alla macchina virtuale, selezionare il menu **Start** e quindi scegliere **Server Manager**.
1. Fare clic su *Aggiungi ruoli e funzionalità* nel riquadro **Dashboard** della finestra **Server Manager**.
1. Nella pagina **Prima di iniziare** dell'*aggiunta guidata ruoli e funzionalità* selezionare **Avanti**.
1. Per *Tipo di installazione* lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e selezionare **Avanti**.
1. Nella pagina **Selezione server** scegliere la macchina virtuale corrente dal pool di server, ad esempio *myvm.aaddscontoso.com* e quindi selezionare **Avanti**.
1. Nella pagina **Ruoli del server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità** espandere il nodo **Strumenti di amministrazione remota del server**, quindi il nodo **Strumenti di amministrazione ruoli**. Selezionare la funzionalità **Strumenti per server DNS** dall'elenco di strumenti di amministrazione ruoli.

    ![Scegliere di installare strumenti server DNS dall'elenco degli strumenti di amministrazione dei ruoli disponibili](./media/manage-dns/install-dns-tools.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti Gestione Criteri di gruppo potrebbe richiedere uno o due minuti.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Aprire la console di gestione DNS per amministrare DNS

Con gli strumenti del server DNS installati, è possibile amministrare i record DNS nel dominio gestito di Servizi di dominio Active Directory di Azure.With the DNS Server tools installed, you can administer DNS records on the Azure AD DS managed domain.

> [!NOTE]
> Per amministrare DNS in un dominio gestito di Servizi di dominio Active Directory di Azure, è necessario accedere a un account utente membro del gruppo *AAD DC Administrators.*

1. Nella schermata Start selezionare **Strumenti di amministrazione.** Viene visualizzato un elenco degli strumenti di gestione disponibili, incluso **il DNS** installato nella sezione precedente. Selezionare **DNS** per avviare la console di gestione DNS.
1. Nella finestra di dialogo **Connetti al** server DNS selezionare Il seguente **computer**, quindi immettere il nome di dominio DNS del dominio gestito, ad esempio *aaddscontoso.com:*

    ![Connettersi al dominio gestito di Servizi di dominio Active Directory di Azure nella console DNSConnect to the Azure AD DS managed domain in the DNS console](./media/manage-dns/connect-dns-server.png)

1. La console DNS si connette al dominio gestito di Azure AD DS specificato. Espandere le zone di **ricerca diretta** o le zone di **ricerca inversa** per creare le voci DNS necessarie o modificare i record esistenti in base alle esigenze.

    ![Console DNS - amministrare il dominio](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Quando si gestiscono i record usando gli strumenti del server DNS, assicurarsi di non eliminare o modificare i record DNS predefiniti usati da Servizi di dominio Active Directory di Azure. I record DNS predefiniti includono record DNS di dominio, record del server dei nomi e altri record usati per l'individuazione dei controller di dominio. Se si modificano questi record, i servizi di dominio verranno interrotti nella rete virtuale.

## <a name="create-conditional-forwarders"></a>Creare server d'inoltro condizionali

Una zona DNS di Azure AD DS deve contenere solo la zona e i record per il dominio gestito stesso. Non creare zone aggiuntive in Servizi di dominio Active Directory di Azure per risolvere le risorse denominate in altri spazi dei nomi DNS. Usare invece i server d'inoltro condizionali nel dominio gestito di Servizi di dominio Active Directory di Azure per indicare al server DNS dove andare per risolvere gli indirizzi per tali risorse.

Un server d'inoltro condizionale è un'opzione di configurazione in un server DNS che consente di definire un dominio DNS, ad esempio *contoso.com*, a cui inoltrare le query. Invece del server DNS locale che tenta di risolvere le query per i record in tale dominio, le query DNS vengono inoltrate al DNS configurato per tale dominio. Questa configurazione assicura che vengano restituiti i record DNS corretti, poiché non si crea una zona DNS locale con record duplicati nel dominio gestito di Servizi di dominio Active Directory di Azure per riflettere tali risorse.

Per creare un server d'inoltro condizionale nel dominio gestito di Servizi di dominio Active Directory di Azure, completare i passaggi seguenti:To create a conditional forwarder in your Azure AD DS managed domain, complete the following steps:

1. Selezionare la zona DNS di Servizi di dominio Active Directory di Azure, ad esempio aaddscontoso.com .vbSelect your Azure AD DS DNS zone, such as *aaddscontoso.com*.vb
1. Selezionare **Server d'inoltro condizionale**, quindi selezionare con il pulsante destro del mouse e scegliere **Nuovo server d'inoltro condizionale...**
1. Immettere l'altro **dominio DNS**, ad esempio *contoso.com*, quindi immettere gli indirizzi IP dei server DNS per tale spazio dei nomi, come illustrato nell'esempio seguente:

    ![Aggiungere e configurare un server di inoltro condizionale per il server DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Selezionare la casella **Archivia questo server d'inoltro condizionale in Active Directory e replicarlo come segue**, quindi selezionare l'opzione *Per Tutti i server DNS del dominio,* come illustrato nell'esempio seguente:

    ![Console DNS - amministrare il dominio](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Se il server d'inoltro condizionale viene archiviato nell'insieme di *strutture* anziché nel *dominio*, il server d'inoltro condizionale ha esito negativo.

1. Per creare il server d'inoltro condizionale, selezionare **OK**.

La risoluzione dei nomi delle risorse in altri spazi dei nomi dalle macchine virtuali connesse al dominio gestito di Azure AD DS ora dovrebbe essere risolta correttamente. Le query per il dominio DNS configurato nel server d'inoltro condizionale vengono passate ai server DNS pertinenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione DNS, vedere l' [articolo sugli strumenti DNS in Technet](https://technet.microsoft.com/library/cc753579.aspx).

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
