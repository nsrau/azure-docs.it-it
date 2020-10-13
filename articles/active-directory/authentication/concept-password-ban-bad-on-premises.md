---
title: Protezione Azure AD password-Azure Active Directory
description: Escludere le password vulnerabili negli ambienti Active Directory Domain Services locali usando la protezione Azure AD password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09ad2991d552cb9886911ac75ea23c690204a71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116649"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Applicare la protezione Azure AD password locale per Active Directory Domain Services

Azure AD Password Protection rileva e blocca le password vulnerabili note e le relative varianti e può anche bloccare ulteriori termini vulnerabili specifici per l'organizzazione. La distribuzione locale di Azure AD la protezione con password usa gli stessi elenchi di password bannate globali e personalizzati archiviati in Azure AD ed esegue gli stessi controlli per le modifiche delle password locali come Azure AD per le modifiche basate sul cloud. Questi controlli vengono eseguiti durante le modifiche delle password e gli eventi di reimpostazione della password per i controller di dominio Active Directory Domain Services locali (AD DS).

## <a name="design-principles"></a>Principi di progettazione

Azure AD la protezione con password è progettata tenendo presenti i principi seguenti:

* I controller di dominio (DCs) non devono mai comunicare direttamente con Internet.
* Non vengono aperte nuove porte di rete nei controller di dominio.
* Non sono necessarie modifiche allo schema di servizi di dominio Active Directory. Il software usa il *contenitore* di servizi di dominio Active Directory e gli oggetti dello schema *serviceConnectionPoint* esistenti.
* Non è richiesto alcun dominio di servizi di dominio Active Directory o livello di funzionalità della foresta (DFL/FFL) minimo.
* Il software non crea o richiede account nei domini di servizi di dominio Active Directory che protegge.
* Le password con testo non crittografato dell'utente lasciano mai il controller di dominio durante le operazioni di convalida della password o in qualsiasi altro momento.
* Il software non dipende da altre funzionalità di Azure AD. Ad esempio, Azure AD la sincronizzazione dell'hash delle password (pH) non è correlata o necessaria per la protezione Azure AD password.
* La distribuzione incrementale è supportata, ma i criteri password vengono applicati solo in cui è installato l'agente controller di dominio (agente DC).

## <a name="incremental-deployment"></a>Distribuzione incrementale

Azure AD Password Protection supporta la distribuzione incrementale tra i controller di dominio in un dominio di servizi di dominio Active Directory. È importante comprendere che cosa significa realmente e quali sono i compromessi.

Il software dell'agente del controller di dominio Azure AD Password Protection può convalidare le password solo quando viene installato in un controller di dominio e solo per le modifiche della password inviate a tale controller di dominio. Non è possibile controllare quali controller di dominio vengono scelti dai computer client Windows per l'elaborazione delle modifiche della password utente. Per garantire un comportamento coerente e l'imposizione della sicurezza universale Azure AD password, il software dell'agente controller di dominio deve essere installato in tutti i controller di dominio in un dominio.

Molte organizzazioni desiderano testare con attenzione Azure AD la protezione delle password in un subset dei controller di dominio prima di una distribuzione completa. Per supportare questo scenario, Azure AD Password Protection supporta la distribuzione parziale. Il software dell'agente del controller di dominio in un determinato controller di dominio convalida attivamente le password anche quando altri controller di dominio nel dominio non hanno installato il software dell'agente DC. Le distribuzioni parziali di questo tipo non sono sicure e non sono consigliate a scopo di test.

## <a name="architectural-diagram"></a>Diagramma dell'architettura

È importante comprendere i concetti relativi alla progettazione e alle funzioni sottostanti prima di distribuire Azure AD la protezione delle password in un ambiente di servizi di dominio Active Directory locale. Il diagramma seguente illustra l'interazione tra i componenti di Protezione password di Azure AD:

