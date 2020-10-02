---
title: Risolvere i problemi delle connessioni in uscita in Azure Load Balancer
description: Soluzioni per problemi comuni con la connettività in uscita tramite la Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: c37c0e9b914854ff41053526740d3454c5c23f90
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628996"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Risoluzione degli errori delle connessioni in uscita

Questo articolo ha lo scopo di fornire risoluzioni per i problemi comuni che possono verificarsi con le connessioni in uscita da un Azure Load Balancer. La maggior parte dei problemi con la connettività in uscita che i clienti riscontrano sono dovuti all'esaurimento delle porte SNAT e ai timeout di connessione che portano a pacchetti eliminati. Questo articolo illustra la procedura per attenuare ognuno di questi problemi.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Gestione dell'esaurimento delle porte SNAT (PAT)
Le [porte](load-balancer-outbound-connections.md) temporanee usate per [Pat](load-balancer-outbound-connections.md) sono una risorsa esauribile, come descritto in [macchina virtuale autonoma senza un indirizzo IP pubblico](load-balancer-outbound-connections.md) e una [macchina virtuale con carico bilanciato senza indirizzo IP pubblico](load-balancer-outbound-connections.md). È possibile monitorare l'utilizzo delle porte temporanee e confrontarsi con l'allocazione corrente per determinare il rischio di o per confermare l'esaurimento SNAT con [questa](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) guida.

