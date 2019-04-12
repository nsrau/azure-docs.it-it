---
title: Configurare Monitoraggio prestazioni rete per circuiti ExpressRoute - Azure | Microsoft Docs
description: Configurare il monitoraggio di rete basato sul cloud per i circuiti Azure ExpressRoute. Illustra il monitoraggio su peering ExpressRoute privato e peering Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 180075f13be2cc2507a78e3d10a67a49a0c0cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118630"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Configurare Monitoraggio prestazioni rete per ExpressRoute

Questo articolo illustra come configurare un'estensione di Monitoraggio prestazioni rete per monitorare ExpressRoute. Monitoraggio prestazioni rete (NPM) è una soluzione di monitoraggio di rete basata su cloud che monitora la connettività tra le distribuzioni cloud di Azure e le posizioni locali (filiali e così via). NPM fa parte dei log di monitoraggio di Azure. NPM offre un'estensione per ExpressRoute che consente di monitorare le prestazioni di rete su circuiti ExpressRoute configurati per l'uso del peering privato o del peering Microsoft. Quando si configura NPM per ExpressRoute, è possibile rilevare problemi di rete da identificare ed eliminare. Questo servizio è disponibile anche per il cloud di Azure per enti pubblici.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

È possibile:

* Monitorare perdita e latenza tra varie reti virtuali e configurare avvisi

* Monitorare tutti i percorsi (inclusi i percorsi ridondanti) sulla rete

* Risolvere i problemi di rete temporanei e temporizzati che sono difficili da replicare

* Determinare un segmento specifico nella rete che è responsabile della riduzione delle prestazioni

* Ottenere la velocità effettiva per rete virtuale (se si hanno agenti installati in ogni rete virtuale)

* Vedere lo stato del sistema ExpressRoute da un momento precedente

## <a name="workflow"></a>Flusso di lavoro

Gli agenti di monitoraggio sono installati su più server, sia in locale che in Azure. Gli agenti comunicano tra loro, ma non inviano dati, inviano pacchetti di handshake TCP. La comunicazione tra gli agenti consente ad Azure di eseguire il mapping della topologia di rete e del percorso che il traffico potrebbe prendere.

1. Creare un'area di lavoro di Monitoraggio prestazioni rete. La procedura corrisponde a quella adottata per creare un'area di lavoro Log Analytics.
2. Installare e configurare gli agenti software. Se si vuole solo monitorare Peering Microsoft, non è necessario installare e configurare gli agenti software. 
    * Installare agenti di monitoraggio sui server locali e sulle macchine virtuali di Azure (per il peering privato).
    * Configurare le impostazioni nei server degli agenti di monitoraggio per consentire agli agenti di monitoraggio di comunicare (aprire le porte del firewall, ecc.).
3. Configurare regole del gruppo di sicurezza di rete per consentire all'agente di monitoraggio installato nelle VM di Azure di comunicare con gli agenti di monitoraggio locali.
4. Configurare il monitoraggio: rilevamento automatico e gestione delle reti visibili in NPM.

Se si usa già Monitoraggio prestazioni rete per monitorare altri oggetti o servizi e si dispone già dell'area di lavoro in una delle aree supportate, è possibile ignorare i passaggi 1 e 2 e iniziare la configurazione dal passaggio 3.

## <a name="configure"></a>Passaggio 1: Creare un'area di lavoro

Creare un'area di lavoro nella sottoscrizione che includa il collegamento delle reti virtuali ai circuiti ExpressRoute.

