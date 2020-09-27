---
title: Concetti di foresta di risorse per Azure Active Directory Domain Services | Microsoft Docs
description: Informazioni su che cos'è una foresta di risorse in Azure Active Directory Domain Services e sui vantaggi che offre all'organizzazione in un ambiente ibrido con opzioni di autenticazione utente limitate o problemi di sicurezza.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 88069c6ac90fce7e33cac6ed3c30accb18ded8bf
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396400"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Concetti e funzionalità della foresta di risorse per Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) offre un'esperienza di accesso per le applicazioni line-of-business legacy, locali. Gli utenti, i gruppi e gli hash delle password degli utenti locali e cloud vengono sincronizzati con il dominio gestito Azure AD DS. Questi hash delle password sincronizzati forniscono agli utenti un singolo set di credenziali che possono usare per servizi di dominio Active Directory, Microsoft 365 e Azure Active Directory locali.

Sebbene sia sicuro e offra vantaggi aggiuntivi per la sicurezza, alcune organizzazioni non possono sincronizzare gli hash delle password utente per Azure AD o Azure AD DS. Gli utenti di un'organizzazione potrebbero non conoscerne la password perché usano solo l'autenticazione mediante smart card. Queste limitazioni impediscono ad alcune organizzazioni di usare Azure AD DS per lo spostamento in Azure delle applicazioni classiche locali.

Per rispondere a queste esigenze e restrizioni, è possibile creare un dominio gestito che usa una foresta di risorse. Questo articolo concettuale spiega cosa sono le foreste e il modo in cui considerano attendibili le altre risorse per fornire un metodo di autenticazione protetto.

## <a name="what-are-forests"></a>Che cosa sono le foreste?

Una *foresta* è un costrutto logico usato da Active Directory Domain Services (AD DS) per raggruppare uno o più *domini*. I domini archiviano quindi gli oggetti per utenti o gruppi e offrono servizi di autenticazione.

In un dominio gestito Azure AD DS, la foresta contiene solo un dominio. Le foreste AD DS locali contengono spesso molti domini. Nelle organizzazioni di grandi dimensioni, soprattutto dopo fusioni e acquisizioni, è possibile che vi siano più foreste locali, ognuna delle quali con più domini.

Per impostazione predefinita, un dominio gestito viene creato come foresta *utente* . Questo tipo di foresta sincronizza tutti gli oggetti di Azure AD, inclusi tutti gli account utente creati in un ambiente AD DS locale. Gli account utente possono eseguire l'autenticazione direttamente nel dominio gestito, ad esempio per accedere a una macchina virtuale aggiunta a un dominio. Una foresta utente funziona quando gli hash delle password possono essere sincronizzati e gli utenti non usano metodi di accesso esclusivi come l'autenticazione mediante smart card.

In una foresta di *risorse* di dominio gestito, gli utenti eseguono l'autenticazione su un *trust* tra foreste unidirezionale rispetto ai servizi di dominio Active Directory locali. Con questo approccio, gli oggetti utente e gli hash delle password non vengono sincronizzati con il dominio gestito. Gli oggetti e le credenziali utente esistono solo in AD DS locale. Questo approccio consente alle aziende di ospitare risorse e piattaforme applicative in Azure che dipendono dall'autenticazione classica, ad esempio LDAPS, Kerberos o NTLM, ma vengono rimossi eventuali problemi di autenticazione.

Le foreste di risorse offrono inoltre la possibilità di trasferire le applicazioni in modalità lift-and-shift con un componente alla volta. Molte applicazioni locali legacy sono a più livelli e spesso usano un server Web o un front-end e molti componenti correlati al database. Questi livelli rendono difficile trasferire in modalità lift-and-shift l'intera applicazione nel cloud in un unico passaggio. Con le foreste di risorse è possibile trasferire l'applicazione nel cloud mediante un approccio a più fasi, semplificando così lo spostamento dell'applicazione in Azure.

## <a name="what-are-trusts"></a>Che cosa sono i trust?

Le organizzazioni che hanno più di un dominio spesso richiedono che gli utenti accedano a risorse condivise in un dominio diverso. Per accedere a queste risorse condivise è necessario che gli utenti di un dominio eseguano l'autenticazione a un altro dominio. Per fornire queste funzionalità di autenticazione e autorizzazione tra client e server in domini diversi, deve esistere un *trust* tra i due domini.

