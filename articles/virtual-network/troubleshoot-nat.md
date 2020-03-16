---
title: Risolvere i problemi di connettività del servizio NAT di rete virtuale di Azure
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Risolvere i problemi relativi al servizio NAT di rete virtuale.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674321"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Risolvere i problemi di connettività del servizio NAT di rete virtuale di Azure

Questo articolo consente agli amministratori di diagnosticare e risolvere i problemi di connettività quando si usa il servizio NAT di rete virtuale.

>[!NOTE] 
>NAT di rete virtuale è attualmente disponibile in anteprima pubblica e in un set limitato di [aree](nat-overview.md#region-availability). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>I problemi

* [Esaurimento SNAT](#snat-exhaustion)
* [Errore del ping ICMP](#icmp-ping-is-failing)
* [Errori di connettività](#connectivity-failures)
* [Coesistenza di IPv6](#ipv6-coexistence)

Per risolvere questi problemi, attenersi alla procedura descritta nella sezione seguente.

## <a name="resolution"></a>Risoluzione

### <a name="snat-exhaustion"></a>Esaurimento SNAT

Una singola [risorsa gateway NAT](nat-gateway-resource.md) supporta un numero di flussi simultanei compreso tra 64.000 e 1 milione.  Ogni indirizzo IP fornisce 64.000 porte SNAT all'inventario disponibile. È possibile usare fino a 16 indirizzi IP per ogni risorsa gateway NAT.  Il meccanismo SNAT è descritto [qui](nat-gateway-resource.md#source-network-address-translation) in modo più dettagliato.

Spesso la causa principale dell'esaurimento SNAT è un anti-modello per il modo in cui la connettività in uscita viene stabilita e gestita.  Leggere attentamente questa sezione.

#### <a name="steps"></a>Passaggi

1. Esaminare il modo in cui l'applicazione crea la connettività in uscita, ad esempio, revisione del codice o acquisizione di pacchetti. 
2. Determinare se questa attività è un comportamento previsto o se l'applicazione non funziona correttamente.  Usare le [metriche](nat-metrics.md) in Monitoraggio di Azure per convalidare i risultati. Usare la categoria "Non riuscita" per la metrica Connessioni SNAT.
3. Valutare se vengono seguiti i modelli appropriati.
4. Valutare se il problema relativo l'esaurimento delle porte SNAT deve essere corretto mediante l'assegnazione di ulteriori indirizzi IP alla risorsa gateway NAT.

#### <a name="design-patterns"></a>Modelli di progettazione

Quando possibile, sfruttare sempre i vantaggi del riutilizzo delle connessioni e del pool di connessioni.  Questi modelli eviteranno problemi di esaurimento delle risorse e genereranno un comportamento prevedibile, affidabile e scalabile. Le primitive per questi modelli sono reperibili in molti framework e librerie di sviluppo.

_**Soluzione:**_ usare i modelli appropriati

- Valutare l'uso di [modelli di polling asincroni](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) per le operazioni a esecuzione prolungata per liberare risorse di connessione per altre operazioni.
- I flussi di lunga durata, ad esempio le connessioni TCP riutilizzate, devono usare i keep-alive TCP o i keep-alive del livello dell'applicazione per evitare il timeout dei sistemi intermedi.
- È consigliabile usare i [criteri di ripetizione](https://docs.microsoft.com/azure/architecture/patterns/retry) dei tentativi per evitare tentativi o picchi aggressivi durante errori temporanei o ripristini da errori.
La creazione di una nuova connessione TCP per ogni operazione HTTP (nota anche come "connessioni atomiche") è un anti-pattern.  Le connessioni atomiche impediscono il ridimensionamento ottimale dell'applicazione e generano uno spreco di risorse.  Concatenare sempre più operazioni nella stessa connessione.  L'applicazione otterrà un vantaggio in termini di velocità delle transazioni e costi delle risorse.  Quando l'applicazione usa la crittografia Transport Layer Security (TLS), l'elaborazione delle nuove connessioni comporta un costo significativo.  Vedere [Schemi progettuali per il cloud di Azure](https://docs.microsoft.com/azure/architecture/patterns/) per altri modelli di procedure consigliate.

#### <a name="possible-mitigations"></a>Possibili procedure di mitigazione

_**Soluzione:**_ ridimensionare la connettività in uscita come indicato di seguito:

| Scenario | Evidenza |Strategia di riduzione del rischio |
|---|---|---|
| Si riscontra una contesa per le porte SNAT e l'esaurimento delle porte SNAT durante i periodi di utilizzo elevato. | La categoria "Non riuscita" per la [metrica](nat-metrics.md) Connessioni SNAT in Monitoraggio di Azure mostra gli errori temporanei o persistenti nel tempo e il volume elevato di connessioni.  | Determinare se è possibile aggiungere risorse indirizzo IP pubblico o risorse prefisso IP pubblico aggiuntive. Questa aggiunta garantirà fino a un totale di 16 indirizzi IP per il gateway NAT, fornirà un inventario più esteso per le porte SNAT disponibili (64.000 per indirizzo IP) e consentirà di dimensionare ulteriormente lo scenario.|
| Sono già stati assegnati 16 indirizzi IP e si riscontra ancora l'esaurimento delle porte SNAT. | Il tentativo di aggiungere un altro indirizzo IP non riesce. Il numero totale di indirizzi IP delle risorse di indirizzi IP pubblici o di prefissi IP pubblici supera il totale di 16. | Distribuire l'ambiente dell'applicazione tra più subnet e specificare una risorsa gateway NAT per ogni subnet.  Rivalutare il modello o i modelli di progettazione per l'ottimizzazione in base alle [indicazioni](#design-patterns) precedenti. |

>[!NOTE]
>È importante comprendere la causa dell'esaurimento delle porte SNAT. Assicurarsi di usare i modelli corretti per gli scenari affidabili e scalabili.  L'aggiunta di più porte SNAT a uno scenario senza aver compreso la causa della domanda deve essere l'ultima eventualità. Se non si comprende il motivo per cui lo scenario fa pressione sull'inventario di porte SNAT, l'aggiunta di più porte SNAT mediante l'aggiunta di altri indirizzi IP ritarderà solamente lo stesso errore di esaurimento man mano che le prestazioni dell'applicazione aumentano.  È possibile che si stiano mascherando altre inefficienze e anti-pattern.

### <a name="icmp-ping-is-failing"></a>Errore del ping ICMP

Il servizio [NAT di rete virtuale](nat-overview.md) supporta i protocolli UDP e TCP IPv4. Il protocollo ICMP non è supportato e genererà quindi errori.  

_**Soluzione:**_ Usare invece i test di connessione TCP, ad esempio "ping TCP", e i test del livello dell'applicazione specifici per UDP per convalidare la connettività end-to-end.

La tabella seguente può essere usata come punto di partenza per gli strumenti da usare per avviare i test.

| Sistema operativo | Test di connessione TCP generico | Test del livello dell'applicazione TCP | UDP |
|---|---|---|---|
| Linux | nc (test di connessione generico) | curl (test del livello dell'applicazione TCP) | specifico dell'applicazione |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) di PowerShell | specifico dell'applicazione |

### <a name="connectivity-failures"></a>Errori di connettività

I problemi di connettività di [NAT di rete virtuale](nat-overview.md) possono essere dovuti a diversi motivi:

* [Esaurimento SNAT](#snat-exhaustion) temporaneo o persistente del gateway NAT
* Errori temporanei nell'infrastruttura di Azure 
* Errori temporanei nel percorso tra Azure e la destinazione Internet pubblica 
* Errori temporanei o persistenti nella destinazione Internet pubblica.

Usare strumenti come i seguenti per verificare la connettività. [Il ping ICMP non è supportato](#icmp-ping-is-failing).

| Sistema operativo | Test di connessione TCP generico | Test del livello dell'applicazione TCP | UDP |
|---|---|---|---|
| Linux | nc (test di connessione generico) | curl (test del livello dell'applicazione TCP) | specifico dell'applicazione |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) di PowerShell | specifico dell'applicazione |

#### <a name="snat-exhaustion"></a>Esaurimento SNAT

Rivedere la sezione sull'[esaurimento SNAT](#snat-exhaustion) in questo articolo.

#### <a name="azure-infrastructure"></a>Infrastruttura di Azure

Anche se Azure monitora e gestisce la propria infrastruttura con grande attenzione, possono verificarsi errori temporanei perché non vi è alcuna garanzia che le trasmissioni avvengano senza perdita di dati.  Usare modelli di progettazione che consentono ritrasmissioni SYN per le applicazioni TCP. Usare timeout di connessione sufficientemente ampi da consentire la ritrasmissione SYN TCP per ridurre gli effetti temporanei causati da un pacchetto SYN perso.

_**Soluzione:**_

* controllare l'[esaurimento SNAT](#snat-exhaustion).
* Il parametro di configurazione in uno stack TCP che controlla il comportamento di ritrasmissione SYN è denominato RTO ([Retransmission Time-Out](https://tools.ietf.org/html/rfc793)). Il valore di RTO è configurabile, ma in genere per impostazione predefinita è impostato almeno su 1 secondo con backoff esponenziale.  Se il timeout della connessione dell'applicazione è troppo breve (ad esempio 1 secondo), si possono verificare sporadici timeout della connessione.  Aumentare il timeout della connessione dell'applicazione.
* Se si osservano timeout imprevisti più lunghi con i comportamenti predefiniti dell'applicazione, aprire un caso di supporto per ulteriori procedure di risoluzione dei problemi.

Non è consigliabile ridurre artificialmente il timeout della connessione TCP o ottimizzare il parametro RTO.

#### <a name="public-internet-transit"></a>Transito su Internet pubblico

La probabilità che si verifichino errori temporanei aumenta nel caso di un percorso più lungo verso la destinazione e di un numero maggiore di sistemi intermedi. È previsto che la frequenza di errori temporanei aumenti sull'[infrastruttura di Azure](#azure-infrastructure). 

Seguire le stesse indicazioni fornite nella precedente sezione sull'[infrastruttura di Azure](#azure-infrastructure).

#### <a name="internet-endpoint"></a>Endpoint Internet

Le sezioni precedenti si applicano in aggiunta alle considerazioni relative all'endpoint Internet con cui viene stabilita la comunicazione. Altri fattori che possono influire sull'esito positivo della connettività sono:

* Gestione del traffico sul lato destinazione, tra cui
- Limitazione della frequenza delle API imposta dal lato di destinazione
- Mitigazione dei volumi DDoS o shaping del traffico sul livello di trasporto
* Firewall o altri componenti nella destinazione 

In genere le acquisizioni di pacchetti nell'origine e nella destinazione (se disponibili) sono necessarie per determinare quali eventi si verificano.

_**Soluzione:**_

* controllare l'[esaurimento SNAT](#snat-exhaustion). 
* Verificare la connettività a un endpoint nella stessa area o altrove per un confronto.  
* Se si creano test di volumi elevati o della frequenza delle transazioni, verificare se la riduzione della frequenza comporta una riduzione degli errori.
* Se la modifica della frequenza ha effetto sulla frequenza degli errori, verificare se sono state raggiunte le limitazioni della frequenza di API o altri vincoli sul lato della destinazione.
* Se l'indagine è senza risultati, aprire un caso di supporto per ulteriori procedure di risoluzione dei problemi.

#### <a name="tcp-resets-received"></a>Reimpostazioni TCP ricevute

Se si rilevano reimpostazioni TCP (pacchetti RST TCP) ricevute nella macchina virtuale di origine, è possibile che siano state generate dal gateway NAT sul lato privato per i flussi non riconosciuti come in corso.  Uno dei possibili motivi è che la connessione TCP prevede timeout di inattività.  È possibile modificare il timeout di inattività da 4 minuti a un massimo di 120 minuti.

Le reimpostazioni TCP non vengono generate sul lato pubblico delle risorse gateway NAT. Se si ricevono reimpostazioni TCP sul lato di destinazione, vengono generate dallo stack della VM di origine e non dalla risorsa gateway NAT.

_**Soluzione:**_

* esaminare le raccomandazioni sui [modelli di progettazione](#design-patterns).  
* Se necessario, aprire un caso di supporto per ulteriori procedure di risoluzione dei problemi.

### <a name="ipv6-coexistence"></a>Coesistenza di IPv6

[NAT di rete virtuale](nat-overview.md) supporta i protocolli UDP IPv4 e TCP e la distribuzione in una [subnet con prefisso IPv6 non è supportata](nat-overview.md#limitations).

_**Soluzione:**_ distribuire il gateway NAT in una subnet senza prefisso IPv6.

È possibile segnalare l'interesse per altre funzionalità tramite [UserVoice per NAT di rete virtuale](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [servizio NAT di rete virtuale](nat-overview.md)
* Informazioni sulla [risorsa gateway NAT](nat-gateway-resource.md)
* Informazioni su [metriche e avvisi per le risorse gateway NAT](nat-metrics.md).
* [Segnalare le nuove funzionalità richieste per NAT di rete virtuale in UserVoice](https://aka.ms/natuservoice).

