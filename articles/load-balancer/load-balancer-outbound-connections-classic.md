---
title: Connessioni in uscita in Azure (versione classica) | Microsoft Docs
description: Questo articolo spiega in che modo Azure consente ai servizi cloud di comunicare con i servizi Internet pubblici.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.openlocfilehash: 7a307a598bd71369615b30476d387c06f473c397
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="outbound-connections-classic"></a>Connessioni in uscita (versione classica)

Azure offre connettività in uscita per le distribuzioni dei clienti tramite vari meccanismi diversi. Questo articolo descrive che cosa sono gli scenari, quando si applicano, come funzionano e come gestirli.

>[!NOTE]
>In questo articolo vengono illustrate solo le distribuzioni classiche.  Vedere [Connessioni in uscita](load-balancer-outbound-connections.md) per tutti gli scenari di distribuzione di Resource Manager in Azure.

Una distribuzione in Azure può comunicare con endpoint all'esterno di Azure nello spazio indirizzi IP pubblici. Quando un'istanza avvia un flusso in uscita verso una destinazione nello spazio indirizzi IP pubblici, Azure esegue in modo dinamico il mapping dell'indirizzo IP privato a un indirizzo IP pubblico. Dopo aver creato questo mapping, il traffico di ritorno per questo flusso con origine in uscita può raggiungere l'indirizzo IP privato da cui ha avuto origine il flusso.

