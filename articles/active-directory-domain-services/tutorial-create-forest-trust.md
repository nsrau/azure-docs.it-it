---
title: 'Esercitazione: Creare un trust tra foreste in Azure AD Domain Services | Microsoft Docs'
description: Informazioni su come creare una foresta in uscita unidirezionale per un dominio Active Directory Domain Services locale nel portale di Azure per Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 40dd7f1b177fd1319b145036c8263ba2c6e30137
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024673"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Esercitazione: Creare un trust tra foreste in uscita per un dominio locale in Azure Active Directory Domain Services (anteprima)

Negli ambienti in cui non è possibile sincronizzare gli hash delle password o in cui gli utenti accedono esclusivamente tramite smart card senza conoscere effettivamente la password, è possibile usare una foresta di risorse in Azure AD Domain Services (Azure AD DS). Una foresta di risorse usa un trust in uscita unidirezionale da Azure AD Domain Services a uno o più ambienti Active Directory Domain Services locali. Questa relazione di trust consente a utenti, applicazioni e computer di eseguire l'autenticazione in un dominio locale dal dominio gestito di Azure AD Domain Services. Le foreste di risorse Azure AD Domain Services sono attualmente disponibili in anteprima.

![Diagramma del trust tra foreste da Azure AD Domain Services ad Active Directory Domain Services locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il DNS in un ambiente Active Directory Domain Services locale per supportare la connettività ad Azure AD Domain Services
> * Creare un trust tra foreste in ingresso unidirezionale in un ambiente Active Directory Domain Services locale
> * Creare un trust tra foreste in uscita unidirezionale in Azure AD Domain Services
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services creato usando una foresta di risorse e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un dominio gestito di Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Assicurarsi di creare un dominio gestito usando una foresta di *risorse*. L'opzione predefinita crea una foresta di *utenti*. Solo le foreste di risorse possono creare trust per gli ambienti Active Directory Domain Services locali.
    >
    > Per il dominio gestito sarà inoltre necessario usare almeno uno SKU *Enterprise*. Se necessario, [cambiare lo SKU di un dominio gestito][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Questa esercitazione illustra come creare e configurare il trust di foreste in uscita da Azure AD Domain Services tramite il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerazioni sulla rete

La rete virtuale che ospita la foresta di risorse Azure AD Domain Services necessita della connettività di rete all'istanza di Active Directory locale. Anche le applicazioni e i servizi necessitano di connettività di rete alla rete virtuale che ospita la foresta di risorse di Azure AD Domain Services. La connettività di rete alla foresta di risorse di Azure AD Domain Services deve essere sempre attiva e stabile. In caso contrario l'autenticazione degli utenti o l'accesso alle risorse potrebbero non riuscire.

Prima di configurare un trust tra foreste in Azure AD Domain Services, assicurarsi che la rete tra Azure e l'ambiente locale soddisfi i requisiti seguenti:

* Usare indirizzi IP privati. Non basarsi sul protocollo DHCP con l'assegnazione di indirizzi IP dinamici.
* Evitare la sovrapposizione degli spazi indirizzi IP per consentire il peering di reti virtuali e il routing per la comunicazione corretta tra Azure e l'ambiente locale.
* Una rete virtuale di Azure richiede una subnet del gateway per configurare un [VPN da sito a sito di Azure][vpn-gateway] o una connessione [ExpressRoute][expressroute]
* Creare subnet con un numero di indirizzi IP sufficiente per supportare lo scenario.
* Assicurarsi che Azure AD Domain Services disponga di una subnet dedicata, non condividere la subnet della rete virtuale con le macchine virtuali e i servizi dell'applicazione.
* Le reti virtuali con peering NON sono transitive.
    * È necessario creare i peering di reti virtuali di Azure tra tutte le reti virtuali per cui si vuole usare il trust tra foreste di risorse di Azure AD Domain Services per l'ambiente Active Directory Domain Services locale.
* Fornire connettività di rete continua alla foresta Active Directory locale. Non usare connessioni su richiesta.
* Assicurarsi che sia presente la risoluzione dei nomi continua (DNS) tra il nome della foresta di risorse Azure AD Domain Services e il nome della foresta Active Directory locale.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurare il DNS nel dominio locale

Per risolvere correttamente il dominio gestito dall'ambiente locale, può essere necessario aggiungere server d'inoltro ai server DNS esistenti. Se l'ambiente locale non è stato configurato per comunicare con il dominio gestito, completare i passaggi seguenti da una workstation di gestione per il dominio di Active Directory Domain Services locale:

1. Selezionare **Start | Strumenti di amministrazione | DNS**
1. Fare clic con il pulsante destro del mouse sul server DNS, ad esempio *myAD01* e quindi scegliere **Proprietà**
1. Scegliere **Server d'inoltro**, quindi **Modifica** per aggiungere altri server d'inoltro.
1. Aggiungere gli indirizzi IP del dominio gestito, ad esempio *10.0.2.4* e *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Creare il trust tra foreste in ingresso nel dominio locale

Il dominio locale di AD DS richiede un trust tra foreste in ingresso per il dominio gestito. Il trust deve essere creato manualmente nel dominio di Active Directory Domain Services locale e non può essere creato nel portale di Azure.

Per configurare il trust in ingresso nel dominio Active Directory Domain Services locale, completare i passaggi seguenti da una workstation di gestione per il dominio di Active Directory Domain Services locale:

1. Fare clic sul pulsante **Start | Strumenti di amministrazione | Domini e trust di Active Directory**
1. Fare clic con il pulsante destro del mouse sul dominio, ad esempio *onprem.contoso.com* e quindi scegliere **Proprietà**
1. Scegliere la scheda **Trust**, quindi **Nuova relazione di trust**
1. Immettere il nome per il dominio di Azure Active Directory Domain Services, ad esempio *aaddscontoso.com* e quindi selezionare **Avanti**
1. Selezionare l'opzione per creare un **Trust tra foreste**, quindi creare un trust **Unidirezionale: in ingresso**.
1. Scegliere di creare il trust per **Solo questo dominio**. Nel passaggio successivo si creerà il trust nel portale di Azure per il dominio gestito.
1. Scegliere di usare l'**Autenticazione estesa a tutta la foresta**, quindi immettere e confermare una password del trust. Questa stessa password verrà anche immessa nel portale di Azure nella sezione successiva.
1. Per le finestre successive lasciare le opzioni predefinite, quindi scegliere l'opzione **No, non confermare il trust in uscita**.
1. Selezionare **Finish** (Fine)

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Creare un trust tra foreste in uscita in Azure AD Domain Services

Dopo aver configurato il dominio locale di Active Directory Domain Services per risolvere il dominio gestito e un trust tra foreste in ingresso, si procederà alla creazione del trust tra foreste in uscita. Questo trust tra foreste in uscita completa la relazione di trust tra il dominio locale di Active Directory Domain Services e il dominio gestito.

Per creare il trust in uscita per il dominio gestito nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure cercare e selezionare **Azure AD Domain Services**, quindi selezionare il dominio gestito, ad esempio *aaddscontoso.com*
1. Nel menu sul lato sinistro del dominio gestito selezionare **Trust**, quindi scegliere **+ Aggiungi** per aggiungere un trust.

   > [!NOTE]
   > Se non viene visualizzata l'opzione di menu **Trust**, verificare le **Proprietà** per *Tipo di foresta*. Solo le foreste di *risorse* possono creare trust. Se il tipo di foresta è *Utente*, non è possibile creare trust. Attualmente non è possibile cambiare il tipo di foresta di un dominio gestito. È necessario eliminare e ricreare il dominio gestito come foresta di risorse.

1. Immettere un nome visualizzato che identifichi il trust, quindi il nome DNS della foresta trusted locale, ad esempio *onprem.contoso.com*
1. Specificare la stessa password del trust usata durante la configurazione il trust tra foreste in ingresso per il dominio Active Directory Domain Services locale nella sezione precedente.
1. Specificare almeno due server DNS per il dominio Active Directory Domain Services locale, ad esempio *10.1.1.4* e *10.1.1.5*
1. Quando si è pronti, fare clic su **Salva** per salvare il trust tra foreste in uscita

    ![Creare un trust tra foreste in uscita nel portale di Azure](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Convalidare l'autenticazione delle risorse

Gli scenari comuni seguenti consentono di verificare che il trust tra foreste esegua correttamente l'autenticazione degli utenti e l'accesso alle risorse:

* [Autenticazione utente locale dalla foresta di risorse Azure AD Domain Services](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Accesso alle risorse nella foresta di risorse Azure AD Domain Services usando un utente locale](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Abilitare la condivisione di file e stampanti](#enable-file-and-printer-sharing)
    * [Creare un gruppo di sicurezza e aggiungere membri](#create-a-security-group-and-add-members)
    * [Creare una condivisione file per l'accesso tra foreste](#create-a-file-share-for-cross-forest-access)
    * [Convalidare l'autenticazione tra foreste a una risorsa](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticazione utente locale dalla foresta di risorse Azure AD Domain Services

È necessario che la macchina virtuale Windows Server sia stata aggiunta al dominio gestito. Usare questa macchina virtuale per verificare che l'utente locale possa eseguire l'autenticazione in una macchina virtuale. Se necessario, [creare una macchina virtuale Windows e aggiungerla al dominio gestito][join-windows-vm].

1. Connettersi alla macchina virtuale Windows Server aggiunta alla foresta di risorse Azure AD Domain Services usando [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) e le credenziali di amministratore di Azure AD Domain Services.
1. Aprire un prompt dei comandi e usare il comando `whoami` per visualizzare il nome distinto dell'utente attualmente autenticato:

    ```console
    whoami /fqdn
    ```

1. Usare il comando `runas` per eseguire l'autenticazione come utente dal dominio locale. Nel comando seguente sostituire `userUpn@trusteddomain.com` con il nome dell'entità utente di un utente dal dominio locale attendibile. Il comando richiede l'immissione della password dell'utente:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se l'autenticazione ha esito positivo, verrà aperto un nuovo prompt dei comandi. Il titolo del nuovo prompt dei comandi include `running as userUpn@trusteddomain.com`.
1. Usare `whoami /fqdn` nel nuovo prompt dei comandi per visualizzare il nome distinto dell'utente autenticato da Active Directory locale.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Accesso alle risorse nella foresta di risorse Azure AD Domain Services usando un utente locale

Usando la macchina virtuale Windows Server aggiunta alla foresta di risorse Azure AD Domain Services, è possibile testare lo scenario in cui gli utenti possono accedere alle risorse ospitate nella foresta di risorse quando eseguono l'autenticazione da computer nel dominio locale con utenti del dominio locale. Gli esempi seguenti illustrano come creare e testare diversi scenari comuni.

#### <a name="enable-file-and-printer-sharing"></a>Abilitare la condivisione di file e stampanti

1. Connettersi alla macchina virtuale Windows Server aggiunta alla foresta di risorse Azure AD Domain Services usando [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) e le credenziali di amministratore di Azure AD Domain Services.

1. Aprire **Impostazioni di Windows**, quindi cercare e selezionare **Centro connessioni di rete e condivisione**.
1. Scegliere l'opzione per **Modifica impostazioni di condivisione avanzate**.
1. In **Profilo di dominio** selezionare **Attiva condivisione file e stampanti** e quindi **Salva modifiche**.
1. Chiudere **Centro connessioni di rete e condivisione**.

#### <a name="create-a-security-group-and-add-members"></a>Creare un gruppo di sicurezza e aggiungere membri

1. Aprire **Utenti e computer di Active Directory**.
1. Fare clic con il pulsante destro del mouse sul nome di dominio, scegliere **Nuovo**, quindi selezionare **Unità organizzativa**.
1. Nella casella del nome digitare *LocalObjects* e quindi selezionare **OK**.
1. Scegliere e fare clic con il pulsante destro del mouse su **LocalObjects** nel riquadro di spostamento. Scegliere **Nuovo** e quindi **Gruppo**.
1. Digitare *FileServerAccess* nella casella **Nome gruppo**. Per **Ambito gruppo** selezionare **Locale dominio**, quindi scegliere **OK**.
1. Nel riquadro del contenuto fare doppio clic su **FileServerAccess**. Selezionare **Membri**, scegliere **Aggiungi**, quindi selezionare **Percorsi**.
1. Selezionare l'istanza di Active Directory locale dalla visualizzazione **Percorsi**, quindi scegliere **OK**.
1. Digitare *Utenti del dominio* nella casella **Immettere i nomi degli oggetti da selezionare**. Selezionare **Controlla nomi**, fornire le credenziali per l'istanza di Active Directory locale, quindi selezionare **OK**.

    > [!NOTE]
    > È necessario fornire le credenziali perché la relazione di trust è unidirezionale. Questo significa che gli utenti del dominio gestito di Azure Active Directory Domain Services non possono accedere alle risorse o cercare utenti o gruppi nel dominio trusted (locale).

1. Il gruppo **Utenti del dominio** dell'istanza di Active Directory locale deve essere membro del gruppo **FileServerAccess**. Selezionare **OK** per salvare il gruppo e chiudere la finestra.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Creare una condivisione file per l'accesso tra foreste

1. Nella macchina virtuale Windows Server aggiunta alla foresta di risorse Azure AD Domain Services creare una cartella e specificare un nome, ad esempio *CrossForestShare*.
1. Fare clic con il pulsante destro del mouse sulla nuova cartella e scegliere **Proprietà**.
1. Selezionare la scheda **Sicurezza** e quindi scegliere **Modifica**.
1. Nella finestra di dialogo *Autorizzazioni per CrossForestShare* selezionare **Aggiungi**.
1. Digitare *FileServerAccess* in **Immettere i nomi degli oggetti da selezionare**, quindi selezionare **OK**.
1. Selezionare *FileServerAccess* dall'elenco **Gruppi o nomi utente**. Nell'elenco **Autorizzazioni per FileServerAccess** scegliere *Consenti* per le autorizzazioni di **modifica** e **scrittura**, quindi selezionare **OK**.
1. Selezionare la scheda **Condivisione**, quindi scegliere **Condivisione avanzata**
1. Scegliere **Condividi la cartella**, quindi immettere un nome facile da ricordare per la condivisione file in **Nome condivisione**, ad esempio *CrossForestShare*.
1. Selezionare **Autorizzazioni**. Nell'elenco **Autorizzazioni per Everyone** scegliere **Consenti** per l'autorizzazione **Modifica**.
1. Selezionare **OK** due volte e quindi **Chiudi**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Convalidare l'autenticazione tra foreste a una risorsa

1. Accedere a un computer Windows aggiunto all'istanza di Active Directory locale usando un account utente di Active Directory locale.
1. Con **Esplora risorse** connettersi alla condivisione creata usando il nome host completo e la condivisione, ad esempio `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Per convalidare l'autorizzazione di scrittura, fare clic con il pulsante destro del mouse sulla cartella, scegliere **Nuovo** e quindi selezionare **Documento di testo**. Usare il nome predefinito **Nuovo documento di testo**.

    Se le autorizzazioni di scrittura sono impostate correttamente, verrà creato un nuovo documento di testo. Nei passaggi seguenti verrà quindi aperto, modificato ed eliminato il file in base alle esigenze.
1. Per convalidare l'autorizzazione di lettura, aprire il **Nuovo documento di testo**.
1. Per convalidare l'autorizzazione di modifica, aggiungere testo al file e chiudere **Blocco note**. Quando viene richiesto di salvare le modifiche, scegliere **Salva**.
1. Per convalidare l'autorizzazione di eliminazione, fare clic con il pulsante destro del mouse su **Nuovo documento di testo** e scegliere **Elimina**. Scegliere **Sì** per confermare l'eliminazione del file.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il DNS in un ambiente Active Directory Domain Services locale per supportare la connettività ad Azure AD Domain Services
> * Creare un trust tra foreste in ingresso unidirezionale in un ambiente Active Directory Domain Services locale
> * Creare un trust tra foreste in uscita unidirezionale in Azure AD Domain Services
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Per informazioni più concettuali sui tipi di foresta in Azure AD Domain Services, vedere [Che cosa sono le foreste di risorse?][concepts-forest] e [Come funzionano i trust tra foreste in Azure Active Directory Domain Services?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md
