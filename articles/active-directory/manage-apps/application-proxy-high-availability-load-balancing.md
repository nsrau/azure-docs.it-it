---
title: Disponibilità elevata e bilanciamento del carico per Azure AD proxy di applicazione | Microsoft Docs
description: Funzionamento della distribuzione del traffico con la distribuzione del proxy di applicazione. Include suggerimenti su come ottimizzare le prestazioni del connettore e utilizzare il bilanciamento del carico per i server back-end.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 014fcf37930800858cd70f15c19e3f494d3f3776
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169797"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Disponibilità elevata e bilanciamento del carico delle applicazioni e dei connettori proxy dell'applicazione

Questo articolo illustra il funzionamento della distribuzione del traffico con la distribuzione del proxy di applicazione. Verranno illustrate le operazioni seguenti:

- Modalità di distribuzione del traffico tra utenti e connettori, oltre a suggerimenti per l'ottimizzazione delle prestazioni del connettore

- Flussi di traffico tra i connettori e i server app back-end, con consigli per il bilanciamento del carico tra più server back-end

## <a name="traffic-distribution-across-connectors"></a>Distribuzione del traffico tra i connettori

I connettori stabiliscono le connessioni in base ai principi per la disponibilità elevata. Non vi è alcuna garanzia che il traffico venga sempre distribuito uniformemente tra i connettori e che non esista alcuna affinità di sessione. Tuttavia, l'utilizzo varia e le richieste vengono inviate in modo casuale alle istanze del servizio proxy di applicazione. Di conseguenza, il traffico viene generalmente distribuito in modo quasi uniforme tra i connettori. Il diagramma e i passaggi seguenti illustrano il modo in cui vengono stabilite le connessioni tra gli utenti e i connettori.