Con i trust di dominio, i meccanismi di autenticazione per ogni dominio considerano attendibili le autenticazioni provenienti dall'altro dominio. I trust consentono l'accesso controllato alle risorse condivise in un dominio di risorse (il dominio *trusting*) verificando che le richieste di autenticazione in ingresso provengano da un'autorità attendibile (il *dominio* attendibile). I trust fungono da ponti che consentono solo alle richieste di autenticazione convalidate di spostarsi tra i domini.

Il modo in cui un trust supera le richieste di autenticazione dipende dalla relativa configurazione. È possibile configurare il trust in uno dei modi seguenti:

* **Modalità unidirezionale**: offre l'accesso dal dominio attendibile alle risorse nel dominio trusting.
* **Modalità bidirezionale**: offre l'accesso da ogni dominio alle risorse nell'altro dominio.

I trust devono inoltre essere configurati per la gestione di relazioni di trust aggiuntive in uno dei modi seguenti:

* **Modalità non transitiva**: il trust esiste solo tra i due domini partner di trust.
* **Modalità transitiva**: il trust si estende automaticamente a qualsiasi altro dominio considerato attendibile da uno dei partner.

In alcuni casi, le relazioni di trust vengono stabilite automaticamente al momento della creazione dei domini. In altri casi, è necessario scegliere un tipo di trust e stabilire in modo esplicito le relazioni appropriate. I tipi specifici di trust utilizzati e la struttura di tali relazioni di trust dipendono dal modo in cui è organizzata la directory di servizi di dominio Active Directory e dal fatto che le versioni di Windows diverse siano coesistenti sulla rete.

## <a name="trusts-between-two-forests"></a>Trust tra due foreste

È possibile estendere i trust di dominio all'interno di una singola foresta a un'altra foresta creando manualmente un trust tra foreste unidirezionali o bidirezionali. Un trust tra foreste è un trust transitivo che esiste solo tra un dominio radice della foresta e un secondo dominio radice della foresta.

* Un trust tra foreste unidirezionale consente a tutti gli utenti in una foresta di considerare attendibili tutti i domini nell'altra foresta.
* Un trust tra foreste bidirezionale costituisce una relazione di trust transitiva tra tutti i domini in entrambe le foreste.

La transitività dei trust tra foreste è limitata ai due partner della foresta. Il trust tra foreste non si estende ad altre foreste considerate attendibili da uno dei partner.

