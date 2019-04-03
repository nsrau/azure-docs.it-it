---
title: Protezione con password AD Azure - Azure Active Directory
description: Escludi password vulnerabili in un'istanza locale di Active Directory usando la protezione con password Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cd9f6112cbca78b323e0a14818b06f891a3f673
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862888"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Applicare la funzione della password di protezione di Azure AD per Windows Server Active Directory

Protezione tramite password di Azure AD è una funzionalità che migliorano i criteri password in un'organizzazione. Protezione tramite password di distribuzione da sito locale Usa entrambi globali e personalizzati di password da escludere gli elenchi che sono archiviati in Azure AD. Esegue la stessa controlli locale come Azure AD per le modifiche e basato sul cloud.

## <a name="design-principles"></a>Principi di progettazione

Protezione tramite password di Azure AD è stato progettato con questi principi presente:

* I controller di dominio mai necessario comunicare direttamente con internet.
* Non vengono aperte nuove porte di rete nei controller di dominio.
* Non sono necessarie modifiche dello schema di Active Directory. Il software utilizza Active Directory esistente **contenitore** e **serviceConnectionPoint** oggetti dello schema.
* Non Active Directory foresta o dominio funzionale livello minimo (funzionalità del dominio/FFL) è obbligatorio.
* Il software non creare o richiedono gli account in domini di Active Directory che protegge.
* Le password non crittografate utente non lasciare il controller di dominio durante le operazioni di convalida delle password o in qualsiasi momento.
* Distribuzione incrementale è supportata, tuttavia i criteri password vengono applicati solo in cui è installato l'agente Controller di dominio (DC agente). Vedere l'argomento successivo per altri dettagli.

## <a name="incremental-deployment"></a>Distribuzione incrementale

Protezione tramite password di Azure AD supporta la distribuzione incrementale tra i controller di dominio in un dominio di Active Directory, ma è importante comprendere il significato reale e quali sono i compromessi.

Il software dell'agente protezione controller di dominio di Azure AD password solo possibile convalidare le password quando viene installato in un controller di dominio e solo per le modifiche delle password che vengono inviati al controller di dominio. Non è possibile controllare quali controller di dominio vengono scelti dal computer client Windows per l'elaborazione delle modifiche di password utente. Per garantire un comportamento coerente e l'imposizione della protezione di protezione universale password, il software dell'agente controller di dominio deve essere installato in tutti i controller di dominio in un dominio.

Molte organizzazioni dovranno eseguire test accurati della protezione tramite password di Azure AD su un subset dei controller di dominio prima di eseguire una distribuzione completa. Protezione tramite password di Azure AD supporta la distribuzione parziale, Internet Explorer il software dell'agente controller di dominio in un determinato controller di dominio verrà convalidato attivamente le password anche quando altri controller di dominio nel dominio non è installato il software agente controller di dominio. Le distribuzioni parziale di questo tipo non sono sicuro e non sono consigliati oltre a scopo di test.

## <a name="architectural-diagram"></a>Diagramma dell'architettura

È importante comprendere la struttura sottostante e concetti di funzione prima di distribuire protezione tramite password di Azure AD in un ambiente Active Directory locale. Il diagramma seguente mostra come interagiscono tra i componenti di protezione con password:

