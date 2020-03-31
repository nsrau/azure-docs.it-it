---
title: Disponibilità elevata e bilanciamento del carico - Proxy di applicazione di Azure ADHigh availability and load balancing - Azure AD Application Proxy
description: Funzionamento della distribuzione del traffico con la distribuzione del proxy di applicazione. Include suggerimenti su come ottimizzare le prestazioni dei connettori e utilizzare il bilanciamento del carico per i server back-end.
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
ms.openlocfilehash: 3202c2fbfedfce0b0b52be94b1e0d165a6e72546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481314"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Disponibilità elevata e bilanciamento del carico dei connettori e delle applicazioni del proxy di applicazioneHigh availability and load balancing of your Application Proxy connectors and applications

In questo articolo viene illustrato il funzionamento della distribuzione del traffico con la distribuzione del proxy di applicazione. Discuteremo di:

- Modalità di distribuzione del traffico tra utenti e connettori, oltre a suggerimenti per ottimizzare le prestazioni dei connettori

- Flusso del traffico tra connettori e server applicazioni back-end, con consigli per il bilanciamento del carico tra più server back-end

## <a name="traffic-distribution-across-connectors"></a>Distribuzione del traffico tra connettori

I connettori stabiliscono le connessioni in base ai principi per la disponibilità elevata. Non è garantito che il traffico venga sempre distribuito uniformemente tra i connettori e non vi sia alcuna affinità di sessione. Tuttavia, l'utilizzo varia e le richieste vengono inviate in modo casuale alle istanze del servizio proxy di applicazione. Di conseguenza, il traffico viene in genere distribuito quasi uniformemente tra i connettori. Il diagramma e i passaggi seguenti illustrano come vengono stabilite le connessioni tra utenti e connettori.