![Diagramma del trust tra foreste da Azure AD Domain Services ad Active Directory Domain Services locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

È possibile creare configurazioni di trust tra domini e foreste diverse a seconda della struttura di servizi di dominio Active Directory dell'organizzazione. Azure AD DS supporta solo trust tra foreste unidirezionali. In questa configurazione, le risorse nel dominio gestito possono considerare attendibili tutti i domini in una foresta locale.

## <a name="supporting-technology-for-trusts"></a>Tecnologia di supporto per i trust

I trust usano vari servizi e funzionalità, ad esempio DNS per individuare i controller di dominio nelle foreste partner. I trust dipendono inoltre dai protocolli di autenticazione NTLM e Kerberos e dai meccanismi di autorizzazione e controllo di accesso basati su Windows per fornire un'infrastruttura di comunicazione protetta tra domini e foreste di servizi di dominio Active Directory. I servizi e le funzionalità seguenti consentono di supportare relazioni di attendibilità riuscite.

### <a name="dns"></a>DNS

Per la posizione e la denominazione del controller di dominio AD DS necessita di DNS. Per il corretto funzionamento dei servizi di AD DS viene fornito il seguente supporto di DNS:

* Un servizio di risoluzione dei nomi che consente a host e servizi di rete di individuare i controller di dominio.
* Una struttura di denominazione che consente a un'azienda di riflettere la struttura organizzativa nei nomi dei propri domini del servizio directory.

In genere viene distribuito uno spazio dei nomi di dominio DNS che rispecchia lo spazio dei nomi di dominio di AD DS. Se è presente uno spazio dei nomi DNS esistente prima della distribuzione di servizi di dominio Active Directory, lo spazio dei nomi DNS viene in genere partizionato per servizi di dominio Active Directory e viene creato un sottodominio DNS e la delega per la radice della foresta di Active Directory. I nomi di dominio DNS aggiuntivi vengono quindi aggiunti per ogni dominio figlio di servizi di dominio Active Directory.

DNS viene inoltre utilizzato per supportare il percorso dei controller di dominio di servizi di dominio Active Directory. Le zone DNS vengono popolate con record di risorse DNS che consentono a host e servizi di rete di individuare i controller di dominio Active Directory.

### <a name="applications-and-net-logon"></a>Applicazioni e accesso alla rete

Entrambe le applicazioni e il servizio Net Logon sono componenti del modello del canale di sicurezza distribuito di Windows. Le applicazioni integrate con Windows Server e servizi di dominio Active Directory utilizzano i protocolli di autenticazione per comunicare con il servizio Accesso rete, in modo che sia possibile stabilire un percorso protetto sul quale può essere eseguita l'autenticazione.

### <a name="authentication-protocols"></a>Protocolli di autenticazione

I controller di dominio di servizi di dominio Active Directory autenticano gli utenti e le applicazioni usando uno dei protocolli seguenti:

* **Protocollo di autenticazione Kerberos versione 5**
    * Il protocollo Kerberos versione 5 è il protocollo di autenticazione predefinito usato da computer locali che eseguono Windows e supportano sistemi operativi di terze parti. Questo protocollo è specificato in RFC 1510 ed è completamente integrato con servizi di dominio Active Directory, SMB (Server Message Block), HTTP e RPC (Remote Procedure Call), nonché le applicazioni client e server che utilizzano questi protocolli.
    * Quando si usa il protocollo Kerberos, il server non deve contattare il controller di dominio. Il client riceve invece un ticket per un server richiedendone uno da un controller di dominio nel dominio dell'account del server. Il server convalida quindi il ticket senza consultare altre autorità.
    * Se un computer coinvolto in una transazione non supporta il protocollo Kerberos versione 5, si usa il protocollo NTLM.

* **Protocollo di autenticazione NTLM**
    * Il protocollo NTLM è un protocollo di autenticazione di rete classico usato dai precedenti sistemi operativi. Per motivi di compatibilità, viene usato dai domini di servizi di dominio Active Directory per elaborare le richieste di autenticazione di rete provenienti da applicazioni progettate per client e server basati su Windows precedenti e sistemi operativi di terze parti.
    * Quando il protocollo NTLM viene usato tra un client e un server, il server deve contattare un servizio di autenticazione del dominio in un controller di dominio per verificare le credenziali del client. Il server autentica il client mediante l'invio delle credenziali del client a un controller di dominio nel dominio dell'account del client.
    * Quando due domini o foreste di servizi di dominio Active Directory sono connessi da un trust, le richieste di autenticazione effettuate mediante questi protocolli possono essere indirizzate per fornire l'accesso alle risorse in entrambe le foreste.

## <a name="authorization-and-access-control"></a>Autorizzazione e controllo di accesso

Le tecnologie di autorizzazione e attendibilità interagiscono per fornire un'infrastruttura di comunicazione protetta tra domini o foreste di servizi di dominio Active Directory. L'autorizzazione determina il livello di accesso di un utente alle risorse in un dominio. I trust facilitano l'autorizzazione degli utenti tra domini fornendo un percorso per l'autenticazione degli utenti in altri domini, in modo che sia possibile autorizzare le richieste di accesso alle risorse condivise in tali domini.

Quando una richiesta di autenticazione eseguita in un dominio trusting viene convalidata dal dominio considerato attendibile, viene passata alla risorsa di destinazione. La risorsa di destinazione determina quindi se autorizzare la richiesta specifica effettuata dall'utente, dal servizio o dal computer nel dominio considerato attendibile in base alla configurazione del controllo di accesso.

I trust forniscono questo meccanismo per convalidare le richieste di autenticazione passate a un dominio trusting. I meccanismi di controllo dell'accesso nel computer delle risorse determinano il livello di accesso finale concesso al richiedente nel dominio considerato attendibile.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui trust, vedere [Funzionamento dei trust tra foreste in Azure AD DS][concepts-trust]

Per iniziare a creare un dominio gestito con una foresta di risorse, vedere [creare e configurare un dominio gestito di Azure AD DS][tutorial-create-advanced]. È quindi possibile [creare un trust tra foreste in uscita per un dominio locale][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
