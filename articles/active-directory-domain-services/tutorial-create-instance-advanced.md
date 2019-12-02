---
title: Esercitazione - Creare un'istanza di Azure Active Directory Domain Services | Microsoft Docs
description: Questa esercitazione illustra come creare e configurare un'istanza di Azure Active Directory Domain Services e specificare le opzioni di configurazione avanzate tramite il portale di Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 334a5c3c76f1ebaf4c8c36020110ef9c0bcc8d69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208714"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Esercitazione: Creare e configurare un'istanza di Azure Active Directory Domain Services con opzioni di configurazione avanzate

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. È possibile utilizzare questi servizi di dominio senza distribuire, gestire e applicare patch manualmente ai controller di dominio. Azure AD DS si integra con il tenant di Azure AD esistente. Questa integrazione consente agli utenti di accedere con le proprie credenziali aziendali ed è possibile usare i gruppi e gli account utente esistenti per proteggere l'accesso alle risorse.

È possibile [creare un dominio gestito usando le opzioni di configurazione predefinite][tutorial-create-instance] per la connessione di rete e la sincronizzazione oppure definire manualmente queste impostazioni. Questa esercitazione illustra come definire queste opzioni di configurazione avanzate per creare e configurare un'istanza di Azure AD DS tramite il portale di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare le impostazioni di DNS e della rete virtuale per un dominio gestito
> * Creare un'istanza di Azure Active Directory Domain Services
> * Aggiungere utenti amministrativi alla gestione del dominio
> * Abilitare la sincronizzazione dell'hash delle password

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Per abilitare Azure AD DS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Per creare le risorse di Azure AD DS richieste, sono necessari privilegi di *collaboratore* nella sottoscrizione di Azure.

Nonostante non sia necessario per Azure Active Directory Domain Services, è consigliabile [configurare la reimpostazione della password self-service][configure-sspr] per il tenant di Azure AD. Gli utenti possono modificare la password senza questa funzionalità, ma la reimpostazione della password self-service è utile se dimenticano la password e devono reimpostarla.

> [!IMPORTANT]
> Dopo aver creato un dominio gestito Azure Active Directory Domain Services, non è possibile spostare l'istanza in un gruppo di risorse, una rete virtuale o una sottoscrizione diversa. Quando si distribuisce l'istanza di Azure Active Directory Domain Services, prestare attenzione a selezionare la sottoscrizione, il gruppo di risorse, l'area e la rete virtuale più appropriati.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione viene creata e configurata l'istanza di Azure AD DS tramite il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Creare un'istanza e configurare le impostazioni di base

Per avviare la procedura guidata **Abilita Azure AD Domain Services**, seguire questa procedura:

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Immettere *Servizi di dominio* nella barra di ricerca, quindi scegliere *Azure AD Domain Services* nei suggerimenti per la ricerca.
1. Nella pagina Azure AD Domain Services selezionare **Crea**. Viene avviata la procedura guidata **Abilita Azure AD Domain Services**.
1. Selezionare la **Sottoscrizione** di Azure in cui si vuole creare il dominio gestito.
1. Selezionare il **Gruppo di risorse** a cui deve appartenere il dominio gestito. Scegliere **Crea nuovo** oppure selezionare un gruppo di risorse esistente.

Quando si crea un'istanza di Azure AD DS, si specifica un nome DNS. Di seguito sono riportate alcune considerazioni per la scelta di questo nome DNS:

* **Nome di dominio predefinito:** per impostazione predefinita, viene usato il nome di dominio predefinito della directory, con il suffisso *.onmicrosoft.com*. Se si vuole abilitare l'accesso LDAP sicuro al dominio gestito tramite Internet, non è possibile creare un certificato digitale per proteggere la connessione con il dominio predefinito. Microsoft è proprietaria del dominio *.onmicrosoft.com*, quindi un'autorità di certificazione (CA) pubblica non emetterà un certificato.
* **Nomi di dominio personalizzati:** l'approccio più comune è quello di specificare un nome di dominio personalizzato, in genere uno di cui si è già proprietari ed è instradabile. Se si usa un dominio personalizzato instradabile, il traffico può fluire correttamente in base alle esigenze per supportare le applicazioni.
* **Suffissi di dominio non instradabili:** è in genere consigliabile evitare un suffisso del nome di dominio non instradabile, ad esempio *contoso.local*. Il suffisso *.local* non è instradabile e può causare problemi con la risoluzione DNS.