![Diagramma che mostra le connessioni tra gli utenti e i connettori](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Un utente su un dispositivo client tenta di accedere a un'applicazione locale pubblicata tramite il proxy di applicazione.
2. La richiesta passa attraverso un Azure Load Balancer per determinare quale istanza del servizio proxy dell'applicazione deve eseguire la richiesta. Per area sono disponibili decine di istanze per accettare la richiesta. Questo metodo consente di distribuire uniformemente il traffico tra le istanze del servizio.
3. La richiesta viene inviata al [bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Il bus di servizio controlla se la connessione usava in precedenza un connettore esistente nel gruppo di connettori. In tal caso, riutilizza la connessione. Se nessun connettore è ancora associato alla connessione, sceglie un connettore disponibile in modo casuale per segnalare. Il connettore preleva quindi la richiesta dal bus di servizio.

   - Nel passaggio 2, le richieste vengono indirizzate a diverse istanze del servizio proxy di applicazione, quindi è più probabile che le connessioni vengano effettuate con connettori diversi. Di conseguenza, i connettori vengono usati quasi uniformemente all'interno del gruppo.

   - Una connessione viene ristabilita solo se la connessione è interruppe o si verifica un periodo di inattività di 10 minuti. Ad esempio, la connessione può essere interrotta quando viene riavviato un computer o un servizio del connettore o si verifica un'interruzione della rete.

5. Il connettore passa la richiesta al server back-end dell'applicazione. Quindi, l'applicazione invia la risposta al connettore.
6. Il connettore completa la risposta aprendo una connessione in uscita all'istanza del servizio da cui proviene la richiesta. Questa connessione viene quindi chiusa immediatamente. Per impostazione predefinita, ogni connettore è limitato a 200 connessioni in uscita simultanee.
7. La risposta viene quindi passata di nuovo al client dall'istanza del servizio.
8. Le richieste successive provenienti dalla stessa connessione ripeteranno i passaggi precedenti fino a quando la connessione non viene interruppe o rimane inattiva per 10 minuti.

Un'applicazione ha spesso molte risorse e apre più connessioni quando viene caricata. Ogni connessione esegue i passaggi precedenti per essere allocata a un'istanza del servizio, selezionare un nuovo connettore disponibile se la connessione non è ancora stata abbinata in precedenza a un connettore.


## <a name="best-practices-for-high-availability-of-connectors"></a>Procedure consigliate per la disponibilità elevata dei connettori

- A causa del modo in cui il traffico viene distribuito tra i connettori per la disponibilità elevata, è essenziale avere sempre almeno due connettori in un gruppo di connettori. Sono preferibili tre connettori per fornire un buffer aggiuntivo tra i connettori. Per determinare il numero corretto di connettori necessari, seguire la documentazione sulla pianificazione della capacità.

- Posizionare i connettori in connessioni in uscita diverse per evitare un singolo punto di errore. Se i connettori usano la stessa connessione in uscita, un problema di rete con la connessione può influisca su tutti i connettori che lo usano.

- Evitare di forzare il riavvio del connettore quando si è connessi alle applicazioni di produzione. Questa operazione potrebbe influire negativamente sulla distribuzione del traffico tra i connettori. Il riavvio dei connettori causa l'indisponibilità di più connettori e forza le connessioni al connettore rimanente disponibile. Il risultato è inizialmente un uso non uniforme dei connettori.

- Evitare tutte le forme di ispezione inline sulle comunicazioni TLS in uscita tra i connettori e Azure. Questo tipo di ispezione inline provoca una riduzione del flusso di comunicazione.

- Assicurarsi di lasciare gli aggiornamenti automatici in esecuzione per i connettori. Se il proxy di applicazione Connector Updater servizio è in esecuzione, i connettori vengono aggiornati automaticamente e ricevono il aggiornato più recente. Se il servizio Connector Updater non è visibile sul server, è necessario reinstallare il connettore per ottenere gli aggiornamenti.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Flusso del traffico tra i connettori e i server applicazioni back-end

Un'altra area chiave in cui la disponibilità elevata è un fattore è la connessione tra i connettori e i server back-end. Quando un'applicazione viene pubblicata tramite Azure AD proxy di applicazione, il traffico dagli utenti alle applicazioni passa attraverso tre hop:

1. L'utente si connette all'endpoint pubblico del servizio proxy dell'applicazione Azure AD in Azure. Viene stabilita la connessione tra l'indirizzo IP del client di origine (pubblico) del client e l'indirizzo IP dell'endpoint del proxy di applicazione.
2. Il connettore del proxy di applicazione estrae la richiesta HTTP del client dal servizio proxy di applicazione.
3. Il connettore del proxy di applicazione si connette all'applicazione di destinazione. Il connettore usa il proprio indirizzo IP per stabilire la connessione.

![Diagramma dell'utente che si connette a un'applicazione tramite il proxy di applicazione](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Considerazioni sui campi di intestazione X-Inoltred-for
In alcune situazioni, ad esempio il controllo, il bilanciamento del carico e così via, la condivisione dell'indirizzo IP di origine del client esterno con l'ambiente locale è un requisito. Per soddisfare i requisiti, Azure AD connettore del proxy di applicazione aggiunge il campo di intestazione X-inoltro-per con l'indirizzo IP del client di origine (pubblico) alla richiesta HTTP. Il dispositivo di rete appropriato (servizio di bilanciamento del carico, firewall) o il server Web o l'applicazione back-end può quindi leggere e usare le informazioni.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Procedure consigliate per il bilanciamento del carico tra più server app
Quando il gruppo di connettori assegnato all'applicazione proxy di applicazione dispone di due o più connettori e si esegue l'applicazione Web back-end su più server (server farm), è necessaria una strategia di bilanciamento del carico adeguata. Una strategia efficace garantisce che i server prelevino le richieste client in modo uniforme e impediscano il sovrautilizzo dei server nel server farm.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenario 1: L'applicazione back-end non richiede la persistenza della sessione
Lo scenario più semplice è quello in cui l'applicazione Web back-end non richiede la persistenza della sessione (persistenza della sessione). Qualsiasi richiesta dell'utente può essere gestita da qualsiasi istanza dell'applicazione back-end nel server farm. È possibile usare un servizio di bilanciamento del carico di livello 4 e configurarlo senza affinità. Alcune opzioni includono il bilanciamento del carico di rete Microsoft e Azure Load Balancer o un servizio di bilanciamento del carico da un altro fornitore. In alternativa, è possibile configurare il DNS round robin.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenario 2: L'applicazione back-end richiede la persistenza della sessione
In questo scenario, l'applicazione Web back-end richiede la persistenza della sessione (persistenza della sessione) durante la sessione autenticata. Tutte le richieste dell'utente devono essere gestite dall'istanza dell'applicazione back-end eseguita nello stesso server del server farm.
Questo scenario può essere più complesso perché il client di solito stabilisce più connessioni al servizio proxy di applicazione. Le richieste su connessioni diverse possono arrivare a connettori e server diversi nella farm. Poiché ogni connettore usa il proprio indirizzo IP per questa comunicazione, il servizio di bilanciamento del carico non può garantire la viscosità della sessione in base all'indirizzo IP dei connettori. Non è possibile usare l'affinità IP di origine.
Di seguito sono riportate alcune opzioni per lo scenario 2:

- Opzione 1: Basare la persistenza della sessione su un cookie di sessione impostato dal servizio di bilanciamento del carico. Questa opzione è consigliata perché consente di distribuire il carico in modo più uniforme tra i server back-end. Richiede un servizio di bilanciamento del carico di livello 7 con questa funzionalità e che può gestire il traffico HTTP e terminare la connessione SSL. È possibile usare applicazione Azure Gateway (affinità di sessione) o un servizio di bilanciamento del carico da un altro fornitore.

- Opzione 2: Basare la persistenza della sessione nel campo dell'intestazione X-Inoltred-for. Questa opzione richiede un servizio di bilanciamento del carico di livello 7 con questa funzionalità e che può gestire il traffico HTTP e terminare la connessione SSL.  

- Opzione 3: Configurare l'applicazione back-end in modo che non richieda la persistenza della sessione.

Per informazioni sui requisiti di bilanciamento del carico dell'applicazione back-end, consultare la documentazione del fornitore del software.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare il proxy dell'applicazione](application-proxy-add-on-premises-application.md)
- [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)
- [Abilitare l'accesso condizionale](application-proxy-integrate-with-sharepoint-server.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](application-proxy-troubleshoot.md)
- [Informazioni su come Azure AD architettura supporta la disponibilità elevata](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
