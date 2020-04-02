---
title: Esercitazione - Creare un trust tra foreste in Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come creare una foresta unidirezionale in uscita in un dominio di Servizi di dominio Active Directory locale nel portale di Azure per Servizi di dominio Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: eb96cb32c05d2ba3fbd38e72c16540d947436117
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519053"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Esercitazione: Creare un trust tra foreste in uscita e un dominio locale in Servizi di dominio Azure Active Directory (anteprima)Tutorial: Create an outbound forest trust to an on-premises domain in Azure Active Directory Domain Services (preview)

Negli ambienti in cui non è possibile sincronizzare gli hashe delle password o si dispone di utenti che accedono esclusivamente tramite smart card in modo che non conoscano la password, è possibile usare una foresta di risorse in Servizi di dominio Azure Active Directory. Una foresta di risorse usa un trust unidirezionale in uscita da Servizi di dominio Active Directory a uno o più ambienti di Servizi di dominio Active Directory locali. Questa relazione di trust consente a utenti, applicazioni e computer di eseguire l'autenticazione in un dominio locale del dominio gestito di Servizi di dominio Active Directory di Azure.This trust relationship lets users, applications, and computers authenticate against an on-premises domain from the Azure AD DS managed domain. Le foreste di risorse di Servizi di dominio Azure sono attualmente in anteprima.

