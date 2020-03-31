---
title: Azure AD password protection - Azure Active Directory
description: Escludere le password deboli in Active Directory locale utilizzando la protezione con password di Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848647"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Applicare la funzione della password di protezione di Azure AD per Windows Server Active Directory

La protezione con password di Azure AD è una funzionalità che migliora i criteri password in un'organizzazione. La distribuzione locale della protezione con password usa gli elenchi delle password non in linea globali e personalizzati archiviati in Azure AD. Esegue gli stessi controlli in locale delle modifiche basate su cloud in Azure AD. Questi controlli vengono eseguiti durante le modifiche della password e gli scenari di reimpostazione della password.

## <a name="design-principles"></a>Principi di progettazione

La protezione con password di Azure AD è progettata tenendo presenti i principi seguenti:Azure AD password protection is designed with these principles in mind:

* I controller di dominio non devono mai comunicare direttamente con Internet.
* Non vengono aperte nuove porte di rete nei controller di dominio.
* Non sono necessarie modifiche dello schema di Active Directory. Il software utilizza gli oggetti dello schema **del contenitore** di Active Directory e **serviceConnectionPoint** esistenti.
* Non è richiesto alcun livello minimo di funzionalità del dominio o della foresta di Active Directory (DFL/FFL).
* Il software non crea o richiede account nei domini di Active Directory che protegge.
* Le password non crittografate dell'utente non lasciano mai il controller di dominio, durante le operazioni di convalida delle password o in qualsiasi altro momento.
* Il software non dipende da altre funzionalità di Azure AD. ad esempio, la sincronizzazione dell'hash delle password di Azure AD non è correlata e non è necessaria per il funzionamento della protezione con password di Azure AD.
* La distribuzione incrementale è supportata, tuttavia i criteri password vengono applicati solo quando è installato l'agente controller di dominio (agente controller di dominio). Vedere l'argomento successivo per ulteriori dettagli.

## <a name="incremental-deployment"></a>Distribuzione incrementale

La protezione con password di Azure AD supporta la distribuzione incrementale tra controller di dominio in un dominio di Active Directory, ma è importante comprendere che cosa significa realmente e quali sono i compromessi.

Il software dell'agente controller di dominio di protezione password di Azure AD può convalidare le password solo quando è installato in un controller di dominio e solo per le modifiche delle password inviate a tale controller di dominio. Non è possibile controllare quali controller di dominio vengono scelti dai computer client Windows per l'elaborazione delle modifiche delle password utente. Per garantire un comportamento coerente e l'imposizione di protezione universale della password, il software dell'agente controller di dominio DEVE essere installato in tutti i controller di dominio di un dominio.

Molte organizzazioni vorranno eseguire un'attenta verifica della protezione con password di Azure AD in un sottoinsieme dei controller di dominio prima di eseguire una distribuzione completa. La protezione con password di Azure AD supporta la distribuzione parziale, ovvero il software dell'agente controller di dominio in un determinato controller di dominio convaliderà attivamente le password anche quando in altri controller di dominio nel dominio non è installato il software dell'agente controller di dominio. Le distribuzioni parziali di questo tipo NON sono sicure e NON sono consigliate se non a scopo di test.

## <a name="architectural-diagram"></a>Diagramma dell'architettura

È importante comprendere i concetti di progettazione e funzionalità sottostanti prima di distribuire la protezione con password di Azure AD in un ambiente Active Directory locale. Nel diagramma seguente viene illustrato come i componenti della protezione con password si uniscono:

