---
title: Gestire il DNS per Servizi di dominio Azure AD - Documenti Microsoft
description: Informazioni su come installare gli strumenti server DNS per gestire DNS per un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to install the DNS Server Tools to manage DNS for an Azure Active Directory Domain Services managed domain.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f0d8f73b47b1110e8e05365013bbf07fd94eb6ca
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655069"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Amministrare DNS in un dominio gestito di Servizi di dominio Azure ADAdminister DNS in an Azure AD Domain Services managed domain

In Servizi di dominio Azure Active Directory (Azure AD DS) un componente chiave è DNS (Domain Name Resolution). Servizi di dominio Active Directory di Azure include un server DNS che fornisce la risoluzione dei nomi per il dominio gestito. Questo server DNS include i record DNS incorporati e gli aggiornamenti per i componenti chiave che consentono l'esecuzione del servizio.

Quando si eseguono le proprie applicazioni e servizi, potrebbe essere necessario creare record DNS per le macchine che non fanno parte del dominio, configurare indirizzi IP virtuali per i servizi di bilanciamento del carico o configurare server d'inoltro DNS esterni. Agli utenti che appartengono al gruppo *Amministratori controller di dominio AAD* vengono concessi privilegi di amministrazione DNS nel dominio gestito di Servizi di dominio Active Directory di Azure e possono creare e modificare record DNS personalizzati.

In a hybrid environment, DNS zones and records configured in an on-premises AD DS environment aren't synchronized to Azure AD DS. Per definire e usare le proprie voci DNS, creare record nel server DNS di Servizi di dominio Active Directory di Azure o usare server di inoltro condizionale che puntino a server DNS esistenti nell'ambiente.

Questo articolo illustra come installare gli strumenti del server DNS e quindi usare la console DNS per gestire i record in Servizi di dominio Active Directory di Azure.This article shows you how to install the DNS Server tools then use the DNS console to manage records in Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
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
1. Nella pagina **Selezione server** scegliere la macchina virtuale corrente dal pool di server, ad esempio *myvm.aaddscontoso.com*, quindi selezionare **Avanti**.
1. Nella pagina **Ruoli server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità** espandere il nodo **Strumenti di amministrazione remota del server**, quindi il nodo **Strumenti di amministrazione ruoli**. Selezionare la funzionalità **Strumenti per server DNS** dall'elenco di strumenti di amministrazione ruoli.

    ![Scegliere di installare strumenti server DNS dall'elenco degli strumenti di amministrazione dei ruoli disponibili](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti Gestione Criteri di gruppo potrebbe richiedere uno o due minuti.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Aprire la console di gestione DNS per amministrare DNS

Con gli strumenti del server DNS installati, è possibile amministrare i record DNS nel dominio gestito di Servizi di dominio Active Directory di Azure.With the DNS Server tools installed, you can administer DNS records on the Azure AD DS managed domain.

> [!NOTE]
> Per amministrare DNS in un dominio gestito di Servizi di dominio Active Directory di Azure, è necessario accedere a un account utente membro del gruppo *AAD DC Administrators.*

1. Nella schermata Start selezionare **Strumenti di amministrazione.** Viene visualizzato un elenco degli strumenti di gestione disponibili, incluso **il DNS** installato nella sezione precedente. Selezionare **DNS** per avviare la console di gestione DNS.
1. Nella finestra di dialogo **Connetti al** server DNS selezionare Il seguente **computer**, quindi immettere il nome di dominio DNS del dominio gestito, ad esempio *aaddscontoso.com:*

    ![Connettersi al dominio gestito di Servizi di dominio Active Directory di Azure nella console DNSConnect to the Azure AD DS managed domain in the DNS console](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. La console DNS si connette al dominio gestito di Azure AD DS specificato. Espandere le zone di **ricerca diretta** o le zone di **ricerca inversa** per creare le voci DNS necessarie o modificare i record esistenti in base alle esigenze.

    ![Console DNS - amministrare il dominio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Quando si gestiscono i record usando gli strumenti del server DNS, assicurarsi di non eliminare o modificare i record DNS predefiniti usati da Servizi di dominio Active Directory di Azure. I record DNS predefiniti includono record DNS di dominio, record del server dei nomi e altri record usati per l'individuazione dei controller di dominio. Se si modificano questi record, i servizi di dominio verranno interrotti nella rete virtuale.

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
