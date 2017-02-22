---
title: Considerazioni relative alla topologia di rete quando si usa il proxy applicazione Azure AD | Documentazione Microsoft
description: Tratta alcune considerazioni relative alla topologia di rete quando si usa il proxy applicazione Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fdc00865f31352026a64c2769d9d65047bb2fa6
ms.openlocfilehash: 26ec9167f1f883c3c71947a55142020fb48e206a


---

# <a name="network-topology-considerations-when-using-azure-ad-application-proxy"></a>Considerazioni relative alla topologia di rete quando si usa il proxy applicazione Azure AD
> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 

Questo articolo spiega alcune considerazioni relative alla topologia di rete quando si usa il proxy applicazione Azure AD per la pubblicazione e l'accesso alle applicazioni da remoto. 

## <a name="traffic-flow"></a>Flusso del traffico

Quando un'applicazione viene pubblicata attraverso il proxy applicazione Azure AD, tutto il traffico dagli utenti alle applicazioni back-end di destinazione passa attraverso i seguenti hop:

* Hop 1: dall'utente all'endpoint pubblico del servizio proxy applicazione Azure AD in Azure
* Hop 2: dal servizio proxy applicazione al connettore
* Hop 3: dal connettore all'applicazione di destinazione

 ![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-app-proxy-service"></a>Posizione del tenant e servizio proxy applicazione

Quando ci si registra per un tenant Azure AD, l'area del tenant (Stati Uniti, EMEA, Asia Pacifico e così via) è determinata in base al paese specificato. Quando si abilita il proxy applicazione, le istanze del servizio proxy applicazione per il tenant sono visualizzate nella stessa area del tenant Azure AD o nell'area più vicina. 

Ad esempio, se l'area del tenant Azure AD è Unione Europea (UE), tutti i connettori del proxy applicazione Azure AD saranno connessi alle istanze del servizio proxy applicazione nei data center di Azure dell'Unione Europea. Questo significa che tutti gli utenti passeranno attraverso le istanze del servizio proxy applicazione in questa posizione quando tenteranno di accedere alle applicazioni pubblicate.

## <a name="considerations-for-reducing-latency"></a>Considerazioni per ridurre la latenza

Tutte le soluzioni proxy introducono latenza nella connessione di rete. Indipendentemente dalla soluzione proxy o VPN scelta per l'accesso remoto, sarà sempre incluso un gruppo di server che consentono la connessione all'interno della rete aziendale. 

Le aziende tradizionalmente usano includere endpoint server nella rete perimetrale (DMZ). Ma con il proxy applicazione Azure AD non è richiesta alcuna rete perimetrale.  Questo perché con il proxy applicazione il traffico passa attraverso il servizio proxy nel cloud, mentre i connettori si trovano nella rete aziendale.

### <a name="connector-placement"></a>Posizionamento dei connettori

Il servizio proxy applicazione sceglie il percorso delle istanze per l'utente, in base alla posizione del tenant. Perciò l'utente deve decidere dove installare il connettore e può in questo modo definire le caratteristiche di latenza end-to-end del traffico di rete.

Ecco alcune domande a cui rispondere quando si configura il servizio proxy applicazione:

* Dove si trova l'app?
* Dove si trovano la maggior parte degli utenti che accedono all'app?
* Dove si trova l'istanza del proxy applicazione (questo dipende dal tenant)?
* È già disponibile una connessione di rete dedicata ai data center di Azure (come Express Route o una configurazione VPN simile)?

Il posizionamento del connettore determinerà la latenza degli hop 2 e 3. Quando si valuta il posizionamento del connettore è necessario considerare i fattori seguenti:

* Il connettore deve vedere direttamente il data center per poter eseguire le operazioni relative alla delega vincolata Kerberos (KCD) quando si desidera l'accesso Single Sign-On (SSO) alle applicazioni back-end.
* Il connettore in genere è installato più vicino all'applicazione per ridurre il tempo richiesto dal connettore all'applicazione.

### <a name="general-approach-to-minimize-latency"></a>Approccio generale per ridurre al minimo la latenza

È possibile provare e ridurre al minimo la latenza del traffico end-to-end ottimizzando ogni hop di rete in modo che il traffico scorra meglio.

Per ottimizzare ogni hop è possibile:

* Ridurre la distanza tra le due estremità dell'hop.
* Scegliere la rete appropriata da attraversare. Ad esempio può risultare più veloce attraversare una rete privata anziché la rete Internet pubblica grazie ai collegamenti dedicati.
 
Se si dispone di un collegamento dedicato VPN/Express Route tra Azure e la rete aziendale, si consiglia di sfruttarlo.

## <a name="focus-your-optimizing-strategy"></a>Individuare la migliore strategia di ottimizzazione

Poiché gli utenti possono accedere alle app in modalità remota su Internet, è sempre consigliabile concentrarsi sull'ottimizzazione degli hop 2 e 3. Di seguito sono indicati alcuni modelli comuni che è possibile integrare.

### <a name="pattern-1-optimize-hop-3"></a>Modello 1: Ottimizzare l'hop 3:

Per ottimizzare l'hop 3, il connettore viene posizionato accanto all'applicazione di destinazione nella rete del cliente. Il vantaggio di questo è che il connettore probabilmente avrà bisogno di vedere direttamente il controller di dominio, come indicato in precedenza. Questo approccio è in genere sufficiente per la maggior parte dei clienti e degli scenari. La maggior parte dei nostri clienti ha adottato questo modello.

 ![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Esistono alcuni scenari in cui è necessario ottimizzare sia l'hop 2 che l'hop 3 per ottenere le caratteristiche di latenza desiderate. Ad esempio, se si dispone di una configurazione VPN o ExpressRoute tra la rete e il data center di Azure, questo scenario consente di ottimizzare l'hop 2 oltre all'hop 3.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Modello 2: Sfruttare ExpressRoute con peering pubblico

Se ExpressRoute è configurato con peering pubblico, si sfrutterà la più veloce connessione ExpressRoute per l'hop 2. L'hop 3 è già ottimizzato, grazie al posizionamento del connettore vicino all'app nella rete del cliente.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-taking-advantage-expressroute-with-private-peering"></a>Modello 3: Sfruttare ExpressRoute con peering privato

Se si ha una configurazione VPN o ExpressRoute dedicata con peering privato tra Azure e la rete aziendale in cui l'applicazione è installata, è disponibile un'altra opzione. In questa configurazione la rete virtuale in Azure è considerata generalmente come estensione della rete aziendale. Pertanto è possibile installare il connettore nel data center di Azure e soddisfare comunque i requisiti di bassa latenza della connessione connettore-app per l'hop 3. 

Poiché il traffico scorre attraverso una connessione dedicata, la latenza non è compromessa. Si ottiene invece il vantaggio di migliorare le caratteristiche di latenza dell'hop 2. Questo perché la connessione servizio-connettore del proxy (hop 2) è un hop più breve, in quanto il connettore è installato in un data center di Azure vicino alla posizione del tenant AAD (e pertanto del proxy applicazione).

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Altri approcci

Questo articolo finora si è concentrato sul posizionamento del connettore. Tuttavia, se si può prendere in considerazione di spostare l'applicazione (ad esempio in Azure o in un altro ambiente in hosting), il posizionamento dell'applicazione può essere modificato per ottenere caratteristiche di latenza migliori. 

Le organizzazioni spostano sempre più spesso le loro reti in ambienti in hosting. Ciò consente loro di posizionare le proprie app nell'ambiente in hosting che fa anche parte della rete aziendale e rimanere comunque all'interno del dominio. In questo caso possono essere applicati i criteri precedenti al nuovo percorso dell'applicazione.

Si può prendere in considerazione di usare gruppi di connettori per raggiungere app che si trovano in percorsi e reti diverse. Se si sta considerando questa opzione, vedere [Azure AD Domain Services](https://azure.microsoft.com/en-us/services/active-directory-ds). 

## <a name="common-scenarios"></a>Scenari comuni

In questa sezione si esamineranno alcuni casi d'uso. Per tutti i casi d'uso seguenti si supponga che il tenant Azure AD, e pertanto l'endpoint del servizio proxy, si trovi negli Stati Uniti. Per gli altri paesi del mondo si applicano in genere le stesse considerazioni.

### <a name="use-case-1"></a>Caso d'uso 1

L'app si trova in una rete del cliente negli Stati Uniti con gli utenti nella stessa area. Non esiste alcuna VPN o ExpressRoute tra il data center di Azure e la rete aziendale.

**Raccomandazione:** seguire il caso d'uso 1 precedente. Per migliorare la latenza, si consideri di sfruttare ExpressRoute, se necessario <vedere i casi d'uso 3 e 4>.

Si tratta di un modello semplice. Il modello più comune è ottimizzare l'hop 3: il connettore viene posizionato vicino all'app. Questa è anche una scelta naturale, in quanto il connettore è installato generalmente in modo che veda direttamente l'app e il data center per poter eseguire le operazioni di delega vincolata Kerberos.
Questo caso d'uso segue il modello 1 riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso d'uso 2

L'app si trova in una rete del cliente negli Stati Uniti con gli utenti sparsi in tutto il mondo. Non esiste alcuna VPN o ExpressRoute tra il data center di Azure e la rete aziendale.

**Raccomandazione:** seguire il caso d'uso 2 precedente. Per migliorare la latenza, si consideri di sfruttare ExpressRoute, se necessario (vedere i casi d'uso 3 e 4).

Ancora una volta, il modello più comune è ottimizzare l'hop 3, in cui il connettore viene posizionato vicino all'app per le ragione descritte in precedenza. L'hop 3 non è in genere costoso, se si trova interamente all'interno della stessa area. L'hop 1 può essere invece più costoso in base a dove si trova l'utente, poiché tutti gli utenti accederanno all'istanza del proxy applicazione negli Stati Uniti. Vale la pena notare che qualsiasi soluzione proxy presenta caratteristiche simili qui per quanto riguarda gli utenti distribuiti in tutto il mondo.

Questo caso d'uso segue il modello 2 riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso d'uso 3

L'app si trova in una rete del cliente negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering pubblico.

**Raccomandazione:** posizionare il connettore più vicino possibile all'app. Il sistema userà automaticamente ExpressRoute per l'hop 2. Viene seguito il modello 2 descritto in precedenza.

Se il collegamento ExpressRoute usa il peering pubblico, il traffico tra il proxy e il connettore scorrerà su tale collegamento e l'hop 2 avrà una latenza ottimizzata.

Questo caso d'uso segue il modello 3 riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso d'uso 4

L'app si trova in una rete del cliente negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering privato.

**Raccomandazione:** posizionare il connettore nel data center di Azure che è connesso alla rete aziendale tramite peering privato di ExpressRoute. Viene seguito il modello 3 descritto in precedenza.

Il connettore può essere posizionato nel data center di Azure. Poiché vede ancora direttamente l'applicazione e il data center attraverso la rete privata, l'hop 3 rimane ottimizzato. Questa configurazione inoltre ottimizza maggiormente l'hop 2.

Il caso d'uso segue il modello 4 riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso d'uso 5

L'app si trova in una rete del cliente nell'Unione Europea con la maggior parte degli utenti negli Stati Uniti.

**Raccomandazione:** posizionare il connettore vicino all'app. Per i motivi descritti sopra, questa è la scelta migliore. Poiché gli utenti degli Stati Uniti accedono a un'istanza del proxy applicazione che si trova nella stessa area, l'hop 1 non è troppo costoso. L'hop 3 è ottimizzato. Tuttavia l'hop 2 è in genere costoso in questo caso d'uso.

Il caso d'uso segue il modello 5a riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Si prenda in considerazione di sfruttare ExpressRoute come indicato nei modelli 2 e 3 descritti sopra. Di seguito è illustrato il modello 2 applicato.

Il caso d'uso segue il modello 5b riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Di seguito è riportata un'altra variante di questo caso d'uso che è possibile considerare.

Se la maggior parte degli utenti nell'organizzazione sono negli Stati Uniti, è possibile che la rete "si estenda" anche negli Stati Uniti. In tal caso il connettore può essere posizionato negli Stati Uniti e può sfruttare la linea della rete aziendale interna dedicata verso l'applicazione dell'Unione Europea. In questo modo vengono ottimizzati l'hop 2 e l'hop 3.

Il caso d'uso segue il modello 5c riportato di seguito.

![Fornitori di cloud IaaS multipli per Azure AD](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passaggi successivi
[Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)<br>
[Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)<br>
[Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)<br>
[Risolvere i problemi che si verificano con il proxy applicazione](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


