---
title: Soluzione di monitoraggio delle prestazioni di rete in Azure Log Analytics | Microsoft Docs
description: "La funzionalità di gestione degli endpoint di servizio in Monitoraggio prestazioni rete consente di monitorare la connettività della rete a qualsiasi endpoint con una porta TCP aperta."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>Monitoraggio endpoint di servizio

La funzionalità Monitoraggio endpoint di servizio in [Monitoraggio prestazioni rete](log-analytics-network-performance-monitor.md) consente di monitorare la connettività della rete a qualsiasi endpoint con una porta TCP aperta. Tali endpoint includono siti Web, applicazioni SaaS, applicazioni PaaS e database SQL. 

Con **Monitoraggio endpoint di servizio** è possibile eseguire le funzioni seguenti: 

- Monitorare la connettività della rete alle applicazioni e ai servizi di rete (ad esempio, Office 365, Dynamics CRM, applicazioni line-of-business interne, database SQL e così via) da più succursali/località 
- Test predefiniti per monitorare la connettività di rete agli endpoint Office365 e Dynamics365 
- Determinare il tempo di risposta, la latenza di rete la perdita di pacchetti riscontrati durante la connessione all'endpoint 
- Determinare se le prestazioni dell'applicazione scarse sono dovute alla rete o a qualche problema da parte del provider dell'applicazione 
- Identificare le aree sensibili della rete che potrebbero essere la causa delle prestazioni dell'applicazione scarse visualizzando la latenza generata da ogni hop in una mappa della topologia. 


![Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Configurazione 
Per aprire la configurazione per Monitoraggio prestazioni rete, aprire la [soluzione Monitoraggio prestazioni rete](log-analytics-network-performance-monitor.md) e fare clic sul pulsante **Configura**.

![Configurare Monitoraggio prestazioni rete](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>Configurare gli agenti di OMS per il monitoraggio  
Abilitare le regole del firewall seguenti nei nodi usati per il monitoraggio in modo che la soluzione possa individuare la topologia dai nodi all'endpoint di servizio: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Creare i test per Monitoraggio endpoint di servizio 

Iniziare a creare i test per monitorare la connettività della rete agli endpoint di servizio 

1. Fare clic sulla scheda **Monitoraggio endpoint di servizio**.
2. Fare clic su **Aggiungi test** e immettere il nome e la descrizione del test. 
3. Selezionare il tipo di test:<br>Selezionare **Test Web** per monitorare la connettività a un servizio che risponde a richieste HTTP/S, ad esempio outlook.office365.com, bing.com.<br>Selezionare **Network test** (Test della rete) per monitorare la connettività a un servizio che risponde a una richiesta TCP, ma non risponde a richieste HTTP/S, ad esempio un server SQL, un server FTP una porta SSH e così via. 
4. Se non si vogliono eseguire misure di rete (latenza di rete, perdita di pacchetti, individuazione della topologia), deselezionare la casella di testo. È consigliabile lasciarla selezionata per ottenere il massimo vantaggio dalla funzionalità. 
5. Immettere indirizzo IP/FQDN/URL di destinazione a cui si vuole monitorare la connettività della rete.  
6. Immettere il numero di porta del servizio di destinazione. 
7. Immettere la frequenza con cui si vuole eseguire il test. 
8. Selezionare i nodi da cui si vuole monitorare la connettività della rete al servizio. 

    >[!NOTE]
    > Per i nodi basati su server Windows, la funzionalità usa richieste basate su TCP per eseguire le misure di rete. Per i nodi basati su client Windows, la funzionalità usa richieste basate su ICMP per eseguire le misure di rete. In alcuni casi, l'applicazione di destinazione blocca le richieste basate su ICMP in ingresso perché, quando i nodi sono basati su client Windows, la soluzione non riesce a eseguire le misure di rete. In tali casi è quindi consigliabile usare nodi basati su server Windows. 

9. Se non si vogliono creare eventi di integrità per gli elementi selezionati, deselezionare **Abilita il monitoraggio dell'integrità nelle destinazioni incluse in questo test**. 
10. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per la generazione di eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità. 
11. Fare clic su **Salva** per salvare la configurazione. 

 ![Configurazione di Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Procedura dettagliata 

Passare alla visualizzazione dashboard di Monitoraggio prestazioni rete ed esaminare la pagina **Monitoraggio endpoint di servizio** per avere un riepilogo dell'integrità dei diversi test creati.  

![Pagina Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Fare clic sul riquadro per eseguire il drill-down e visualizzare i dettagli dei test nella pagina **Test**. Nella tabella sul lato sinistro è possibile visualizzare l'integrità in un determinato momento e il valore del tempo di risposta, della latenza di rete e della perdita di pacchetti del servizio per tutti i test. È possibile usare il controllo di registrazione dello stato di rete per visualizzare lo snapshot della rete in un altro memento nel passato. Nella tabella fare clic sul test che si vuole esaminare. È possibile visualizzare la tendenza cronologica relativa ai valori di perdita, latenza e tempo di risposta dai grafici nel riquadro sul lato destro. Fare clic sul collegamento Dettagli test per visualizzare le prestazioni di ogni nodo. 

![Test di Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

Nella visualizzazione **Nodi test** è possibile osservare la connettività della rete da ogni nodo. Fare clic sul nodo che presenta una riduzione del livello delle prestazioni.  Si tratta del nodo in cui si osserva il rallentamento dell'esecuzione dell'applicazione. 

Determinare se le prestazioni dell'applicazione scarse sono dovute alla rete o a qualche problema da parte del provider dell'applicazione osservando la correlazione tra il tempo di risposta dell'applicazione e la latenza di rete. 

**Problema dell'applicazione:** se si verifica un picco nel tempo di risposta, ma la latenza di rete è coerente, è evidente che la rete funziona nel modo previsto e che il problema è dovuto all'applicazione.  

![Problema dell'applicazione Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**Problema di rete:** se a un picco nel tempo di risposta corrisponde un picco nella latenza di rete, è evidente che l'aumento nel tempo di risposta è dovuto a un aumento nella latenza di rete.  

![Problema di rete di Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Dopo avere determinato che il problema è causato dalla rete, è possibile fare clic sul collegamento della visualizzazione **Topologia** per identificare l'area problematica nella mappa della topologia. Nell'immagine seguente, ad esempio, è possibile notare che, dei 105 ms di latenza totale tra il nodo e l'endpoint applicazione, 96 ms sono dovuti all'hop contrassegnato in rosso. Dopo avere identificato l'hop problematico, è possibile adottare un'azione correttiva.  

![Test di Monitoraggio endpoint di servizio](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostica 

Se si osserva un'anomalia, seguire questa procedura:

Se il tempo di risposta, la perdita di rete e la latenza del servizio vengono visualizzati come non disponibili, il motivo può essere uno o più dei seguenti:
- L'applicazione è inattiva.
- Il nodo usato per controllare la connettività della rete al servizio è inattivo.
- La destinazione immessa nella configurazione di test non è corretta.
- Il nodo non ha connettività di rete.

Se viene visualizzato un tempo di risposta del servizio valido, ma la perdita di rete e la latenza vengono visualizzate come non disponibili, il motivo può essere uno o più dei seguenti:
- Se il nodo usato per controllare la connettività di rete al servizio è il computer client Windows, il servizio di destinazione sta bloccando le richieste ICMP o un firewall di rete sta bloccando le richieste ICMP che hanno origine dal nodo.
- La casella di controllo **Esegui misure di rete** è stata deselezionata nella configurazione di test. 

Se il tempo di risposta del servizio non è disponibile, ma la perdita di rete e la latenza sono valide, è evidente che il servizio di destinazione non è un'applicazione Web. Modificare la configurazione di test e scegliere Network test (Test della rete) invece di Test Web come tipo di test. 

Se l'esecuzione dell'applicazione risulta rallentata, determinare se le prestazioni dell'applicazione scarse sono dovute alla rete o a qualche problema da parte del provider dell'applicazione.


## <a name="next-steps"></a>Passaggi successivi
* [Effettuare una ricerca nei log](log-analytics-log-searches.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
