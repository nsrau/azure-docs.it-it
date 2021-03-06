---
title: Progettare servizi disponibili a livello globale
description: Informazioni sulla progettazione di applicazioni per servizi a disponibilità elevata con database SQL di Azure.
keywords: ripristino di emergenza cloud, soluzioni di ripristino di emergenza, backup dei dati delle app, replica geografica, pianificazione della continuità aziendale
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321680"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Progettazione di servizi disponibili a livello globale con il database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Quando si compilano e si distribuiscono servizi cloud con il database SQL di Azure, la [replica geografica attiva](active-geo-replication-overview.md) o i [gruppi di failover automatico](auto-failover-group-overview.md) consentono di garantire resilienza in caso di interruzioni a livello di area ed errori irreversibili. La stessa funzionalità consente di creare applicazioni distribuite a livello globale ottimizzate per l'accesso locale ai dati. Questo articolo illustra modelli di applicazione comuni, nonché i vantaggi e gli svantaggi di ogni opzione.

> [!NOTE]
> Se si usano pool elastici e database premium o business critical, è possibile renderli resistenti alle interruzioni a livello di area convertendoli in una configurazione di distribuzione con ridondanza della zona. Vedere [Database con ridondanza della zona](high-availability-sla.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: Uso di due aree di Azure per la continuità aziendale con tempo di inattività minimo

In questo scenario le applicazioni presentano le caratteristiche seguenti:

* L'applicazione è attiva in un'area di Azure
* Tutte le sessioni del database richiedono l'accesso in lettura e scrittura ai dati
* Il livello Web e il livello dati devono essere collocati in modo da ridurre la latenza e il costo del traffico
* Essenzialmente, il tempo di inattività rappresenta per queste applicazioni un rischio aziendale più elevato rispetto alla perdita di dati

In questo caso, la topologia di distribuzione dell'applicazione è ottimizzata per la gestione delle emergenze locali quando è necessario eseguire il failover di tutti i componenti dell'applicazione. Il diagramma seguente illustra questa topologia. Per garantire la ridondanza geografica, le risorse dell'applicazione vengono distribuite nelle aree A e B. Le risorse nell'area B, tuttavia, non vengono utilizzate finché non si verifica un errore nell'area A. Tra le due aree viene configurato un gruppo di failover per gestire la connettività del database, la replica e il failover. Il servizio Web in entrambe le aree è configurato per accedere al database tramite il listener di lettura/scrittura- ** &lt; nome-gruppo-failover &gt; . database.Windows.NET** (1). Gestione traffico di Azure è configurato per l'uso del [metodo di routing prioritario](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md) viene usato in questo articolo solo a scopo illustrativo. È possibile usare qualsiasi soluzione di bilanciamento del carico che supporti il metodo di routing per priorità.

Il diagramma seguente illustra questa configurazione prima di un'interruzione:

![Scenario 1. Configurazione prima dell'interruzione.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Dopo un'interruzione nell'area primaria, il database SQL rileva che il database primario non è accessibile e attiva il failover nell'area secondaria in base ai parametri dei criteri di failover automatico (1). A seconda del contratto di servizio dell'applicazione, è possibile configurare un periodo di tolleranza che controlla l'intervallo di tempo tra il rilevamento dell'interruzione e il failover stesso. È possibile che gestione traffico di Azure avvii il failover dell'endpoint prima che il gruppo di failover attivi il failover del database. In tal caso, l'applicazione Web non può riconnettersi immediatamente al database. Le riconnessioni, tuttavia, avranno automaticamente esito positivo non appena verrà completato il failover del database. Quando l'area in cui si è verificato l'errore è ripristinata e di nuovo online, il database primario precedente si riconnette automaticamente come nuovo database secondario. Il diagramma seguente illustra la configurazione dopo il failover.

> [!NOTE]
> Tutte le transazioni di cui è stato eseguito il commit dopo il failover andranno perse durante la riconnessione. Dopo il completamento del failover, l'applicazione nell'area B può riconnettersi e riavviare l'elaborazione delle richieste utente. Sia l'applicazione Web che il database primario si trovano nell'area B e continuano a condividere lo stesso percorso.

![Scenario 1. Configurazione dopo il failover](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

In caso di interruzione nell'area B, il processo di replica tra il database primario e quello secondario viene sospeso ma il collegamento tra i due resta intatto (1). Gestione traffico rileva che la connettività all'area B è interruppe e contrassegna l'endpoint Web App 2 come danneggiato (2). Le prestazioni dell'applicazione non subiscono alcun impatto in questo caso, ma il database risulta esposto e quindi maggiormente a rischio di perdita di dati in caso di errore in successione nell'area A.

> [!NOTE]
> Per il ripristino di emergenza è consigliabile la configurazione con distribuzione dell'applicazione limitata a due aree. Infatti la maggior parte delle geografie di Azure ha solo due aree. Questa configurazione non protegge l'applicazione da un errore irreversibile simultaneo in entrambe le aree. Nell'improbabile eventualità di un errore di questo genere, è possibile recuperare i database in una terza area con un'[operazione di ripristino geografico](disaster-recovery-guidance.md#recover-using-geo-restore).
>

 Dopo che l'interruzione è stata risolta, il database secondario viene automaticamente risincronizzato con quello primario. Durante la sincronizzazione, le prestazioni del database primario possono risultare peggiorate. L'impatto specifico dipende dalla quantità di dati acquisita dal nuovo database primario dal momento del failover. 

> [!NOTE]
> Dopo che l'interruzione è stata attenuata, gestione traffico avvierà il routing delle connessioni all'applicazione nell'area A come endpoint con priorità più alta. Se si intende tenere il database primario nell'area B per un certo periodo di tempo, è necessario modificare di conseguenza la tabella priorità nel profilo di Trafic Manager. 
>
 
 Il diagramma seguente illustra un'interruzione nell'area secondaria:

![Scenario 1. Configurazione dopo un'interruzione nell'area secondaria.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

I **vantaggi** chiave di questo modello di progettazione sono:

* La stessa applicazione Web viene distribuita in entrambe le aree senza alcuna configurazione specifica dell'area e non richiede logica aggiuntiva per la gestione del failover.
* Il failover non influisce sulle prestazioni dell'applicazione perché l'applicazione Web e il database condividono sempre lo stesso percorso.

Il principale **svantaggio** è rappresentato dal fatto che le risorse dell'applicazione nell'area B sono sottoutilizzate nella maggior parte dei casi.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Aree di Azure per la continuità aziendale con mantenimento massimo dei dati

Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Qualsiasi perdita di dati rappresenta un rischio aziendale elevato. Il failover del database può essere usato solo come ultima soluzione se l'interruzione è causata da un errore irreversibile.
* L'applicazione supporta le modalità di sola lettura e lettura/scrittura delle operazioni e può funzionare in "modalità di sola lettura" per un periodo di tempo.

In questo modello l'applicazione passa alla modalità di sola lettura quando le connessioni di lettura-scrittura iniziano a dare errori di timeout. L'applicazione Web viene distribuita in entrambe le aree e include una connessione all'endpoint del listener di lettura e scrittura e una connessione diversa all'endpoint del listener di sola lettura (1). Il profilo di gestione traffico deve usare il [routing prioritario](../../traffic-manager/traffic-manager-configure-priority-routing-method.md). Il [monitoraggio degli endpoint](../../traffic-manager/traffic-manager-monitoring.md) dovrà essere abilitato per l'endpoint applicazione in ogni area (2).

Il diagramma seguente illustra questa configurazione prima di un'interruzione:

![Scenario 2. Configurazione prima dell'interruzione.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando Traffic Manager rileva un errore di connettività nell'area A, passa automaticamente il traffico utente all'istanza dell'applicazione nell'area B. Con questo modello, è importante impostare il periodo di tolleranza con perdita di dati su un valore sufficientemente elevato, ad esempio 24 ore. In questo modo si impedisce la perdita di dati se l'interruzione del servizio viene risolta entro tale intervallo di tempo. Quando l'applicazione Web nell'area B viene attivata, le operazioni di lettura/scrittura iniziano ad avere esito negativo. A questo punto, passerà alla modalità di sola lettura (1). In questa modalità, le richieste vengono instradate automaticamente al database secondario. Se l'interruzione è causata da un errore irreversibile, molto probabilmente non potrà essere risolta entro il periodo di tolleranza. Alla scadenza, il gruppo di failover attiva il failover. Il listener di lettura/scrittura diventa quindi disponibile e le connessioni non hanno più esito negativo (2). Il diagramma seguente illustra le due fasi del processo di ripristino.

> [!NOTE]
> Se l'interruzione nell'area primaria viene attenuata entro il periodo di tolleranza, gestione traffico rileva il ripristino della connettività nell'area primaria e passa di nuovo il traffico utente all'istanza dell'applicazione nell'area A. L'istanza dell'applicazione riprende e funziona in modalità di lettura/scrittura usando il database primario nell'area A come illustrato nel diagramma precedente.

![Scenario 2. Fasi del ripristino di emergenza.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se si verifica un'interruzione nell'area B, gestione traffico rileva l'errore dell'endpoint Web-App-2 nell'area B e lo contrassegna come danneggiato (1). Nel frattempo, il gruppo di failover trasferisce il listener di sola lettura all'area A (2). Questa interruzione non influisca sull'esperienza dell'utente finale, ma il database primario viene esposto durante l'interruzione. Il diagramma seguente illustra un errore nell'area secondaria:

![Scenario 2. Interruzione nell'area secondaria.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Dopo che l'interruzione del servizio è stata risolta, il database secondario viene immediatamente sincronizzato con quello primario e il listener di sola lettura viene passato nuovamente al database secondario nell'area B. Durante la sincronizzazione, le prestazioni del database primario potrebbero essere leggermente influenzate a seconda della quantità di dati che deve essere sincronizzata.

Questo modello di progettazione presenta diversi **vantaggi**:

* Impedisce la perdita dei dati durante le interruzioni temporanee.
* Il tempo di inattività dipende solo dalla velocità con cui Traffic Manager rileva l'errore di connettività, che è configurabile.

Lo **svantaggio** è rappresentato dal fatto che l'applicazione deve poter funzionare in modalità di sola lettura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: Spostamento dell'applicazione in una diversa area geografica senza perdita di dati e tempo di inattività quasi nullo

In questo scenario, l'applicazione presenta le caratteristiche seguenti:

* Gli utenti finali accedono all'applicazione da diverse aree geografiche
* L'applicazione include carichi di lavoro di sola lettura che non dipendono dalla sincronizzazione completa con gli aggiornamenti più recenti
* L'accesso in scrittura ai dati deve essere supportato nella stessa area geografica per la maggior parte degli utenti
* La latenza di lettura è fondamentale per l'esperienza dell'utente finale

Per soddisfare questi requisiti, è necessario garantire che il dispositivo utente si connetta **sempre** all'applicazione distribuita nella stessa area geografica per le operazioni di sola lettura, ad esempio l'esplorazione dei dati, l'analisi e così via. Mentre le operazioni OLTP vengono elaborate nella stessa area geografica per la **maggior parte del tempo**. Ad esempio, le operazioni OLTP potrebbero essere elaborate nella stessa area geografica in orario diurno e in un'altra area geografica durante gli orari di minore attività. Se l'attività dell'utente finale avviene per lo più durante le ore lavorative, è possibile garantire le prestazioni ottimali per la maggior parte degli utenti nella maggior parte dei casi. Il diagramma seguente illustra questa topologia.

Le risorse dell'applicazione dovranno essere distribuite in ogni area geografica in cui si ha un'elevata esigenza di utilizzo. Se l'applicazione viene usata attivamente in Stati Uniti, Unione Europa e Asia sud-orientale, ad esempio, dovrà essere distribuita in tutte queste aree geografiche. Il database primario dovrà essere trasferito automaticamente da un'area geografica alla successiva al termine dell'orario lavorativo. Questo metodo è detto "follow the sun". Il carico di lavoro OLTP si connette sempre al database tramite il listener di lettura/scrittura ** &lt; nome-gruppo-failover &gt; . database.Windows.NET** (1). Il carico di lavoro di sola lettura si connette direttamente al database locale utilizzando l'endpoint server database ** &lt; nome-server &gt; . database.Windows.NET** (2). Gestione traffico è configurato con il [metodo di routing delle prestazioni](../../traffic-manager/traffic-manager-configure-performance-routing-method.md). Garantisce che il dispositivo dell'utente finale sia connesso al servizio Web nell'area più vicina. Gestione traffico deve essere configurato con il monitoraggio endpoint abilitato per ogni endpoint di servizio Web (3).

> [!NOTE]
> La configurazione del gruppo di failover definisce l'area usata per il failover. Dato che il nuovo database primario si trova in una diversa area geografica, il failover determina una latenza superiore per i carichi di lavoro sia OLTP che di sola lettura finché l'area interessata non è di nuovo online.

![Scenario 3. Configurazione con database primario nell'area Stati Uniti orientali.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Alla fine della giornata, ad esempio alle 23.00 ora locale, i database attivi devono essere passati all'area successiva (Europa settentrionale). Questa attività può essere completamente automatizzata usando [app](../../logic-apps/logic-apps-overview.md)per la logica di Azure. L'attività include i passaggi seguenti:

* Trasferire il server primario del gruppo di failover all'area Europa settentrionale con il failover semplice (1).
* Rimuovere il gruppo di failover tra Stati Uniti orientali ed Europa settentrionale.
* Creare un nuovo gruppo di failover con lo stesso nome ma tra Europa settentrionale e Asia orientale (2).
* Aggiungere il database primario nell'area Europa settentrionale e il database secondario nell'area Asia orientale a questo gruppo di failover (3).

Il diagramma seguente illustra la nuova configurazione dopo il failover pianificato:

![Scenario 3. Transizione del database primario all'area Europa settentrionale.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

In caso di interruzione nell'area Europa settentrionale, ad esempio, il gruppo di failover avvia il failover automatico del database, che di fatto determina lo spostamento dell'applicazione nell'area successiva in anticipo rispetto alla pianificazione (1).  In tal caso, gli Stati Uniti orientali sono l'unica area secondaria rimanente finché l'area Europa settentrionale non è di nuovo online. Le due aree rimanenti gestiscono i clienti in tutte e tre le aree geografiche cambiando i ruoli. Le app per la logica di Azure devono essere modificate di conseguenza. Poiché le aree rimanenti ricevono traffico utente aggiuntivo dall'Europa, le prestazioni dell'applicazione sono influiscate non solo da una latenza aggiuntiva, ma anche da un numero maggiore di connessioni degli utenti finali. Dopo che l'interruzione in Europa settentrionale è stata risolta, il database secondario in tale area viene immediatamente sincronizzato con il database primario corrente. Il diagramma seguente illustra un'interruzione nell'area Europa settentrionale:

![Scenario 3. Interruzione nell'area Europa settentrionale.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> È possibile ridurre il periodo di tempo in cui l'esperienza degli utenti finali in Europa risulta compromessa dalla latenza elevata. A tale scopo, è consigliabile distribuire in modo proattivo una copia dell'applicazione e creare i database secondari in un'altra area locale (Europa occidentale) per la sostituzione dell'istanza dell'applicazione offline in Europa settentrionale. Quando quest'ultima è di nuovo online, si può decidere di continuare a usare l'area Europa occidentale oppure rimuovere tale copia dell'applicazione e tornare a usare l'area Europa settentrionale.

I principali **vantaggi** di questa progettazione sono i seguenti:

* Il carico di lavoro di sola lettura dell'applicazione accede sempre ai dati nell'area più vicina.
* Il carico di lavoro di lettura/scrittura dell'applicazione accede ai dati nell'area più vicina durante il periodo di maggiore attività in ogni area geografica.
* Dato che l'applicazione viene distribuita in più aree, può superare la perdita di una delle aree senza tempi di inattività significativi.

Esistono tuttavia alcuni **svantaggi**:

* Un'interruzione a livello di area determina una latenza superiore nell'area geografica. I carichi di lavoro di lettura/scrittura e di sola lettura vengono serviti dall'applicazione in un'area geografica diversa.
* I carichi di lavoro di sola lettura devono connettersi a un diverso endpoint in ogni area.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Pianificazione della continuità aziendale: Scegliere una progettazione di applicazioni per il ripristino di emergenza cloud

La strategia di ripristino di emergenza cloud specifica può combinare o estendere questi modelli di progettazione per soddisfare al meglio le esigenze dell'applicazione.  Come accennato in precedenza, la strategia scelta si basa sul contratto di servizio che si vuole offrire ai clienti e sulla topologia di distribuzione dell'applicazione. Per facilitare la decisione, la tabella seguente confronta le opzioni in base all'obiettivo del punto di ripristino (RPO) e al tempo di recupero stimato (ERT).

| Modello | RPO | ERT |
|:--- |:--- |:--- |
| Distribuzione attiva/passiva per il ripristino di emergenza con accesso al database con percorso condiviso |Accesso in lettura/scrittura < 5 sec |Ora di rilevamento dell'errore + DNS TTL |
| Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione |Accesso in lettura/scrittura < 5 sec |Ora di rilevamento dell'errore + DNS TTL |
| Distribuzione attiva/passiva per la conservazione dei dati |Accesso in sola lettura < 5 sec | Accesso in sola lettura = 0 |
||Accesso in lettura/scrittura = zero | Accesso in lettura-scrittura = ora di rilevamento dell'errore + periodo di tolleranza con perdita di dati |
|||

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica e scenari di continuità aziendale, vedere [Panoramica della continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Per informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](active-geo-replication-overview.md).
* Per informazioni sui gruppi di failover automatico, vedere [gruppi di failover automatico](auto-failover-group-overview.md).
* Per informazioni sulla replica geografica attiva con i pool elastici, vedere strategie di ripristino di emergenza per i [pool elastici](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