> [!TIP]
> Se si crea un nome di dominio personalizzato, prestare attenzione agli spazi dei nomi DNS esistenti. È consigliabile includere un prefisso univoco per il nome di dominio. Se, ad esempio, il nome radice DNS è *contoso.com*, creare un dominio gestito Azure Active Directory Domain Services con il nome di dominio personalizzato *corp.contoso.com* o *ds.contoso.com*. In un ambiente ibrido con un ambiente Active Directory Domain Services locale, questi prefissi potrebbero essere già in uso. Usare un prefisso univoco per Azure Active Directory Domain Services.
>
> È possibile usare il nome DNS radice per il dominio gestito di Azure Active Directory Domain Services, ma potrebbe essere necessario creare alcuni record DNS aggiuntivi per altri servizi nell'ambiente in uso. Ad esempio, se si esegue un server Web che ospita un sito con il nome DNS radice, possono essere presenti conflitti di denominazione che richiedono voci DNS aggiuntive.
>
> In queste esercitazioni e articoli sulle procedure come esempio breve viene usato il dominio personalizzato *contoso.com*. In tutti i comandi specificare il proprio nome di dominio, che può includere un prefisso univoco.
>
> Per altre informazioni, vedere [Selezione di un prefisso di denominazione per il dominio][naming-prefix].

Si applicano anche le seguenti restrizioni relative ai nomi DNS:

* **Limitazioni dei prefissi di dominio:** non è possibile creare un dominio gestito con un prefisso più lungo di 15 caratteri. Il prefisso del nome di dominio specificato (ad esempio, *contoso* nel nome di dominio *contoso.com*) può essere composto da un massimo di 15 caratteri.
* **Conflitti nei nomi di rete:** il nome di dominio DNS per il dominio gestito non deve essere già presente nella rete virtuale. In particolare, verificare i seguenti scenari che potrebbero causare un conflitto di nomi:
    * È già presente un dominio di Active Directory con lo stesso nome di dominio DNS nella rete virtuale.
    * La rete virtuale in cui si intende abilitare il dominio gestito ha una connessione VPN alla rete locale. In questo caso, verificare che non sia presente un dominio con lo stesso nome di dominio DNS nella rete locale.
    * Esiste un servizio cloud di Azure con lo stesso nome della rete virtuale di Azure.

Completare i campi della finestra *Informazioni di base* del portale di Azure per creare un'istanza di Azure AD DS:

1. Immettere un **nome di dominio DNS** per il dominio gestito, prendendo in considerazione i punti precedenti.
1. Scegliere la **Località** di Azure in cui deve essere creato il dominio gestito. Se si sceglie un'area che supporta le zone di disponibilità, le risorse di Azure AD DS vengono distribuite in più zone per garantire maggiore ridondanza.

    Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate.

    Non è necessario eseguire alcuna operazione di configurazione per distribuire Azure AD DS in più zone. La piattaforma Azure gestisce automaticamente la distribuzione delle risorse nelle zone. Per altre informazioni e per consultare la disponibilità delle zone, vedere [Informazioni sulle zone di disponibilità di Azure][availability-zones].

