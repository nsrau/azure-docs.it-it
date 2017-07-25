---
title: Considerazioni relative alla topologia di rete quando si usa il proxy applicazione di Azure Active Directory | Microsoft Docs
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
ms.date: 07/21/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: e85242e251b53cd45b583429bd25c9ef08d2b101
ms.contentlocale: it-it
ms.lasthandoff: 04/21/2017

---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerazioni relative alla topologia di rete quando si usa il proxy applicazione di Azure Active Directory

Questo articolo presenta alcune considerazioni relative alla topologia di rete quando si usa il proxy applicazione di Azure Active Directory (Azure AD) per la pubblicazione delle applicazioni e il relativo accesso da remoto.

## <a name="traffic-flow"></a>Flusso del traffico

Quando un'applicazione viene pubblicata tramite il proxy applicazione di Azure AD, il traffico dagli utenti alle applicazioni passa attraverso tre connessioni:

1. L'utente si connette all'endpoint pubblico del servizio proxy applicazione di Azure AD in Azure
2. Il proxy applicazione si connette al connettore
3. Il connettore si connette all'applicazione di destinazione.

 ![Diagramma che illustra il flusso del traffico dall'utente all'applicazione di destinazione](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Località del tenant e servizio proxy applicazione

Al momento dell'iscrizione a un tenant di Azure AD, l'area del tenant è determinata dal paese che si specifica. Quando si abilita il proxy applicazione, le istanze del servizio proxy applicazione per il tenant vengono visualizzate nella stessa area del tenant di Azure AD o nell'area più vicina.

Se ad esempio l'area del tenant di Azure AD è Unione Europea (UE), tutti i connettori del proxy applicazione usano istanze del servizio nei data center di Azure nell'Unione Europea. Quando gli utenti accedono alle applicazioni pubblicate, il traffico passa attraverso le istanze del servizio proxy applicazione in questa località.

## <a name="considerations-for-reducing-latency"></a>Considerazioni per ridurre la latenza

Tutte le soluzioni proxy introducono latenza nella connessione di rete. Indipendentemente dalla soluzione proxy o VPN scelta per l'accesso remoto, include sempre un set di server che consente la connessione all'interno della rete aziendale.

Le organizzazioni includono in genere endpoint server nella rete perimetrale. Con il proxy applicazione di Azure AD, tuttavia, il traffico passa attraverso il servizio proxy nel cloud, mentre i connettori si trovano nella rete aziendale. Non è richiesta alcuna rete perimetrale.

### <a name="connector-placement"></a>Posizionamento dei connettori

Il servizio proxy applicazione sceglie automaticamente la posizione delle istanze in base alla località del tenant. L'utente deve decidere dove installare il connettore e può in questo modo definire le caratteristiche di latenza del traffico di rete.

Quando si configura il servizio proxy applicazione, è consigliabile porsi le domande seguenti:

* Dove si trova l'app?
* Dove si trova la maggior parte degli utenti che accedono all'app?
* Dove si trova l'istanza del proxy applicazione?
* È già disponibile una connessione di rete dedicata ai data center di Azure, ad esempio Azure ExpressRoute o VPN simile?

Il connettore deve comunicare con Azure e le applicazioni, pertanto la posizione del connettore influisce sulla latenza di queste due connessioni. Quando si valuta il posizionamento del connettore, è consigliabile tenere presente quanto segue:

* Se si intende usare la delega vincolata Kerberos (KCD) per l'accesso Single Sign-On, il connettore deve avere visibilità su un data center. 
* In caso di dubbi, installare il connettore più vicino all'applicazione.

### <a name="general-approach-to-minimize-latency"></a>Approccio generale per ridurre al minimo la latenza

Si può provare a ridurre al minimo la latenza del traffico end-to-end ottimizzando ognuna delle connessioni di rete. Ogni connessione può essere ottimizzata eseguendo le operazioni seguenti:

* Ridurre la distanza tra le due estremità dell'hop.
* Scegliere la rete appropriata da attraversare. Ad esempio può risultare più veloce attraversare una rete privata anziché la rete Internet pubblica grazie ai collegamenti dedicati.

Se è presente un collegamento VPN o ExpressRoute dedicato tra Azure e la rete aziendale, è consigliabile usare tale collegamento.

## <a name="focus-your-optimizing-strategy"></a>Individuare la migliore strategia di ottimizzazione

Non si può fare molto per controllare la connessione tra gli utenti e il proxy applicazione di AD Azure, in quanto gli utenti possono accedere alle applicazioni da una rete domestica, un bar o da un paese diverso. È possibile invece ottimizzare le connessioni dal proxy applicazione ai connettori e alle applicazioni. È consigliabile incorporare i modelli seguenti nell'ambiente in uso.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Modello 1: Inserire il connettore vicino all'applicazione

Posizionare il connettore vicino all'applicazione di destinazione nella rete del cliente. Il vantaggio di questo modello risiede nel fatto che il connettore ha bisogno di avere visibilità sul controller di dominio. Questo approccio è sufficiente per la maggior parte degli scenari, motivo per cui la maggior parte dei nostri clienti seguono questo modello. Questo modello può anche essere combinato con il modello 2 per ottimizzare il traffico tra il proxy applicazione e il connettore.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Modello 2: Sfruttare ExpressRoute con peering pubblico

Se ExpressRoute è configurato con peering pubblico, è possibile usare la connessione ExpressRoute più veloce per il traffico tra il proxy applicazione e il connettore. Il connettore risiede ancora nella rete, vicino all'app.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Modello 3: Sfruttare ExpressRoute con peering privato

Se tra Azure e la rete aziendale è configurato un collegamento VPN o ExpressRoute dedicato con peering privato, è disponibile un'altra opzione. In questa configurazione, la rete virtuale in Azure è in genere considerata come un'estensione della rete aziendale. È quindi possibile installare il connettore nel data center di Azure soddisfacendo comunque i requisiti di bassa latenza della connessione da connettore ad app.

Poiché il traffico scorre attraverso una connessione dedicata, la latenza non è compromessa. Si migliora inoltre la latenza dal servizio proxy applicazione al connettore perché il connettore è installato in un data center di Azure vicino alla posizione del tenant di Azure AD.

![Diagramma che illustra l'installazione del connettore in un data center di Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Altri approcci

Nonostante questo articolo sia incentrato sul posizionamento del connettore, per ottenere caratteristiche di latenza migliori è anche possibile modificare il posizionamento dell'applicazione.

Le organizzazioni spostano sempre più spesso le loro reti in ambienti in hosting. Ciò consente di posizionare le app in un ambiente ospitato che fa anche parte della rete aziendale rimanendo comunque all'interno del dominio. In questo caso, i modelli illustrati nelle sezioni precedenti possono essere applicati alla nuova posizione dell'applicazione.

Si può prendere in considerazione di usare [gruppi di connettori](active-directory-application-proxy-connectors.md) per raggiungere app che si trovano in posizioni e reti diverse. Se si sta considerando questa opzione, vedere [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md).

## <a name="common-use-cases"></a>Casi d'uso comuni

In questa sezione si esaminano alcuni scenari comuni. Si supponga che il tenant di Azure AD e di conseguenza l'endpoint del servizio proxy si trovino negli Stati Uniti. Le considerazioni illustrate in questi casi d'uso si applicano anche ad altre aree nel mondo.

In questi scenari ogni connessione viene chiamata "hop" e viene numerata per semplificare la discussione:

- **Hop 1**: dall'utente al proxy applicazione di Azure AD
- **Hop 2**: dal proxy applicazione di Azure AD al connettore
- **Hop 3**: dal connettore all'applicazione di destinazione 

### <a name="use-case-1"></a>Caso d'uso 1

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti, con utenti nella stessa area. Tra il data center di Azure e la rete aziendale non esiste alcun collegamento VPN o ExpressRoute.

**Raccomandazione:** seguire il modello 1 illustrato nella sezione precedente. Per migliorare la latenza, valutare la possibilità di usare ExpressRoute, se necessario.

Si tratta di un modello semplice. Si ottimizza l'hop 3 posizionando il connettore vicino all'app. Questa è anche una scelta naturale, perché il connettore viene in genere installato in modo che comunichi con l'app e il data center per eseguire operazioni KCD.

![Diagramma che mostra che gli utenti, il proxy, il connettore e l'app sono tutti negli Stati Uniti](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso d'uso 2

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti, con utenti distribuiti in tutto il mondo. Tra il data center di Azure e la rete aziendale non esiste alcun collegamento VPN o ExpressRoute.

**Raccomandazione:** seguire il modello 2 illustrato nella sezione precedente. 

Anche in questo caso, il modello comune consiste nell'ottimizzare l'hop 3, posizionando il connettore vicino all'app. L'hop 3 non è in genere costoso, se si trova interamente all'interno della stessa area. L'hop 1 può invece essere più costoso a seconda di dove si trova l'utente, perché gli utenti nel mondo devono accedere all'istanza del proxy applicazione negli Stati Uniti. È opportuno notare che qualsiasi soluzione proxy presenta caratteristiche simili in relazione a utenti distribuiti in tutto il mondo.

![Diagramma che mostra che gli utenti sono distribuiti a livello globale, ma il proxy, il connettore e l'app sono negli Stati Uniti](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso d'uso 3

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering pubblico.

**Raccomandazione:** posizionare il connettore più vicino possibile all'app. Il sistema usa automaticamente ExpressRoute per l'hop 2. Viene così seguito il modello 2 illustrato nella sezione precedente.

Se il collegamento ExpressRoute usa il peering pubblico, il traffico tra il proxy e il connettore viene trasmesso su tale collegamento. L'hop 2 ha una latenza ottimizzata.

![Diagramma che mostra ExpressRoute tra il proxy e il connettore](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso d'uso 4

**Scenario:** l'app si trova in una rete dell'organizzazione negli Stati Uniti. Tra Azure e la rete aziendale è presente ExpressRoute con peering privato.

**Raccomandazione:** posizionare il connettore nel data center di Azure connesso alla rete aziendale tramite il peering privato di ExpressRoute. Viene così seguito il modello 3 illustrato nella sezione precedente.

Il connettore può essere posizionato nel data center di Azure. Dato che il connettore comunica comunque con l'applicazione e il data center tramite la rete privata, l'hop 3 rimane ottimizzato. Viene anche ulteriormente ottimizzato l'hop 2.

![Diagramma che mostra il connettore in un data center di Azure ed ExpressRoute tra il connettore e l'app](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso d'uso 5

**Scenario:** l'app è in una rete aziendale dell'Unione europea, con l'istanza del proxy applicazione e la maggior parte degli utenti negli Stati Uniti.

**Raccomandazione:** posizionare il connettore vicino all'app. Dato che gli utenti degli Stati Uniti accedono a un'istanza del proxy applicazione che si trova nella stessa area, l'hop 1 non è troppo costoso. L'hop 3 è ottimizzato. È consigliabile usare ExpressRoute per ottimizzare l'hop 2. 

![Diagramma che mostra gli utenti e il proxy negli Stati Uniti, con il connettore e l'app dell'Unione europea](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

In questa situazione è anche possibile prendere in considerazione l'uso di un'altra variante. Se la maggior parte degli utenti nell'organizzazione si trova negli Stati Uniti, la rete potrebbe estendersi anche negli Stati Uniti. In tal caso, il connettore può essere posizionato negli Stati Uniti e può usare la linea della rete aziendale interna dedicata verso l'applicazione nell'Unione Europea. In questo modo, gli hop 2 e 3 vengono ottimizzati.

![Diagramma che mostra gli utenti, il proxy e il connettore negli Stati Uniti e l'app nell'Unione europea](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Risolvere i problemi che si verificano con il proxy applicazione](active-directory-application-proxy-troubleshoot.md)

