---
title: Concetti relativi alla foresta di risorse per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su una foresta di risorse in Servizi di dominio Azure Active Directory e su come traggono vantaggio dall'organizzazione in un ambiente ibrido con opzioni di autenticazione utente limitate o problemi di sicurezza.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: a583e32cbc3d58d5dfc5616335b2f38ad20fac14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233610"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Concetti e funzionalità della foresta di risorse per Servizi di dominio Azure Active DirectoryResource forest concepts and features for Azure Active Directory Domain Services

Servizi di dominio Azure Active Directory offre un'esperienza di accesso per le applicazioni legacy, locali e line-of-business. Gli utenti, i gruppi e gli accessi delle password degli utenti locali e cloud vengono sincronizzati con il dominio gestito di Servizi di dominio Active Directory di Azure.Users, groups, and password hashes of on-premises and cloud is synchronized to the Azure AD DS managed domain. Questi set di password sincronizzati sono ciò che offre agli utenti un singolo set di credenziali che possono usare per Servizi di dominio Active Directory, Office 365 e Azure Active Directory locali.

Sebbene sia sicura e offra ulteriori vantaggi in termini di sicurezza, alcune organizzazioni non possono sincronizzare gli accessi delle password utente con Azure AD o Azure AD DS. Gli utenti di un'organizzazione potrebbero non conoscere la password perché utilizzano solo l'autenticazione con smart card. Queste limitazioni impediscono ad alcune organizzazioni di usare Servizi di dominio Azure AD per sollevare e spostare le applicazioni classiche locali in Azure.These limitations prevent some organizations from using Azure AD DS to lift and shift on-premises classic applications to Azure.

Per soddisfare queste esigenze e restrizioni, è possibile creare un dominio gestito di Azure AD DS che usa una foresta di risorse. In questo articolo concettuale viene illustrato cosa sono le foreste e come si considerano attendibili altre risorse per fornire un metodo di autenticazione sicuro. Le foreste di risorse di Servizi di dominio Azure sono attualmente in anteprima.

> [!IMPORTANT]
> Azure AD DS resource forests don't currently support Azure HDInsight or Azure Files. Le foreste utente predefinite di Servizi di dominio Active Directory supportano entrambi questi servizi aggiuntivi.

## <a name="what-are-forests"></a>Cosa sono le foreste?

Una *foresta* è un costrutto logico utilizzato da Servizi di dominio Active Directory per raggruppare uno o più *domini.* I domini archiviano quindi gli oggetti per utenti o gruppi e forniscono servizi di autenticazione.

In Servizi di dominio Active Directory di Azure la foresta contiene un solo dominio. Le foreste locali di Servizi di dominio Active Directory spesso contengono molti domini. Nelle organizzazioni di grandi dimensioni, in particolare dopo fusioni e acquisizioni, è possibile che si verifichino più foreste locali che contengono più domini.

Per impostazione predefinita, un dominio gestito di Servizi di dominio Active Directory di Azure viene creato come foresta *utente.* Questo tipo di foresta sincronizza tutti gli oggetti di Azure AD, inclusi tutti gli account utente creati in un ambiente AD DS locale. Gli account utente possono eseguire direttamente l'autenticazione nel dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio per accedere a una macchina virtuale aggiunta a un dominio. Una foresta utente funziona quando gli isemi delle password possono essere sincronizzati e gli utenti non utilizzano metodi di accesso esclusivi come l'autenticazione con smart card.

In una foresta di *risorse* di Servizi di dominio Active Directory di Azure gli utenti eseguono l'autenticazione tramite un *trust* tra foreste unidirezionale dal dominio Active Directory locale. Con questo approccio, gli oggetti utente e gli ishe delle password non vengono sincronizzati con Azure AD DS. Gli oggetti utente e le credenziali esistono solo in Servizi di dominio Active Directory locale. Questo approccio consente alle aziende di ospitare risorse e piattaforme di applicazioni in Azure che dipendono dall'autenticazione classica, ad esempio LDAPS, Kerberos o NTLM, ma vengono rimossi eventuali problemi di autenticazione. Le foreste di risorse di Servizi di dominio Azure sono attualmente in anteprima.

Le foreste di risorse consentono inoltre di sollevare e spostare le applicazioni un componente alla volta. Molte applicazioni locali legacy sono multilivello, spesso utilizzando un server Web o front-end e molti componenti correlati al database. Questi livelli rendono difficile sollevare e spostare l'intera applicazione nel cloud in un unico passaggio. Con le foreste di risorse, è possibile passare l'applicazione al cloud in un approccio graduale, che semplifica lo spostamento dell'applicazione in Azure.With resource forests, you can lift your application to the cloud in phased approach, which makes it easier to move your application to Azure.

