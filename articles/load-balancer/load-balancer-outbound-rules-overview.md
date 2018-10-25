---
title: Regole in uscita in Azure Load Balancer | Microsoft Docs
description: Usare le regole in uscita per definire le conversioni per gli indirizzi di rete in uscita
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/8/2018
ms.author: kumud
ms.openlocfilehash: 15783822631a5372b181f2d65746664d90b389da
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883959"
---
# <a name="load-balancer-outbound-rules"></a>Regole in uscita di Load Balancer

Azure Load Balancer offre connettività in uscita da una rete virtuale oltre alla connettività in ingresso.  Le regole in uscita semplificano la configurazione della conversione degli indirizzi di rete in uscita di [Load Balancer Standard](load-balancer-standard-overview.md) pubblico.  È disponibile il controllo dichiarativo completo sulla connettività in uscita per ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche.

![Regole in uscita di Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Con le regole in uscita è possibile usare Load Balancer per: 
- Definire il processo NAT in uscita da zero.
- Ridimensionare e ottimizzare il comportamento del processo NAT in uscita esistente. 

Le regole in uscita consentono di controllare:
- Quali macchine virtuali devono essere convertite in quali indirizzi IP pubblici. 
- La modalità di allocazione delle [porte SNAT in uscita](load-balancer-outbound-connections.md#snat).
- Per quali protocolli implementare la conversione in uscita.
- Quale durata usare per il timeout di inattività delle connessioni in uscita.
- Se inviare un TCP Reset per il timeout di inattività (anteprima pubblica). 

Le regole in uscite estendono lo [scenario 2](load-balancer-outbound-connections.md#lb) descritto nell'articolo dedicato alle [connessioni in uscita](load-balancer-outbound-connections.md) senza variazioni per l'ordine di precedenza degli scenari.

## <a name="outbound-rule"></a>Regola in uscita

Come tutte le regole di Load Balancer, le regole in uscita seguono la stessa sintassi familiare delle regole di bilanciamento del carico e delle regole NAT in ingresso:

**front-end** + **parametri** + **pool back-end**

Una regola in uscita configura NAT in uscita per _tutte le macchine virtuali identificate dal pool back-end_ da convertire in _front-end_.  E i _parametri_ offrono ulteriore controllo dettagliato sull'algoritmo NAT in uscita.

La versione "2018-07-01" dell'API supporta una definizione di regola in uscita strutturata come segue:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>L'effettiva configurazione di NAT in uscita è il risultato della composizione di tutte le regole in uscita e di bilanciamento del carico. Le regole in uscita sono incrementali rispetto alle regole di bilanciamento del carico. Vedere [Disable outbound NAT for a load balancing rule](#disablesnat) (Disabilitare NAT in uscita per una regola di bilanciamento del carico) per gestire la conversione NAT in uscita effettiva quando a una macchina virtuale si applicano più regole. È necessario [disabilitare NAT in uscita](#disablesnat) quando si definisce una regola in uscita che usa lo stesso indirizzo IP pubblico di una regola di bilanciamento del carico.

### <a name="scale"></a> Scalabilità di NAT in uscita con più indirizzi IP

Anche se una regola in uscita può essere usata con un singolo indirizzo IP pubblico, le regole in uscita semplificano l'impegno di configurazione per la scalabilità di NAT in uscita. È possibile usare più indirizzi IP per gestire scenari su larga scala ed è possibile usare le regole in uscita per risolvere i problemi dei modelli soggetti a [esaurimento delle porte SNAT](load-balancer-outbound-connections.md#snatexhaust).  

Ogni indirizzo IP aggiuntivo fornito da un front-end rende disponibili 64.000 porte temporanee che Load Balancer può usare come porte SNAT. Mentre le regole di bilanciamento del carico o le regole NAT in ingresso hanno un unico front-end, la regola in uscita espande il concetto di front-end e consente più front-end per ogni regola.  Con più front-end per ogni regola, la quantità di porte SNAT disponibili è moltiplicata per ogni indirizzo IP pubblico e si possono supportare scenari di dimensioni molto grandi.

Inoltre, è possibile usare un [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) direttamente con una regola in uscita.  Questo consente di gestire più facilmente la scalabilità e l'elenco elementi consentiti per i flussi provenienti dalla distribuzione di Azure. È possibile configurare una configurazione IP front-end all'interno della risorsa Load Balancer in modo da fare riferimento direttamente a un prefisso di indirizzo IP pubblico.  In questo modo Load Balancer ha il controllo esclusivo sul prefisso IP pubblico e la regola in uscita userà automaticamente tutti gli indirizzi IP pubblici compresi nel prefisso per le connessioni in uscita.  Ognuno degli indirizzi IP all'interno dell'intervallo del prefisso IP pubblico rende disponibili 64.000 porte temporanee per ogni indirizzo IP utilizzabili da Load Balancer come porte SNAT.   

Non è possibile creare singole risorse indirizzo IP pubblico dal prefisso IP pubblico quando si usa questa opzione perché la regola in uscita deve avere il controllo completo del prefisso IP pubblico.  Se è necessario disporre di un controllo più granulare, è possibile creare una singola risorsa indirizzo IP pubblico dal prefisso IP pubblico e assegnare più indirizzi IP pubblici singolarmente al front-end di una regola in uscita.

### <a name="snatports"></a> Ottimizzare l'allocazione delle porte SNAT

È possibile usare le regole in uscita per ottimizzare l'[allocazione automatica delle porte SNAT in base alle dimensioni del pool back-end](load-balancer-outbound-connections.md#preallocatedports) e aumentare o ridurre le porte allocale rispetto a quanto previsto dall'allocazione automatica delle porte SNAT.

Usare il parametro seguente per allocare 10.000 porte SNAT per ogni macchina virtuale (configurazione IP NIC).
 

          "allocatedOutboundPorts": 10000

Ogni indirizzo IP pubblico da tutti i front-end di una regola in uscita rende disponibili fino a 64.000 porte temporanee da usare come porte SNAT.  Load Balancer alloca le porte SNAT in multipli di 8. Se si specifica un valore non divisibile per 8, l'operazione di configurazione viene rifiutata.  Se si tenta di allocare più porte SNAT di quante ne siano disponibili in base al numero di indirizzi IP pubblici, l'operazione di configurazione viene rifiutata.  Ad esempio, se si allocano 10.000 porte per ogni macchina virtuale e 7 macchine virtuali in un back-end pool condividono un singolo indirizzo IP pubblico, la configurazione viene rifiutata (7 x 10.000 porte SNAT > 64.000 porte SNAT).  È possibile aggiungere altri indirizzi IP al front-end della regola in uscita per abilitare questo scenario.

È possibile tornare all'[allocazione automatica delle porte SNAT basata sulle dimensioni dei pool back-end](load-balancer-outbound-connections.md#preallocatedports) specificando 0 per il numero di porte.

### <a name="idletimeout"></a> Controllare il timeout di inattività per i flussi in uscita

Le regole in uscita includono un parametro di configurazione per controllare il timeout di inattività per i flussi in uscita in modo da adattarlo alle esigenze dell'applicazione.  Il timeout di inattività in uscita è per impostazione predefinita di 4 minuti.  Il parametro accetta un valore compreso tra 4 e 66 a specifica il numero di minuti per il timeout di inattività per i flussi corrispondenti alla specifica regola.

Per impostare il timeout di inattività in uscita su 1 ora, usare il parametro seguente:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Abilitare TCP Reset in caso di timeout di inattività (anteprima)

Il comportamento predefinito di Load Balancer prevede l'eliminazione automatica del flusso quando viene raggiunto il timeout di inattività in uscita.  Con il parametro enableTCPReset, è possibile abilitare un comportamento dell'applicazione più prevedibile e stabilire se inviare un TCP Reset bidirezionale (TCP RST) quando si verifica il timeout di inattività in uscita. 

Per abilitare TCP Reset per una regola in uscita, usare il parametro seguente:

           "enableTcpReset": true

Vedere [TCP Reset in caso di timeout di inattività (anteprima)](https://aka.ms/lbtcpreset) per informazioni dettagliate, inclusa la disponibilità a livello di area.

### <a name="proto"></a> Supportare sia TCP che UDP come protocolli di trasporto con una singola regola

È probabile che si scelga di supportare tutti i protocolli di trasporto per la regola in uscita, ma è anche possibile applicare la regola in uscita a un protocollo di trasporto specifico, se necessario.

Per impostare i protocolli TCP e UDP, usare il parametro seguente:

          "protocol": "All"

### <a name="disablesnat"></a> Disabilitare NAT in uscita per una regola di bilanciamento del carico

Come indicato in precedenza, le regole di bilanciamento del carico supportano la programmazione automatica di NAT in uscita. Tuttavia, per alcuni scenari è preferibile o necessario disabilitare la programmazione automatica di NAT in uscita con la regola di bilanciamento del carico, per controllare o mettere a punto il comportamento.  In alcuni scenari per le regole in uscita è importante interrompere la programmazione NAT in uscita automatica.

È possibile usare questo parametro in due modi:
- Eliminazione facoltativa dell'uso dell'indirizzo IP in ingresso per NAT in uscita.  Le regole in uscita sono incrementali rispetto alle regole di bilanciamento del carico e con l'impostazione di questo parametro il controllo passa alla regola in uscita.
  
- Ottimizzare i parametri NAT in uscita di un indirizzo IP usato in ingresso e in uscita contemporaneamente.  La programmazione NAT in uscita automatica deve essere disabilitata per consentire a una regola in uscita di assumere il controllo.  Ad esempio, per poter modificare l'allocazione della porta SNAT di un indirizzo usato anche in ingresso, questo parametro deve essere impostato su true.  Se si prova a usare una regola in uscita per ridefinire i parametri di un indirizzo IP usato anche in ingresso e la programmazione NAT in uscita automatica non viene disabilitata per la regola di bilanciamento del carico, l'operazione di configurazione di una regola in uscita avrà esito negativo.

>[!IMPORTANT]
> La macchina virtuale non disporrà della connettività in uscita se si imposta questo parametro su true e non è disponibile una regola in uscita (o uno [scenario con indirizzo IP pubblico a livello di istanza](load-balancer-outbound-connections.md#ilpip) per definire la connettività in uscita.  Alcune operazioni della macchina virtuale o dell'applicazione possono dipendere dalla disponibilità della connettività in uscita. Assicurarsi di valutare le dipendenze dello scenario e l'impatto di questa modifica.

È possibile disabilitare SNAT in uscita per la regola di bilanciamento del carico con questo parametro di configurazione:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Il parametro disableOutboundSNAT è false per impostazione predefinita, ovvero la regola di bilanciamento del carico **include** la conversione NAT in uscita automatica come immagine speculare della configurazione della regola di bilanciamento del carico.  

Se si imposta disableOutboundSnat su true per la regola di bilanciamento del carico, questa cede il controllo della programmazione NAT in uscita altrimenti automatica.  La conversione SNAT in uscita viene disabilitata come conseguenza della regola di bilanciamento del carico.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Riutilizzare pool back-end esistenti o definirne di nuovi

Le regole in uscita non introducono un nuovo concetto per la definizione del gruppo di macchine virtuali a cui applicare la regola.  Al contrario, viene riutilizzato il concetto di pool back-end, usato anche per le regole di bilanciamento del carico. È possibile usare questo meccanismo per semplificare la configurazione, riutilizzando una definizione di pool back-end esistente o creandone una specifica per una regola in uscita.

## <a name="scenarios"></a>Scenari

### <a name="groom"></a> Limitare le connessioni in uscita a un set specifico di indirizzi IP pubblici

È possibile usare una regola in uscita per limitare le connessioni in uscita in modo che risultino provenienti da un set specifico di indirizzi IP pubblici per semplificare gli scenari di creazione di elenchi di elementi consentiti.  Questo indirizzo IP pubblico di origine può essere lo stesso usato da una regola di bilanciamento del carico o un set diverso di indirizzi IP pubblici usato da una regola di bilanciamento del carico.  

1. Creare il [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) (o gli indirizzi IP pubblici dal prefisso IP pubblico)
2. Creare un servizio Load Balancer Standard pubblico
3. Creare front-end che fanno riferimento al prefisso IP pubblico (o agli indirizzi IP pubblici) che si desidera usare
4. Riutilizzare un pool back-end o creare un pool back-end e posizionare le macchine virtuali in un pool back-end del Load Balancer pubblico
5. Configurare una regola in uscita per il Load Balancer pubblico per programmare la conversione NAT in uscita per queste macchine virtuali tramite i front-end
   
Se non si desidera che la regola di bilanciamento del carico venga usata per il traffico in uscita, è necessario [disabilitare SNAT in uscita](#disablesnat) nella regola di bilanciamento del carico.

### <a name="modifysnat"></a> Modificare l'allocazione delle porte SNAT

È possibile usare le regole in uscita per ottimizzare l'[allocazione automatica delle porte SNAT in base alle dimensioni del pool back-end](load-balancer-outbound-connections.md#preallocatedports).

Ad esempio, con due macchine virtuali che condividono un singolo indirizzo IP pubblico per NAT in uscita, potrebbe essere utile aumentare il numero di porte SNAT allocate rispetto alle 1024 porte predefinite, se si verifica l'esaurimento delle porte SNAT. Ogni indirizzo IP pubblico può rendere disponibili fino a 64.000 porte temporanee.  Se si configura una regola in uscita con un singolo front-end per indirizzi IP pubblici, è possibile distribuire un totale di 64.000 porte SNAT alle macchine virtuali nel pool back-end.  Per due macchine virtuali, è possibile allocare un massimo di 32.000 porte SNAT con una regola in uscita (2 x 32.000 = 64.000).

Vedere le informazioni sulle [connessioni in uscita](load-balancer-outbound-connections.md) e i dettagli su come vengono allocate e usate le porte [SNAT](load-balancer-outbound-connections.md#snat).

### <a name="outboundonly"></a> Abilitare NAT solo in uscita

È possibile usare un Load Balancer Standard pubblico per rendere disponibile la conversione NAT in uscita per un gruppo di macchine virtuali. In questo scenario, è possibile usare esclusivamente una regola in uscita, senza che siano necessarie regole aggiuntive.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Solo NAT in uscita per le macchine virtuali (non in ingresso)

Definire un Load Balancer Standard pubblico, posizionare le macchine virtuali nel pool back-end e configurare una regola in uscita per programmare NAT in uscita e limitare le connessioni in uscita in modo che provengano da uno specifico indirizzo IP pubblico. È anche possibile usare un prefisso IP pubblico per semplificare l'inserimento dell'origine delle connessioni in uscita nell'elenco elementi consentiti.

1. Creare un Load Balancer Standard pubblico.
2. Creare un pool back-end e posizionare le macchine virtuali in un pool back-end del Load Balancer pubblico.
3. Configurare una regola in uscita per il Load Balancer pubblico per programmare la conversione NAT in uscita per queste macchine virtuali.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT in uscita per scenari con Load Balancer Standard interno

Quando si usa un Load Balancer Standard interno, NAT in uscita non è disponibile se non è stata dichiarata in modo esplicito una connettività in uscita. È possibile definire la connettività in uscita usando una regola in uscita per creare la connettività in uscita per le macchine virtuali dietro a un Load Balancer Standard interno nel modo indicato di seguito:

1. Creare un Load Balancer Standard pubblico.
2. Creare un pool back-end e posizionare le macchine virtuali in un pool back-end del Load Balancer pubblico oltre al Load Balancer interno.
3. Configurare una regola in uscita per il Load Balancer pubblico per programmare la conversione NAT in uscita per queste macchine virtuali.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Abilitare entrambi i protocolli TCP e UDP per NAT in uscita con un Load Balancer Standard pubblico

- Quando si usa un Load Balancer Standard pubblico, la programmazione NAT in uscita automatica fornita corrisponde al protocollo di trasporto della regola di bilanciamento del carico.  

   1. Disabilitare SNAT in uscita per la regola di bilanciamento del carico.
   2. Configurare una regola in uscita per lo stesso Load Balancer.
   3. Riutilizzare il pool back-end già usato dalle macchine virtuali.
   4. Specificare "protocol": "All" come parte della regola in uscita.

- Quando si usano solo regole NAT in ingresso, non viene fornita alcuna conversione NAT in uscita.

   1. Posizionare le macchine virtuali in un pool back-end.
   2. Definire una o più configurazioni IP front-end con indirizzi IP pubblici o un prefisso IP pubblico.
   3. Configurare una regola in uscita per lo stesso Load Balancer.
   4. Specificare "protocol": "All" come parte della regola in uscita

## <a name="limitations"></a>Limitazioni

- Il numero massimo di porte temporanee utilizzabili per ogni indirizzo IP front-end è 64.000.
- L'intervallo del timeout di inattività in uscita configurabile è compreso tra 4 a 66 minuti (da 240 a 4000 secondi).
- Load Balancer non supporta ICMP per NAT in uscita.
- Non è possibile usare il portale per configurare o visualizzare le regole in uscita.  Usare in alternativa i modelli, l'API REST, l'interfaccia della riga di comando di Azure 2.0 o PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'uso di [Load Balancer per le connessioni in uscita](load-balancer-outbound-connections.md).
- Altre informazioni su [Azure Load Balancer Standard](load-balancer-standard-overview.md).
- Altre informazioni su [TCP Reset bidirezionale in caso di timeout di inattività](load-balancer-tcp-reset.md).
- [Configurare le regole in uscita e il bilanciamento del carico in Load Balancer Standard usando l'interfaccia della riga di comando di Azure](configure-load-balancer-outbound-cli.md).