Sono disponibili diverse opzioni di mitigazione generali, se si è certi che verranno avviate numerose connessioni in uscita TCP o UDP allo stesso indirizzo IP e alla stessa porta di destinazione e se si riscontrano connessioni in uscita con errore o se il supporto tecnico ha comunicato il possibile esaurimento delle porte SNAT ([porte temporanee](load-balancer-outbound-connections.md#preallocatedports) preallocate usate da [PAT](load-balancer-outbound-connections.md)). Esaminare queste opzioni e scegliere quella disponibile e ottimale per lo scenario specifico. È possibile che una o più opzioni risultino utili per gestire questo scenario.

In caso di problemi a comprendere il comportamento delle connessioni in uscita, è possibile usare le statistiche dello stack IP (netstat) o può essere utile osservare i comportamenti di connessione tramite le acquisizioni di pacchetti. È possibile eseguire queste acquisizioni di pacchetti nel sistema operativo guest dell'istanza o usare [Network Watcher per l'acquisizione dei pacchetti](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Allocare manualmente le porte SNAT per ottimizzare le porte SNAT per macchina virtuale
Come definito in [porte preallocate](load-balancer-outbound-connections.md#preallocatedports), il servizio di bilanciamento del carico alloca automaticamente le porte in base al numero di macchine virtuali nel back-end. Per impostazione predefinita, questa operazione viene eseguita in modo conservativo per garantire la scalabilità. Se si conosce il numero massimo di macchine virtuali disponibili nel back-end, è possibile allocare manualmente le porte SNAT in ogni regola in uscita. Ad esempio, se si è certi di disporre di un massimo di 10 macchine virtuali, è possibile allocare 6.400 porte SNAT per macchina virtuale anziché l'impostazione predefinita 1.024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modificare l'applicazione per riutilizzare le connessioni 
È possibile ridurre la richiesta di porte temporanee usate per SNAT usando di nuovo le connessioni nell'applicazione. Il riutilizzo delle connessioni è particolarmente rilevante per i protocolli come HTTP/1.1, in cui il riutilizzo della connessione è il valore predefinito. Altri protocolli che usano HTTP come trasporto, ad esempio REST, possono trarne vantaggio a loro volta. 

Il riutilizzo è sempre meglio delle singole connessioni TCP atomiche per ogni richiesta. Il riutilizzo garantisce transazioni TCP molto efficienti e con prestazioni più elevate.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modificare l'applicazione per usare pool di connessioni
Nella connessione è possibile usare una uno schema di pool della connessione, in cui le richieste sono distribuite internamente in un set predefinito di connessioni, ognuna delle quali viene riutilizzata quando possibile. Questo schema limita il numero di porte temporanee in uso e crea un ambiente più prevedibile. Può anche migliorare la velocità effettiva delle richieste, consentendo più operazioni simultanee, quando una connessione si blocca sulla risposta di un'operazione.  

Il pool delle connessioni potrebbe essere già presente nell'ambito usato per sviluppare l'applicazione o le impostazioni di configurazione dell'applicazione. È possibile combinare il pool di connessioni con il riutilizzo delle connessioni. Le richieste multiple impiegano quindi un numero prevedibile fisso di porte per la stessa porta e lo stesso indirizzo IP di destinazione. Le richieste traggono anche vantaggio dall'uso efficiente delle transazioni TCP che riducono la latenza e l'utilizzo delle risorse. Anche le transazioni UDP possono offrire vantaggi, perché gestire il numero di flussi UDP può a sua volta evitare condizioni di esaurimento e consente di gestire l'utilizzo delle porte SNAT.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modificare l'applicazione per usare una logica di ripetizione meno aggressiva
Quando vengono esaurite le [porte temporanee preallocate](load-balancer-outbound-connections.md#preallocatedports) usate per [PAT](load-balancer-outbound-connections.md) o si verificano errori nell'applicazione, i tentativi di ripetizione aggressivi o di forza bruta senza logica di backoff e decadimento causano il verificarsi o la persistenza dell'esaurimento. È possibile ridurre la richiesta di porte temporanee usando una logica di ripetizione meno aggressiva. 

Le porte temporanee hanno un timeout di inattività di 4 minuti (non modificabile). Se i tentativi di ripetizione sono troppo aggressivi, l'esaurimento delle porte non viene eliminato autonomamente. La modalità e la frequenza con cui l'applicazione effettua nuovi tentativi di transazioni sono quindi un fattore fondamentale della progettazione.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Assegnare un indirizzo IP pubblico a ogni macchina virtuale
L'assegnazione di un indirizzo IP pubblico modifica lo scenario da [IP pubblico a una macchina virtuale](load-balancer-outbound-connections.md). Tutte le porte temporanee dell'IP pubblico usate per ogni VM sono disponibili per la VM, In contrapposizione agli scenari in cui le porte temporanee di un IP pubblico sono condivise con tutte le VM associate al rispettivo pool back-end. Ci sono compromessi da considerare, ad esempio il costo aggiuntivo degli indirizzi IP pubblici e il potenziale impatto del filtraggio di un numero elevato di singoli indirizzi IP.

>[!NOTE] 
>Questa opzione non è disponibile per i ruoli di lavoro Web.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Usare più front-end
Quando si usa un'istanza di Load Balancer Standard pubblica, si assegnano [più indirizzi IP front-end per le connessioni in uscita](load-balancer-outbound-connections.md) e [si moltiplica il numero di porte SNAT disponibili](load-balancer-outbound-connections.md#preallocatedports).  Creare una configurazione IP front-end, una regola e un pool back-end per attivare la programmazione di SNAT per l'IP pubblico del front-end.  La regola non deve necessariamente funzionare e un probe di integrità non deve necessariamente avere esito positivo.  Se si usano più front-end anche per le connessioni in entrata (anziché solo per quelle in uscita), è consigliabile usare probe di integrità personalizzati per garantire l'affidabilità.

>[!NOTE]
>Nella maggior parte dei casi l'esaurimento delle porte SNAT indica una progettazione non valida.  Assicurarsi di conoscere il motivo dell'esaurimento delle porte prima di usare più front-end per aggiungere porte SNAT.  Potrebbe essere indice di un problema che può causare errori in un secondo momento.

## <a name="scale-out"></a><a name="scaleout"></a>Scalabilità orizzontale
Le [porte preallocate](load-balancer-outbound-connections.md#preallocatedports) vengono assegnate in base alle dimensioni del pool back-end e raggruppate in livelli per ridurre al minimo le interruzioni quando alcune delle porte devono essere riallocate in base al successivo livello di dimensioni maggiore del pool back-end.  È possibile scegliere di aumentare l'utilizzo delle porte SNAT per un determinato front-end ridimensionando il pool back-end alla dimensione massima per un determinato livello.  Tenere presente che l'allocazione delle porte predefinita è necessaria per la scalabilità orizzontale dell'applicazione senza rischi di esaurimento SNAT.

Ad esempio, due macchine virtuali nel pool back-end devono avere 1024 porte SNAT disponibili per ogni configurazione IP, per un totale di 2048 porte SNAT per la distribuzione.  Se la distribuzione viene aumentata a 50 macchine virtuali, anche se il numero di porte preallocate resta costante per ogni macchina virtuale, potrà essere usato un totale di 51.200 (50 x 1024) porte SNAT dalla distribuzione.  Se si vuole scalare in orizzontale la distribuzione, controllare il numero di [porte preallocate](load-balancer-outbound-connections.md#preallocatedports) per ogni livello per assicurarsi di definire la scalabilità orizzontale per il rispettivo livello massimo.  Se nell'esempio precedente si sceglie di aumentare il numero di istanze fino a 51 anziché a 50, si passa al livello successivo, finendo con meno porte SNAT per ogni macchina virtuale e in totale.

Se si aumenta il numero di istanze fino al successivo livello più alto delle dimensioni del pool back-end, è possibile che si verifichi il timeout di alcune connessioni in uscita se le porte allocate devono essere riallocate.  Se si usano solo alcune delle porte SNAT, la scalabilità orizzontale fino al successivo livello più alto delle dimensioni del pool back-end non è rilevante.  Metà delle porte esistenti verranno riallocate ogni volta che si passerà al successivo livello del pool back-end.  Se non si vuole che questo avvenga, è necessario definire la distribuzione in base alle dimensioni del livello.  In alternativa, assicurarsi che l'applicazione possa eseguire il rilevamento delle porte e nuovi tentativi in base alle esigenze.  I keep-alive TCP possono aiutare a rilevare i casi in cui le porte SNAT non funzionano più a causa della riallocazione.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Usare keep-alive per reimpostare il timeout di inattività per le connessioni uscita
Il timeout di inattività delle connessioni in uscita è di 4 minuti. Questo timeout è regolabile tramite [Regole in uscita](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout). È anche possibile usare keep-alive del livello trasporto (ad esempio, keep-alive TCP) o del livello applicazione per aggiornare un flusso inattivo e reimpostare il timeout di inattività, se necessario.  

Quando si usano keep-alive TCP, è sufficiente abilitarli sul lato della connessione. Ad esempio, è sufficiente abilitarli sul lato server solo per reimpostare il timer di inattività del flusso, mentre questo non è necessario per entrambi i lati per i keep-alive TCP avviati.  Si applicano concetti simili anche per il livello dell'applicazione, tra cui le configurazioni client-server di database.  Controllare il lato server per individuare le opzioni disponibili per i Keep-Alive specifici dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Stiamo sempre cercando di migliorare l'esperienza dei nostri clienti. Se si verificano problemi con la connettività in uscita non elencata o risolta in questo articolo, inviare commenti e suggerimenti tramite GitHub nella parte inferiore di questa pagina per risolvere i commenti e suggerimenti appena possibile.
