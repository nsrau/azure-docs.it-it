---
title: 'Esercitazione: creare un trust tra foreste in Azure AD Domain Services | Microsoft Docs'
description: Informazioni su come creare una foresta in uscita unidirezionale a un dominio di servizi di dominio Active Directory locale nell'portale di Azure per Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: f861303b7f3bc8d37caf6da0eaf2f4cef4b36ee5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233597"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Esercitazione: creare un trust tra foreste in uscita per un dominio locale in Azure Active Directory Domain Services (anteprima)

Negli ambienti in cui non è possibile sincronizzare gli hash delle password oppure si hanno utenti che accedono esclusivamente tramite smart card in modo che non conoscano la password, è possibile usare una foresta di risorse in Azure Active Directory Domain Services (AD DS). Una foresta di risorse utilizza un trust in uscita unidirezionale da Azure AD DS a uno o più ambienti di servizi di dominio Active Directory locali. Questa relazione di trust consente a utenti, applicazioni e computer di eseguire l'autenticazione in un dominio locale dal dominio gestito Azure AD DS. Le foreste di risorse Azure AD DS sono attualmente in anteprima.

![Diagramma del trust tra foreste da Azure AD DS a servizi di dominio Active Directory locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare DNS in un ambiente AD DS locale per supportare la connettività Azure AD DS
> * Creazione di un trust tra foreste in ingresso unidirezionale in un ambiente Active Directory Domain Services locale
> * Creazione di un trust tra foreste in uscita unidirezionale in Azure AD DS
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito Azure Active Directory Domain Services creato con una foresta di risorse e configurato nel tenant del Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione viene creato e configurato il trust tra foreste in uscita da Azure AD DS usando il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerazioni sulla rete

La rete virtuale che ospita la foresta di risorse Azure AD DS necessita della connettività di rete per la Active Directory locale. Anche le applicazioni e i servizi necessitano di connettività di rete alla rete virtuale che ospita la foresta di risorse Azure AD DS. La connettività di rete alla foresta delle risorse Azure AD DS deve essere sempre attiva e stabile in caso contrario gli utenti potrebbero non essere in grado di autenticare o accedere alle risorse.

Prima di configurare un trust tra foreste in Azure AD DS, assicurarsi che la rete tra Azure e l'ambiente locale soddisfi i requisiti seguenti:

* Usare indirizzi IP privati. Non fare affidamento su DHCP con l'assegnazione di indirizzi IP dinamici.
* Evitare la sovrapposizione degli spazi di indirizzi IP per consentire il peering di rete virtuale e il routing per la comunicazione corretta tra Azure e l'ambiente locale.
* Una rete virtuale di Azure richiede una subnet del gateway per configurare una connessione VPN da sito a sito (S2S) o ExpressRoute
* Creare subnet con un numero sufficiente di indirizzi IP per supportare lo scenario.
* Assicurarsi che Azure AD DS disponga di una propria subnet, non condividere la subnet della rete virtuale con le macchine virtuali e i servizi dell'applicazione.
* Le reti virtuali con peering non sono transitive.
    * I peering di rete virtuale di Azure devono essere creati tra tutte le reti virtuali per cui si vuole usare il Azure AD trust tra foreste di servizi di dominio Active Directory per l'ambiente di servizi di dominio Active Directory locale.
* Fornire connettività di rete continua alla foresta Active Directory locale. Non usare connessioni su richiesta.
* Assicurarsi che sia presente la risoluzione dei nomi continua (DNS) tra il nome della foresta delle risorse Azure AD DS e il nome della foresta Active Directory locale.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurare DNS nel dominio locale

Per risolvere correttamente il dominio gestito di Azure AD DS dall'ambiente locale, potrebbe essere necessario aggiungere server d'inoltri ai server DNS esistenti. Se l'ambiente locale non è stato configurato per comunicare con il dominio gestito di Azure AD DS, completare i passaggi seguenti da una workstation di gestione per il dominio di servizi di dominio Active Directory locale:

1. Selezionare **Start | Strumenti di amministrazione | DNS**
1. Fare clic con il pulsante destro del mouse su server DNS, ad esempio *myAD01*, selezionare **Proprietà** .
1. Scegliere **server d'avanzamento**, quindi **modifica** per aggiungere altri server d'inoltri.
1. Aggiungere gli indirizzi IP del dominio gestito Azure AD DS, ad esempio *10.0.1.4* e *10.0.1.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Creare un trust tra foreste in ingresso nel dominio locale

Il dominio di servizi di dominio Active Directory locale richiede un trust tra foreste in ingresso per il dominio gestito Azure AD DS. Questa relazione di trust deve essere creata manualmente nel dominio di servizi di dominio Active Directory locale e non può essere creata dal portale di Azure.

Per configurare il trust in ingresso nel dominio servizi di dominio Active Directory locale, completare i passaggi seguenti da una workstation di gestione per il dominio di servizi di dominio Active Directory locale:

1. Selezionare **Start | Strumenti di amministrazione | Domini Active Directory e trust**
1. Fare clic con il pulsante destro del mouse su dominio, ad esempio *OnPrem.contoso.com*, selezionare **Proprietà**
1. Scegliere la scheda **trust** , quindi **nuovo trust**
1. Immettere il nome Azure AD nome di dominio DS, ad esempio *aadds.contoso.com*, quindi fare clic su **Avanti** .
1. Selezionare l'opzione per creare un **trust tra foreste**, quindi creare un trust in **ingresso unidirezionale** .
1. Scegliere di creare il trust **solo per questo dominio**. Nel passaggio successivo si creerà il trust nel portale di Azure per il dominio gestito Azure AD DS.
1. Scegliere di usare **l'autenticazione a livello di foresta**, quindi immettere e confermare una password di attendibilità. Questa stessa password viene immessa anche nella portale di Azure nella sezione successiva.
1. Scorrere le finestre successive con le opzioni predefinite, quindi scegliere l'opzione **No, non confermare il trust in uscita**.
1. Selezionare **Finish** (Fine)

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Creare un trust tra foreste in uscita in Azure AD DS

Con il dominio di servizi di dominio Active Directory locale configurato per risolvere il dominio gestito Azure AD DS e un trust tra foreste in ingresso creato, ora è stato creato il trust tra foreste in uscita. Questo trust tra foreste in uscita completa la relazione di trust tra il dominio di servizi di dominio Active Directory locale e il dominio gestito Azure AD DS.

Per creare la relazione di trust in uscita per il dominio gestito Azure AD DS nel portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**, quindi selezionare il dominio gestito, ad esempio *aadds.contoso.com*
1. Dal menu sul lato sinistro del dominio gestito di Azure AD DS selezionare **trust**, quindi scegliere **+ Aggiungi** un trust.
1. Immettere un nome visualizzato che identifichi il trust, quindi il nome DNS della foresta trusted locale, ad esempio *OnPrem.contoso.com*
1. Specificare la stessa password di trust utilizzata quando si configura il trust tra foreste in ingresso per il dominio servizi di dominio Active Directory locale nella sezione precedente.
1. Fornire almeno due server DNS per il dominio AD DS locale, ad esempio *10.0.2.4* e *10.0.2.5*
1. Quando è pronto, **salvare** il trust tra foreste in uscita

    [Creare un trust tra foreste in uscita nella portale di Azure](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Convalida autenticazione risorse

Gli scenari comuni seguenti consentono di verificare che il trust tra foreste esegua correttamente l'autenticazione degli utenti e l'accesso alle risorse:

* [Autenticazione utente locale dalla foresta di risorse Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Accedere alle risorse nella foresta di risorse Azure AD DS usando un utente locale](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Abilita condivisione file e stampanti](#enable-file-and-printer-sharing)
    * [Creare un gruppo di sicurezza e aggiungere membri](#create-a-security-group-and-add-members)
    * [Creare una condivisione file per l'accesso tra foreste](#create-a-file-share-for-cross-forest-access)
    * [Convalidare l'autenticazione tra foreste a una risorsa](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticazione utente locale dalla foresta di risorse Azure AD DS

È necessario che la macchina virtuale Windows Server sia stata aggiunta al dominio delle risorse Azure AD DS. Usare questa macchina virtuale per verificare che l'utente locale possa eseguire l'autenticazione in una macchina virtuale.

1. Connettersi alla macchina virtuale Windows Server aggiunta alla foresta delle risorse Azure AD DS usando Desktop remoto e le credenziali di amministratore di Azure AD DS. Se viene ricevuto un errore di Autenticazione a livello di rete (NLA), controllare che l'account utente usato non sia un account utente di dominio.

    > [!NOTE]
    > Per connettersi in modo sicuro alle macchine virtuali Unite a Azure AD Domain Services, è possibile usare il [servizio host di Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) in aree di Azure supportate.

1. Aprire un prompt dei comandi e usare il comando `whoami` per visualizzare il nome distinto dell'utente attualmente autenticato:

    ```console
    whoami /fqdn
    ```

1. Usare il comando `runas` per eseguire l'autenticazione come utente dal dominio locale. Nel comando seguente sostituire `userUpn@trusteddomain.com` con l'UPN di un utente dal dominio locale attendibile. Il comando richiede la password dell'utente:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se l'autenticazione ha esito positivo, viene aperto un nuovo prompt dei comandi. Il titolo del nuovo prompt dei comandi include `running as userUpn@trusteddomain.com`.
1. Usare `whoami /fqdn` nel prompt dei comandi nuovo per visualizzare il nome distinto dell'utente autenticato dall'Active Directory locale.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Accedere alle risorse nella foresta di risorse Azure AD DS usando un utente locale

Usando la macchina virtuale Windows Server aggiunta alla foresta delle risorse Azure AD DS, è possibile testare lo scenario in cui gli utenti possono accedere alle risorse ospitate nella foresta delle risorse quando eseguono l'autenticazione da computer nel dominio locale con gli utenti del dominio locale. Gli esempi seguenti illustrano come creare e testare diversi scenari comuni.

#### <a name="enable-file-and-printer-sharing"></a>Abilita condivisione file e stampanti

1. Connettersi alla macchina virtuale Windows Server aggiunta alla foresta delle risorse Azure AD DS usando Desktop remoto e le credenziali di amministratore di Azure AD DS. Se viene ricevuto un errore di Autenticazione a livello di rete (NLA), controllare che l'account utente usato non sia un account utente di dominio.

    > [!NOTE]
    > Per connettersi in modo sicuro alle macchine virtuali Unite a Azure AD Domain Services, è possibile usare il [servizio host di Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) in aree di Azure supportate.

1. Aprire **impostazioni di Windows**, quindi cercare e selezionare **Centro rete e condivisione**.
1. Scegliere l'opzione Modifica impostazioni di **condivisione avanzate** .
1. Sotto il **profilo di dominio**selezionare **Attiva condivisione file e stampanti** , quindi **salvare le modifiche**.
1. Chiudere **Centro rete e condivisione**.

#### <a name="create-a-security-group-and-add-members"></a>Creare un gruppo di sicurezza e aggiungere membri

1. Aprire **Utenti e computer di Active Directory**.
1. Fare clic con il pulsante destro del mouse sul nome del dominio, scegliere **nuovo**, quindi selezionare **unità organizzativa**.
1. Nella casella nome digitare *LocalObjects*e quindi fare clic su **OK**.
1. Selezionare e fare clic con il pulsante destro del mouse su **LocalObjects** nel riquadro di spostamento. Selezionare **nuovo** e quindi **gruppo**.
1. Digitare *FileServerAccess* nella casella **nome gruppo** . Per l' **ambito del gruppo**, selezionare **dominio locale**, quindi scegliere **OK**.
1. Nel riquadro del contenuto fare doppio clic su **FileServerAccess**. Selezionare **membri**, scegliere **Aggiungi**, quindi selezionare **percorsi**.
1. Selezionare il Active Directory locale dalla visualizzazione **percorso** , quindi scegliere **OK**.
1. Digitare *Domain Users* nella casella **immettere i nomi degli oggetti da selezionare** . Selezionare **Controlla nomi**, fornire le credenziali per la Active Directory locale e quindi fare clic su **OK**.

    > [!NOTE]
    > È necessario fornire le credenziali perché la relazione di trust è unidirezionale. Questo significa che gli utenti di Azure AD DS non possono accedere alle risorse o cercare utenti o gruppi nel dominio trusted (locale).

1. Il gruppo **Domain Users** del Active Directory locale deve essere un membro del gruppo **FileServerAccess** . Selezionare **OK** per salvare il gruppo e chiudere la finestra.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Creare una condivisione file per l'accesso tra foreste

1. Nella macchina virtuale Windows Server aggiunta alla foresta delle risorse Azure AD DS creare una cartella e specificare un nome, ad esempio *CrossForestShare*.
1. Fare clic con il pulsante destro del mouse sulla cartella e scegliere **Proprietà**.
1. Selezionare la scheda **sicurezza** , quindi scegliere **modifica**.
1. Nella finestra di dialogo *autorizzazioni per CrossForestShare* selezionare **Aggiungi**.
1. Digitare *FileServerAccess* in **immettere i nomi degli oggetti da selezionare**, quindi fare clic su **OK**.
1. Selezionare *FileServerAccess* nell'elenco **gruppi o nomi utente** . Nell'elenco **autorizzazioni per FileServerAccess** scegliere *Consenti* per le autorizzazioni **modifica** e **scrittura** , quindi fare clic su **OK**.
1. Selezionare la scheda **condivisione** , quindi scegliere **condivisione avanzata...**
1. Scegliere **Condividi questa cartella**, quindi immettere un nome memorabile per la condivisione file in **nome condivisione** , ad esempio *CrossForestShare*.
1. Selezionare **autorizzazioni**. Nell'elenco **autorizzazioni per Everyone** scegliere **Consenti** per l'autorizzazione **modifica** .
1. Fare clic su **OK** due volte e quindi su **Chiudi**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Convalidare l'autenticazione tra foreste a una risorsa

1. Accedere a un computer Windows aggiunto al Active Directory locale usando un account utente della Active Directory locale.
1. Utilizzando **Esplora risorse**, connettersi alla condivisione creata utilizzando il nome host completo e la condivisione, ad esempio `\\fs1.aadds.contoso.com\CrossforestShare`.
1. Per convalidare l'autorizzazione di scrittura, fare clic con il pulsante destro del mouse nella cartella, scegliere **nuovo**, quindi selezionare **documento di testo**. Usare il nome predefinito **nuovo documento di testo**.

    Se le autorizzazioni di scrittura sono impostate correttamente, viene creato un nuovo documento di testo. Nei passaggi seguenti verrà quindi aperto, modificato ed eliminato il file in base alle esigenze.
1. Per convalidare l'autorizzazione di lettura, aprire **nuovo documento di testo**.
1. Per convalidare l'autorizzazione di modifica, aggiungere testo al file e chiudere il **blocco note**. Quando viene richiesto di salvare le modifiche, scegliere **Salva**.
1. Per convalidare l'autorizzazione DELETE, fare clic con il pulsante destro del mouse su **nuovo documento testo** e scegliere **Elimina**. Scegliere **Sì** per confermare l'eliminazione del file.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare DNS in un ambiente AD DS locale per supportare la connettività Azure AD DS
> * Creazione di un trust tra foreste in ingresso unidirezionale in un ambiente Active Directory Domain Services locale
> * Creazione di un trust tra foreste in uscita unidirezionale in Azure AD DS
> * Testare e convalidare la relazione di trust per l'autenticazione e l'accesso alle risorse

Per informazioni più concettuali sui tipi di foresta in Azure AD DS, vedere [che cosa sono le foreste di risorse?][concepts-forest] e [come funzionano i trust tra foreste in Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md