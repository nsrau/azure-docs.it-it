---
title: SNAT per le connessioni in uscita
description: Descrive il modo in cui viene usato Azure Load Balancer per eseguire SNAT per la connettività Internet in uscita
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: b3924a563d8266cfa38f24106dbb84102031a182
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331873"
---
# <a name="using-snat-for-outbound-connections"></a>Uso di SNAT per le connessioni in uscita

Gli indirizzi IP front-end di un servizio di bilanciamento del carico pubblico di Azure possono essere usati per fornire connettività in uscita a Internet per le istanze back-end. Questa configurazione utilizza la **Network Address Translation di origine (SNAT)**. SNAT riscrive l'indirizzo IP del back-end nell'indirizzo IP pubblico del servizio di bilanciamento del carico. 

SNAT Abilita il **mascheramento IP** dell'istanza back-end. Questo mascheramento impedisce alle origini esterne di avere un indirizzo diretto per le istanze back-end. La condivisione di un indirizzo IP tra le istanze di back-end riduce il costo degli indirizzi IP pubblici statici e supporta scenari come la semplificazione degli elenchi di indirizzi IP consentiti con traffico da indirizzi IP pubblici noti. 

>[!Note]
> Per le applicazioni con che richiedono un numero elevato di connessioni in uscita o clienti aziendali che richiedono un singolo set di indirizzi IP da usare da una determinata rete virtuale, la [rete virtuale NAT](https://docs.microsoft.com/azure/virtual-network/nat-overview) è la soluzione consigliata. L'allocazione dinamica consente una configurazione semplice e > l'utilizzo più efficiente delle porte SNAT da ogni indirizzo IP. Consente inoltre a tutte le risorse della rete virtuale di condividere un set di indirizzi IP senza che sia necessario condividere > un servizio di bilanciamento del carico.

>[!Important]
> Anche senza la configurazione di SNAT in uscita, gli account di archiviazione di Azure all'interno della stessa area saranno ancora accessibili e le risorse back-end potranno comunque accedere ai servizi Microsoft, ad esempio gli aggiornamenti di Windows.

>[!NOTE] 
>Questo articolo illustra solo le distribuzioni di Azure Resource Manager. Vedere [Connessioni in uscita (versione classica)](load-balancer-outbound-connections-classic.md) per tutti gli scenari di distribuzione classica in Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Condivisione dell'indirizzo IP front-end tra risorse back-end

Se le risorse back-end di un servizio di bilanciamento del carico non hanno indirizzi IP pubblici a livello di istanza (ILPIP), stabiliscono connettività in uscita tramite l'IP front-end del servizio di bilanciamento del carico pubblico. Le porte vengono usate per generare identificatori univoci usati per mantenere flussi distinti. Internet usa una tupla con cinque elementi per fornire questa distinzione.

La tupla con cinque elementi è costituita da:

* IP di destinazione
* Porta di destinazione
* IP di origine
* Porta di origine e protocollo per fornire questa distinzione.

Se per le connessioni in ingresso viene usata una porta, sarà presente un **listener** per le richieste di connessione in ingresso su tale porta e non potrà essere usata per le connessioni in uscita. Per stabilire una connessione in uscita, è necessario usare una **porta temporanea** per fornire la destinazione con una porta per la comunicazione e la gestione di un flusso di traffico distinto. Quando queste porte temporanee vengono usate per eseguire SNAT, sono denominate **porte SNAT** 

Per definizione, ogni indirizzo IP dispone di 65.535 porte. Ogni porta può essere utilizzata per le connessioni in ingresso o in uscita per TCP (Transmission Control Protocol) e UDP (User Datagram Protocol). Quando un indirizzo IP pubblico viene aggiunto come IP front-end a un servizio di bilanciamento del carico, Azure fornisce 64.000 idoneo per l'uso come porte SNAT. 

>[!NOTE]
> Ogni porta usata per una regola di bilanciamento del carico o NAT in ingresso utilizzerà un intervallo di otto porte da queste porte 64.000, riducendo il numero di porte idonee per SNAT. Se una regola di bilanciamento del carico > o NAT è nello stesso intervallo di otto come altro, non utilizzerà porte aggiuntive. 

Tramite le [regole in uscita](https://docs.microsoft.com/azure/load-balancer/outbound-rules) e le regole di bilanciamento del carico, queste porte SNAT possono essere distribuite alle istanze back-end per consentire loro di condividere gli indirizzi IP pubblici del servizio di bilanciamento del carico per le connessioni in uscita.

Quando viene configurato lo [scenario 2](#scenario2) seguente, l'host per ogni istanza di back-end eseguirà SNAT nei pacchetti che fanno parte di una connessione in uscita. Quando si esegue SNAT in una connessione in uscita da un'istanza di back-end, l'host riscrive l'indirizzo IP di origine in uno degli IP front-end. Per mantenere i flussi univoci, l'host riscrive la porta di origine di ogni pacchetto in uscita in una delle porte SNAT allocate per l'istanza back-end.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Comportamento delle connessioni in uscita per diversi scenari
  * Macchina virtuale con indirizzo IP pubblico.
  * Macchina virtuale senza indirizzo IP pubblico.
  * Macchina virtuale senza IP pubblico e senza Load Balancer standard.
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Scenario 1: macchina virtuale con IP pubblico


 | Associazioni | Metodo | Protocolli IP |
 | ---------- | ------ | ------------ |
 | Servizio di bilanciamento del carico pubblico o autonomo | [SNAT (origine Network Address Translation)](#snat) </br> non viene usata. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (incapsulamento del payload di sicurezza) |


 #### <a name="description"></a>Descrizione


 Azure usa l'indirizzo IP pubblico assegnato alla configurazione IP della scheda di interfaccia di rete dell'istanza per tutti i flussi in uscita. L'istanza ha tutte le porte temporanee disponibili. Non importa se la macchina virtuale è con carico bilanciato o meno. Questo scenario ha la precedenza rispetto agli altri. 


 Un indirizzo IP pubblico assegnato a una macchina virtuale è una relazione 1:1, anziché 1:molti, e viene implementato come NAT 1:1 senza stato.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenario 2: macchina virtuale senza IP pubblico e dietro Load Balancer pubblico standard


 | Associazioni | Metodo | Protocolli IP |
 | ------------ | ------ | ------------ |
 | Bilanciamento del carico pubblico | Uso degli indirizzi IP front-end del servizio di bilanciamento del carico per [SNAT](#snat).| TCP </br> UDP |


 #### <a name="description"></a>Descrizione


 La risorsa del servizio di bilanciamento del carico è configurata con una regola in uscita o una regola di bilanciamento del carico che Abilita SNAT predefiniti. Questa regola viene usata per creare un collegamento tra il front-end IP pubblico e il pool back-end. 


 Se non si completa questa configurazione della regola, il comportamento è come descritto nello scenario 3. 


 Una regola con un listener non è necessaria affinché il probe di integrità abbia esito positivo.


 Quando una macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine nell'indirizzo IP pubblico del front-end pubblico del servizio di bilanciamento del carico. Questa traduzione viene eseguita tramite [SNAT](#snat). 


 Le porte temporanee dell'indirizzo IP pubblico front-end del servizio di bilanciamento del carico vengono usate per distinguere i singoli flussi originati dalla macchina virtuale. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. 


 In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT. È consigliabile che una regola in [uscita](https://docs.microsoft.com/azure/load-balancer/outbound-rules) sia configurata in modo esplicito. Se si usa SNAT predefinito tramite una regola di bilanciamento del carico, le porte SNAT vengono pre-allocate come descritto nella [tabella di allocazione delle porte SNAT predefinite](#snatporttable).


 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario3"></a>Scenario 3: macchina virtuale senza IP pubblico e dietro Load Balancer Basic


 | Associazioni | Metodo | Protocolli IP |
 | ------------ | ------ | ------------ |
 |nessuno </br> Servizio di bilanciamento del carico di base | [SNAT](#snat) con indirizzo IP dinamico a livello di istanza| TCP </br> UDP | 

 #### <a name="description"></a>Descrizione


 Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine in un indirizzo IP di origine pubblica allocato in modo dinamico. Questo indirizzo IP pubblico **non è configurabile** e non può essere riservato. Questo indirizzo non viene conteggiato rispetto al limite di risorse IP pubblico della sottoscrizione. 


 L'indirizzo IP pubblico verrà rilasciato e verrà richiesto un nuovo indirizzo IP pubblico se si ridistribuisce: 


 * Macchina virtuale
 * Set di disponibilità
 * Set di scalabilità di macchine virtuali 


 Non usare questo scenario per aggiungere indirizzi IP a un elenco Consenti. Usare lo scenario 1 o 2 in cui si dichiara in modo esplicito il comportamento in uscita. Le porte [SNAT](#snat) vengono preallocate come descritto nella [tabella di allocazione delle porte SNAT predefinite](#snatporttable).


## <a name="exhausting-ports"></a><a name="scenarios"></a> Porte esaurite

Ogni connessione allo stesso indirizzo IP di destinazione e alla stessa porta di destinazione userà una porta SNAT. Questa connessione mantiene un **flusso di traffico** distinto dall'istanza o dal **client** back-end a un **server**. Questo processo fornisce al server una porta distinta su cui indirizzare il traffico. Senza questo processo, il computer client non è a conoscenza del flusso di cui fa parte un pacchetto.

Si supponga di avere a che fare con più browser https://www.microsoft.com , ovvero:

* IP di destinazione = 23.53.254.142
* Porta di destinazione = 443
* Protocollo = TCP

Senza porte di destinazione diverse per il traffico di ritorno (la porta SNAT utilizzata per stabilire la connessione), il client non sarà in grado di separare il risultato di una query da un altro.

È possibile che le connessioni in uscita si rompano. Un'istanza back-end può essere allocata A porte insufficienti. Senza il **riutilizzo della connessione** abilitato, viene aumentato il rischio di **esaurimento delle porte** SNAT.

Le nuove connessioni in uscita a un IP di destinazione avranno esito negativo quando si verifica l'esaurimento delle porte. Quando una porta diventa disponibile, le connessioni vengono riuscite. Questo esaurimento si verifica quando le porte 64.000 da un indirizzo IP sono distribuite in modo sottile in molte istanze di back-end. Per istruzioni sulla mitigazione dell'esaurimento delle porte SNAT, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

Per le connessioni TCP, il servizio di bilanciamento del carico userà una singola porta SNAT per ogni indirizzo IP e porta di destinazione. Questo multiuso consente più connessioni allo stesso indirizzo IP di destinazione con la stessa porta SNAT. Questa multiuso è limitata se la connessione non è a porte di destinazione diverse.

Per le connessioni UDP, il servizio di bilanciamento del carico usa un algoritmo **NAT cono con restrizioni di porta** , che usa una porta SNAT per ogni indirizzo IP di destinazione, indipendentemente dalla porta di destinazione. 

Una porta viene riutilizzata per un numero illimitato di connessioni. La porta viene riutilizzata solo se la porta o l'indirizzo IP di destinazione è diverso.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Allocazione porta predefinita

A ogni indirizzo IP pubblico assegnato come IP front-end del servizio di bilanciamento del carico vengono concesse 64.000 porte SNAT per i membri del pool back-end. Non è possibile condividere le porte con i membri del pool back-end. Un intervallo di porte SNAT può essere usato solo da una singola istanza di back-end per garantire che i pacchetti restituiti vengano instradati correttamente. 

Si consiglia di usare una regola in uscita esplicita per configurare l'allocazione della porta SNAT. Questa regola massimizza il numero di porte SNAT disponibili per le connessioni in uscita per ogni istanza di back-end. 

Se si utilizza l'allocazione automatica dei SNAT in uscita tramite una regola di bilanciamento del carico, nella tabella di allocazione verrà definita l'allocazione delle porte.

La <a name="snatporttable"></a> tabella seguente mostra le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 | 

>[!NOTE]
> Se si dispone di un pool back-end con una dimensione massima di 10, ogni istanza può avere 64000/10 = 6.400 porte se si definisce una regola esplicita in uscita. In base alla tabella precedente, ognuno avrà 1.024 solo se si sceglie l'allocazione automatica.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regole in uscita e NAT della rete virtuale

Azure Load Balancer le regole in uscita e la rete virtuale NAT sono opzioni disponibili per l'uscita da una rete virtuale.

Per ulteriori informazioni sulle regole in uscita, vedere [regole in uscita](outbound-rules.md).

Per ulteriori informazioni sulla rete virtuale di Azure NAT, vedere informazioni su [rete virtuale di Azure NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Vincoli

*   Le porte verranno rilasciate dopo 15 secondi se viene ricevuto o inviato un **RST TCP**
*   Le porte verranno rilasciate dopo 240 secondi se viene ricevuto o inviato un **FINACK**
*   Quando una connessione è inattiva e non viene inviato alcun nuovo pacchetto, le porte verranno rilasciate dopo 4 – 120 minuti.
  * Questa soglia può essere configurata tramite regole in uscita.
*   Ogni indirizzo IP fornisce porte 64.000 che è possibile usare per SNAT.
*   Ogni porta può essere utilizzata per le connessioni TCP e UDP a un indirizzo IP di destinazione
  * È necessario specificare una porta UDP SNAT se la porta di destinazione è univoca o meno. Per ogni connessione UDP a un indirizzo IP di destinazione, viene usata una porta SNAT UDP.
  * Una porta TCP SNAT può essere utilizzata per più connessioni allo stesso indirizzo IP di destinazione purché le porte di destinazione siano diverse.
*   L'esaurimento SNAT si verifica quando un'istanza back-end esaurisce le porte SNAT specificate. Un servizio di bilanciamento del carico può ancora avere porte SNAT inutilizzate. Se le porte SNAT utilizzate da un'istanza back-end superano le porte SNAT specificate, non sarà possibile stabilire nuove connessioni in uscita.

## <a name="next-steps"></a>Passaggi successivi

*   [Risolvere gli errori di connessione in uscita a causa dell'esaurimento SNAT](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Esaminare le metriche di SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) e acquisire familiarità con il modo corretto per filtrare, suddividere e visualizzare le metriche.

