---
title: Concetti relativi alla foresta delle risorse per Azure AD Domain Services | Microsoft Docs
description: Informazioni sul modo in cui una foresta di risorse è Azure Active Directory Domain Services e su come traggono vantaggio dall'organizzazione nell'ambiente ibrido con opzioni di autenticazione utente limitate o problemi di sicurezza.
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233610"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Concetti e funzionalità della foresta di risorse per Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS) offre un'esperienza di accesso per le applicazioni line-of-business legacy, locali. Gli utenti, i gruppi e gli hash delle password degli utenti locali e cloud vengono sincronizzati con il dominio gestito Azure AD DS. Questi hash delle password sincronizzati forniscono agli utenti un singolo set di credenziali che possono usare per servizi di dominio Active Directory, Office 365 e Azure Active Directory locali.

Sebbene sia sicuro e fornisca vantaggi aggiuntivi per la sicurezza, alcune organizzazioni non possono sincronizzare gli hash delle password utente per Azure AD o Azure AD DS. Gli utenti di un'organizzazione potrebbero non conoscerne la password perché usano solo l'autenticazione con smart card. Queste limitazioni impediscono ad alcune organizzazioni di usare Azure AD DS per spostare in Azure le applicazioni classiche locali.

Per rispondere a queste esigenze e restrizioni, è possibile creare un dominio gestito Azure AD DS che usa una foresta di risorse. Questo articolo concettuale illustra le foreste e il modo in cui considera attendibili le altre risorse per fornire un metodo di autenticazione protetto. Le foreste di risorse Azure AD DS sono attualmente in anteprima.

> [!IMPORTANT]
> Le foreste di risorse Azure AD DS attualmente non supportano Azure HDInsight o File di Azure. Le foreste utente Azure AD DS predefinite supportano entrambi i servizi aggiuntivi.

## <a name="what-are-forests"></a>Che cosa sono le foreste?

Una *foresta* è un costrutto logico usato da Active Directory Domain Services (ad DS) per raggruppare uno o più *domini*. I domini archiviano quindi gli oggetti per utenti o gruppi e forniscono servizi di autenticazione.

In Azure AD DS la foresta contiene solo un dominio. Le foreste di servizi di dominio Active Directory locali contengono spesso molti domini. Nelle organizzazioni di grandi dimensioni, soprattutto dopo fusioni e acquisizioni, è possibile che si verifichino più foreste locali, ognuna delle quali contiene più domini.

Per impostazione predefinita, un dominio gestito Azure AD DS viene creato come foresta *utente* . Questo tipo di foresta Sincronizza tutti gli oggetti da Azure AD, inclusi tutti gli account utente creati in un ambiente di servizi di dominio Active Directory locale. Gli account utente possono eseguire l'autenticazione direttamente nel dominio gestito di Azure AD DS, ad esempio per accedere a una macchina virtuale aggiunta a un dominio. Una foresta utente funziona quando gli hash delle password possono essere sincronizzati e gli utenti non usano metodi di accesso esclusivi come l'autenticazione con smart card.

In una foresta di *risorse* Azure AD DS, gli utenti eseguono l'autenticazione su un *trust* tra foreste unidirezionale rispetto ai servizi di dominio Active Directory locali. Con questo approccio, gli oggetti utente e gli hash delle password non vengono sincronizzati con Azure AD DS. Gli oggetti e le credenziali utente esistono solo in servizi di dominio Active Directory locale. Questo approccio consente alle aziende di ospitare risorse e piattaforme applicative in Azure che dipendono dall'autenticazione classica, ad esempio LDAPs, Kerberos o NTLM, ma vengono rimossi eventuali problemi di autenticazione o problemi. Le foreste di risorse Azure AD DS sono attualmente in anteprima.

Le foreste di risorse offrono inoltre la possibilità di trasferire in modalità Lift-and-Shift le applicazioni un componente alla volta. Molte applicazioni locali legacy sono a più livelli e spesso usano un server Web o un front-end e molti componenti correlati al database. Questi livelli rendono difficile trasferire in modo Lift-and-Shift l'intera applicazione nel cloud in un unico passaggio. Con le foreste di risorse è possibile trasferire l'applicazione al cloud in un approccio in più fasi, semplificando così lo spostamento dell'applicazione in Azure.

## <a name="what-are-trusts"></a>Che cosa sono i trust?

