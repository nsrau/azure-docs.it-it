---
title: Anteprima della protezione password di Azure AD
description: Escludere password vulnerabili in Active Directory locale usando la funzionalità di anteprima di protezione password di Azure AD
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
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415749"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Anteprima: Applicare la funzione della password di protezione di Azure AD per Windows Server Active Directory

|     |
| --- |
| La password di protezione e l'elenco personalizzato di password escluse di Azure AD sono le funzioni in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

La protezione password di Azure AD è una nuova funzionalità in anteprima pubblica basata su Azure Active Directory (Azure AD) volta a migliorare i criteri password in un'organizzazione. La distribuzione locale della protezione password di Azure AD usa l'elenco globale e l'elenco personalizzato di password escluse archiviati in Azure AD ed esegue in locale gli stessi controlli svolti per le modifiche basate sul cloud di Azure AD.

## <a name="design-principles"></a>Principi di progettazione

Protezione password di AD Azure per Active Directory è stata progettata tenendo conto dei principi seguenti:

* I controller di dominio non sono mai necessari per comunicare direttamente con Internet
* Non vengono aperte nuove porte di rete nei controller di dominio.
* Non sono necessarie modifiche dello schema di Active Directory. Il software usa il contenitore Active Directory esistente e gli oggetti dello schema serviceConnectionPoint.
* Non è obbligatorio nessun requisito minimo per il livello funzionale della foresta o del dominio di Active Directory.
* Il software non crea né richiede account nei domini di Active Directory protetti.
* Le password utente non crittografate non lasciano mai il controller di dominio (durante le operazioni di convalida delle password o in qualsiasi altro momento).
* La distribuzione incrementale è supportata, ma i criteri password vengono applicati solo dove è installato l'agente del controller di dominio.
* È consigliabile installare l'agente del controller di dominio su tutti i controller di dominio per garantire l'imposizione di sicurezza diffusa della protezione della password.

## <a name="architectural-diagram"></a>Diagramma dell'architettura

È importante comprendere la struttura sottostante e i concetti funzionali prima di distribuire Protezione password di Azure AD in un ambiente Active Directory locale. Il diagramma seguente illustra l'interazione tra i componenti di Protezione password di Azure AD:

