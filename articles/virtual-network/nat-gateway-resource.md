---
title: Progettazione di reti virtuali con risorse gateway NAT
titleSuffix: Azure Virtual Network NAT
description: Informazioni su come progettare reti virtuali con le risorse gateway NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d920bde856521f1e662536c1187881e143612039
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359102"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Progettazione di reti virtuali con risorse gateway NAT (anteprima pubblica)

Le risorse gateway NAT fanno parte del servizio [NAT di rete virtuale](nat-overview.md) e forniscono la connettività Internet in uscita per una o più subnet di una rete virtuale. La subnet della rete virtuale determina quale gateway NAT verrà usato. NAT fornisce funzionalità SNAT (Source Network Address Translation) per una subnet.  Le risorse gateway NAT specificano gli indirizzi IP statici usati dalle macchine virtuali durante la creazione di flussi in uscita. Gli indirizzi IP statici provengono da risorse di indirizzi IP pubblici, da risorse di prefissi di indirizzi IP pubblici o da entrambe. Una risorsa gateway NAT può usare fino a 16 indirizzi IP statici da una delle due risorse.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT di rete virtuale per il flusso in uscita verso Internet">
</p>

*Figura: NAT di rete virtuale per il flusso in uscita verso Internet*


>[!NOTE] 
>NAT di rete virtuale è attualmente disponibile in anteprima pubblica e in un set limitato di [aree](nat-overview.md#region-availability). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="how-to-deploy-nat"></a>Come distribuire NAT

La configurazione e l'uso del gateway NAT sono procedure intenzionalmente semplificate:  

Risorsa gateway NAT:
- Creare una risorsa gateway NAT a livello di area o di zona (con isolamento della zona)
- Assegnare gli indirizzi IP
- Modificare il timeout di inattività (facoltativo).

Rete virtuale
- Configurare la subnet di rete virtuale per l'uso di un gateway NAT.

Le route definite dall'utente non sono necessarie.

## <a name="resource"></a>Risorsa

La risorsa è progettata per essere semplice, come si può notare nell'esempio di Azure Resource Manager seguente, in un formato simile a un modello.  Questo formato viene mostrato di seguito per illustrare i concetti e la struttura.  Modificare l'esempio in base alle esigenze.  Questo documento non è concepito come un'esercitazione.

Il diagramma seguente mostra i riferimenti scrivibili tra le diverse risorse di Azure Resource Manager.  La freccia indica la direzione del riferimento, originata dal punto in cui è scrivibile. Verifica 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Modello a oggetti di NAT di rete virtuale">
</p>

*Figura: Modello a oggetti di NAT di rete virtuale*

NAT è consigliato per la maggior parte dei carichi di lavoro, a meno che non esista una dipendenza specifica dalla [connettività in uscita con Load Balancer basata sul pool](../load-balancer/load-balancer-outbound-connections.md).  

È possibile eseguire la migrazione da scenari di bilanciamento del carico standard, con [regole in uscita](../load-balancer/load-balancer-outbound-rules-overview.md), al gateway NAT. Per eseguire la migrazione, spostare le risorse di indirizzi IP pubblici e prefissi di indirizzi IP pubblici dai front-end del servizio di bilanciamento del carico al gateway NAT. Per il gateway NAT, non sono necessari nuovi indirizzi IP. È possibile riutilizzare indirizzi e prefissi di indirizzi IP pubblici standard, purché il totale non superi 16 indirizzi IP. Pianificare la migrazione tenendo presente l'interruzione del servizio durante la transizione.  Per ridurre l'interruzione, è possibile automatizzare il processo. Testare prima la migrazione in un ambiente di staging.  Durante la transizione, i flussi originati in ingresso non sono interessati.

L'esempio seguente mostra come creare una risorsa gateway NAT denominata _myNATGateway_ nell'area _Stati Uniti orientali 2, AZ 1_ con timeout di inattività di _4 minuti_. Gli indirizzi IP in uscita forniti sono:
- Un set di risorse di indirizzi IP pubblici _myIP1_ e _myIP2_ 
- Un set di risorse di prefissi di indirizzi IP pubblici _myPrefix1_ e _myPrefix2_. 

Il numero totale di indirizzi IP forniti da tutte e quattro le risorse di indirizzi IP non può superare 16 indirizzi IP. È consentito usare un numero qualsiasi di indirizzi IP compreso tra 1 e 16.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

Una volta creata, la risorsa gateway NAT può essere usata in una o più subnet di una rete virtuale. Specificare quali subnet usano la risorsa gateway NAT. Un gateway NAT può essere usato in un'unica rete virtuale. Non è necessario assegnare lo stesso gateway NAT a tutte le subnet di una rete virtuale. È possibile configurare le singole subnet con risorse gateway NAT diverse.

Gli scenari in cui non si usano zone di disponibilità saranno a livello di area (senza zone specificate). Se si usano zone di disponibilità, è possibile specificare una zona specifica in cui isolare NAT. La ridondanza della zona non è supportata. Vedere le [zone di disponibilità](#availability-zones) per NAT.


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
I gateway NAT vengono definiti con una proprietà in una subnet all'interno di una rete virtuale. Il gateway NAT verrà usato dai flussi creati dalle macchine virtuali nella subnet _mySubnet1_ della rete virtuale _myVNet_. Per tutta la connettività in uscita verranno usati gli indirizzi IP associati a _myNatGateway_ come indirizzi IP di origine.


## <a name="design-guidance"></a>Linee guida per la progettazione

Leggere questa sezione per acquisire familiarità con gli aspetti da considerare per la progettazione di reti virtuali con NAT.  

1. [Ottimizzazione dei costi](#cost-optimization)
1. [Coesistenza di flussi in ingresso e in uscita](#coexistence-of-inbound-and-outbound)
2. [Gestione delle risorse di base](#managing-basic-resources)
3. [Zone di disponibilità](#availability-zones)

### <a name="cost-optimization"></a>Ottimizzazione dei costi

Gli [endpoint di servizio](virtual-network-service-endpoints-overview.md) e il [collegamento privato](../private-link/private-link-overview.md) sono due opzioni da considerare per ottimizzare i costi quando non è necessario usare NAT.  Il traffico indirizzato agli endpoint di servizio o al collegamento privato non viene elaborato dal NAT della rete virtuale.  

Gli endpoint di servizio vincolano le risorse dei servizi di Azure alla rete virtuale e controllano l'accesso a tali risorse. Ad esempio, quando si accede all'archiviazione di Azure, usare un endpoint di servizio per l'archiviazione per evitare gli addebiti associati ai dati elaborati da NAT. Gli endpoint di servizio sono gratuiti.

Il collegamento privato espone il servizio PaaS di Azure (o altri servizi ospitati con un collegamento privato) come endpoint privato all'interno di una rete virtuale.  Il collegamento privato viene fatturato in base alla durata e ai dati elaborati.

Valutare se uno o entrambi questi approcci sono indicati per uno specifico scenario e adottarli in base alle necessità.

### <a name="coexistence-of-inbound-and-outbound"></a>Coesistenza di flussi in ingresso e in uscita

Il gateway NAT è compatibile con:

 - Load Balancer Standard
 - Indirizzo IP pubblico standard
 - Prefisso di indirizzo IP pubblico standard

Per lo sviluppo di una nuova distribuzione, iniziare con SKU standard.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT di rete virtuale per il flusso in uscita verso Internet">
</p>

*Figura: NAT di rete virtuale per il flusso in uscita verso Internet*

Lo scenario con il solo flusso in uscita verso Internet fornito dal gateway NAT può essere ampliato con funzionalità di flusso in ingresso proveniente da Internet. Ogni risorsa riconosce la direzione da cui si origina il flusso. In una subnet con un gateway NAT, tutti gli scenari del flusso in uscita verso Internet vengono sostituiti dal gateway NAT. Gli scenari con flusso in ingresso proveniente da Internet vengono forniti dalla rispettiva risorsa.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT e VM con indirizzo IP pubblico a livello di istanza

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="NAT di rete virtuale e VM con indirizzo IP pubblico a livello di istanza">
</p>

*Figura: NAT di rete virtuale e VM con indirizzo IP pubblico a livello di istanza*

| Direction | Risorsa |
|:---:|:---:|
| In ingresso | VM con indirizzo IP pubblico a livello di istanza |
| In uscita | Gateway NAT |

La VM userà il gateway NAT per il flusso in uscita.  Il flusso in ingresso originato non è interessato.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT e VM con Load Balancer pubblico

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="NAT di rete virtuale e VM con Load Balancer pubblico">
</p>

*Figura: NAT di rete virtuale e VM con Load Balancer pubblico*

| Direction | Risorsa |
|:---:|:---:|
| In ingresso | Load Balancer pubblico |
| In uscita | Gateway NAT |

Qualsiasi configurazione in uscita proveniente da una regola di bilanciamento del carico o da regole in uscita viene sostituita dal gateway NAT.  Il flusso in ingresso originato non è interessato.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT e VM con indirizzo pubblico a livello di istanza e Load Balancer pubblico

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="NAT di rete virtuale e VM con indirizzo pubblico a livello di istanza e Load Balancer pubblico">
</p>

*Figura: NAT di rete virtuale e VM con indirizzo pubblico a livello di istanza e Load Balancer pubblico*

| Direction | Risorsa |
|:---:|:---:|
| In ingresso | VM con indirizzo pubblico a livello di istanza e Load Balancer pubblico |
| In uscita | Gateway NAT |

Qualsiasi configurazione in uscita proveniente da una regola di bilanciamento del carico o da regole in uscita viene sostituita dal gateway NAT.  La VM userà anche il gateway NAT per il flusso in uscita.  Il flusso in ingresso originato non è interessato.

### <a name="managing-basic-resources"></a>Gestione delle risorse di base

Load Balancer Standard, gli indirizzi IP pubblici e i prefissi di indirizzi IP pubblici sono compatibili con il gateway NAT. I gateway NAT operano nell'ambito di una subnet. Lo SKU di base di questi servizi deve essere distribuito in una subnet senza gateway NAT. Questa separazione consente a entrambe le varianti dello SKU di coesistere nella stessa rete virtuale.

I gateway NAT hanno la precedenza sugli scenari in uscita della subnet. Load Balancer di base o l'IP pubblico (e tutti i servizi gestiti associati) non può essere modificato con le traduzioni corrette. Il gateway NAT assume il controllo del traffico in uscita verso Internet in una subnet. Il traffico in ingresso verso il servizio di bilanciamento del carico di base e l'indirizzo IP pubblico non è disponibile. Il traffico in ingresso verso un servizio di bilanciamento del carico di base e/o un indirizzo IP pubblico configurato in una macchina virtuale non sarà disponibile.

### <a name="availability-zones"></a>Zone di disponibilità

Anche senza le zone di disponibilità, NAT è resiliente e può sopravvivere a più errori dei componenti dell'infrastruttura. Se le zone di disponibilità fanno parte dello scenario, è necessario configurare NAT per una zona specifica.  Le operazioni del piano di controllo e il piano dati sono vincolati alla zona specificata. Un errore in una zona diversa da quella in cui si trova lo scenario non dovrebbe avere effetti su NAT. Il traffico in uscita proveniente da macchine virtuali nella stessa zona avrà esito negativo a causa dell'isolamento della zona.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="NAT di rete virtuale con zone di disponibilità">
</p>

*Figura: NAT di rete virtuale con zone di disponibilità*

Un gateway NAT con isolamento della zona richiede indirizzi IP che corrispondono alla zona del gateway NAT. Le risorse gateway NAT con indirizzi IP di una zona diversa o senza una zona non sono supportate.

Le reti virtuali e le subnet sono a livello di area e non sono allineate alla zona. Una VM deve trovarsi nella stessa zona del gateway NAT per una promessa di zona delle connessioni in uscita. L'isolamento della zona viene creato con uno "stack" di zona per ogni zona di disponibilità. Una promessa di zona non esiste quando si attraversano le zone di un gateway NAT di zona o si una un gate NAT di area con VM di zona.

Quando si distribuiscono set di scalabilità di macchine virtuali da usare con NAT, si distribuisce un set di scalabilità di zona nella rispettiva subnet a cui si collega il gateway NAT della zona corrispondente. Se si usano set di scalabilità che si estendono in due o più zone, NAT non rispetta la promessa di zona.  NAT non supporta la ridondanza della zona.  È supportato solo l'isolamento a livello di area o di zona.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="NAT di rete virtuale in più zone">
</p>

*Figura: NAT di rete virtuale in più zone*

La proprietà delle zone non è modificabile.  Ridistribuire la risorsa gateway NAT con la preferenza prevista per area o zona.

>[!NOTE] 
>Gli indirizzi IP non sono di per sé con ridondanza della zona se non viene specificata una zona.  Il front-end di [Load Balancer Standard è con ridondanza della zona](../load-balancer/load-balancer-standard-availability-zones.md#frontend) se non viene creato un indirizzo IP in una zona specifica.  Ciò non si applica a NAT.  È supportato solo l'isolamento a livello di area o di zona.

## <a name="source-network-address-translation"></a>Source Network Address Translation

SNAT (Source Network Address Translation) riscrive l'origine di un flusso per impostare un indirizzo IP diverso.  Le risorse gateway NAT usano una variante di SNAT nota come PAT (Port Address Translation). PAT riscrive l'indirizzo e la porta di origine. Con SNAT, non esiste una relazione fissa tra il numero di indirizzi privati e i relativi indirizzi pubblici tradotti.  

### <a name="fundamentals"></a>Nozioni fondamentali

Esaminiamo un esempio di quattro flussi per illustrare il concetto di base.  Il gateway NAT usa la risorsa di indirizzo IP pubblico 65.52.0.2.

| Flusso | Tupla di origine | Tupla di destinazione |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Questi flussi potrebbero essere simili ai seguenti dopo l'applicazione di PAT:

| Flusso | Tupla di origine | Tupla di origine con SNAT | Tupla di destinazione | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

La destinazione vedrà l'origine del flusso come 65.52.0.2 (tupla di origine con SNAT) con la porta assegnata mostrata.  PAT, come illustrato nella tabella precedente, viene anche detto SNAT con mascheramento delle porte.  Vengono mascherate più origini private dietro un indirizzo IP e una porta.

Non considerare il modo specifico in cui vengono assegnate le porte.  La tabella precedente serve solo a illustrare il concetto di base.

SNAT fornito da NAT è diverso da [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) per molti versi.

### <a name="on-demand"></a>Su richiesta

NAT prevede porte SNAT su richiesta per i nuovi flussi di traffico in uscita. Tutte le porte SNAT in inventario vengono usate da qualsiasi macchina virtuale nelle subnet configurate con NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="SNAT in uscita su richiesta per NAT di rete virtuale">
</p>

*Figura: SNAT in uscita su richiesta per NAT di rete virtuale*

Qualsiasi configurazione IP di una macchina virtuale può creare flussi in uscita su richiesta secondo necessità.  Non è necessario procedere alla pianificazione di pre-allocazione per ogni istanza, incluso l'overprovisioning del caso peggiore per istanza.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Differenze negli scenari di esaurimento">
</p>

*Figura: Differenze negli scenari di esaurimento*

Le porte SNAT rilasciate diventano disponibili per l'uso da parte di qualsiasi macchina virtuale nelle subnet configurate con NAT.  L'allocazione su richiesta consente di usare le porte SNAT secondo necessità per i carichi di lavoro dinamici e divergenti nelle subnet.  Purché siano disponibili porte SNAT in inventario, il flusso SNAT avrà esito positivo. Una maggiore concentrazione di porte SNAT trae invece vantaggio dall'inventario più ampio. La porte SNAT non vengono lasciate inutilizzate per le macchine virtuali che le richiedono attivamente.

### <a name="scaling"></a>Scalabilità

Per lo scenario in uscita completo di NAT, è necessario un inventario di porte SNAT sufficiente. La scalabilità di NAT è principalmente una funzione della gestione dell'inventario di porte SNA disponibile e condiviso. Deve esistere un inventario sufficiente per gestire il flusso di picco in uscita per tutte le subnet collegate alla risorsa gateway NAT.

SNAT esegue il mapping di più indirizzi privati a un unico indirizzo pubblico e usa più indirizzi IP pubblici per la scalabilità.

Una risorsa gateway NAT userà 64.000 porte (SNAT) di un indirizzo IP pubblico.  Queste porte SNAT diventano l'inventario disponibile per il mapping del flusso privato a quello pubblico. Inoltre, l'aggiunta di altri indirizzi IP pubblici aumenta l'inventario disponibile di porte SNAT. Le risorse gateway NAT sono scalabili fino a 16 indirizzi IP pubblici e 1 milione di porte SNAT.  TCP e UDP sono inventari di porte SNAT separati e non correlati.

Le risorse gateway NAT riutilizzano opportunisticamente le porte di origine. Ai fini della scalabilità, è consigliabile presupporre che ogni flusso richieda una nuova porta SNAT e aumentare il numero totale di indirizzi IP pubblici per il traffico in uscita.

### <a name="protocols"></a>Protocolli

Le risorse gateway NAT interagiscono con le intestazioni IP e di trasporto IT dei flussi UDP e TCP e sono indipendenti dai payload del livello applicativo.  Gli altri protocolli IP non sono supportati.

### <a name="timers"></a>Timer

Il timeout di inattività può essere cambiato da 4 minuti (impostazione predefinita) a 120 minuti (2 ore) per tutti i flussi.  Inoltre, è possibile reimpostare il timer di inattività con il traffico di un flusso.  Un modello consigliato per l'aggiornamento di connessioni inattive per lungo tempo e il rilevamento dell'attività degli endpoint è costituito dai keep-alive TCP.  I keep-alive TCP vengono considerati come ACK duplicati dagli endpoint, prevedono un sovraccarico ridotto e sono invisibili al livello applicativo.

Per il rilascio di porte SNAT vengono usati i timer seguenti:

| Timer | valore |
|---|---|
| TCP FIN | 60 secondi |
| TCP RST | 10 secondi |
| TCP half open | 30 secondi |

Una porta SNAT è disponibile per il riutilizzo nello stesso indirizzo IP di destinazione e nella stessa porta di destinazione dopo 5 secondi.

>[!NOTE] 
>Queste impostazioni dei timer sono soggette a modifica. I valori vengono forniti per facilitare la risoluzione dei problemi e al momento non bisogna fare affidamento su timer specifici.

## <a name="limitations"></a>Limitazioni

- NAT è compatibile con risorse di indirizzi IP pubblici, prefissi di indirizzi IP pubblici e servizi di bilanciamento del carico di SKU standard.   Le risorse di base, ad esempio il servizio di bilanciamento del carico di base, nonché qualsiasi prodotto derivato non sono compatibili con NAT.  Le risorse di base devono essere inserite in una subnet non configurata con NAT.
- La famiglia di indirizzi IPv4 è supportata.  NAT non interagisce con la famiglia di indirizzi IPv6.  Non è possibile distribuire NAT in una subnet con prefisso IPv6.
- La registrazione del flusso del gruppo di sicurezza di rete non è supportato con l'uso di NAT.
- NAT non può estendersi in più reti virtuali.

## <a name="preview-participation"></a>Partecipazione all'anteprima

Seguire le [istruzioni per abilitare la sottoscrizione](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Commenti e suggerimenti

Ci interessa sapere come possiamo migliorare il servizio. Gli utenti sono invitati a condividere il loro [feedback sull'anteprima pubblica](https://aka.ms/natfeedback).  E possono proporre nuove funzionalità o esprimere un voto in [UserVoice per NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [servizio NAT di rete virtuale](nat-overview.md).
* Informazioni su [metriche e avvisi per le risorse gateway NAT](nat-metrics.md).
* Informazioni sulla [risoluzione dei problemi delle risorse gateway NAT](troubleshoot-nat.md).
* [Segnalare le nuove funzionalità richieste per NAT di rete virtuale in UserVoice](https://aka.ms/natuservoice).
* [Inviare feedback sull'anteprima pubblica](https://aka.ms/natfeedback).
* Esercitazione per la convalida del gateway NAT
  - [Interfaccia della riga di comando di Azure](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-cli.md)
  - [Portale](tutorial-create-validate-nat-gateway-cli.md)
* Avvio rapido per la distribuzione di una risorsa gateway NAT
  - [Interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portale](./quickstart-create-nat-gateway-portal.md).
* Informazioni sull'API della risorsa gateway NAT
  - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
* Informazioni sulle [zone di disponibilità](../availability-zones/az-overview.md).
* Informazioni su [Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md).
* Informazioni su [zone di disponibilità e Load Balancer Standard](../load-balancer/load-balancer-standard-availability-zones.md).