Le organizzazioni che hanno più di un dominio spesso richiedono che gli utenti accedano a risorse condivise in un dominio diverso. Per accedere a queste risorse condivise è necessario che gli utenti di un dominio eseguano l'autenticazione a un altro dominio. Per fornire queste funzionalità di autenticazione e autorizzazione tra client e server in domini diversi, è necessario che esista una relazione di *trust* tra i due domini.

Con i trust di dominio, i meccanismi di autenticazione per ogni dominio considerano attendibili le autenticazioni provenienti dall'altro dominio. I trust consentono di fornire l'accesso controllato alle risorse condivise in un dominio di risorse (il dominio *trusting* ) verificando che le richieste di autenticazione in ingresso provengano da un'autorità attendibile (dominio *Trusted* ). I trust fungono da Bridge che consentono solo le richieste di autenticazione convalidate per spostarsi tra i domini.

Il modo in cui un trust supera le richieste di autenticazione dipende dalla relativa configurazione. I trust possono essere configurati in uno dei modi seguenti:

* **Unidirezionale** : fornisce l'accesso dal dominio trusted alle risorse nel dominio trusting.
* **Bidirezionale** : fornisce l'accesso da ogni dominio alle risorse nell'altro dominio.

I trust sono inoltre configurati per la gestione di relazioni di trust aggiuntive in uno dei modi seguenti:

* Non **transitivo** : il trust esiste solo tra i due domini trust partner.
* **Transitive** : l'attendibilità si estende automaticamente a qualsiasi altro dominio considerato attendibile da uno dei partner.

In alcuni casi, le relazioni di trust vengono stabilite automaticamente al momento della creazione dei domini. In altri casi, è necessario scegliere un tipo di trust e stabilire in modo esplicito le relazioni appropriate. I tipi specifici di trust utilizzati e la struttura di tali relazioni di trust dipendono dal modo in cui il servizio di Active Directory directory è organizzato e dal fatto che le versioni di Windows diverse siano coesistenti sulla rete.

## <a name="trusts-between-two-forests"></a>Trust tra due foreste

È possibile estendere i trust di dominio all'interno di una singola foresta a un'altra foresta creando manualmente un trust tra foreste unidirezionale o bidirezionale. Un trust tra foreste è un trust transitivo esistente solo tra un dominio radice della foresta e un secondo dominio radice della foresta.

* Un trust tra foreste unidirezionale consente a tutti gli utenti di una foresta di considerare attendibili tutti i domini nell'altra foresta.
* Un trust tra foreste bidirezionale costituisce una relazione di trust transitiva tra tutti i domini in entrambe le foreste.

La transitività dei trust tra foreste è limitata ai due partner della foresta. Il trust tra foreste non si estende ad altre foreste considerate attendibili da uno dei partner.

