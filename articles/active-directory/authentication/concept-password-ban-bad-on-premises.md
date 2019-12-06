---
title: Protezione Azure AD password-Azure Active Directory
description: Escludere le password vulnerabili nei Active Directory locali usando la protezione Azure AD password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848647"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Applicare la funzione della password di protezione di Azure AD per Windows Server Active Directory

Azure AD Password Protection è una funzionalità che migliora i criteri password in un'organizzazione. La distribuzione locale di password Protection usa gli elenchi di password vietate globali e personalizzati archiviati in Azure AD. Esegue le stesse verifiche locali come Azure AD per le modifiche basate sul cloud. Questi controlli vengono eseguiti durante le modifiche delle password e gli scenari di reimpostazione della password.

## <a name="design-principles"></a>Principi di progettazione

Azure AD la protezione con password è progettata tenendo conto di questi principi:

* I controller di dominio non devono mai comunicare direttamente con Internet.
* Non vengono aperte nuove porte di rete nei controller di dominio.
* Non sono necessarie modifiche dello schema di Active Directory. Il software utilizza gli oggetti esistenti di Active Directory **container** e dello schema **serviceConnectionPoint** .
* Non è richiesto alcun livello di funzionalità minimo Active Directory dominio o foresta (DFL/FFL).
* Il software non crea o richiede account nei domini Active Directory protetti.
* Le password con testo non crittografato dell'utente lasciano mai il controller di dominio durante le operazioni di convalida della password o in qualsiasi altro momento.
* Il software non dipende da altre funzionalità di Azure AD; ad esempio Azure AD la sincronizzazione degli hash delle password non è correlata e non è necessaria per il funzionamento della protezione Azure AD password.
* La distribuzione incrementale è supportata, ma i criteri password vengono applicati solo in cui è installato l'agente controller di dominio (agente DC). Per ulteriori informazioni, vedere l'argomento successivo.

## <a name="incremental-deployment"></a>Distribuzione incrementale

Azure AD Password Protection supporta la distribuzione incrementale tra i controller di dominio in un dominio Active Directory, ma è importante comprendere il significato e i compromessi.

Il software dell'agente del controller di dominio Azure AD Password Protection può convalidare le password solo quando è installato in un controller di dominio e solo per le modifiche della password inviate al controller di dominio. Non è possibile controllare quali controller di dominio vengono scelti dai computer client Windows per l'elaborazione delle modifiche della password utente. Per garantire un comportamento coerente e l'imposizione della sicurezza Universal Password Protection, il software dell'agente controller di dominio deve essere installato in tutti i controller di dominio di un dominio.

Molte organizzazioni desiderano eseguire un attento test della protezione Azure AD password su un sottoinsieme dei controller di dominio prima di eseguire una distribuzione completa. Azure AD Password Protection supporta la distribuzione parziale, ovvero il software dell'agente di controller di dominio in un controller di dominio specifico convaliderà attivamente le password anche quando nel dominio non è installato il software dell'agente DC. Le distribuzioni parziali di questo tipo non sono sicure e non sono consigliate a scopo di test.

## <a name="architectural-diagram"></a>Diagramma dell'architettura

È importante comprendere i concetti relativi alla progettazione e alle funzioni sottostanti prima di distribuire Azure AD la protezione delle password in un ambiente Active Directory locale. Il diagramma seguente illustra il funzionamento combinato dei componenti di protezione con password:

