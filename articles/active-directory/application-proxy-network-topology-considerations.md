---
title: Considerazioni relative alla topologia di rete quando si usa il proxy di applicazione di Azure Active Directory | Microsoft Docs
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
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6869453e0776405841890978eef97f549be97541
ms.lasthandoff: 04/03/2017


---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerazioni relative alla topologia di rete quando si usa il proxy di applicazione di Azure Active Directory

Questo articolo presenta alcune considerazioni relative alla topologia di rete quando si usa il proxy di applicazione di Azure Active Directory (Azure AD) per la pubblicazione delle applicazioni e il relativo accesso da remoto.

## <a name="traffic-flow"></a>Flusso del traffico

Quando un'applicazione viene pubblicata tramite il proxy di applicazione di Azure AD, tutto il traffico dagli utenti alle applicazioni back-end di destinazione passa attraverso gli hop riportati di seguito.

* Hop 1: dall'utente all'endpoint pubblico del servizio proxy di applicazione di Azure AD in Azure
* Hop 2: dal servizio proxy di applicazione al connettore
* Hop 3: dal connettore all'applicazione di destinazione

 ![Diagramma che illustra il flusso del traffico dall'utente all'applicazione di destinazione](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Località del tenant e servizio proxy di applicazione

Al momento dell'iscrizione a un tenant di Azure AD, l'area del tenant è determinata dal paese che si specifica. Quando si abilita il proxy di applicazione, le istanze del servizio proxy di applicazione per il tenant vengono visualizzate nella stessa area del tenant di Azure AD o nell'area più vicina.

Se l'area del tenant di Azure AD è Unione Europea (UE), ad esempio, tutti i connettori del proxy di applicazione usano istanze del servizio nei data center di Azure nell'Unione Europea. Questo significa anche che tutti gli utenti passano attraverso le istanze del servizio proxy di applicazione in questa località, quando provano ad accedere alle applicazioni pubblicate.

## <a name="considerations-for-reducing-latency"></a>Considerazioni per ridurre la latenza

Tutte le soluzioni proxy introducono latenza nella connessione di rete. Indipendentemente dalla soluzione proxy o VPN scelta per l'accesso remoto, include sempre un set di server che consente la connessione all'interno della rete aziendale.

Le organizzazioni includono in genere endpoint server nella rete perimetrale. Con il proxy di applicazione di Azure AD, tuttavia, non è necessaria alcuna rete perimetrale. Questo perché il traffico passa attraverso il servizio proxy nel cloud, mentre i connettori si trovano nella rete aziendale.

### <a name="connector-placement"></a>Posizionamento dei connettori

Il servizio proxy di applicazione sceglie automaticamente la posizione delle istanze in base alla località del tenant. Di conseguenza, l'utente deve decidere dove installare il connettore e può in questo modo definire le caratteristiche di latenza del traffico di rete.

Quando si configura il servizio proxy di applicazione, è consigliabile porsi le domande seguenti:

* Dove si trova l'app?
* Dove si trova la maggior parte degli utenti che accedono all'app?
* Dove si trova l'istanza del proxy di applicazione (in base a quanto determinato dal tenant)?
* È già disponibile una connessione di rete dedicata ai data center di Azure (ad esempio Azure ExpressRoute o VPN simile)?

Il posizionamento del connettore determina la latenza degli hop 2 e 3 (descritti nella sezione precedente). Quando si valuta il posizionamento del connettore, è consigliabile tenere presente quanto segue:

* Il connettore deve comunicare con il data center. Questo consente al connettore di eseguire operazioni di delega vincolata Kerberos (KCD) per l'accesso Single Sign-On (SSO) alle applicazioni back-end.
* Il connettore in genere è installato più vicino all'applicazione per ridurre il tempo richiesto dal connettore all'applicazione.

### <a name="general-approach-to-minimize-latency"></a>Approccio generale per ridurre al minimo la latenza

Si può provare a ridurre al minimo la latenza del traffico end-to-end ottimizzando ogni hop di rete. Per ottimizzare ogni hop è possibile:

* Ridurre la distanza tra le due estremità dell'hop.
* Scegliere la rete appropriata da attraversare. Ad esempio può risultare più veloce attraversare una rete privata anziché la rete Internet pubblica grazie ai collegamenti dedicati.

Se è presente un collegamento VPN o ExpressRoute dedicato tra Azure e la rete aziendale, è consigliabile usare tale collegamento.

## <a name="focus-your-optimizing-strategy"></a>Individuare la migliore strategia di ottimizzazione

Poiché gli utenti possono accedere alle app in modalità remota su Internet, è sempre consigliabile concentrarsi sull'ottimizzazione degli hop 2 e 3. Di seguito sono indicati alcuni modelli comuni che è possibile incorporare.

### <a name="pattern-1-optimize-hop-3"></a>Modello 1: Ottimizzare l'hop 3

Per ottimizzare l'hop 3, il connettore viene posizionato accanto all'applicazione di destinazione nella rete del cliente. Il vantaggio risiede nel fatto che il connettore deve probabilmente comunicare con il controller di dominio. Questo approccio è sufficiente per la maggior parte degli scenari. Questo modello è infatti seguito dalla maggior parte dei clienti.

 ![Diagramma che illustra l'ottimizzazione dell'hop 3, con posizionamento del connettore vicino all'applicazione di destinazione](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
In alcuni scenari, per ottenere le caratteristiche di latenza desiderate è necessario ottimizzare sia l'hop 2 che l'hop 3. Se tra la rete e il data center di Azure è configurato un collegamento VPN o ExpressRoute, ad esempio, è possibile ottimizzare entrambi questi hop.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Modello 2: Sfruttare ExpressRoute con peering pubblico

Se ExpressRoute è configurato con peering pubblico, è possibile usare la connessione ExpressRoute più veloce per l'hop 2. L'hop 3 è già ottimizzato grazie al posizionamento del connettore vicino all'app nella propria rete.

![Diagramma che illustra l'ottimizzazione dell'hop 2 con una connessione ExpressRoute](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Modello 3: Sfruttare ExpressRoute con peering privato

Se tra Azure e la rete aziendale è configurato un collegamento VPN o ExpressRoute dedicato con peering privato, è disponibile un'altra opzione. In questa configurazione, la rete virtuale in Azure è in genere considerata come un'estensione della rete aziendale. È quindi possibile installare il connettore nel data center di Azure soddisfacendo comunque i requisiti di bassa latenza della connessione dal connettore all'app per l'hop 3.

La latenza non è compromessa perché il traffico viene trasmesso su una connessione dedicata. Si ottiene anche il vantaggio aggiuntivo di migliorare le caratteristiche di latenza dell'hop 2, perché la connessione dal servizio proxy di applicazione al connettore è un hop più breve. Il connettore viene installato in un data center di Azure vicino alla località del tenant di Azure AD e quindi del proxy di applicazione.

![Diagramma che illustra l'installazione del connettore in un data center di Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Altri approcci

Nonostante questo articolo sia incentrato sul posizionamento del connettore, per ottenere caratteristiche di latenza migliori è anche possibile modificare il posizionamento dell'applicazione.

Le organizzazioni spostano sempre più spesso le loro reti in ambienti in hosting. Ciò consente di posizionare le app in un ambiente ospitato che fa anche parte della rete aziendale rimanendo comunque all'interno del dominio. In questo caso, i modelli illustrati nelle sezioni precedenti possono essere applicati alla nuova posizione dell'applicazione.

Si può prendere in considerazione di usare gruppi di connettori per raggiungere app che si trovano in percorsi e reti diverse. Se si sta considerando questa opzione, vedere [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md).

## <a name="common-scenarios"></a>Scenari comuni

In questa sezione si esamineranno alcuni casi d'uso. Si supponga che il tenant di Azure AD e di conseguenza l'endpoint del servizio proxy si trovino negli Stati Uniti. Le considerazioni illustrate in questi casi d'uso si applicano in genere anche ad altre aree nel mondo.

### <a name="use-case-1"></a>Caso d'uso 1

L'app si trova in una rete dell'organizzazione negli Stati Uniti, con utenti nella stessa area. Tra il data center di Azure e la rete aziendale non esiste alcun collegamento VPN o ExpressRoute.

**Raccomandazione:** seguire il modello 1 illustrato nella sezione precedente. Per migliorare la latenza, valutare la possibilità di usare ExpressRoute, se necessario.

Si tratta di un modello semplice. Si ottimizza l'hop 3 posizionando il connettore vicino all'app. Questa è anche una scelta naturale, perché il connettore viene in genere installato in modo che comunichi con l'app e il data center per eseguire operazioni KCD.

![Diagramma che illustra la struttura negli Stati Uniti e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso d'uso 2

L'app si trova in una rete dell'organizzazione negli Stati Uniti, con utenti distribuiti in tutto il mondo. Tra il data center di Azure e la rete aziendale non esiste alcun collegamento VPN o ExpressRoute.

**Raccomandazione:** seguire il modello 2 illustrato nella sezione precedente. Per migliorare la latenza, valutare la possibilità di usare ExpressRoute, se necessario.

Anche in questo caso, il modello comune consiste nell'ottimizzare l'hop 3, posizionando il connettore vicino all'app. L'hop 3 non è in genere costoso, se si trova interamente all'interno della stessa area. L'hop 1 può invece essere più costoso a seconda di dove si trova l'utente, perché tutti gli utenti accedono all'istanza del proxy di applicazione negli Stati Uniti. È opportuno notare che qualsiasi soluzione proxy presenta caratteristiche simili in relazione a utenti distribuiti in tutto il mondo.

![Diagramma che illustra la struttura in diversi continenti del mondo e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso d'uso 3

L'app si trova in una rete dell'organizzazione negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering pubblico.

**Raccomandazione:** posizionare il connettore più vicino possibile all'app. Il sistema usa automaticamente ExpressRoute per l'hop 2. Viene così seguito il modello 2 illustrato nella sezione precedente.

Se il collegamento ExpressRoute usa il peering pubblico, il traffico tra il proxy e il connettore viene trasmesso su tale collegamento. L'hop 2 ha una latenza ottimizzata.

![Diagramma che illustra la struttura negli Stati Uniti e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso d'uso 4

L'app si trova in una rete dell'organizzazione negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering privato.

**Raccomandazione:** posizionare il connettore nel data center di Azure connesso alla rete aziendale tramite il peering privato di ExpressRoute. Viene così seguito il modello 3 illustrato nella sezione precedente.

Il connettore può essere posizionato nel data center di Azure. Dato che il connettore comunica comunque con l'applicazione e il data center tramite la rete privata, l'hop 3 rimane ottimizzato. Viene anche ulteriormente ottimizzato l'hop 2.

![Diagramma che illustra la struttura negli Stati Uniti e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso d'uso 5

L'app si trova in una rete dell'organizzazione nell'Unione Europea, con la maggior parte degli utenti negli Stati Uniti.

**Raccomandazione:** posizionare il connettore vicino all'app. Dato che gli utenti degli Stati Uniti accedono a un'istanza del proxy di applicazione che si trova nella stessa area, l'hop 1 non è troppo costoso. L'hop 3 è ottimizzato. L'hop 2, tuttavia, è in genere costoso in questo caso d'uso.

![Diagramma che illustra la struttura in diversi continenti del mondo e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Valutare la possibilità di usare ExpressRoute, come descritto nelle sezioni precedenti relative ai modelli 2 e 3.

![Diagramma che illustra la struttura in diversi continenti del mondo e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

In questa situazione è anche possibile prendere in considerazione l'uso di un'altra variante. Se la maggior parte degli utenti nell'organizzazione si trova negli Stati Uniti, la rete potrebbe estendersi anche negli Stati Uniti. In tal caso, il connettore può essere posizionato negli Stati Uniti e può usare la linea della rete aziendale interna dedicata verso l'applicazione nell'Unione Europea. In questo modo, gli hop 2 e 3 vengono ottimizzati.

![Diagramma che illustra la struttura in diversi continenti del mondo e la disposizione degli hop in questo caso d'uso](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