1. Una *foresta* è un costrutto logico usato da Active Directory Domain Services per raggruppare uno o più domini. Per impostazione predefinita, viene creato un dominio gestito di Azure AD DS come foresta *Utente*. Questo tipo di foresta sincronizza tutti gli oggetti di Azure AD, inclusi tutti gli account utente creati in un ambiente AD DS locale. Una foresta *Risorsa* sincronizza solo gli utenti e i gruppi creati direttamente in Azure AD. Le foreste Risorsa sono attualmente disponibili in anteprima. Per altre informazioni sulle foreste *Risorsa*, inclusi i motivi per cui usarle e come creare trust tra foreste con domini di AD DS locali, vedere [Panoramica delle foreste di risorse di Azure AD DS][resource-forests].

    Per questa esercitazione, scegliere di creare una foresta *Utente*.

    ![Configurare le impostazioni di base per un'istanza di Azure AD Domain Services](./media/tutorial-create-instance-advanced/basics-window.png)

1. Per configurare manualmente opzioni aggiuntive, scegliere **Avanti: Rete**. In caso contrario, selezionare **Rivedi e crea** per accettare le opzioni di configurazione predefinite e quindi passare alla sezione per [distribuire il dominio gestito](#deploy-the-managed-domain). Quando si sceglie questa opzione di creazione, vengono configurate le impostazioni predefinite seguenti:

* Crea una rete virtuale denominata *aadds-vnet* che usa l'intervallo di indirizzi IP *10.0.1.0/24*.
* Crea una subnet denominata *aadds-subnet* che usa l'intervallo di indirizzi IP *10.0.1.0/24*.
* Sincronizza *tutti* gli utenti di Azure AD nel dominio gestito di Azure AD DS.

## <a name="create-and-configure-the-virtual-network"></a>Creare e configurare la rete virtuale

Per fornire connettività, sono necessarie una rete virtuale di Azure e una subnet dedicata. Azure AD DS è abilitato in questa subnet di rete virtuale. In questa esercitazione viene creata una rete virtuale, ma è anche possibile scegliere di usarne una esistente. In entrambi gli approcci è necessario creare una subnet dedicata per l'uso da parte di Azure AD DS.

Di seguito sono riportate alcune considerazioni relative alla subnet della rete virtuale dedicata:

* Per supportare le risorse di Azure AD DS, la subnet deve avere almeno 3-5 indirizzi IP disponibili nell'intervallo di indirizzi.
* Non selezionare la subnet *Gateway* per la distribuzione di Azure AD DS. La distribuzione di Azure AD DS in una subnet *Gateway* non è supportata.
* Non distribuire eventuali altre macchine virtuali in questa subnet. Le applicazioni e le macchine virtuali usano spesso gruppi di sicurezza di rete per proteggere la connettività. L'esecuzione di questi carichi di lavoro in una subnet separata consente di applicare tali gruppi di sicurezza di rete senza compromettere la connettività al dominio gestito.
* Non è possibile spostare il dominio gestito in una rete virtuale diversa dopo l'abilitazione di Azure AD DS.

Per altre informazioni su come pianificare e configurare la rete virtuale, vedere le [considerazioni sulla rete per Azure Active Directory Domain Services][network-considerations].

Completare i campi della finestra *Rete* come indicato di seguito:

1. Nella pagina **Rete** scegliere una rete virtuale per distribuire Azure AD DS nel menu a discesa oppure selezionare **Crea nuova**.
    1. Se si sceglie di creare una rete virtuale, immettere un nome per la rete virtuale, ad esempio *myVnet*, quindi specificare un intervallo di indirizzi, ad esempio *10.0.1.0/24*.
    1. Creare una subnet dedicata con un nome chiaro, ad esempio *DomainServices*. Specificare un intervallo di indirizzi, ad esempio *10.0.1.0/24*.

    ![Creare una rete virtuale e una subnet per l'uso con Azure AD Domain Services](./media/tutorial-create-instance-advanced/create-vnet.png)

    Assicurarsi di scegliere un intervallo di indirizzi all'interno del proprio intervallo di indirizzi IP privato. Gli intervalli di indirizzi IP di cui non si è proprietari che si trovano nello spazio di indirizzi pubblici generano errori all'interno di Azure AD DS.

1. Selezionare una subnet della rete virtuale, ad esempio *DomainServices*.
1. Quando si è pronti, scegliere **Avanti: Amministrazione**.

## <a name="configure-an-administrative-group"></a>Configurare un gruppo amministrativo

Per la gestione del dominio di Azure AD DS viene usato un gruppo amministrativo speciale denominato *Amministratori di AAD DC*. Ai membri di questo gruppo vengono concesse autorizzazioni amministrative per le VM aggiunte al dominio gestito. Nelle VM aggiunte al dominio questo gruppo viene aggiunto al gruppo di amministratori locali. Inoltre, i membri di questo gruppo possono usare Desktop remoto per connettersi in remoto alle VM aggiunte al dominio.

Per i domini gestiti tramite Azure AD DS non vengono concesse autorizzazioni di *amministratore di dominio* o *amministratore dell'organizzazione*. Queste autorizzazioni sono riservate dal servizio e non vengono rese disponibili per gli utenti all'interno del tenant. Al contrario, il gruppo *Amministratori di AAD DC* consente di eseguire alcune operazioni con privilegi. Queste operazioni includono l'aggiunta di computer al dominio, l'appartenenza al gruppo di amministrazione nelle VM aggiunte al dominio e la configurazione di Criteri di gruppo.

La procedura guidata crea automaticamente il gruppo *Amministratori di AAD DC* nella directory di Azure AD. Se si dispone di un gruppo esistente con questo nome nella directory di Azure AD, la procedura guidata seleziona questo gruppo. Facoltativamente, è possibile scegliere di aggiungere altri utenti al gruppo *Amministratori di AAD DC* durante il processo di distribuzione. Questi passaggi possono essere completati in un secondo momento.

1. Per aggiungere altri utenti al gruppo *Amministratori di AAD DC*, selezionare **Gestisci l'appartenenza ai gruppi**.

    ![Configurare l'appartenenza al gruppo Amministratori di AAD DC](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selezionare il pulsante **Aggiungi membri**, quindi cercare e selezionare gli utenti dalla directory di Azure AD. Ad esempio, cercare il proprio account e aggiungerlo al gruppo *Amministratori di AAD DC*.
1. Se necessario, modificare o aggiungere altri destinatari per le notifiche quando nel dominio gestito di Azure AD DS sono presenti avvisi che richiedono attenzione.
1. Quando si è pronti, scegliere **Avanti: Sincronizzazione**.

## <a name="configure-synchronization"></a>Configurare la sincronizzazione

Azure AD DS consente di sincronizzare *tutti* gli utenti e i gruppi disponibili in Azure AD oppure di eseguire una sincronizzazione *con ambito* solo di gruppi specifici. Se si sceglie di sincronizzare *tutti* gli utenti e i gruppi, non sarà più possibile scegliere di eseguire solo una sincronizzazione con ambito. Per altre informazioni sulla sincronizzazione con ambito, vedere [Sincronizzazione con ambito in Azure AD Domain Services][scoped-sync].

1. Per questa esercitazione, scegliere di sincronizzare **tutti** gli utenti e i gruppi. Questa scelta di sincronizzazione è l'opzione predefinita.

    ![Eseguire una sincronizzazione completa di utenti e gruppi da Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selezionare **Rivedi e crea**.

## <a name="deploy-the-managed-domain"></a>Distribuire il dominio gestito

Nella pagina **Riepilogo** della procedura guidata controllare le impostazioni di configurazione per il dominio gestito. È possibile tornare in qualsiasi passaggio precedente della procedura guidata per apportare modifiche. Per ridistribuire in modo coerente un dominio gestito di Azure AD DS in un tenant di Azure AD diverso usando queste opzioni di configurazione, è anche possibile **scaricare un modello per l'automazione**.

1. Per creare il dominio gestito, selezionare **Crea**. Viene visualizzata una nota in cui si specifica che alcune opzioni di configurazione, ad esempio il nome DNS o la rete virtuale, non possono essere modificate dopo la creazione del servizio di Azure AD DS gestito. Per continuare, selezionare **OK**.
1. Il processo di provisioning del dominio gestito può richiedere fino a un'ora. Nel portale viene visualizzata una notifica che mostra lo stato di avanzamento della distribuzione di Azure AD DS. Selezionare la notifica per visualizzare lo stato di avanzamento dettagliato del processo di distribuzione.

    ![Notifica nel portale di Azure sulla distribuzione in corso](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selezionare il gruppo di risorse, ad esempio *myResourceGroup*, quindi scegliere l'istanza di Azure AD DS dall'elenco di risorse di Azure, ad esempio *contoso.com*. La scheda **Panoramica** indica che il dominio gestito è attualmente in fase di *Distribuzione*. Non è possibile configurare il dominio gestito fino a quando non ne è stato completato il provisioning.

    ![Stato di Domain Services durante lo stato di provisioning](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Al termine del provisioning del dominio gestito, nella scheda **Panoramica** il dominio gestito è impostato su *In esecuzione*.

    ![Stato di Domain Services dopo il provisioning](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

Il dominio gestito è associato al tenant di Azure AD. Durante il processo di provisioning, Azure AD DS crea due applicazioni aziendali denominate *Domain Controller Services* e *AzureActiveDirectoryDomainControllerServices* nel tenant di Azure AD. Queste applicazioni aziendali sono necessarie per gestire il dominio gestito Non eliminare queste applicazioni.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aggiornare le impostazioni DNS per la rete virtuale di Azure

Con la distribuzione di Azure AD DS completata, è ora possibile configurare la rete virtuale in modo da consentire ad altre VM e applicazioni connesse di usare il dominio gestito. Per fornire questa connettività, aggiornare le impostazioni del server DNS per la rete virtuale in modo che puntino ai due indirizzi IP in cui è stato distribuito Azure AD DS.

1. La scheda **Panoramica** per il dominio gestito mostra alcuni **passaggi di configurazione necessari**. Il primo passaggio di configurazione consiste nell'aggiornare le impostazioni del server DNS per la rete virtuale. Una volta configurate correttamente le impostazioni DNS, questo passaggio non viene più visualizzato.

    Gli indirizzi elencati sono i controller di dominio da usare nella rete virtuale. In questo esempio gli indirizzi sono *10.1.0.4* e *10.1.0.5*. In seguito questi indirizzi IP si possono trovare nella scheda **Proprietà**.

    ![Configurare le impostazioni DNS per la rete virtuale con gli indirizzi IP di Azure AD Domain Services](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Per aggiornare le impostazioni del server DNS per la rete virtuale, selezionare il pulsante **Configura**. Le impostazioni DNS vengono configurate automaticamente per la rete virtuale.

> [!TIP]
> Se è stata selezionata una rete virtuale esistente nei passaggi precedenti, le macchine virtuali connesse alla rete ottengono le nuove impostazioni DNS solo dopo un riavvio. È possibile riavviare le VM tramite il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Abilitare gli account utente per Azure AD DS

Per autenticare gli utenti nel dominio gestito, Azure AD DS necessita dell'hash delle password in un formato idoneo per l'autenticazione NTLM (NT LAN Manager) e Kerberos. Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

> [!NOTE]
> Dopo la corretta configurazione, gli hash delle password utilizzabili vengono archiviati nel dominio gestito di Azure AD DS. Se si elimina il dominio gestito di Azure AD DS, verranno eliminati anche gli hash delle password archiviati in quel momento. Le informazioni sulle credenziali sincronizzate in Azure AD non possono essere riutilizzate se in un secondo momento si crea un dominio gestito di Azure AD DS. È necessario riconfigurare la sincronizzazione degli hash delle password per archiviarli di nuovo. Le VM o gli utenti precedentemente aggiunti al domino non saranno in grado di eseguire immediatamente l'autenticazione. Azure AD deve generare e archiviare gli hash delle password nel nuovo dominio gestito di Azure AD DS. Per altre informazioni, vedere [Processo di sincronizzazione degli hash delle password per Azure AD DS e Azure AD Connect][password-hash-sync-process].

La procedura per generare e archiviare questi hash delle password è diversa per gli account utente solo cloud creati in Azure AD rispetto agli account utente sincronizzati dalla directory locale tramite Azure AD Connect. Un account utente solo cloud è un account creato nella directory di Azure AD tramite il portale di Azure o i cmdlet di Azure AD PowerShell. Questi account utente non vengono sincronizzati da una directory locale. In questa esercitazione verrà usato un account utente solo cloud di base. Per altre informazioni sui passaggi aggiuntivi necessari per usare Azure ad Connect, vedere [Sincronizzare gli hash delle password per gli account utente sincronizzati dall'istanza locale di AD con il dominio gestito][on-prem-sync].

> [!TIP]
> Se il tenant di Azure AD include una combinazione di utenti solo cloud e utenti dell'istanza locale di AD, è necessario eseguire entrambe le serie di passaggi.

Per gli account utente solo cloud, gli utenti devono cambiare le loro password prima di poter usare Azure AD DS. Con questo processo di modifica delle password, in Azure AD vengono generati e archiviati gli hash delle password per l'autenticazione Kerberos e NTLM. È possibile impostare come scadute le password per tutti gli utenti del tenant che devono usare Azure AD DS, il che forza una modifica delle password al successivo avvio, oppure chiedere a tali utenti di cambiare manualmente le loro password. Per questa esercitazione, verrà cambiata manualmente la password di un utente.

Prima che un utente possa reimpostare la propria password, è necessario che il tenant di Azure AD venga [configurato per la reimpostazione delle password self-service][configure-sspr].

Per cambiare la password, gli utenti solo cloud devono completare i passaggi seguenti:

1. Passare al Pannello di accesso di Azure AD all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Nell'angolo in alto a destra selezionare il proprio nome, quindi scegliere **Profilo** dal menu a discesa.

    ![Selezionare un profilo](./media/tutorial-create-instance-advanced/select-profile.png)

1. Nella pagina **Profilo** selezionare **Cambia password**.
1. Nella pagina **Cambia password** digitare la password esistente (precedente), quindi immetterne una nuova e confermarla.
1. Selezionare **Submit** (Invia).

Dopo la modifica, sono necessari alcuni minuti prima che la nuova password sia utilizzabile in Azure AD DS e per l'accesso ai computer aggiunti al dominio gestito.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare le impostazioni di DNS e della rete virtuale per un dominio gestito
> * Creare un'istanza di Azure Active Directory Domain Services
> * Aggiungere utenti amministrativi alla gestione del dominio
> * Abilitare gli account utente per Azure AD DS e generare gli hash delle password

Per vedere il dominio gestito in azione, creare e aggiungere una macchina virtuale al dominio.

> [!div class="nextstepaction"]
> [Aggiungere una macchina virtuale Windows Server a un dominio gestito](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