![Diagramma che mostra le connessioni tra utenti e connettori](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Un utente in un dispositivo client tenta di accedere a un'applicazione locale pubblicata tramite il proxy di applicazione.
2. La richiesta passa tramite un servizio di bilanciamento del carico di Azure per determinare quale istanza del servizio proxy di applicazione deve accettare la richiesta. Per ogni area, sono disponibili decine di istanze per accettare la richiesta. Questo metodo consente di distribuire in modo uniforme il traffico tra le istanze del servizio.
3. La richiesta viene inviata al [bus](https://docs.microsoft.com/azure/service-bus-messaging/)di servizio .
4. Il bus di servizio verifica se la connessione in precedenza utilizzava un connettore esistente nel gruppo di connettori. In tal caso, riutilizza la connessione. Se nessun connettore è ancora associato alla connessione, sceglie un connettore disponibile in modo casuale a cui segnalare. Il connettore preleva quindi la richiesta dal bus di servizio.

   - Nel passaggio 2, le richieste passano a istanze del servizio proxy di applicazione diverse, pertanto è più probabile che le connessioni vengano effettuate con connettori diversi. Di conseguenza, i connettori vengono utilizzati quasi in modo uniforme all'interno del gruppo.

   - Una connessione viene ristabilita solo se la connessione viene interrotta o si verifica un periodo di inattività di 10 minuti. Ad esempio, la connessione potrebbe essere interrotta quando un computer o il servizio connettore viene riavviato o si verifica un'interruzione della rete.

5. Il connettore passa la richiesta al server back-end dell'applicazione. Quindi l'applicazione invia la risposta al connettore.
6. Il connettore completa la risposta aprendo una connessione in uscita all'istanza del servizio da cui proveniva la richiesta. Quindi questa connessione viene immediatamente chiusa. Per impostazione predefinita, ogni connettore è limitato a 200 connessioni in uscita simultanee.
7. La risposta viene quindi passata al client dall'istanza del servizio.
8. Le richieste successive dalla stessa connessione ripetere i passaggi precedenti fino a quando la connessione non viene interrotta o è inattiva per 10 minuti.

Un'applicazione ha spesso molte risorse e apre più connessioni quando viene caricata. Ogni connessione passa attraverso i passaggi precedenti per essere allocata a un'istanza del servizio, selezionare un nuovo connettore disponibile se la connessione non è ancora precedentemente associata a un connettore.


## <a name="best-practices-for-high-availability-of-connectors"></a>Procedure consigliate per la disponibilità elevata dei connettoriBest practices for high availability of connectors

- A causa del modo in cui il traffico viene distribuito tra i connettori per la disponibilità elevata, è essenziale avere sempre almeno due connettori in un gruppo di connettori. È preferibile fornire buffer aggiuntivo tra i connettori. Per determinare il numero corretto di connettori necessari, seguire la documentazione relativa alla pianificazione della capacità.

- Posizionare i connettori su connessioni in uscita diverse per evitare un singolo punto di errore. Se i connettori utilizzano la stessa connessione in uscita, un problema di rete con la connessione può influire su tutti i connettori che la utilizzano.

- Evitare di forzare il riavvio dei connettori quando si è connessi alle applicazioni di produzione. Questa operazione potrebbe influire negativamente sulla distribuzione del traffico tra i connettori. Il riavvio dei connettori causa la disponibilità di un numero maggiore di connettori e forza le connessioni al connettore disponibile rimanente. Il risultato è un uso irregolare dei connettori inizialmente.

- Evitare tutte le forme di ispezione in linea sulle comunicazioni TLS in uscita tra i connettori e Azure.Avoid all forms of inline inspection on outbound TLS communications between connectors and Azure. Questo tipo di ispezione in linea causa la degradazione del flusso di comunicazione.

- Assicurarsi di mantenere in esecuzione gli aggiornamenti automatici per i connettori. Se il servizio Application Proxy Connector Updater è in esecuzione, i connettori vengono aggiornati automaticamente e ricevono l'aggiornamento più recente. Se non viene visualizzato il servizio di aggiornamento del connettore nel server, è necessario reinstallare il connettore per ottenere gli aggiornamenti.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Flusso di traffico tra connettori e server applicazioni back-end

Un'altra area chiave in cui la disponibilità elevata è un fattore è la connessione tra i connettori e i server back-end. Quando un'applicazione viene pubblicata tramite il proxy di applicazione di Azure AD, il traffico dagli utenti alle applicazioni passa attraverso tre hop:When an application is published through Azure AD Application Proxy, traffic from the users to the applications flows through three hops:

1. The user connects to the Azure AD Application Proxy service public endpoint on Azure. La connessione viene stabilita tra l'indirizzo IP del client di origine (pubblico) del client e l'indirizzo IP dell'endpoint del proxy di applicazione.
2. Il connettore proxy di applicazione estrae la richiesta HTTP del client dal servizio proxy di applicazione.
3. Il connettore proxy di applicazione si connette all'applicazione di destinazione. Il connettore utilizza il proprio indirizzo IP per stabilire la connessione.

![Diagramma dell'utente che si connette a un'applicazione tramite il proxy di applicazione](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Considerazioni sul campo di intestazione X-Forwarded-For
In alcune situazioni (ad esempio il controllo, il bilanciamento del carico e così via), è necessario condividere l'indirizzo IP di origine del client esterno con l'ambiente locale. Per soddisfare il requisito, il connettore proxy di applicazione di Azure AD aggiunge il campo di intestazione X-Forwarded-For con l'indirizzo IP del client di origine (pubblico) alla richiesta HTTP. Il dispositivo di rete appropriato (bilanciamento del carico, firewall) o il server Web o l'applicazione back-end possono quindi leggere e utilizzare le informazioni.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Procedure consigliate per il bilanciamento del carico tra più server applicazioniBest practices for load balancing among multiple app servers
Quando il gruppo di connettori assegnato all'applicazione proxy di applicazione dispone di due o più connettori e si esegue l'applicazione Web back-end in più server (server farm), è necessaria una buona strategia di bilanciamento del carico. Una buona strategia garantisce che i server rilevano le richieste dei client in modo uniforme e impediscono un utilizzo eccessivo o inesondante dei server nella server farm.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenario 1: l'applicazione back-end non richiede la persistenza della sessioneScenario 1: Back-end application does not require session persistence
Lo scenario più semplice è quello in cui l'applicazione Web back-end non richiede la persistenza della sessione (persistenza della sessione). Qualsiasi richiesta dell'utente può essere gestita da qualsiasi istanza dell'applicazione back-end nella server farm. È possibile utilizzare un servizio di bilanciamento del carico di livello 4 e configurarlo senza affinità. Alcune opzioni includono Microsoft Network Load Balancing e Azure Load Balancer o un servizio di bilanciamento del carico di un altro fornitore. In alternativa, è possibile configurare il DNS round robin.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenario 2: l'applicazione back-end richiede la persistenza della sessioneScenario 2: Back-end application requires session persistence
In questo scenario, l'applicazione web back-end richiede la vissistenza della sessione (persistenza della sessione) durante la sessione autenticata. Tutte le richieste dell'utente devono essere gestite dall'istanza dell'applicazione back-end in esecuzione nello stesso server della server farm.
Questo scenario può essere più complicato perché il client stabilisce in genere più connessioni al servizio proxy di applicazione. Le richieste su connessioni diverse potrebbero arrivare a connettori e server diversi nella farm. Poiché ogni connettore utilizza il proprio indirizzo IP per questa comunicazione, il servizio di bilanciamento del carico non è in grado di garantire la viibilità della sessione in base all'indirizzo IP dei connettori. Non è possibile utilizzare l'affinità IP di origine.
Di seguito sono riportate alcune opzioni per lo scenario 2:Here are some options for scenario 2:

- Opzione 1: Basare la persistenza della sessione in un cookie di sessione impostato dal servizio di bilanciamento del carico. Questa opzione è consigliata perché consente di distribuire il carico in modo più uniforme tra i server back-end. Richiede un servizio di bilanciamento del carico di livello 7 con questa funzionalità e in grado di gestire il traffico HTTP e terminare la connessione TLS. È possibile usare il gateway applicazione di Azure (affinità di sessione) o un servizio di bilanciamento del carico di un altro fornitore.

- Opzione 2: Basare la persistenza della sessione nel campo di intestazione X-Forwarded-For. Questa opzione richiede un servizio di bilanciamento del carico di livello 7 con questa funzionalità e in grado di gestire il traffico HTTP e terminare la connessione TLS.  

- Opzione 3: Configurare l'applicazione back-end in modo che non richieda la persistenza della sessione.

Consultare la documentazione del fornitore del software per comprendere i requisiti di bilanciamento del carico dell'applicazione back-end.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare il proxy dell’applicazione](application-proxy-add-on-premises-application.md)
- [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)
- [Abilitare l'accesso condizionaleEnable Conditional Access](application-proxy-integrate-with-sharepoint-server.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](application-proxy-troubleshoot.md)
- [Informazioni su come l'architettura di Azure AD supporta la disponibilità elevataLearn how Azure AD architecture supports high availability](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