![Come si integrano i componenti della password di protezione di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Il diagramma precedente mostra i tre componenti software di base che costituiscono la protezione password di Azure AD:

* Il servizio proxy di Protezione password di Azure AD viene eseguito in tutti i computer aggiunti a un dominio nella foresta di Active Directory corrente. Lo scopo principale è di inoltrare le richieste dai controller di dominio ad Azure AD e restituire la risposta da Azure AD al controller di dominio.
* La DLL di filtro delle password dell'agente del controller di dominio di Protezione password di Azure AD, riceve le richieste di convalida delle password dal sistema operativo e le inoltra al servizio agente del controller di dominio di Protezione password di Azure AD in esecuzione in locale nel controller di dominio.
* Il servizio agente del controller di dominio di Protezione password di Azure AD riceve le richieste di convalida della password dalla DLL di filtro delle password dell'agente del controller di dominio, le elabora in base ai criteri password correnti disponibili localmente e restituisce il risultato di esito positivo o negativo.

## <a name="theory-of-operations"></a>Teoria delle operazioni

Considerati quindi i principi di progettazione e diagramma precedenti, come funziona Protezione Password di Azure AD?

Ogni servizio proxy di Protezione password di Azure AD promuove se stesso ai controller di dominio nella foresta tramite la creazione di un oggetto serviceConnectionPoint in Active Directory.

Ogni servizio agente del controller di dominio di Protezione password di Azure AD crea anche un oggetto serviceConnectionPoint in Active Directory. Tuttavia viene usato principalmente per la creazione di report e per la diagnostica.

Il servizio agente del controller di dominio di Protezione password di Azure AD è responsabile dell'avvio del download di nuovi criteri di password da Azure AD. Il primo passaggio serve a individuare un servizio proxy di protezione delle Password di Azure AD eseguendo una query di una foresta di oggetti proxy serviceConnectionPoint. Dopo aver trovato un servizio proxy disponibile, viene inviata una richiesta di download dei criteri di password dal servizio agente del controller di dominio al servizio proxy, che a sua volta li invia ad Azure AD e quindi restituisce la risposta al servizio agente del controller di dominio. Dopo aver ricevuto nuovi criteri di password da Azure AD, il servizio agente del controller di dominio archivia i criteri in una cartella dedicata, alla radice della condivisione sysvol di dominio. Il servizio agente del controller di dominio monitora anche questa cartella, nel caso in cui criteri più recenti effettuino la replica da altri servizio agente del controller di dominio all'interno del dominio.

Il servizio agente del controller di dominio di Protezione password di Azure AD richiederà sempre nuovi criteri all'avvio del servizio. Dopo l'avvio, il servizio agente del controller di dominio verificherà periodicamente (una volta ogni ora) l'età dei criteri correnti disponibili localmente; se i criteri correnti sono più vecchi di un'ora, il servizio agente del controller di dominio richiederà nuovi criteri ad Azure AD come descritto sopra, in caso contrario l'agente del controller di dominio continuerà ad usare i criteri correnti.

Il servizio agente del controller di dominio di Protezione password di Azure AD comunica con il servizio proxy della password di protezione di Azure AD tramite RPC (Remote Procedure Call) su TCP. Il servizio proxy è in ascolto per queste chiamate su porta dinamica o RPC statica (come configurato).

Il servizio agente del controller di dominio di Protezione password di Azure AD non ascolta mai su una porta disponibile in rete e il servizio proxy non tenta mai di chiamare il servizio agente del controller di dominio.

Il servizio proxy di Protezione password di Azure AD è senza stato; non memorizza mai nella cache i criteri o qualsiasi altro stato scaricato da Azure.

Il servizio agente del controller di dominio di Protezione password di Azure AD valuterà sempre e solo la password di un utente usando i criteri di password più recenti disponibili in locale. Se non sono disponibili criteri di password nel controller di dominio locale, la password verrà automaticamente accettata e verrà registrato un messaggio di log eventi per avvisare l'amministratore.

Protezione password di Azure AD non è un motore di applicazione dei criteri in tempo reale. Potrebbe esserci un ritardo tra una modifica della configurazione dei criteri password in Azure AD e la sua distribuzione e applicazione in tutti i controller di dominio.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Associazione di foresta o tenant per Protezione password di Azure AD

La distribuzione di Protezione password di Azure AD in una foresta di Active Directory richiede la registrazione della foresta di Active Directory e di qualsiasi servizio proxy distribuito di Protezione password di Azure AD, con Azure AD. Tali registrazioni (foreste e proxy) sono associate a uno specifico tenant di Azure AD che è identificato in modo implicito tramite le credenziali usate durante la registrazione. Ogni volta che vengono scaricati i criteri di password di Protezione password di Azure AD, sono sempre specifici per questo tenant; ad esempio, quei criteri saranno sempre una combinazione dell'elenco globale delle password vietate di Microsoft e l'elenco delle password vietate personalizzate per tenant. Non è supportato per configurare diversi domini o proxy in una foresta da legare a diversi tenant di Azure AD.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I vantaggi dell'elenco globale di password escluse si applicano a tutti gli utenti di Azure Active Directory (Azure AD).

L'elenco personalizzato di password escluse richiede licenze di Azure AD Basic.

Protezione password di Azure AD per Windows Server Active Directory richiede licenze di Azure AD Premium.

Informazioni aggiuntive sulle licenze, inclusi i costi, sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Download

I due agenti di installazione per Protezione password di Azure AD, possono essere scaricati dall'[area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
