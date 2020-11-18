---
title: Risolvere i problemi relativi alla disponibilità di Azure Load Balancer risorse, front-end e back-end
description: Usare le metriche disponibili per diagnosticare il Load Balancer Standard di Azure danneggiato o non disponibile.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: dcfce06bb158888b56483a73ededd354c229a99b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696320"
---
# <a name="troubleshoot-resource-health-frontend-and-backend-availability-issues"></a>Risolvere i problemi di disponibilità di integrità delle risorse, front-end e back-end 

Questo articolo è una guida per esaminare i problemi che influiscano sulla disponibilità dell'IP front-end del servizio di bilanciamento del carico e delle risorse back-end. 

## <a name="about-the-metrics-well-use"></a>Informazioni sulle metriche da usare
Le due metriche da usare sono lo stato di *disponibilità del percorso dei dati* e del *Probe di integrità* ed è importante comprenderne il significato per derivare informazioni corrette. 

## <a name="data-path-availability"></a>Disponibilità del percorso dati
La metrica di disponibilità del percorso dati viene generata da un ping TCP ogni 25 secondi su tutte le porte front-end che hanno configurato il bilanciamento del carico e le regole NAT in ingresso. Questo ping TCP verrà quindi instradato a qualsiasi istanza di back-end (con Probe) integro. Se il servizio riceve una risposta al ping, viene considerato un esito positivo e la somma della metrica verrà iterata una volta, in caso negativo. Il numero di questa metrica è 1/100 del totale dei ping TCP per ogni periodo di campionamento. Pertanto, si desidera considerare la media, che visualizzerà la media della somma o del conteggio per il periodo di tempo. La metrica di disponibilità del percorso dati aggregata in base alla media offre quindi una percentuale di esecuzioni riuscite per i ping TCP sull'IP front-end: porta per ciascuna delle regole NAT in ingresso e di bilanciamento del carico.

## <a name="health-probe-status"></a>Stato del probe di integrità
La metrica dello stato del probe di integrità viene generata da un ping del protocollo definito nel Probe di integrità. Questo ping viene inviato a ogni istanza nel pool back-end e sulla porta definita nel Probe di integrità. Per i probe HTTP e HTTPS, un ping con esito positivo richiede una risposta HTTP 200 OK, mentre con i probe TCP qualsiasi risposta viene considerata corretta. I successi consecutivi o gli errori di ogni Probe determinano quindi se un'istanza back-end è integra e in grado di ricevere traffico per le regole di bilanciamento del carico a cui è assegnato il pool back-end. In modo analogo alla disponibilità del percorso dati, viene utilizzata l'aggregazione media, che indica la media dei ping riusciti/totali durante l'intervallo di campionamento. Questo valore di stato del probe di integrità indica l'integrità back-end in isolamento dal servizio di bilanciamento del carico eseguendo il sondaggio delle istanze back-end senza inviare il traffico attraverso il front-end.