![Diagramma del trust tra foreste da Servizi di dominio Active Directory di Azure a Servizi di dominio Active Directory locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare DNS in un ambiente di servizi di dominio Active Directory locale per supportare la connettività di Servizi di dominio Active Directory di AzureConfigure DNS in an on-premises AD DS environment to support Azure AD DS connectivity
> * Creare un trust tra foreste in ingresso unidirezionale in un ambiente di Servizi di dominio Active Directory localeCreate a one-way inbound forest trust in an on-premises Ad DS environment
> * Creare un trust di foresta in uscita unidirezionale in Servizi di dominio Active DirectoryCreate a one-way outbound forest trust in Azure AD DS
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Servizi di dominio Azure Active Directory creato usando una foresta di risorse e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Assicurarsi di creare un dominio gestito di Servizi di dominio Active Directory di Azure usando una foresta di *risorse.* L'opzione predefinita crea una foresta *utente.* Solo le foreste di risorse possono creare trust per ambienti di Servizi di dominio Active Directory precedente. È inoltre necessario utilizzare almeno *SKU Enterprise* per il dominio gestito. Se necessario, modificare lo SKU per un dominio gestito di [Azure AD DS.][howto-change-sku]

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione si creerà e si configura il trust tra foreste in uscita da Servizi di dominio Active Directory di Azure usando il portale di Azure.In this tutorial, you create and configure the outbound forest trust from Azure AD DS using the Azure portal. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerazioni sulla rete

La rete virtuale che ospita la foresta di risorse di Servizi di dominio Active Directory di Azure AD richiede la connettività di rete ad Active Directory locale. Le applicazioni e i servizi richiedono inoltre la connettività di rete alla rete virtuale che ospita la foresta di risorse di Servizi di dominio Active Directory di Azure.Applications and services also need network connectivity to the virtual network hosting the Azure AD DS resource forest. La connettività di rete alla foresta di risorse di Servizi di dominio Active Directory di Azure deve essere sempre attiva e stabile altrimenti gli utenti potrebbero non riuscire ad autenticare o accedere alle risorse.Network connectivity to the Azure AD DS resource forest must be always on and stable it users may fail to authenticate or access resources.

Prima di configurare un trust tra foreste in Servizi di dominio Active Directory di Azure, assicurarsi che la rete tra Azure e l'ambiente locale soddisfi i requisiti seguenti:Before you configure a forest trust in Azure AD DS, make sure your networking between Azure and on-premises environment meets the following requirements:

* Utilizzare indirizzi IP privati. Non fare affidamento su DHCP con assegnazione di indirizzi IP dinamici.
* Evitare la sovrapposizione degli spazi di indirizzi IP per consentire al peering e al routing della rete virtuale di comunicare correttamente tra Azure e l'ambiente locale.
* Una rete virtuale di Azure richiede una subnet gateway per configurare una connessione VPN o [ExpressRoute][expressroute] di [Azure da sito a sito (S2S)][vpn-gateway]
* Creare subnet con indirizzi IP sufficienti per supportare lo scenario.
* Assicurarsi che Azure AD DS abbia una propria subnet, non condividere questa subnet di rete virtuale con macchine virtuali e servizi dell'applicazione.
* Le reti virtuali con peerING NON sono transitive.
    * È necessario creare peering di rete virtuale di Azure tra tutte le reti virtuali che si vuole usare il trust della foresta di risorse di Servizi di dominio Active Directory di Azure per l'ambiente di Servizi di dominio Active Directory locale.
* Fornire connettività di rete continua alla foresta di Active Directory locale. Non utilizzare connessioni su richiesta.
* Assicurarsi che vi sia una risoluzione continua dei nomi (DNS) tra il nome della foresta di risorse di Servizi di dominio Active Directory di Azure e il nome della foresta di Active Directory locale.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurare DNS nel dominio locale

Per risolvere correttamente il dominio gestito di Servizi di dominio Active Directory di Azure dall'ambiente locale, potrebbe essere necessario aggiungere server d'inoltro ai server DNS esistenti. Se non è stato configurato l'ambiente locale per comunicare con il dominio gestito di Servizi di dominio Active Directory di Azure, completare i passaggi seguenti da una workstation di gestione per il dominio di Servizi di dominio Active Directory locale:If you haven't configure the on-premises environment to communicate with the Azure AD DS managed domain, complete the following steps from a management workstation for the on-premises AD DS domain:

1. Selezionare **Start . Strumenti di amministrazione - SISTEMA DNS**
1. Selezionare il pulsante destro del mouse sul server DNS, ad esempio *myAD01*, selezionare **Proprietà**
1. Scegliere **Server d'inoltro**, quindi **Modifica** per aggiungere altri server d'inoltro.
1. Aggiungere gli indirizzi IP del dominio gestito di Azure Servizi di dominio Active Directory, ad esempio *10.0.2.4* e *10.0.2.5.*

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Creare un trust tra foreste in ingresso nel dominio localeCreate inbound forest trust in the on-premises domain

Il dominio di Servizi di dominio Active Directory locale necessita di un trust tra foreste in ingresso per il dominio gestito di Servizi di dominio Active Directory di Azure.The on-premises AD DS domain needs an incoming forest trust for the Azure AD DS managed domain. Questa relazione di trust deve essere creata manualmente nel dominio di Servizi di dominio Active Directory locale, non può essere creata dal portale di Azure.This trust must be manually created in the on-premises AD DS domain, can't be created from the Azure portal.

Per configurare il trust in ingresso nel dominio di Servizi di dominio Active Directory locale, completare i passaggi seguenti da una workstation di gestione per il dominio di Servizi di dominio Active Directory locale:

1. Selezionare **Start . Strumenti di amministrazione - Domini e trust di Active Directory**
1. Selezionare il dominio di selezione del pulsante destro del mouse, ad esempio *onprem.contoso.com*, selezionare **Proprietà**
1. Scegliere la scheda **Trust,** quindi **Nuova relazione di trust**
1. Immettere il nome nel nome di dominio di Azure AD DS, ad esempio *aaddscontoso.com*, quindi selezionare **Avanti**
1. Selezionare l'opzione per creare un **trust tra foreste**, quindi creare un trust **unidirezionale: in ingresso.**
1. Scegliere di creare il trust solo per **questo dominio.** Nel passaggio successivo si creerà la relazione di trust nel portale di Azure per il dominio gestito di Servizi di dominio Active Directory di Azure.In the next step, you create the trust in the Azure portal for the Azure AD DS managed domain.
1. Scegliere di utilizzare **l'autenticazione**a livello di foresta, quindi immettere e confermare una password di trust. Questa stessa password viene immessa anche nel portale di Azure nella sezione successiva.
1. Scorrere le seguenti finestre successive con le opzioni predefinite, quindi scegliere l'opzione **No, non confermare il trust in uscita**.
1. Selezionare **Fine**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Creare un trust tra foreste in uscita in Azure AD DSCreate outbound forest trust in Azure AD DS

Con il dominio di Servizi di dominio Active Directory locale configurato per risolvere il dominio gestito di Servizi di dominio Active Directory di Azure e viene creato un trust tra foreste in ingresso, è stato creato il trust tra foreste in uscita. Questo trust tra foreste in uscita completa la relazione di trust tra il dominio di Servizi di dominio Active Directory locale e il dominio gestito di Servizi di dominio Active Directory di Azure.This outbound forest trust completes the trust relationship between the on-premises AD DS domain and the Azure AD DS managed domain.

Per creare il trust in uscita per il dominio gestito di Servizi di dominio Active Directory di Azure nel portale di Azure, completare i passaggi seguenti:To create the outbound trust for the Azure AD DS managed domain in the Azure portal, complete the following steps:

1. Nel portale di Azure cercare e selezionare Servizi di **dominio Azure AD**, quindi selezionare il dominio gestito, ad esempio *aaddscontoso.com*
1. Dal menu sul lato sinistro del dominio gestito di Servizi di dominio Active Directory di Azure selezionare **Trusts**, quindi scegliere **Aggiungi** una relazione di trust.

   > [!NOTE]
   > Se l'opzione di menu **Trust** non è visualizzata, selezionare **Proprietà** per il *tipo di foresta*. Solo le foreste di *risorse* possono creare trust. Se il tipo di foresta è *Utente*, non è possibile creare trust. Attualmente non è possibile modificare il tipo di foresta di un dominio gestito di Servizi di dominio Active Directory di Azure.There's currently no way to change the forest type of an Azure AD DS managed domain. È necessario eliminare e ricreare il dominio gestito come foresta di risorse.

1. Immettere un nome visualizzato che identifichi il trust, quindi il nome DNS della foresta trusted locale, ad esempio *onprem.contoso.com*
1. Specificare la stessa password di trust utilizzata durante la configurazione del trust tra foreste in ingresso per il dominio di Servizi di dominio Active Directory locale nella sezione precedente.
1. Fornire almeno due server DNS per il dominio di Servizi di dominio Active Directory locale, ad esempio *10.1.1.4* e *10.1.1.5*
1. Quando possibile, **salvare** il trust tra foreste in uscita

    ![Creare un trust tra foreste in uscita nel portale di AzureCreate outbound forest trust in the Azure portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Convalidare l'autenticazione delle risorseValidate resource authentication

Gli scenari comuni seguenti consentono di convalidare il trust tra foreste correttamente autentica gli utenti e l'accesso alle risorse:The following common scenarios let you validate that forest trust correctly authenticates users and access to resources:

* [Autenticazione utente locale dalla foresta di risorse di Servizi di dominio Active Directory di Azure AD](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Accedere alle risorse nella foresta di risorse di Servizi di dominio Active Directory di Azure usando l'utente localeAccess resources in the Azure AD DS resource forest using on-premises user](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Abilitare la condivisione di file e stampanti](#enable-file-and-printer-sharing)
    * [Creare un gruppo di sicurezza e aggiungere membriCreate a security group and add members](#create-a-security-group-and-add-members)
    * [Creare una condivisione file per l'accesso tra foresteCreate a file share for cross-forest access](#create-a-file-share-for-cross-forest-access)
    * [Convalidare l'autenticazione tra foreste in una risorsaValidate cross-forest authentication to a resource](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticazione utente locale dalla foresta di risorse di Servizi di dominio Active Directory di Azure AD

È necessario aggiungere una macchina virtuale Windows Server al dominio di risorse di Servizi di dominio Active Directory di Azure.You should have Windows Server virtual machine joined to the Azure AD DS resource domain. Usare questa macchina virtuale per testare l'autenticazione dell'utente locale in una macchina virtuale.

1. Connettersi alla macchina virtuale di Windows Server aggiunta alla foresta di risorse di Servizi di dominio Active Directory di Azure usando [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) e le credenziali di amministratore di Azure AD DS.Connect to the Windows Server VM joined to the Azure AD DS resource forest using Azure AD DS e your Azure AD DS administrator credentials.
1. Aprire un prompt dei `whoami` comandi e utilizzare il comando per visualizzare il nome distinto dell'utente attualmente autenticato:

    ```console
    whoami /fqdn
    ```

1. Usare `runas` il comando per eseguire l'autenticazione come utente dal dominio locale. Nel comando seguente `userUpn@trusteddomain.com` sostituire con l'UPN di un utente del dominio locale trusted. Il comando richiede la password dell'utente:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se l'autenticazione ha esito positivo, viene aperto un nuovo prompt dei comandi. Il titolo del nuovo `running as userUpn@trusteddomain.com`prompt dei comandi include .
1. Utilizzare `whoami /fqdn` nel nuovo prompt dei comandi per visualizzare il nome distinto dell'utente autenticato da Active Directory locale.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Accedere alle risorse nella foresta di risorse di Servizi di dominio Active Directory di Azure usando l'utente localeAccess resources in the Azure AD DS resource forest using on-premises user

Usando la macchina virtuale di Windows Server aggiunta alla foresta di risorse di Servizi di dominio Active Directory di Azure, è possibile testare lo scenario in cui gli utenti possono accedere alle risorse ospitate nella foresta di risorse quando eseguono l'autenticazione da computer nel dominio locale con gli utenti del dominio locale. Negli esempi seguenti viene illustrato come creare e testare vari scenari comuni.

#### <a name="enable-file-and-printer-sharing"></a>Abilitare la condivisione di file e stampanti

1. Connettersi alla macchina virtuale di Windows Server aggiunta alla foresta di risorse di Servizi di dominio Active Directory di Azure usando [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) e le credenziali di amministratore di Azure AD DS.Connect to the Windows Server VM joined to the Azure AD DS resource forest using Azure AD DS e your Azure AD DS administrator credentials.

1. Aprire **Impostazioni di Windows**, quindi cercare e selezionare Centro connessioni di rete e **condivisione**.
1. Scegliere l'opzione Modifica impostazioni di **condivisione avanzate.**
1. In **Profilo di dominio**selezionare **Attiva condivisione file e stampanti** e quindi **Salva modifiche**.
1. Chiudere **Centro connessioni di rete e condivisione**.

#### <a name="create-a-security-group-and-add-members"></a>Creare un gruppo di sicurezza e aggiungere membriCreate a security group and add members

1. Aprire **Utenti e computer di Active Directory**.
1. Fare clic con il pulsante destro del mouse sul nome di dominio, scegliere **Nuovo**, quindi **unità organizzativa**.
1. Nella casella nome digitare *Oggetti locali*, quindi scegliere **OK**.
1. Selezionare e fare clic con il pulsante destro del mouse su **LocalObjects** nel riquadro di spostamento. Selezionare **Nuovo** e quindi **Gruppo**.
1. Digitare *FileServerAccess* nella casella **Nome gruppo.** Per ambito **gruppo**, selezionare Locale di **dominio**, quindi scegliere **OK**.
1. Nel riquadro del contenuto fare doppio clic su **FileServerAccess**. Selezionare **Membri**, scegliere **Aggiungi**, quindi **Selezionare Percorsi**.
1. Selezionare Active Directory locale dalla visualizzazione **Percorso,** quindi scegliere **OK**.
1. Digitare *Domain Users* nella casella **Immettere i nomi degli oggetti da selezionare.** Selezionare **Controlla nomi**, specificare le credenziali per Active Directory locale, quindi scegliere **OK**.

    > [!NOTE]
    > È necessario fornire le credenziali perché la relazione di trust è solo un modo. Ciò significa che gli utenti di Servizi di dominio Active Directory di Azure non possono accedere alle risorse o cercare utenti o gruppi nel dominio trusted (locale).

1. Il gruppo **Domain Users** di Active Directory locale deve essere membro del gruppo **FileServerAccess.** Selezionare **OK** per salvare il gruppo e chiudere la finestra.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Creare una condivisione file per l'accesso tra foresteCreate a file share for cross-forest access

1. Nella macchina virtuale Windows Server aggiunta alla foresta di risorse di Azure AD S creare una cartella e specificare il nome, ad esempio *CrossForestShare*.
1. Selezionare la cartella con il pulsante destro del mouse e scegliere **Proprietà**.
1. Seleziona la scheda **Protezione,** quindi scegli **Modifica**.
1. Nella finestra di dialogo *Autorizzazioni per CrossForestShare* selezionare **Aggiungi**.
1. Digitare *FileServerAccess* in **Immettere i nomi degli oggetti da selezionare**, quindi scegliere **OK**.
1. Selezionare *FileServerAccess* dall'elenco **Gruppi o Nomi utente.** Nell'elenco **Autorizzazioni per FileServerAccess** scegliere *Consenti* per le autorizzazioni **Modifica** e **Scrittura,** quindi selezionare **OK**.
1. Seleziona la scheda **Condivisione,** quindi scegli **Condivisione avanzata...**
1. Scegliere **Condividi questa cartella**, quindi immettere un nome per la condivisione file in Nome **condivisione,** ad esempio *CrossForestShare*.
1. Selezionare **Autorizzazioni**. Nell'elenco **Autorizzazioni per tutti** scegliere **Consenti** per l'autorizzazione **di modifica.**
1. Selezionare **OK** due volte e quindi **Chiudi**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Convalidare l'autenticazione tra foreste in una risorsaValidate cross-forest authentication to a resource

1. Accedere a un computer Windows aggiunto ad Active Directory locale utilizzando un account utente da Active Directory locale.
1. Utilizzando **Esplora risorse**, connettersi alla condivisione creata utilizzando il `\\fs1.aaddscontoso.com\CrossforestShare`nome host completo e la condivisione, ad esempio .
1. Per convalidare l'autorizzazione di scrittura, selezionare con il pulsante destro del mouse nella cartella , scegliere **Nuovo**, quindi Documento di **testo**. Utilizzare il nome predefinito **Nuovo documento di testo**.

    Se le autorizzazioni di scrittura sono impostate correttamente, viene creato un nuovo documento di testo. I passaggi seguenti apriranno, modificheranno ed elimineranno il file in base alle esigenze.
1. Per convalidare l'autorizzazione di lettura, aprire **Nuovo documento di testo**.
1. Per convalidare l'autorizzazione di modifica, aggiungere testo al file e chiudere **Blocco note**. Quando viene richiesto di salvare le modifiche, scegliere **Salva**.
1. Per convalidare l'autorizzazione di eliminazione, selezionare con il pulsante destro del mouse **Nuovo documento** di testo e scegliere **Elimina**. Scegliere **Sì** per confermare l'eliminazione dei file.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare DNS in un ambiente di servizi di dominio Active Directory locale per supportare la connettività di Servizi di dominio Active Directory di AzureConfigure DNS in an on-premises AD DS environment to support Azure AD DS connectivity
> * Creare un trust tra foreste in ingresso unidirezionale in un ambiente di Servizi di dominio Active Directory localeCreate a one-way inbound forest trust in an on-premises Ad DS environment
> * Creare un trust di foresta in uscita unidirezionale in Servizi di dominio Active DirectoryCreate a one-way outbound forest trust in Azure AD DS
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Per altre informazioni concettuali sui tipi di foresta in Servizi di dominio Active Directory di Azure, vedere Che cosa sono le foreste di [risorse?][concepts-forest] e Come funzionano i trust della [foresta in Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