Azure usa SNAT (Source Network Address Translation) per eseguire questa funzione. Quando più indirizzi IP privati si mascherano dietro un singolo indirizzo IP pubblico, Azure usa [PAT (Port Address Translation)](#pat) per il mascheramento degli indirizzi IP privati. Per PAT vengono usate porte temporanee [preallocate](#preallocatedports) in base alle dimensioni del pool.

Sono presenti più [scenari in uscita](#scenarios). È possibile combinare questi scenari in base alle esigenze. Esaminarli con attenzione per comprenderne le funzionalità, i vincoli e gli schemi, in riferimento a modelli di distribuzione e scenari di applicazione specifici. Vedere le linee guida per la [gestione di questi scenari](#snatexhaust).

## <a name="scenarios"></a>Panoramica dello scenario

Azure offre tre metodi diversi per ottenere distribuzioni classiche con connettività in uscita.  Non tutte le distribuzioni classiche hanno la disponibilità di tutti e tre gli scenari:

| Scenario | Metodo | DESCRIZIONE | Ruolo di lavoro Web | IaaS | 
| --- | --- | --- | --- | --- |
| [1. Macchina virtuale con un indirizzo IP pubblico a livello di istanza](#ilpip) | SNAT, il mascheramento delle porte non viene usato |Azure usa la macchina virtuale con l'IP pubblico assegnato. L'istanza ha tutte le porte temporanee disponibili. | No  | Sì |
| [2. Endpoint pubblico con carico bilanciato](#publiclbendpoint) | SNAT con mascheramento delle porte (PAT) per l'endpoint pubblico |Azure condivide l'endpoint pubblico con indirizzo IP pubblico con più endpoint privati. Azure usa le porte temporanee dell'endpoint pubblico per il mascheramento delle porte. | Sì | Sì |
| [3. Macchina virtuale autonoma ](#defaultsnat) | SNAT con mascheramento delle porte (PAT) | Azure designa automaticamente un indirizzo IP pubblico per SNAT, condivide questo indirizzo IP pubblico con l'intera distribuzione e usa le porte temporanee dell'indirizzo IP dell'endpoint pubblico per il mascheramento delle porte. Si tratta di uno scenario di fallback per gli scenari precedenti. Non è consigliato se sono necessari visibilità e controllo. | Sì | Sì|

Si tratta di un subset di funzionalità di connessione in uscita disponibile per le distribuzioni di Resource Manager in Azure.  

Distribuzioni diverse nella versione classica hanno funzionalità differenti:

| Distribuzione classica | Funzionalità disponibili | 
| --- | --- |
| Macchina virtuale | scenario [1](#ilpip), [2](#publiclbendpoint) o [3](#defaultsnat) |
| Ruolo di lavoro Web | solo scenario [2](#publiclbendpoint), [3](#defaultsnat) | 

Anche per le [strategie di mitigazione](#snatexhaust) esistono le stesse differenze.

L'[algoritmo usato per la preallocazione delle porte temporanee](#ephemeralports) per il mascheramento delle porte (PAT) per le distribuzioni classiche è lo stesso usato per le distribuzioni di risorse di Azure Resource Manager.

### <a name="ilpip"></a>Scenario 1: macchina virtuale con un indirizzo IP pubblico a livello di istanza

In questo scenario la macchina virtuale ha assegnato un IP pubblico a livello di istanza. Per quanto riguarda le connessioni in uscita, non è importante se la macchina virtuale abbia un endpoint con carico bilanciato o meno. Questo scenario ha la precedenza rispetto agli altri. Quando si usa un indirizzo IP pubblico a livello di istanza, la macchina virtuale usa tale indirizzo IP per tutti i flussi in uscita.  

Il mascheramento delle porte (PAT) non viene usato e tutte le porte temporanee sono disponibili per l'uso per la macchina virtuale.

Se l'applicazione avvia numerosi flussi in uscita e si assiste a un esaurimento delle porte SNAT, prendere in considerazione l'assegnazione di un [indirizzo IP pubblico a livello di istanza per ridurre i vincoli SNAT](#assignilpip). Leggere per intero [Gestione dell'esaurimento SNAT](#snatexhaust).

### <a name="publiclbendpoint"></a>Scenario 2: endpoint pubblico con carico bilanciato

In questo scenario la macchina virtuale o il ruolo di lavoro Web è associato a un indirizzo IP pubblico tramite l'endpoint con carico bilanciato. Alla macchina virtuale non è assegnato un indirizzo IP pubblico. 

Quando la macchina virtuale con carico bilanciato crea un flusso in uscita, Azure converte l'indirizzo IP di origine privato del flusso in uscita nell'indirizzo IP pubblico dell'endpoint pubblico con carico bilanciato. Azure usa SNAT per eseguire questa funzione. Azure usa anche [PAT](#pat) per mascherare più indirizzi IP privati dietro un indirizzo IP pubblico. 

Per distinguere i singoli flussi provenienti dalla macchina virtuale vengono usate le porte temporanee del front-end dell'indirizzo IP pubblico di Load Balancer. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT.

Le porte SNAT vengono preallocate come descritto nella sezione [Informazioni su SNAT e PAT](#snat). Sono una risorsa limitata che può esaurirsi. L'importante è capire come vengono [usate](#pat). Per informazioni su come progettare questo consumo e come attenuarlo in base alle necessità, vedere [Gestione dell'esaurimento SNAT](#snatexhaust).

Se esistono [più endpoint pubblici con carico bilanciato](load-balancer-multivip.md), uno qualsiasi di questi indirizzi IP pubblici è un [candidato per i flussi in uscita](#multivipsnat) e ne viene selezionato uno in modo casuale.  

### <a name="defaultsnat"></a>Scenario 3: nessun indirizzo IP pubblico associato

In questo scenario la macchina virtuale o il ruolo di lavoro Web non fa parte di un endpoint pubblico con carico bilanciato.  Nel caso specifico di una macchina virtuale, non dispone di un indirizzo ILPIP assegnato. Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita in un indirizzo IP di origine pubblica. L'indirizzo IP pubblico usato per questo flusso in uscita non è configurabile e non interferisce con il limite della risorsa IP pubblico della sottoscrizione.  Azure alloca automaticamente questo indirizzo.

Azure usa SNAT con il mascheramento delle porte ([PAT](#pat)) per eseguire questa funzione. Questo scenario è simile allo [scenario 2](#lb), ma non è disponibile alcun controllo sull'indirizzo IP usato. Si tratta di uno scenario di fallback per quando non esistono gli scenari 1 e 2. Questo scenario non è consigliato se si vuole avere il controllo dell'indirizzo in uscita. Se le connessioni in uscita sono una parte essenziale dell'applicazione, è consigliabile scegliere un altro scenario.

Le porte SNAT vengono preallocate come descritto nella sezione [Informazioni su SNAT e PAT](#snat).  Il numero di macchine virtuali o di ruoli di lavoro Web che condividono l'indirizzo IP pubblico determina il numero di porte temporanee preallocate.   L'importante è capire come vengono [usate](#pat). Per informazioni su come progettare questo consumo e come attenuarlo in base alle necessità, vedere [Gestione dell'esaurimento SNAT](#snatexhaust).

## <a name="snat"></a>Informazioni su SNAT e PAT

### <a name="pat"></a>Mascheramento delle porte SNAT (PAT)

Quando una distribuzione stabilisce una connessione in uscita, ogni origine della connessione in uscita viene riscritta. L'origine viene riscritta dallo spazio indirizzi IP privato all'IP pubblico associato alla distribuzione (basata sugli scenari descritti in precedenza). Nello spazio degli indirizzi IP pubblici, le 5 tuple del flusso (indirizzo IP di origine, porta di origine, protocollo di trasporto IP, indirizzo IP di destinazione, porta di destinazione) devono essere univoche.  

Per ottenere ciò vengono usate porte temporanee (porte SNAT) dopo la riscrittura dell'indirizzo IP di origine privato, perché più flussi hanno origine da un singolo indirizzo IP pubblico. 

Viene utilizzata una singola porta SNAT per flusso verso una singola combinazione di indirizzo IP, porta e protocollo di destinazione. Nel caso di più flussi verso la stessa combinazione di indirizzo IP, porta e protocollo di destinazione, ogni flusso utilizza una singola porta SNAT. Si garantisce così che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e quando sono destinati allo stesso indirizzo IP, alla stessa porta e allo stesso protocollo di destinazione. 

Più flussi destinati ognuno a un indirizzo IP, una porta e un protocollo diversi condividono una sola porta SNAT. L'indirizzo IP, la porta e il protocollo di destinazione rendono univoci i flussi senza la necessità di porte di origine aggiuntive per distinguere i flussi nello spazio indirizzi IP pubblici.

Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che i flussi esistenti rilasciano le porte SNAT. Il servizio Load Balancer recupera le porte SNAT alla chiusura del flusso e usa un [timeout per inattività di 4 minuti](#idletimeout) per il recupero delle porte SNAT dai flussi inattivi.

Per informazioni sui modelli per la mitigazione delle condizioni che portano comunemente all'esaurimento delle porte SNAT, vedere la sezione [Gestione di SNAT](#snatexhaust).

### <a name="preallocatedports"></a>Preallocazione di porte temporanee per il mascheramento delle porte SNAT (PAT)

Azure usa un algoritmo per determinare il numero di porte SNAT preallocate disponibili in base alla dimensione del pool back-end quando si usa il mascheramento delle porte SNAT ([PAT](#pat)). Le porte SNAT sono porte temporanee disponibili per un particolare indirizzo di origine IP pubblico.

Azure prealloca le porte SNAT quando viene distribuita un'istanza in base a quante istanze di macchina virtuale o ruolo di lavoro Web condividono un determinato indirizzo IP pubblico.  Quando vengono creati i flussi in uscita, [PAT](#pat) usa (fino al limite preallocato) e rilascia in modo dinamico tali porte alla chiusura del flusso o quando si verifica un [timeout per inattività](#ideltimeout).

Nella tabella seguente sono riportate le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Istanze | Porte SNAT preallocate per istanza |
| --- | --- |
| 1-50 | 1.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 |

Ricordare che il numero di porte SNAT disponibili non viene convertito direttamente in un numero di flussi. È possibile riutilizzare una singola porta SNAT per più destinazioni univoche. Le porte vengono usate solo se è necessario per rendere univoci i flussi. Per indicazioni sulla progettazione e la mitigazione, vedere la sezione su [come gestire questa risorsa soggetta a esaurimento](#snatexhaust) e la sezione che descrive [PAT](#pat).

La modifica delle dimensioni della distribuzione potrebbe influire su alcuni dei flussi stabiliti. Se le dimensioni del pool back-end aumentano e si esegue la transizione al livello successivo, metà delle porte SNAT preallocate vengono recuperate durante la transizione al livello di pool back-end più grande successivo. I flussi associati a una porta SNAT recuperata raggiungeranno il timeout e dovranno essere ristabiliti. Se viene tentato un nuovo flusso, il flusso avrà esito positivo immediatamente, purché siano disponibili porte preallocate.

Se le dimensioni della distribuzione diminuiscono e si ha una transizione a un livello più basso, il numero di porte SNAT disponibili aumenta. In questo caso, non vi sono effetti per le porte SNAT allocate esistenti e i rispettivi flussi.

## <a name="problemsolving"></a> Risoluzione dei problemi 

Questa sezione è stata pensata per attenuare i problemi relativi all'esaurimento SNAT e ad altri scenari che possono verificarsi con le connessioni in uscita in Azure.

### <a name="snatexhaust"></a> Gestione dell'esaurimento delle porte SNAT (PAT)
Le [porte temporanee](#preallocatedports) usate per [PAT](#pat) sono una risorsa esauribile, come descritto in [nessun indirizzo IP pubblico associato](#defaultsnat) ed [endpoint pubblico con carico bilanciato](#publiclbendpoint).

Sono disponibili diverse opzioni di mitigazione generali, se si è certi che verranno avviate numerose connessioni in uscita TCP o UDP allo stesso indirizzo IP e alla stessa porta di destinazione e se si riscontrano connessioni in uscita con errore o se il supporto tecnico ha comunicato il possibile esaurimento delle porte SNAT ([porte temporanee](#preallocatedports) preallocate usate da [PAT](#pat)). Esaminare queste opzioni e scegliere quella disponibile e ottimale per lo scenario specifico. È possibile che una o più opzioni risultino utili per gestire questo scenario.

In caso di problemi a comprendere il comportamento delle connessioni in uscita, è possibile usare le statistiche dello stack IP (netstat) o può essere utile osservare i comportamenti di connessione tramite le acquisizioni di pacchetti.

#### <a name="connectionreuse"></a>Modificare l'applicazione per riutilizzare le connessioni 
È possibile ridurre la richiesta di porte temporanee usate per SNAT usando di nuovo le connessioni nell'applicazione. Ciò vale soprattutto per i protocolli HTTP/1.1, in cui il riutilizzo della connessione è automatico. Altri protocolli che usano HTTP come trasporto, ad esempio REST, possono trarne vantaggio a loro volta. 

Il riutilizzo è sempre meglio delle singole connessioni TCP atomiche per ogni richiesta. Il riutilizzo garantisce transazioni TCP molto efficienti e con prestazioni più elevate.

#### <a name="connection pooling"></a>Modificare l'applicazione per usare pool di connessioni
Nella connessione è possibile usare una uno schema di pool della connessione, in cui le richieste sono distribuite internamente in un set predefinito di connessioni, ognuna delle quali viene riutilizzata quando possibile. Questo schema limita il numero di porte temporanee in uso e crea un ambiente più prevedibile. Può anche migliorare la velocità effettiva delle richieste, consentendo più operazioni simultanee, quando una connessione si blocca sulla risposta di un'operazione.  

Il pool delle connessioni potrebbe essere già presente nell'ambito usato per sviluppare l'applicazione o le impostazioni di configurazione dell'applicazione. È possibile combinare il pool di connessioni con il riutilizzo delle connessioni. Le richieste multiple impiegano quindi un numero prevedibile fisso di porte per la stessa porta e lo stesso indirizzo IP di destinazione. Le richieste traggono anche vantaggio dall'uso efficiente delle transazioni TCP che riducono la latenza e l'utilizzo delle risorse. Anche le transazioni UDP possono offrire vantaggi, perché gestire il numero di flussi UDP può a sua volta evitare condizioni di esaurimento e consente di gestire l'utilizzo delle porte SNAT.

#### <a name="retry logic"></a>Modificare l'applicazione per usare una logica di ripetizione meno aggressiva
Quando vengono esaurite le [porte temporanee preallocate](#preallocatedports) usate per [PAT](#pat) o si verificano errori nell'applicazione, i tentativi di ripetizione aggressivi o di forza bruta senza logica di backoff e decadimento causano il verificarsi o la persistenza dell'esaurimento. È possibile ridurre la richiesta di porte temporanee usando una logica di ripetizione meno aggressiva. 

Le porte temporanee hanno un timeout di inattività di 4 minuti (non modificabile). Se i tentativi di ripetizione sono troppo aggressivi, l'esaurimento delle porte non viene eliminato autonomamente. La modalità e la frequenza con cui l'applicazione effettua nuovi tentativi di transazioni sono quindi un fattore fondamentale della progettazione.

#### <a name="assignilpip"></a>Assegnare un indirizzo IP pubblico a livello di istanza a ogni macchina virtuale
Con l'assegnazione di un indirizzo IP pubblico a livello di istanza lo scenario cambia in [Indirizzo IP pubblico a livello di istanza a una macchina virtuale](#ilpip). Tutte le porte temporanee dell'IP pubblico usate per ogni VM sono disponibili per la VM, al contrario di quanto accade negli scenari in cui le porte temporanee di un IP pubblico sono condivise con tutte le VM associate alla rispettiva distribuzione. Bisogna considerare i compromessi, ad esempio l'impatto potenziale di creare un elenco con un numero elevato di singoli indirizzi IP consentiti.

>[!NOTE] 
>Questa opzione non è disponibile per i ruoli di lavoro Web.

### <a name="idletimeout"></a>Usare keep-alive per reimpostare il timeout di inattività per le connessioni uscita

Il timeout di inattività delle connessioni in uscita è di 4 minuti. Questo timeout non è modificabile. È comunque possibile usare keep-alive del livello trasporto (ad esempio, keep-alive TCP) o del livello applicazione per aggiornare un flusso inattivo e reimpostare il timeout di inattività, se necessario.  Rivolgersi al fornitore del pacchetto software per sapere se questa funzionalità è supportata o come abilitarla.  In genere, solo un lato necessita di generare keep-alive per reimpostare il timeout di inattività. 

## <a name="discoveroutbound"></a>Individuazione dell'IP pubblico usato da una VM
Esistono molti modi per determinare l'indirizzo IP di origine pubblica di una connessione in uscita. OpenDNS offre un servizio che consente di visualizzare l'indirizzo IP pubblico della macchina virtuale. 

Usando il comando nslookup è possibile inviare una query DNS per il nome myip.opendns.com al resolver OpenDNS. Il servizio restituisce l'indirizzo IP di origine usato per inviare la query. Quando si esegue la query seguente dalla macchina virtuale, la risposta è l'indirizzo IP pubblico usato per tale VM:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer](load-balancer-overview.md) usato nelle distribuzioni di Resource Manager.