>[!IMPORTANT]
>Lo stato del probe di integrità viene campionato in base a un minuto. Questo può causare fluttuazioni secondarie in un valore altrimenti costante. Se, ad esempio, sono presenti due istanze di back-end, una con Probe e una analizzata, il servizio Probe di integrità può acquisire 7 campioni per l'istanza integro e 6 per l'istanza non integra. In questo modo si otterrà un valore fisso in precedenza pari a 50 come 46,15 per un intervallo di un minuto. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnosticare i bilanciamenti del carico ridotti e non disponibili
Come illustrato nell' [articolo sull'integrità delle risorse](load-balancer-standard-diagnostics.md#resource-health-status), un servizio di bilanciamento del carico danneggiato è uno che mostra una disponibilità del percorso dati compresa tra il 25% e il 90% e un servizio di bilanciamento del carico non disponibile con una disponibilità del percorso dati inferiore al 25%, in un periodo di due minuti. Gli stessi passaggi possono essere eseguiti per esaminare l'errore riscontrato in qualsiasi stato del probe di integrità o di disponibilità del percorso dati configurato. Si esplorerà il caso in cui è stata verificata l'integrità delle risorse e il servizio di bilanciamento del carico non è disponibile con una disponibilità del percorso dati pari allo 0%. il servizio è inattivo.

Prima di tutto, viene visualizzata la visualizzazione metrica dettagliata del pannello Insights di Load Balancer. È possibile eseguire questa operazione tramite il pannello della risorsa di bilanciamento del carico o il collegamento nel messaggio di integrità delle risorse.  Passare quindi alla scheda disponibilità front-end e back-end ed esaminare una finestra di trenta minuti del periodo di tempo in cui si è verificato lo stato danneggiato o non disponibile. Se si nota che la disponibilità del percorso dati è pari allo 0%, è possibile che si verifichi un problema che impedisce il traffico per tutte le regole NAT in ingresso e di bilanciamento del carico e che sia in grado di visualizzare la durata di questo effetto. 

Il punto successivo da considerare è la metrica dello stato del probe di integrità per determinare se il percorso dei dati non è disponibile perché non sono disponibili istanze back-end integre per gestire il traffico. Se è disponibile almeno un'istanza di back-end integro per tutte le regole di bilanciamento del carico e in ingresso, sappiamo che non si tratta di una configurazione che causa i percorsi dati non disponibili. Questo scenario indica un problema della piattaforma Azure, sebbene raro, non preoccupare se vengono rilevati come un avviso automatizzato inviato al team per risolvere rapidamente tutti i problemi della piattaforma.

## <a name="diagnose-health-probe-failures"></a>Diagnosticare gli errori del probe di integrità
Supponiamo di controllare lo stato del probe di integrità e di scoprire che tutte le istanze vengono visualizzate come non integre. Questa scoperta spiega perché il percorso dei dati non è disponibile perché il traffico non è disponibile. È quindi necessario esaminare l'elenco di controllo seguente per escludere gli errori di configurazione comuni:
* Controllare l'utilizzo della CPU per le risorse per verificare se le istanze sono effettivamente integre
  * È possibile selezionare questa opzione 
* Se si usa un probe HTTP o HTTPS, verificare se l'applicazione è integra e reattiva
  * Verificare che sia funzionale accedendo direttamente alle applicazioni tramite l'indirizzo IP privato o l'indirizzo IP pubblico a livello di istanza associato all'istanza back-end
* Esaminare i gruppi di sicurezza di rete applicati alle risorse back-end. Assicurarsi che non esistano regole con una priorità più elevata rispetto a AllowAzureLoadBalancerInBound che bloccherà il probe di integrità
  * È possibile eseguire questa operazione visitando il pannello rete delle VM back-end o dei set di scalabilità di macchine virtuali
  * Se si riscontra questo problema NSG, spostare la regola Consenti esistente o creare una nuova regola di priorità alta per consentire il traffico AzureLoadBalancer
* Controllare il sistema operativo. Verificare che le macchine virtuali siano in ascolto sulla porta Probe ed esaminare le regole del firewall del sistema operativo per assicurarsi che non blocchino il traffico Probe originato da indirizzo IP 168.63.129.16
  * È possibile controllare le porte in ascolto eseguendo netstat-a prompt dei comandi di Windows o netstat-l in un terminale Linux
* Non inserire una macchina virtuale del firewall appliance virtuale di sistema nel pool back-end del servizio di bilanciamento del carico, usare [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md#user-defined) per instradare il traffico alle istanze back-end attraverso il firewall
* Assicurarsi di usare il protocollo appropriato, se si usa HTTP per eseguire il probe di una porta in ascolto di un'applicazione non HTTP, il probe avrà esito negativo

Se si è passati a questo elenco di controllo e si trovano ancora errori di probe di integrità, potrebbero verificarsi problemi di piattaforma rari che influiscano sul servizio Probe per le istanze. In questo caso, Azure è stato riavviato e viene inviato un avviso automatico al team per risolvere rapidamente tutti i problemi della piattaforma.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul Probe di integrità Azure Load Balancer](load-balancer-custom-probe-overview.md)
* [Altre informazioni sulle metriche di Azure Load Balancer](load-balancer-standard-diagnostics.md)