![Come si integrano i componenti della password di protezione di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Il servizio proxy di Protezione password di Azure AD viene eseguito in tutti i computer aggiunti a un dominio nella foresta di Active Directory corrente. Il suo scopo principale consiste nell'inoltrare le richieste di download dei criteri di password dai controller di dominio ad Azure AD. Restituisce quindi le risposte da Azure AD per il controller di dominio.
* DLL dell'agente controller di dominio per il filtro password riceve le richieste di convalida delle password utente dal sistema operativo. Li inoltra al servizio agente controller di dominio in cui è in esecuzione in locale nel controller di dominio.
* Il servizio di controller di dominio agente protezione con password riceve le richieste di convalida della password dalla DLL dell'agente controller di dominio per il filtro password. Li elabora con i criteri password (disponibile sul computer locale) corrente e restituisce il risultato: *passa* oppure *esito negativo*.

## <a name="how-password-protection-works"></a>Come funziona la protezione con password

Ogni istanza del servizio Proxy di protezione delle Password di Azure AD promuove se stesso per i controller di dominio nella foresta tramite la creazione di un **serviceConnectionPoint** oggetto in Active Directory.

Ogni servizio dell'agente controller di dominio per la protezione con password crea anche un **serviceConnectionPoint** oggetto in Active Directory. Questo oggetto viene usato principalmente per reporting e la diagnostica.

Il servizio agente controller di dominio è responsabile dell'avvio del download di un nuovo criterio di password da Azure AD. Il primo passaggio consiste nel trovare un servizio Proxy di protezione delle Password di Azure AD eseguendo una query della foresta per il proxy **serviceConnectionPoint** oggetti. Quando viene trovato un servizio proxy disponibile, l'agente controller di dominio invia una richiesta di download dei criteri password per il servizio proxy. Il servizio proxy a sua volta invia la richiesta ad Azure AD. Il servizio proxy restituisce quindi la risposta per il servizio agente controller di dominio.

Dopo che il servizio agente controller di dominio riceve un nuovo criterio di password da Azure AD, il servizio archivia i criteri in una cartella dedicata alla radice del dominio *sysvol* condivisione cartella. Il servizio agente controller di dominio monitora anche questa cartella, nel caso in cui eseguire la replica di criteri più recenti da altri servizi di agente controller di dominio nel dominio.

Il servizio agente controller di dominio richiede sempre un nuovo criterio all'avvio del servizio. Una volta avviato il servizio agente controller di dominio, controlla la validità del criterio corrente disponibile sul computer locale ogni ora. Se il criterio è antecedente a un'ora, l'agente controller di dominio richiede un nuovo criterio da Azure AD, come descritto in precedenza. Se il criterio corrente non è antecedente a un'ora, l'agente controller di dominio continua a usare tale criterio.

Ogni volta che viene scaricato un criterio di password di Azure AD password protezione, tale criterio è specifico di un tenant. In altre parole, i criteri password sono sempre una combinazione di elenco della password da escludere globale di Microsoft e l'elenco di password da escludere personalizzati per ogni tenant.

L'agente controller di dominio comunica con il servizio proxy tramite RPC su TCP. Il servizio proxy è in ascolto per queste chiamate su una porta RPC dinamica o statica, a seconda della configurazione.

L'agente controller di dominio è mai in attesa su una porta di rete disponibili.

Il servizio proxy non chiama mai il servizio agente controller di dominio.

Il servizio proxy è senza stato. Memorizza nella cache mai i criteri o qualsiasi altro stato scaricato da Azure.

Il servizio agente controller di dominio Usa sempre i criteri password in locale disponibile più recente per valutare una password dell'utente. Se nessun criterio di password è disponibile nel controller di dominio locale, viene accettata automaticamente la password. In questo caso, un messaggio di evento viene registrato per informare l'utente amministratore.

Protezione tramite password di Azure AD non è un motore di applicazione dei criteri in tempo reale. Si può verificare un ritardo tra quando viene effettuata una modifica della configurazione dei criteri password in Azure AD e quando che modifica raggiunge e viene applicati a tutti i controller di dominio.

## <a name="foresttenant-binding-for-password-protection"></a>Associazione di foresta o al tenant per la protezione con password

Distribuzione di protezione di password di Azure AD in una foresta di Active Directory richiede la registrazione della foresta con Azure AD. Ogni servizio di proxy che viene distribuito anche deve essere registrato con Azure AD. Queste registrazioni per l'insieme di strutture e i proxy sono associati a uno specifico tenant di Azure AD, che è identificato in modo implicito dalle credenziali usate durante la registrazione.

La foresta di Active Directory e tutti i servizi proxy distribuito all'interno di una foresta devono essere registrati con lo stesso tenant. Si dispone di una foresta di Active Directory o i servizi proxy in insieme di strutture in corso la registrazione ad Azure AD diversi tenant non è supportato. I sintomi di questo tipo di distribuzione configurato correttamente includono l'impossibilità di scaricare i criteri password.

## <a name="download"></a>Download

I due programmi di installazione dell'agente necessari per la protezione di password di Azure AD sono disponibili i [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passaggi successivi
[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