![Modalità di interazione tra i componenti di protezione password di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Il servizio proxy di Protezione password di Azure AD viene eseguito in tutti i computer aggiunti a un dominio nella foresta di Active Directory corrente. Lo scopo principale è quello di inviare le richieste di download dei criteri password dai controller di dominio al Azure AD. Restituisce quindi le risposte da Azure AD al controller di dominio.
* La DLL del filtro password dell'agente del controller di dominio riceve le richieste di convalida della password utente dal sistema operativo. Li trasmette al servizio agente controller di dominio in esecuzione localmente sul controller di dominio.
* Il servizio agente di controller di dominio di protezione delle password riceve le richieste di convalida della password dalla DLL del filtro della password dell'agente del controller di dominio. Li elabora usando i criteri password correnti (disponibili localmente) e restituisce il risultato: *superato* o *non superato*.

## <a name="how-password-protection-works"></a>Funzionamento della protezione con password

Ogni istanza del servizio proxy Azure AD Password Protection si annuncia ai controller di dominio nella foresta creando un oggetto **serviceConnectionPoint** in Active Directory.

Ogni servizio agente di controller di dominio per la protezione con password crea anche un oggetto **serviceConnectionPoint** in Active Directory. Questo oggetto viene utilizzato principalmente per la creazione di report e la diagnostica.

Il servizio agente del controller di dominio è responsabile dell'avvio del download di un nuovo criterio password da Azure AD. Il primo passaggio consiste nell'individuare un servizio proxy di protezione Azure AD password eseguendo una query sulla foresta per gli oggetti proxy **serviceConnectionPoint** . Quando viene trovato un servizio proxy disponibile, l'agente del controller di dominio invia una richiesta di download dei criteri password al servizio proxy. Il servizio proxy invia a sua volta la richiesta a Azure AD. Il servizio proxy restituisce quindi la risposta al servizio agente del controller di dominio.

Dopo che il servizio agente di controller di dominio riceve un nuovo criterio password da Azure AD, il servizio archivia i criteri in una cartella dedicata nella radice della condivisione di cartelle *SYSVOL* del dominio. Il servizio agente controller di dominio monitora inoltre questa cartella nel caso in cui i criteri più recenti vengano replicati in da altri servizi DC Agent nel dominio.

Il servizio agente di controller di dominio richiede sempre un nuovo criterio all'avvio del servizio. Dopo l'avvio del servizio agente controller di dominio, verifica la validità del criterio attualmente disponibile localmente. Se il criterio è più vecchio di un'ora, l'agente del controller di dominio richiede un nuovo criterio da Azure AD tramite il servizio proxy, come descritto in precedenza. Se il criterio corrente non è precedente a un'ora, l'agente del controller di dominio continuerà a utilizzare tale criterio.

Ogni volta che viene scaricato un criterio password di protezione Azure AD password, tale criterio è specifico di un tenant. In altre parole, i criteri password sono sempre una combinazione dell'elenco Microsoft Global Banned-password e dell'elenco di password escluse per ogni tenant.

L'agente del controller di dominio comunica con il servizio proxy tramite RPC su TCP. Il servizio proxy è in ascolto delle chiamate su una porta RPC dinamica o statica, a seconda della configurazione.

L'agente del controller di dominio non è mai in ascolto su una porta disponibile per la rete.

Il servizio proxy non chiama mai il servizio agente di controller di dominio.

Il servizio proxy è senza stato. Non memorizza mai nella cache i criteri o altri stati scaricati da Azure.

Il servizio agente controller di dominio usa sempre i criteri password disponibili localmente più recenti per valutare la password di un utente. Se nel controller di dominio locale non è disponibile alcun criterio password, la password viene accettata automaticamente. Quando si verifica questo problema, viene registrato un messaggio di evento per avvisare l'amministratore.

Azure AD la protezione con password non è un motore di applicazione dei criteri in tempo reale. È possibile che si verifichi un ritardo tra il momento in cui viene apportata una modifica della configurazione dei criteri password in Azure AD e quando la modifica raggiunge e viene applicata a tutti i controller di dominio.

Azure AD la protezione con password funge da supplemento ai criteri di Active Directory password esistenti, non a una sostituzione. Sono incluse eventuali altre DLL di filtro password di terze parti che possono essere installate. Active Directory richiede sempre che tutti i componenti di convalida della password accettino prima di accettare una password.

## <a name="foresttenant-binding-for-password-protection"></a>Associazione di insiemi di strutture/tenant per la protezione con password

La distribuzione di Azure AD la protezione delle password in una foresta Active Directory richiede la registrazione di tale foresta con Azure AD. Ogni servizio proxy distribuito deve anche essere registrato con Azure AD. Queste registrazioni di foreste e proxy sono associate a un tenant di Azure AD specifico, che viene identificato in modo implicito dalle credenziali utilizzate durante la registrazione.

La foresta Active Directory e tutti i servizi proxy distribuiti in una foresta devono essere registrati con lo stesso tenant. Non è supportata la registrazione di una foresta Active Directory o di tutti i servizi proxy in tale foresta in tenant Azure AD diversi. I sintomi di tale distribuzione configurata in modo errato includono l'impossibilità di scaricare i criteri password.

## <a name="download"></a>Download

I due programmi di installazione degli agenti necessari per la protezione Azure AD password sono disponibili nell' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passaggi successivi
[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