1. Nel [portale di Azure](https://portal.azure.com) selezionare la sottoscrizione che include le reti virtuali di cui è stato eseguito il peer al circuito ExpressRoute. Quindi, nell'elenco di servizi in **Marketplace** cercare "Monitoraggio prestazioni rete". Nell'elenco dei risultati fare clic per aprire la pagina **Monitoraggio prestazioni rete**.

   >[!NOTE]
   >È possibile creare una nuova area di lavoro o usarne una esistente. Se si vuole usare un'area di lavoro esistente, verificare che sia stata eseguita la migrazione dell'area di lavoro al nuovo linguaggio di query. [Altre informazioni...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Nella parte inferiore della pagina principale **Monitoraggio prestazioni rete** fare clic su **Crea** per aprire la pagina **Monitoraggio prestazioni rete - Crea nuova soluzione**. Fare clic su **area di lavoro Log Analytics - Selezionare un'area di lavoro** per aprire la pagina Aree di lavoro. Fare clic su **+Crea nuova area di lavoro** per aprire la pagina Area di lavoro.
3. Nel **dell'area di lavoro di Log Analitica** pagina, selezionare **Crea nuovo**, quindi configurare le impostazioni seguenti:

   * area di lavoro Log Analytics: immettere un nome per l'area di lavoro.
   * Sottoscrizione: in presenza di più sottoscrizioni, scegliere quella da associare alla nuova area di lavoro.
   * Gruppo di risorse: creare un gruppo di risorse o usarne uno esistente.
   * Località: questa località viene usata per specificare la località dell'account di archiviazione usato per i log di connessione dell'agente.
   * Piano tariffario: selezionare il piano tariffario.
  
     >[!NOTE]
     >Il circuito ExpressRoute può essere ovunque nel mondo. Non deve necessariamente trovarsi nella stessa area dell'area di lavoro.
     >
  
     ![area di lavoro](./media/how-to-npm/4.png)<br><br>
4. Fare clic su **OK** per salvare e distribuire il modello di impostazioni. Dopo la convalida del modello, fare clic su **Crea** per distribuire l'area di lavoro.
5. Dopo che l'area di lavoro è stata distribuita, passare alla risorsa **NetworkMonitoring(name)** che è stata creata. Convalidare le impostazioni e quindi fare clic su **La soluzione richiede una configurazione aggiuntiva**.

   ![configurazioni aggiuntive](./media/how-to-npm/5.png)

## <a name="agents"></a>Passaggio 2: Installare e configurare agenti

### <a name="download"></a>2.1: Scaricare il file di installazione dell'agente

1. Passare alla scheda **Impostazioni comuni** della pagina **Configurazione del monitoraggio prestazioni di rete** della risorsa. Fare clic sull'agente corrispondente al processore del server indicato nella sezione **Installa agenti di Log Analytics** e scaricare il file di installazione.
2. Quindi copiare l'**ID area di lavoro** e la **chiave primaria** nel Blocco note.
3. Nella sezione **Configurare gli agenti di Log Analytics per il monitoraggio tramite il protocollo TCP** scaricare lo script PowerShell. Lo script di PowerShell consente di aprire la porta firewall pertinente per le transazioni TCP.

   ![Script di PowerShell](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: Installare un agente di monitoraggio in ogni server di monitoraggio (in ogni rete virtuale che si vuole monitorare)

È consigliabile installare almeno due agenti su ogni lato della connessione ExpressRoute per la ridondanza (ad esempio, locale o su reti virtuali di Azure). L'agente deve essere installato in un server Windows (2008 SP1 o versione successiva). Il monitoraggio dei circuiti ExpressRoute con il sistema operativo Windows Desktop e il sistema operativo Linux non è supportato. Usare la procedura seguente per installare gli agenti:
   
  >[!NOTE]
  >Gli agenti dei quali SCOM esegue il push (tra cui [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) potrebbero non essere in grado di rilevare in modo coerente la propria posizione se sono ospitati in Azure. È consigliabile non usare questi agenti in reti virtuali di Azure per monitorare ExpressRoute.
  >

1. Eseguire **Installa** per installare l'agente in ogni server che si vuole usare per il monitoraggio di ExpressRoute. Il server usato per il monitoraggio può essere una macchina virtuale o un server locale e deve avere accesso a Internet. È necessario installare almeno un agente in locale e un agente in ogni segmento di rete che si vuole monitorare in Azure.
2. Nella pagina di **benvenuto** fare clic su **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nel **opzioni di installazione dell'agente** pagina, è possibile scegliere di connettere l'agente a Operations Manager o i log di monitoraggio di Azure. In alternativa è possibile omettere le scelte se si desidera configurare l'agente in un secondo momento. Dopo aver eseguito le selezioni, fare clic su **Avanti**.

   * Se si sceglie di connettersi ad **Azure Log Analytics**, incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro** (chiave primaria) copiati nel Blocco note nella sezione precedente. Quindi fare clic su **Next**.

     ![ID e chiave](./media/how-to-npm/8.png)
   * Se si sceglie di connettersi a **Operations Manager**, nella pagina **Configurazione gruppo di gestione** digitare il **nome del gruppo di gestione**, il **nome del server di gestione** e la **porta del server di gestione**. Quindi fare clic su **Next**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Nella pagina **Account azione agente** scegliere l'account di **sistema locale** o un **account di dominio o del computer locale**. Quindi fare clic su **Next**.

     ![Account](./media/how-to-npm/10.png)
6. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi fare clic su **Installa**.
7. Nella pagina **Configurazione completata** fare clic su **Fine**.
8. Al termine verrà visualizzato Microsoft Monitoring Agent nel Pannello di controllo. È possibile rivedere la configurazione e verificare che l'agente sia connesso a log di monitoraggio di Azure. Quando connesso, l'agente visualizza il seguente messaggio: **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite**.

9. Ripetere questa procedura per ogni rete virtuale da monitorare.

### <a name="proxy"></a>2.3: Configurare le impostazioni proxy (facoltativo)

Se si usa un proxy Web per accedere a Internet, eseguire la procedura seguente per configurare le impostazioni proxy per Microsoft Monitoring Agent. Eseguire questa procedura per ogni server. Se è necessario configurare molti server, può risultare più semplice usare uno script per automatizzare il processo. In questo caso, vedere [Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite uno script](../log-analytics/log-analytics-windows-agent.md).

Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite il Pannello di controllo:

1. Aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent**.
3. Fare clic sulla scheda **Impostazioni proxy**.
4. Selezionare **Usa server proxy** e digitare l'URL e il numero di porta, se necessario. Se il server proxy richiede l'autenticazione, digitare il nome utente e la password per accedere al server proxy.

   ![proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Verificare la connettività dell'agente

È possibile verificare facilmente se gli agenti comunicano.

1. In un server con l'agente di monitoraggio, aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent**.
3. Fare clic sulla scheda **Azure Log Analytics**.
4. Nel **stato** colonna, si dovrebbe vedere che l'agente sia connesso correttamente al log di monitoraggio di Azure.

   ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Aprire le porte del firewall nei server dell'agente di monitoraggio

Per usare il protocollo TCP, è necessario aprire le porte del firewall per assicurarsi che gli agenti di monitoraggio possano comunicare.

Per creare le chiavi del Registro di sistema necessarie per Monitoraggio prestazioni rete è possibile eseguire uno script PowerShell. Lo script crea anche le regole di Windows Firewall per consentire agli agenti di monitoraggio di creare connessioni TCP tra loro. Le chiavi del Registro di sistema create dallo script specificano se registrare i log di debug e il percorso del file di log. Inoltre definiscono la porta TCP agente usata per la comunicazione. I valori per queste chiavi vengono impostati automaticamente dallo script. Non modificare manualmente queste chiavi.

La porta 8084 è aperta per impostazione predefinita. È possibile usare una porta personalizzata fornendo il parametro "portNumber" allo script. In questo caso però è necessario specificare la stessa porta per tutti i server in cui viene eseguito lo script.

>[!NOTE]
>Lo script di PowerShell "EnableRules" configura le regole di Windows Firewall solo nel server in cui viene eseguito lo script. Se si dispone di un firewall di rete, è necessario assicurarsi che consenta il traffico destinato alla porta TCP usata dal monitoraggio delle prestazioni di rete.
>
>

Aprire i server degli agenti e aprire una finestra di PowerShell con privilegi amministrativi. Eseguire lo script di PowerShell [EnableRules](https://aka.ms/npmpowershellscript) (che è stato scaricato in precedenza). Non utilizzare alcun parametro.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Passaggio 3: Configurare le regole del gruppo di sicurezza di rete

Per monitorare i server degli agenti presenti in Azure è necessario configurare regole del gruppo di sicurezza di rete per consentire il traffico TCP su una porta usata da Monitoraggio prestazioni rete per le transazioni sintetiche. Per impostazione predefinita è la porta 8084. Ciò consente a un agente di monitoraggio installato in una macchina virtuale di Azure di comunicare con un agente di monitoraggio locale.

Per altre informazioni su NSG, vedere [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Assicurarsi di aver installato gli agenti (sia l'agente server locale che l'agente server di Azure) e di aver eseguito lo script di PowerShell prima di procedere con questo passaggio.
>

## <a name="setupmonitor"></a>Passaggio 4: individuare le connessioni peering

1. Passare al riquadro della panoramica di Monitoraggio prestazioni rete accedendo alla pagina **Tutte le risorse**, quindi fare clic sull'area di lavoro di Monitoraggio prestazioni rete inserita nell'elenco elementi consentiti.

   ![area di lavoro npm](./media/how-to-npm/npm.png)
2. Fare clic sul riquadro della panoramica di **Monitoraggio prestazioni rete** per visualizzare il dashboard. Il dashboard contiene una pagina di ExpressRoute, indicante che ExpressRoute è in uno "stato non configurato". Fare clic su **Installazione della funzionalità** per aprire la pagina di configurazione di Monitoraggio prestazioni rete.

   ![installazione delle funzionalità](./media/how-to-npm/npm2.png)
3. Nella pagina di configurazione passare alla scheda "Peering ExpressRoute" nel riquadro di sinistra. Quindi fare clic su **Individua adesso**.

   ![individua](./media/how-to-npm/13.png)
4. Al termine del processo di individuazione, verrà visualizzato un elenco contenente gli elementi seguenti:
   * Tutte le connessioni peering Microsoft nei circuiti ExpressRoute associati alla sottoscrizione.
   * Tutte le connessioni di peering privato che si connettono alle reti virtuali associate alla sottoscrizione.
            
## <a name="configmonitor"></a>Passaggio 5: configurare i monitoraggi

In questa sezione vengono configurati i monitoraggi. Seguire i passaggi per il tipo di peering da monitorare: **peering privato** o **peering Microsoft**.

### <a name="private-peering"></a>Peering privato

Per il peering privato, al termine del processo di individuazione verranno visualizzate le regole per i nomi univoci **Nome circuito** e **Nome rete virtuale**. Inizialmente queste regole sono disabilitate.

![regole](./media/how-to-npm/14.png)

1. Selezionare la casella di controllo **Monitora questo peering**.
2. Selezionare la casella di controllo **Abilita Monitoraggio integrità per questo peering**.
3. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per generare eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità.
4. Fare clic sul pulsante **Aggiungi agenti** in AGENTI LOCALI per aggiungere i server locali dai quali si vuole monitorare la connessione di peering privato. Assicurarsi di scegliere solo agenti con connettività all'endpoint del servizio Microsoft specificato nella sezione relativa al passaggio 2. Gli agenti locali devono essere in grado di raggiungere l'endpoint usando la connessione ExpressRoute.
5. Salvare le impostazioni.
6. Dopo aver attivato le regole e selezionato i valori e gli agenti che si desidera monitorare, c'è un'attesa di circa 30-60 prima che i valori inizino a popolarsi e i riquadri **Monitoraggio di ExpressRoute** diventino disponibili.

### <a name="microsoft-peering"></a>Peering Microsoft

Per il peering Microsoft, fare clic sulle connessioni peering Microsoft da monitorare e configurare le impostazioni.

1. Selezionare la casella di controllo **Monitora questo peering**. 
2. (Facoltativo) È possibile modificare l'endpoint di destinazione del servizio Microsoft. Per impostazione predefinita, Monitoraggio prestazioni rete sceglie un endpoint del servizio Microsoft come destinazione. Monitoraggio prestazioni rete monitora la connettività dai server locali a questo endpoint di destinazione tramite ExpressRoute. 
    * Per modificare l'endpoint di destinazione, fare clic sul collegamento **(modifica)** in **Destinazione:** e selezionare un altro endpoint di destinazione del servizio Microsoft dall'elenco di URL.
      ![modifica destinazione](./media/how-to-npm/edit_target.png)<br>

    * È possibile usare un URL personalizzato o un indirizzo IP. Questa opzione è particolarmente utile se si usa il peering Microsoft per stabilire una connessione a servizi Azure PaaS, ad esempio Archiviazione di Azure, database SQL e siti Web offerti su indirizzi IP pubblici. A questo scopo, fare clic sul collegamento **(Usare l'indirizzo IP o l'URL personalizzato)** in fondo all'elenco di URL e immettere l'endpoint pubblico del servizio Azure PaaS connesso tramite il peering Microsoft ExpressRoute.
    ![URL personalizzato](./media/how-to-npm/custom_url.png)<br>

    * Se si usano queste impostazioni facoltative, verificare che qui sia selezionato solo l'endpoint del servizio Microsoft. L'endpoint deve essere connesso a ExpressRoute e raggiungibile dagli agenti locali.
3. Selezionare la casella di controllo **Abilita Monitoraggio integrità per questo peering**.
4. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per generare eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità.
5. Fare clic sul pulsante **Aggiungi agenti** in AGENTI LOCALI per aggiungere i server locali dai quali si vuole monitorare la connessione peering Microsoft. Assicurarsi di scegliere solo agenti con connettività agli endpoint del servizio Microsoft specificati nella sezione relativa al passaggio 2. Gli agenti locali devono essere in grado di raggiungere l'endpoint usando la connessione ExpressRoute.
6. Salvare le impostazioni.
7. Dopo aver attivato le regole e selezionato i valori e gli agenti che si desidera monitorare, c'è un'attesa di circa 30-60 prima che i valori inizino a popolarsi e i riquadri **Monitoraggio di ExpressRoute** diventino disponibili.

## <a name="explore"></a>Passaggio 6: visualizzare i riquadri di monitoraggio

Dopo aver visualizzato i riquadri di monitoraggio, i circuiti ExpressRoute e le risorse di connessione vengono monitorate da NPM. Per eseguire il drill-down sull'integrità delle connessioni peering Microsoft è possibile fare clic sul riquadro Peering Microsoft.

![riquadri di monitoraggio](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Pagina Monitoraggio prestazioni rete

La pagina NPM contiene una pagina per ExpressRoute che mostra una panoramica dell'integrità dei circuiti e dei peering ExpressRoute.

![dashboard](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Elenco dei circuiti

Per visualizzare un elenco di tutti i circuiti ExpressRoute monitorati, fare clic sul riquadro **Circuiti ExpressRoute**. È possibile selezionare un circuito e visualizzare il relativo stato di integrità, i grafici di tendenza per la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza. I grafici sono interattivi. È possibile selezionare un intervallo di tempo personalizzato per tracciare i grafici. Si può trascinare il puntatore del mouse su un'area del grafico per eseguire lo zoom avanti e visualizzare i punti dati con granularità fine.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Tendenza di perdita, latenza e velocità effettiva

I grafici della larghezza di banda, la latenza e la perdita sono interattivi. Ogni sezione di questi grafici può essere ingrandita mediante i controlli del mouse. È possibile vedere anche i dati di larghezza di banda, latenza e perdita per altri intervalli, facendo clic su **Data/ora**, che si trova sotto il pulsante Azioni in alto a sinistra.

![tendenza](./media/how-to-npm/16.png)

### <a name="peerings"></a>Elenco dei peering

Per visualizzare un elenco di tutte le connessioni alle reti virtuali su peering privato, fare clic sul riquadro **Peering privati** nel dashboard. È possibile selezionare una connessione di rete virtuale e visualizzare il relativo stato di integrità e i grafici di tendenza riguardanti la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza.

![elenco dei circuiti](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Visualizzazione dei nodi

Per visualizzare un elenco di tutti i collegamenti tra i nodi locali e le macchine virtuali di Azure o gli endpoint del servizio Microsoft per la connessione peering ExpressRoute scelta, fare clic su **Visualizza collegamenti nodo**. È possibile visualizzare lo stato di integrità di ogni collegamento, nonché la tendenza di perdite e latenza a esso associata.

![visualizzazione dei nodi](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Topologia del circuito

Per visualizzare la topologia del circuito, fare clic sul riquadro **Topologia**. Verrà visualizzata la vista della topologia del circuito o peering. Il diagramma della topologia indica la latenza per ogni segmento della rete. Ogni hop di livello 3 è rappresentato da un nodo del diagramma. Facendo clic su un hop, vengono visualizzate altre informazioni sull'hop.

È possibile aumentare il livello di visibilità per includere hop locali spostando il dispositivo di scorrimento sotto a **Filtri**. Spostare il dispositivo di scorrimento a sinistra o a destra, aumenta o diminuisce il numero di hop nel grafico della topologia. È visibile la latenza per ogni segmento e questo consente di isolare più velocemente i segmenti ad alta latenza nella rete.

![filters](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Visualizzazione dettagliata della Topologia di un circuito

Questa visualizzazione mostra le connessioni della rete virtuale.
![topologia dettagliata](./media/how-to-npm/17.png)