![Come interagiscono i componenti di Protezione password di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Il servizio proxy Azure AD Password Protection viene eseguito in qualsiasi computer aggiunto a un dominio nella foresta di servizi di dominio Active Directory corrente. Lo scopo principale del servizio è quello di inviare le richieste di download dei criteri password dai controller di dominio al Azure AD e quindi restituire le risposte da Azure AD al controller di dominio.
* La DLL del filtro password dell'agente del controller di dominio riceve le richieste di convalida della password utente dal sistema operativo. Il filtro li trasmette al servizio agente controller di dominio in esecuzione localmente sul controller di dominio.
* Il servizio agente di controller di dominio di Azure AD la protezione con password riceve le richieste di convalida della password dalla DLL del filtro della password dell'agente del controller di dominio. Il servizio agente *controller di dominio* li elabora utilizzando i criteri password correnti (disponibili localmente) e restituisce il risultato di *esito positivo o negativo*.

## <a name="how-azure-ad-password-protection-works"></a>Funzionamento della protezione Azure AD password

I componenti di protezione Azure AD password locali funzionano come segue:

1. Ogni istanza del servizio proxy Azure AD Password Protection si annuncia ai controller di dominio nella foresta creando un oggetto *serviceConnectionPoint* in Active Directory.

    Ogni servizio agente di controller di dominio per la protezione Azure AD password crea anche un oggetto *serviceConnectionPoint* in Active Directory. Questo oggetto viene utilizzato principalmente per la creazione di report e la diagnostica.

1. Il servizio agente del controller di dominio è responsabile dell'avvio del download di un nuovo criterio password da Azure AD. Il primo passaggio consiste nell'individuare un servizio proxy di protezione Azure AD password eseguendo una query sulla foresta per gli oggetti proxy *serviceConnectionPoint* .

1. Quando viene trovato un servizio proxy disponibile, l'agente del controller di dominio invia una richiesta di download dei criteri password al servizio proxy. Il servizio proxy invia a sua volta la richiesta a Azure AD, quindi restituisce la risposta al servizio agente del controller di dominio.

1. Dopo che il servizio agente di controller di dominio riceve un nuovo criterio password da Azure AD, il servizio archivia i criteri in una cartella dedicata nella radice della condivisione di cartelle *SYSVOL* del dominio. Il servizio agente controller di dominio monitora inoltre questa cartella nel caso in cui i criteri più recenti vengano replicati in da altri servizi DC Agent nel dominio.

1. Il servizio agente di controller di dominio richiede sempre un nuovo criterio all'avvio del servizio. Dopo l'avvio del servizio agente controller di dominio, verifica la validità del criterio attualmente disponibile localmente. Se il criterio è più vecchio di un'ora, l'agente del controller di dominio richiede un nuovo criterio da Azure AD tramite il servizio proxy, come descritto in precedenza. Se il criterio corrente non è precedente a un'ora, l'agente del controller di dominio continuerà a utilizzare tale criterio.

1. Quando gli eventi di modifica della password vengono ricevuti da un controller di dominio, i criteri memorizzati nella cache vengono usati per determinare se la nuova password viene accettata o rifiutata.

### <a name="key-considerations-and-features"></a>Considerazioni e funzionalità principali

* Ogni volta che viene scaricato un criterio password di protezione Azure AD password, tale criterio è specifico di un tenant. In altre parole, i criteri password sono sempre una combinazione dell'elenco Microsoft Global Banned-password e dell'elenco di password escluse per ogni tenant.
* L'agente del controller di dominio comunica con il servizio proxy tramite RPC su TCP. Il servizio proxy è in ascolto delle chiamate su una porta RPC dinamica o statica, a seconda della configurazione.
* L'agente del controller di dominio non è mai in ascolto su una porta disponibile per la rete.
* Il servizio proxy non chiama mai il servizio agente di controller di dominio.
* Il servizio proxy è senza stato. Non memorizza mai nella cache i criteri o altri stati scaricati da Azure.
* Il servizio agente controller di dominio usa sempre i criteri password disponibili localmente più recenti per valutare la password di un utente. Se nel controller di dominio locale non è disponibile alcun criterio password, la password viene accettata automaticamente. Quando si verifica questo problema, viene registrato un messaggio di evento per avvisare l'amministratore.
* Azure AD la protezione con password non è un motore di applicazione dei criteri in tempo reale. È possibile che si verifichi un ritardo tra il momento in cui viene apportata una modifica alla configurazione dei criteri password in Azure AD e quando la modifica raggiunge e viene applicata a tutti i controller di dominio.
* Azure AD password protection funge da supplemento per i criteri password di servizi di dominio Active Directory esistenti, non per la sostituzione. Sono incluse eventuali altre DLL di filtro password di terze parti che possono essere installate. Active Directory Domain Services richiede sempre che tutti i componenti di convalida della password accettino prima di accettare una password.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Associazione di insiemi di strutture/tenant per la protezione Azure AD password

La distribuzione di Azure AD la protezione delle password in una foresta di Active Directory Domain Services richiede la registrazione di tale foresta con Azure AD. Ogni servizio proxy distribuito deve anche essere registrato con Azure AD. Queste registrazioni di foreste e proxy sono associate a un tenant di Azure AD specifico, che viene identificato in modo implicito dalle credenziali utilizzate durante la registrazione.

La foresta di servizi di dominio Active Directory e tutti i servizi proxy distribuiti in una foresta devono essere registrati con lo stesso tenant. Non è supportata la presenza di una foresta di servizi di dominio Active Directory o di qualsiasi servizio proxy nella foresta registrata in tenant Azure AD diversi. I sintomi di tale distribuzione configurata in modo errato includono l'impossibilità di scaricare i criteri password.

> [!NOTE]
> I clienti che hanno più tenant di Azure AD devono quindi scegliere un tenant distinto per registrare ogni foresta per Azure AD la protezione con password.

## <a name="download"></a>Download

I due programmi di installazione degli agenti necessari per la protezione Azure AD password sono disponibili nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la protezione Azure AD password locale, completare le procedure seguenti:

> [!div class="nextstepaction"]
> [Distribuire la protezione Azure AD password locale](howto-password-ban-bad-on-premises-deploy.md)