![Come si integrano i componenti della password di protezione di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Il servizio proxy di Protezione password di Azure AD viene eseguito in tutti i computer aggiunti a un dominio nella foresta di Active Directory corrente. Il suo scopo principale è quello di inoltrare le richieste di download dei criteri password dai controller di dominio ad Azure AD. Restituisce quindi le risposte da Azure AD al controller di dominio.
* La DLL del filtro password dell'agente controller di dominio riceve le richieste di convalida della password dell'utente dal sistema operativo. Vengono inoltrati al servizio agente di controller di dominio in esecuzione localmente sul controller di dominio.
* Il servizio Agente controller di dominio di protezione con password riceve le richieste di convalida della password dalla DLL del filtro password dell'agente controller di dominio. Li elabora utilizzando i criteri password correnti (disponibili localmente) e restituisce il risultato: *pass* o *fail*.

## <a name="how-password-protection-works"></a>Come funziona la protezione con password

Ogni istanza del servizio Proxy di protezione password di Azure AD annuncia i controller di dominio nella foresta creando un oggetto serviceConnectionPoint in Active Directory.Each AD Password Protection Proxy service instance to the domain controllers in the forest by creating a **serviceConnectionPoint** object in Active Directory.

Ogni servizio Agente controller di dominio per la protezione con password crea anche un oggetto serviceConnectionPoint in Active Directory.Each DC Agent service for password protection also creates a **serviceConnectionPoint** object in Active Directory. Questo oggetto viene utilizzato principalmente per la creazione di report e la diagnostica.

Il servizio Agente controller di dominio è responsabile dell'avviamento del download di un nuovo criterio password da Azure AD. Il primo passaggio consiste nell'individuare un servizio proxy di protezione password di Azure AD eseguendo una query sulla foresta per gli oggetti **serviceConnectionPoint** proxy. Quando viene trovato un servizio proxy disponibile, l'agente controller di dominio invia una richiesta di download dei criteri password al servizio proxy. Il servizio proxy invia a sua volta la richiesta ad Azure AD. Il servizio proxy restituisce quindi la risposta al servizio Agente controller di dominio.

Dopo che il servizio Agente controller di dominio riceve un nuovo criterio password da Azure AD, il servizio archivia i criteri in una cartella dedicata nella radice della condivisione della cartella *sysvol* del dominio. Il servizio Agente controller di dominio monitora inoltre questa cartella nel caso in cui i criteri più recenti vengano replicati da altri servizi Agente controller di dominio nel dominio.

Il servizio Agente controller di dominio richiede sempre un nuovo criterio all'avvio del servizio. Dopo l'avvio del servizio Agente controller di dominio, controlla l'età dei criteri disponibili localmente correnti ogni ora. Se i criteri sono più vecchi di un'ora, l'agente controller di dominio richiede un nuovo criterio da Azure AD tramite il servizio proxy, come descritto in precedenza. Se il criterio corrente non è più vecchio di un'ora, l'agente controller di dominio continua a utilizzare tale criterio.

Ogni volta che viene scaricato un criterio password di password di password di password di protezione password di Azure AD, tale criterio è specifico di un tenant. In altre parole, i criteri password sono sempre una combinazione dell'elenco globale delle password vietate di Microsoft e dell'elenco delle password escluse personalizzate per tenant.

L'agente controller di dominio comunica con il servizio proxy tramite RPC su TCP. Il servizio proxy è in ascolto di queste chiamate su una porta RPC dinamica o statica, a seconda della configurazione.

L'agente controller di dominio non è mai in ascolto su una porta disponibile per la rete.

Il servizio proxy non chiama mai il servizio Agente controller di dominio.

Il servizio proxy è senza stato. Non memorizza mai nella cache i criteri o qualsiasi altro stato scaricato da Azure.It never caches policies or any other state downloaded from Azure.

Il servizio Agente controller di dominio utilizza sempre i criteri password disponibili localmente più recenti per valutare la password di un utente. Se nel controller di dominio locale non è disponibile alcun criterio password, la password viene accettata automaticamente. In questo caso, viene registrato un messaggio di evento per avvisare l'amministratore.

La protezione con password di Azure AD non è un motore di applicazione dei criteri in tempo reale. Può verificarsi un ritardo tra quando viene apportata una modifica alla configurazione dei criteri password in Azure AD e quando tale modifica raggiunge e viene applicata a tutti i controller di dominio.

La protezione con password di Azure AD funge da supplemento ai criteri password di Active Directory esistenti, non come sostituzione. Sono incluse tutte le altre DLL di filtro password di terze parti che possono essere installate. Active Directory richiede sempre che tutti i componenti di convalida delle password accettino prima di accettare una password.

## <a name="foresttenant-binding-for-password-protection"></a>Binding tra foreste/tenant per la protezione con password

La distribuzione della protezione con password di Azure AD in una foresta di Active Directory richiede la registrazione di tale foresta con Azure AD. Ogni servizio proxy distribuito deve essere registrato anche in Azure AD. Queste registrazioni di foreste e proxy sono associate a un tenant di Azure AD specifico, identificato in modo implicito dalle credenziali usate durante la registrazione.

La foresta di Active Directory e tutti i servizi proxy distribuiti all'interno di una foresta devono essere registrati con lo stesso tenant. Non è supportato che una foresta di Active Directory o servizi proxy in tale foresta vengano registrati in tenant di Azure AD diversi. I sintomi di una distribuzione non configurata in modo errato includono l'impossibilità di scaricare i criteri password.

## <a name="download"></a>Download

I due programmi di installazione degli agenti necessari per la protezione con password di Azure AD sono disponibili [nell'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passaggi successivi
[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