![Diagramma del trust tra foreste da Azure AD DS a servizi di dominio Active Directory locale](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

È possibile creare configurazioni di trust tra domini e foreste diverse a seconda della struttura Active Directory dell'organizzazione. Azure AD DS supporta solo un trust tra foreste unidirezionali. In questa configurazione le risorse in Azure AD DS possono considerare attendibili tutti i domini in una foresta locale.

## <a name="supporting-technology-for-trusts"></a>Tecnologia di supporto per i trust

I trust utilizzano vari servizi e funzionalità, ad esempio DNS, per individuare i controller di dominio nelle foreste partner. I trust dipendono inoltre dai protocolli di autenticazione NTLM e Kerberos e dai meccanismi di autorizzazione e controllo di accesso basati su Windows per fornire un'infrastruttura di comunicazione protetta tra Active Directory domini e foreste. I servizi e le funzionalità seguenti consentono di supportare relazioni di attendibilità riuscite.

### <a name="dns"></a>DNS

Servizi di dominio Active Directory necessita di DNS per la posizione e la denominazione del controller di dominio (DC). Per il corretto funzionamento di servizi di dominio Active Directory viene fornito il supporto di DNS seguente:

* Un servizio di risoluzione dei nomi che consente a host e servizi di rete di individuare i controller di dominio.
* Struttura di denominazione che consente a un'azienda di riflettere la struttura organizzativa nei nomi dei propri domini del servizio directory.

Viene in genere distribuito uno spazio dei nomi di dominio DNS che rispecchia lo spazio dei nomi del dominio di Active Directory Se è presente uno spazio dei nomi DNS esistente prima della distribuzione di servizi di dominio Active Directory, lo spazio dei nomi DNS viene in genere partizionato per Active Directory e viene creato un sottodominio DNS e una delega per la radice della foresta Active Directory. I nomi di dominio DNS aggiuntivi vengono quindi aggiunti per ogni Active Directory dominio figlio.

DNS viene usato anche per supportare la posizione dei controller di dominio Active Directory. Le zone DNS vengono popolate con record di risorse DNS che consentono a host e servizi di rete di individuare i controller di dominio Active Directory.

### <a name="applications-and-net-logon"></a>Applicazioni e accesso rete

Entrambe le applicazioni e il servizio Accesso rete sono componenti del modello di canale di sicurezza distribuita di Windows. Le applicazioni integrate con Windows Server e Active Directory utilizzano i protocolli di autenticazione per comunicare con il servizio Accesso rete, in modo da poter stabilire un percorso protetto sul quale può essere eseguita l'autenticazione.

### <a name="authentication-protocols"></a>Protocolli di autenticazione

I controller di dominio Active Directory autenticano gli utenti e le applicazioni usando uno dei protocolli seguenti:

* **Protocollo di autenticazione Kerberos versione 5**
    * Il protocollo Kerberos versione 5 è il protocollo di autenticazione predefinito usato da computer locali che eseguono Windows e supportano sistemi operativi di terze parti. Questo protocollo è specificato in RFC 1510 ed è completamente integrato con Active Directory, SMB (Server Message Block), HTTP e RPC (Remote Procedure Call), nonché le applicazioni client e server che utilizzano questi protocolli.
    * Quando si utilizza il protocollo Kerberos, il server non deve contattare il controller di dominio. Il client riceve invece un ticket per un server richiedendo uno da un controller di dominio nel dominio dell'account del server. Il server convalida quindi il ticket senza consultare altre autorità.
    * Se un computer che ha partecipato a una transazione non supporta il protocollo Kerberos versione 5, viene utilizzato il protocollo NTLM.

* **Protocollo di autenticazione NTLM**
    * Il protocollo NTLM è un protocollo di autenticazione di rete classico utilizzato dai sistemi operativi precedenti. Per motivi di compatibilità, viene usato da Active Directory domini per elaborare le richieste di autenticazione di rete provenienti da applicazioni progettate per client e server basati su Windows precedenti e sistemi operativi di terze parti.
    * Quando il protocollo NTLM viene usato tra un client e un server, il server deve contattare un servizio di autenticazione del dominio in un controller di dominio per verificare le credenziali del client. Il server autentica il client mediante l'invio delle credenziali del client a un controller di dominio nel dominio dell'account del client.
    * Quando due Active Directory domini o foreste sono connessi da un trust, le richieste di autenticazione effettuate usando questi protocolli possono essere indirizzate per fornire l'accesso alle risorse in entrambe le foreste.

## <a name="authorization-and-access-control"></a>Autorizzazione e controllo di accesso

Le tecnologie di autorizzazione e attendibilità interagiscono per fornire un'infrastruttura di comunicazione protetta tra domini Active Directory o foreste. L'autorizzazione determina il livello di accesso di un utente alle risorse in un dominio. I trust facilitano l'autorizzazione tra domini degli utenti fornendo un percorso per l'autenticazione degli utenti in altri domini, in modo che le richieste alle risorse condivise in tali domini possano essere autorizzate.

Quando una richiesta di autenticazione eseguita in un dominio trusting viene convalidata dal dominio trusted, viene passata alla risorsa di destinazione. La risorsa di destinazione determina quindi se autorizzare la richiesta specifica effettuata dall'utente, dal servizio o dal computer nel dominio trusted in base alla configurazione del controllo di accesso.

I trust forniscono questo meccanismo per convalidare le richieste di autenticazione passate a un dominio trusting. I meccanismi di controllo dell'accesso nel computer delle risorse determinano il livello di accesso finale concesso al richiedente nel dominio trusted.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui trust, vedere [come funzionano i trust tra foreste in Azure AD DS?][concepts-trust]

Per iniziare a creare un dominio gestito di Azure AD DS con una foresta di risorse, vedere [creare e configurare un dominio gestito di Azure AD DS][tutorial-create-advanced]. È quindi possibile [creare un trust tra foreste in uscita per un dominio locale (anteprima)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