## <a name="what-are-trusts"></a>Cosa sono i trust?

Le organizzazioni che dispongono di più di un dominio spesso richiedono agli utenti di accedere alle risorse condivise in un dominio diverso. L'accesso a queste risorse condivise richiede che gli utenti di un dominio eseguano l'autenticazione in un altro dominio. Per fornire queste funzionalità di autenticazione e autorizzazione tra client e server in domini diversi, deve essere presente una relazione di *trust* tra i due domini.

Con i trust di dominio, i meccanismi di autenticazione per ogni dominio considerano attendibili le autenticazioni provenienti dall'altro dominio. I trust consentono di fornire l'accesso controllato alle risorse condivise in un dominio di risorse (il dominio *trusting)* verificando che le richieste di autenticazione in ingresso provengano da un'autorità attendibile (dominio *trusted).* I trust fungono da bridge che consentono solo alle richieste di autenticazione convalidate di spostarsi tra domini.

Il modo in cui un trust passa le richieste di autenticazione dipende da come è configurato. I trust possono essere configurati in uno dei modi seguenti:

* **Unidirezionale:** fornisce l'accesso dal dominio trusted alle risorse nel dominio trusting.
* **Bidirezionale:** fornisce l'accesso da ogni dominio alle risorse nell'altro dominio.

I trust vengono inoltre configurati per gestire relazioni di trust aggiuntive in uno dei modi seguenti:

* **Non transitivo:** il trust esiste solo tra i due domini partner di trust.
* **Transitivo:** il trust si estende automaticamente a tutti gli altri domini considerati attendibili da uno dei partner.

In alcuni casi, le relazioni di trust vengono stabilite automaticamente quando vengono creati i domini. Altre volte, è necessario scegliere un tipo di trust e stabilire in modo esplicito le relazioni appropriate. I tipi specifici di trust utilizzati e la struttura di tali relazioni di trust dipendono dall'organizzazione del servizio directory Active Directory e dalla coesistenza della diversa rete di versioni di Windows.

## <a name="trusts-between-two-forests"></a>Trust tra due foreste

È possibile estendere i trust di dominio all'interno di una singola foresta a un'altra foresta creando manualmente un trust tra foreste unidirezionale o bidirezionale. Un trust tra foreste è un trust transitivo che esiste solo tra un dominio radice della foresta e un secondo dominio radice della foresta.

* Un trust tra foreste unidirezionale consente a tutti gli utenti di una foresta di considerare attendibili tutti i domini nell'altra foresta.
* Un trust tra foreste bidirezionale forma una relazione di trust transitiva tra ogni dominio in entrambe le foreste.

La transitività dei trust tra foreste è limitata ai due partner forestali. Il trust tra foreste non si estende alle foreste aggiuntive attendibili da uno dei partner.

