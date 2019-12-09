---
title: Esercitazione - Creare un'istanza di Azure Active Directory Domain Services | Microsoft Docs
description: Questa esercitazione illustra come creare e configurare un'istanza di Azure Active Directory Domain Services tramite il portale di Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: ef203eec1398e9f23fb162845b9d570316083ecf
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703702"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Esercitazione: Creare e configurare un'istanza di Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. È possibile utilizzare questi servizi di dominio senza distribuire, gestire e applicare patch manualmente ai controller di dominio. Azure AD DS si integra con il tenant di Azure AD esistente. Questa integrazione consente agli utenti di accedere con le proprie credenziali aziendali ed è possibile usare i gruppi e gli account utente esistenti per proteggere l'accesso alle risorse.

È possibile creare un dominio gestito usando le opzioni di configurazione predefinite per la connessione di rete e la sincronizzazione oppure [definire manualmente queste impostazioni][tutorial-create-instance-advanced]. Questa esercitazione illustra come usare le opzioni predefinite per creare e configurare un'istanza di Azure AD DS tramite il portale di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Informazioni sui requisiti DNS per un dominio gestito
> * Creare un'istanza di Azure Active Directory Domain Services
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

## <a name="create-an-instance"></a>Creare un'istanza

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
> In queste esercitazioni e articoli sulle procedure come esempio breve viene usato il dominio personalizzato *aadds.contoso.com*. In tutti i comandi specificare il proprio nome di dominio, che può includere un prefisso univoco.
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

    ![Configurare le impostazioni di base per un'istanza di Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

Per creare rapidamente un dominio gestito di Azure AD DS, è possibile selezionare **Rivedi e crea** per accettare altre opzioni di configurazione predefinite. Quando si sceglie questa opzione di creazione, vengono configurate le impostazioni predefinite seguenti:

* Crea una rete virtuale denominata *aadds-vnet* che usa l'intervallo di indirizzi IP *10.0.1.0/24*.
* Crea una subnet denominata *aadds-subnet* che usa l'intervallo di indirizzi IP *10.0.1.0/24*.
* Sincronizza *tutti* gli utenti di Azure AD nel dominio gestito di Azure AD DS.

1. Selezionare **Rivedi e crea** per accettare queste opzioni di configurazione predefinite.

## <a name="deploy-the-managed-domain"></a>Distribuire il dominio gestito

Nella pagina **Riepilogo** della procedura guidata controllare le impostazioni di configurazione per il dominio gestito. È possibile tornare in qualsiasi passaggio precedente della procedura guidata per apportare modifiche. Per ridistribuire in modo coerente un dominio gestito di Azure AD DS in un tenant di Azure AD diverso usando queste opzioni di configurazione, è anche possibile **scaricare un modello per l'automazione**.

1. Per creare il dominio gestito, selezionare **Crea**. Viene visualizzata una nota in cui si specifica che alcune opzioni di configurazione, ad esempio il nome DNS o la rete virtuale, non possono essere modificate dopo la creazione del servizio di Azure AD DS gestito. Per continuare, selezionare **OK**.
1. Il processo di provisioning del dominio gestito può richiedere fino a un'ora. Nel portale viene visualizzata una notifica che mostra lo stato di avanzamento della distribuzione di Azure AD DS. Selezionare la notifica per visualizzare lo stato di avanzamento dettagliato del processo di distribuzione.

    ![Notifica nel portale di Azure sulla distribuzione in corso](./media/tutorial-create-instance/deployment-in-progress.png)

1. La pagina verrà caricata con aggiornamenti sul processo di distribuzione, inclusa la creazione di nuove risorse nella directory.
1. Selezionare il gruppo di risorse, ad esempio *myResourceGroup*, quindi scegliere l'istanza di Azure Active Directory Domain Services dall'elenco di risorse di Azure, ad esempio *aadds.contoso.com*. La scheda **Panoramica** indica che il dominio gestito è attualmente in fase di *Distribuzione*. Non è possibile configurare il dominio gestito fino a quando non ne è stato completato il provisioning.

    ![Stato di Domain Services durante lo stato di provisioning](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Al termine del provisioning del dominio gestito, nella scheda **Panoramica** il dominio gestito è impostato su *In esecuzione*.

    ![Stato di Domain Services dopo il provisioning](./media/tutorial-create-instance/successfully-provisioned.png)

Il dominio gestito è associato al tenant di Azure AD. Durante il processo di provisioning, Azure AD DS crea due applicazioni aziendali denominate *Domain Controller Services* e *AzureActiveDirectoryDomainControllerServices* nel tenant di Azure AD. Queste applicazioni aziendali sono necessarie per gestire il dominio gestito Non eliminare queste applicazioni.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aggiornare le impostazioni DNS per la rete virtuale di Azure

Con la distribuzione di Azure AD DS completata, è ora possibile configurare la rete virtuale in modo da consentire ad altre VM e applicazioni connesse di usare il dominio gestito. Per fornire questa connettività, aggiornare le impostazioni del server DNS per la rete virtuale in modo che puntino ai due indirizzi IP in cui è stato distribuito Azure AD DS.

1. La scheda **Panoramica** per il dominio gestito mostra alcuni **passaggi di configurazione necessari**. Il primo passaggio di configurazione consiste nell'aggiornare le impostazioni del server DNS per la rete virtuale. Una volta configurate correttamente le impostazioni DNS, questo passaggio non viene più visualizzato.

    Gli indirizzi elencati sono i controller di dominio da usare nella rete virtuale. In questo esempio gli indirizzi sono *10.1.0.4* e *10.1.0.5*. In seguito questi indirizzi IP si possono trovare nella scheda **Proprietà**.

    ![Configurare le impostazioni DNS per la rete virtuale con gli indirizzi IP di Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

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

    ![Selezionare un profilo](./media/tutorial-create-instance/select-profile.png)

1. Nella pagina **Profilo** selezionare **Cambia password**.
1. Nella pagina **Cambia password** digitare la password esistente (precedente), quindi immetterne una nuova e confermarla.
1. Selezionare **Submit** (Invia).

Dopo la modifica, sono necessari alcuni minuti prima che la nuova password sia utilizzabile in Azure AD DS e per l'accesso ai computer aggiunti al dominio gestito.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Informazioni sui requisiti DNS per un dominio gestito
> * Creare un'istanza di Azure Active Directory Domain Services
> * Aggiungere utenti amministrativi alla gestione del dominio
> * Abilitare gli account utente per Azure AD DS e generare gli hash delle password

Prima di aggiungere a un dominio le macchine virtuali e distribuire le applicazioni che usano il dominio gestito di Azure AD DS, configurare una rete virtuale di Azure per i carichi di lavoro dell'applicazione.

> [!div class="nextstepaction"]
> [Configurare la rete virtuale di Azure per i carichi di lavoro dell'applicazione per l'uso del dominio gestito](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
