---
title: Soluzione Wire Data in Log Analytics | Microsoft Docs
description: I dati in transito sono dati consolidati di rete e sulle prestazioni provenienti da computer con agenti OMS, inclusi Operations Manager e gli agenti connessi a Windows. I dati di rete vengono combinati con i dati dei log per poter correlare i dati.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: banders

---
# <a name="wire-data-solution-in-log-analytics"></a>Soluzione Wire Data in Log Analytics
I dati in transito sono dati consolidati di rete e sulle prestazioni provenienti da computer con agenti OMS, inclusi Operations Manager e gli agenti connessi a Windows. I dati di rete vengono combinati con i dati dei log per poter correlare i dati. Gli agenti OMS installati nei computer dell’infrastruttura IT monitorano i dati di rete inviati verso e da tali computer per i livelli di rete 2 e 3 nel [modello OSI](https://en.wikipedia.org/wiki/OSI_model) , inclusi i diversi protocolli e porte usati.

> [!NOTE]
> Attualmente, la soluzione Wire Data non può essere aggiunta ad aree di lavoro, I clienti che hanno già abilitato la soluzione Wire Data possono continuare a utilizzarla.
> 
> 

Per impostazione predefinita, OMS raccoglie dati registrati sulle prestazioni di CPU, memoria, dischi e rete dai contatori predefiniti di Windows. La raccolta dei dati di rete e di altro tipo viene eseguita in tempo reale per ogni agente, inclusi subnet e protocolli a livello di applicazione usati dal computer. È possibile aggiungere altri contatori delle prestazioni nella pagina Settings della scheda Logs.

Se si è usato [sFlow](http://www.sflow.org/) o un altro software con il [protocollo NetFlow di Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), le statistiche e i dati visualizzati dai dati in transito risulteranno familiari.

Alcuni tipi di query di ricerca nei log predefinite includono:

* Agenti che forniscono dati in transito
* Indirizzo IP degli agenti che forniscono dati in transito
* Comunicazioni in uscita da parte degli indirizzi IP
* Numero di byte inviati dai protocolli applicativi
* Numero di byte inviati da un servizio dell’applicazione
* Byte ricevuti da protocolli diversi
* Byte totali inviati e ricevuti dall’IP
* Indirizzi IP che hanno comunicato con agenti nella subnet 10.0.0.0/8
* Latenza media per le connessioni misurate in modo affidabile
* Processi dei computer che hanno avviato o ricevuto traffico di rete
* Quantità di traffico di rete per un processo

Quando si esegue una ricerca usando i dati in transito, è possibile filtrare e raggruppare i dati per visualizzare le informazioni su agenti e protocolli principali. Oppure è possibile esaminare quando determinati computer (indirizzi IP/indirizzi MAC) hanno comunicato tra loro, per quanto tempo e quanti dati sono stati inviati. In pratica, si visualizzano i metadati sul traffico di rete, basati sulla ricerca.

Tuttavia, poiché si visualizzano i metadati, non sono necessariamente utili per una risoluzione dei problemi approfondita. I data in transito in OMS non offrono una panoramica completa dei dati di rete, quindi non sono destinati a una risoluzione dei problemi approfondita a livello di pacchetto.
Il vantaggio di usare l’agente, rispetto ad altri metodi di raccolta, è che non è necessario installare appliance, riconfigurare i commutatori di rete o eseguire complesse configurazioni. I dati in transito si basano semplicemente sull’agente, che viene installato in un computer e monitorerà il proprio traffico di rete. Un altro vantaggio è evidente quando si vuole monitorare i carichi di lavoro in esecuzione nei provider di servizi cloud o che ospitano provider di servizi o Microsoft Azure, dove l’utente non è proprietario del livello infrastruttura.

Non si ha invece la visibilità completa su ciò che avviene in rete se non si installano gli agenti in tutti i computer dell’infrastruttura di rete.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* La soluzione Wire Data acquisisce i dati dai computer che eseguono Windows Server 2012 R2, Windows 8.1 e versioni successive.
* Nei computer da cui si desidera acquisire i dati in transito è necessario che sia installato Microsoft .NET Framework 4.0 o versione successiva.
* Aggiungere la soluzione Wire Data all'area di lavoro di OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.
* Se si desidera visualizzare i dati in transito per una soluzione specifica, è necessario disporre della soluzione già aggiunta all'area di lavoro di OMS.

## <a name="wire-data-data-collection-details"></a>Dettagli sulla raccolta dei dati per i dati in transito
Wire Data raccoglie i metadati sul traffico di rete tramite gli agenti abilitati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per i dati in transito.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows (2012 R2/8.1 o versioni successive) |![Sì](./media/log-analytics-wire-data/oms-bullet-green.png) |![Sì](./media/log-analytics-wire-data/oms-bullet-green.png) |![No](./media/log-analytics-wire-data/oms-bullet-red.png) |![No](./media/log-analytics-wire-data/oms-bullet-red.png) |![No](./media/log-analytics-wire-data/oms-bullet-red.png) |Ogni minuto |

## <a name="combining-wire-data-with-other-solution-data"></a>Combinazione dei dati in transito con i dati di altre soluzioni
I dati restituiti dalle query predefinite elencate sopra sono già di per sé interessanti, ma la massima utilità si ottiene combinandoli con le informazioni provenienti da altre soluzioni OMS. Ad esempio, è possibile usare i dati degli eventi di sicurezza raccolti dalla soluzione Security and Audit e combinarli con i dati in transito per cercare tentativi insoliti di accesso alla rete per i processi denominati.  In questo esempio, si useranno gli operatori IN e DISTINCT per aggiungere i punti dati nella query di ricerca.

Requisiti: per usare l’esempio seguente, la soluzione Security and Audit deve essere installata. È però possibile usare i dati di altre soluzioni per combinarli con i dati in transito e ottenere così risultati simili.

### <a name="to-combine-wire-data-with-security-events"></a>Per combinare i dati in transito con gli eventi di sicurezza
1. Nella pagina Overview (Panoramica) fare clic sul riquadro **WireData** .
2. Nell'elenco **Query comuni sui dati in transito** fare clic su **Quantità di traffico di rete (in byte) per processo** per visualizzare l'elenco di processi restituiti. 
    ![query di WireData](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Se l’elenco di processi è troppo lungo per poterlo visualizzare facilmente, è possibile sostituire la query di ricerca con la seguente:
   
    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    L'esempio seguente illustra un processo denominato DancingPigs.exe, che può sembrare sospetto.
    ![risultati della ricerca di WireData](./media/log-analytics-wire-data/oms-wiredata-02.png)
4. Usando i dati restituiti nell’elenco, fare clic su un processo denominato. In questo esempio si è fatto clic su DancingPigs.exe. I risultati illustrati sotto descrivono il tipo di traffico di rete, ad esempio comunicazioni in uscita su diversi protocolli.
    ![risultati di WireData che illustrano un processo denominato](./media/log-analytics-wire-data/oms-wiredata-03.png)
5. Poiché la soluzione Security and Audit è installata, è possibile analizzare gli eventi di sicurezza che hanno lo stesso valore del campo ProcessName modificando la query di ricerca con gli operatori di query di ricerca IN e DISTINCT. È possibile eseguire questa operazione quando i dati in transito e i log delle altre soluzioni hanno i valori nello stesso formato. Sostituire la query di ricerca con la seguente:
   
    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    
   
    ![Risultati di WireData con i dati combinati](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Nei risultati sopra si noterà che sono visualizzate le informazioni sull’account. Ora è possibile affinare la query di ricerca per sapere quanto spesso l’account, che visualizza i dati di Security and Audit, è stato usato dal processo con una query come la seguente:        
   
    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```
   
    ![Risultati di WireData con i dati dell’account](./media/log-analytics-wire-data/oms-wiredata-05.png)

## <a name="next-steps"></a>Passaggi successivi
* [Ricerche nei log](log-analytics-log-searches.md) per visualizzare i record di ricerca dettagliati su Wire Data.
* Leggere il post del blog di Dan dal titolo [Using Wire Data in Operations Management Suite Log Search](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) (Uso di Wire Data nella ricerca nei log di Operations Management Suite) per altre informazioni sulla frequenza con cui i dati vengono raccolti e su come modificare le proprietà di raccolta per gli agenti di Operations Manager.

<!--HONumber=Oct16_HO2-->