![Diagramma del trust tra foreste da Servizi di dominio Active Directory di Azure a Servizi di dominio Active Directory locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

È possibile creare configurazioni di trust tra domini e foreste diverse a seconda della struttura di Active Directory dell'organizzazione. Servizi di dominio Active Directory di Azure supporta solo un trust tra foreste unidirezionali. In questa configurazione, le risorse in Servizi di dominio Active Directory di Azure possono considerare attendibili tutti i domini in una foresta locale.

## <a name="supporting-technology-for-trusts"></a>Supporto della tecnologia per i trust

I trust utilizzano vari servizi e funzionalità, ad esempio DNS per individuare i controller di dominio nelle foreste partner. I trust dipendono inoltre dai protocolli di autenticazione NTLM e Kerberos e dai meccanismi di autorizzazione e controllo di accesso basati su Windows per fornire un'infrastruttura di comunicazione protetta tra domini e foreste di Active Directory. I servizi e le funzionalità seguenti consentono di supportare le relazioni di trust riuscite.

### <a name="dns"></a>DNS

Servizi di dominio Active Directory necessita di DNS per la posizione e la denominazione dei controller di dominio. Per il funzionamento di Servizi di dominio Active Directory viene fornito il seguente supporto da DNS:

* Servizio di risoluzione dei nomi che consente agli host di rete e ai servizi di individuare i controller di dominio.
* Struttura di denominazione che consente a un'azienda di riflettere la propria struttura organizzativa nei nomi dei relativi domini del servizio directory.

Uno spazio dei nomi di dominio DNS viene in genere distribuito che rispecchia lo spazio dei nomi di dominio di Servizi di dominio Active Directory.A DNS domain namespace is usually deployed that mirrors the AD DS domain namespace. Se è presente uno spazio dei nomi DNS esistente prima della distribuzione di Servizi di dominio Active Directory, lo spazio dei nomi DNS viene in genere partizionato per Active Directory e vengono creati un sottodominio DNS e una delega per la radice della foresta di Active Directory. Vengono quindi aggiunti nomi di dominio DNS aggiuntivi per ogni dominio figlio di Active Directory.

DNS viene inoltre utilizzato per supportare la posizione dei controller di dominio Active Directory. Le zone DNS vengono popolate con record di risorse DNS che consentono agli host e ai servizi di rete di individuare i controller di dominio di Active Directory.

### <a name="applications-and-net-logon"></a>Applicazioni e accesso alla rete

Sia le applicazioni che il servizio Accesso rete sono componenti del modello di canale di protezione distribuito di Windows. Le applicazioni integrate con Windows Server e Active Directory utilizzano i protocolli di autenticazione per comunicare con il servizio Accesso rete in modo da poter stabilire un percorso protetto su cui può verificarsi l'autenticazione.

### <a name="authentication-protocols"></a>Protocolli di autenticazione

I controller di dominio Active Directory autenticano utenti e applicazioni utilizzando uno dei protocolli seguenti:

* **Protocollo di autenticazione Kerberos versione 5**
    * Il protocollo Kerberos versione 5 è il protocollo di autenticazione predefinito utilizzato dai computer locali che eseguono Windows e supportano sistemi operativi di terze parti. Questo protocollo è specificato in RFC 1510 ed è completamente integrato con Active Directory, SMB (Server Message Block), HTTP e RPC (Remote Procedure Call), nonché con le applicazioni client e server che utilizzano questi protocolli.
    * Quando viene utilizzato il protocollo Kerberos, il server non deve contattare il controller di dominio. Al contrario, il client ottiene un ticket per un server richiedendo uno da un controller di dominio nel dominio dell'account del server. Il server convalida quindi il ticket senza consultare altre autorità.
    * Se un computer coinvolto in una transazione non supporta il protocollo Kerberos versione 5, viene utilizzato il protocollo NTLM.

* **Protocollo di autenticazione NTLM**
    * Il protocollo NTLM è un protocollo di autenticazione di rete classico utilizzato dai sistemi operativi precedenti. Per motivi di compatibilità, viene utilizzato dai domini di Active Directory per elaborare le richieste di autenticazione di rete provenienti da applicazioni progettate per client e server basati su Windows precedenti e sistemi operativi di terze parti.
    * Quando il protocollo NTLM viene utilizzato tra un client e un server, il server deve contattare un servizio di autenticazione del dominio su un controller di dominio per verificare le credenziali del client. Il server autentica il client inoltrando le credenziali client a un controller di dominio nel dominio dell'account client.
    * Quando due domini o foreste di Active Directory sono connessi tramite un trust, le richieste di autenticazione effettuate utilizzando questi protocolli possono essere instradate per fornire l'accesso alle risorse in entrambe le foreste.

## <a name="authorization-and-access-control"></a>Autorizzazione e controllo di accesso

Le tecnologie di autorizzazione e attendibilità collaborano per fornire un'infrastruttura di comunicazione protetta tra domini o foreste di Active Directory. L'autorizzazione determina il livello di accesso di un utente alle risorse di un dominio. I trust facilitano l'autorizzazione tra domini degli utenti fornendo un percorso per l'autenticazione degli utenti in altri domini in modo che le richieste alle risorse condivise in tali domini possano essere autorizzate.

Quando una richiesta di autenticazione effettuata in un dominio trusting viene convalidata dal dominio trusted, viene passata alla risorsa di destinazione. La risorsa di destinazione determina quindi se autorizzare la richiesta specifica effettuata dall'utente, dal servizio o dal computer nel dominio trusted in base alla configurazione del controllo di accesso.

I trust forniscono questo meccanismo per convalidare le richieste di autenticazione passate a un dominio trusting. I meccanismi di controllo di accesso nel computer delle risorse determinano il livello finale di accesso concesso al richiedente nel dominio trusted.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui trust, vedere Come funzionano i trust tra [foreste in Azure AD DS?][concepts-trust]

Per iniziare a creare un dominio gestito di Servizi di dominio Active Directory di Azure con una foresta di risorse, vedere [Creare e configurare un dominio gestito][tutorial-create-advanced]di Servizi di dominio Active Directory di Azure. È quindi possibile [creare un trust tra foreste in uscita a un dominio locale (anteprima).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
