---
title: Configurare Monitoraggio prestazioni rete per circuiti ExpressRoute di Azure (anteprima) | Microsoft Docs
description: Configurare Monitoraggio prestazioni rete per circuiti ExpressRoute di Azure. (Anteprima)
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: cherylmc
ms.openlocfilehash: 6a03986288fdb6acaf234a8abf690f728d160fd7
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>Configurare Monitoraggio prestazioni rete per ExpressRoute (anteprima)

Monitoraggio prestazioni rete (NPM) è una soluzione di monitoraggio di rete basata su cloud che monitora la connettività tra le distribuzioni cloud di Azure e le posizioni locali (filiali e così via). NPM fa parte di Microsoft Operations Management Suite (OMS). NPM offre ora un'estensione per ExpressRoute che consente di monitorare le prestazioni di rete su circuiti ExpressRoute configurati per l'utilizzo del peering privato. Quando si configura NPM per ExpressRoute, è possibile rilevare problemi di rete da identificare ed eliminare.

È possibile:

* Monitorare perdita e latenza tra varie reti virtuali e configurare avvisi

* Monitorare tutti i percorsi (inclusi i percorsi ridondanti) sulla rete

* Risolvere i problemi di rete temporanei e temporizzati che sono difficili da replicare

* Determinare un segmento specifico nella rete che è responsabile della riduzione delle prestazioni

* Ottenere la velocità effettiva per rete virtuale (se si hanno agenti installati in ogni rete virtuale)

* Vedere lo stato del sistema ExpressRoute da un momento precedente

## <a name="regions"></a>Aree supportate

È possibile monitorare i circuiti di ExpressRoute in qualsiasi parte del mondo tramite un'area di lavoro ospitata in una delle seguenti aree:

* Europa occidentale 
* Stati Uniti orientali 
* Asia sudorientale 

## <a name="workflow"></a>Flusso di lavoro

Gli agenti di monitoraggio sono installati su più server, sia in locale che in Azure. Gli agenti comunicano tra loro, ma non inviano dati, inviano pacchetti di handshake TCP. La comunicazione tra gli agenti consente ad Azure di eseguire il mapping della topologia di rete e del percorso che il traffico potrebbe prendere.

1. Creare un'area di lavoro NPM in una delle [aree supportate](#regions).
2. Installare e configurare gli agenti software: 
    * Installare agenti di monitoraggio su server locali e VM di Azure.
    * Configurare le impostazioni nei server degli agenti di monitoraggio per consentire agli agenti di monitoraggio di comunicare (aprire le porte del firewall, ecc.).
3. Configurare regole del gruppo di sicurezza di rete per consentire all'agente di monitoraggio installato nelle VM di Azure di comunicare con gli agenti di monitoraggio locali.
4. Richiedere di inserire l'area di lavoro NPM nell'elenco elementi consentiti.
5. Configurare il monitoraggio: rilevamento automatico e gestione delle reti visibili in NPM.

Se si usa già Monitoraggio prestazioni rete per monitorare altri oggetti o servizi e si dispone già dell'area di lavoro in una delle aree supportate, è possibile ignorare i passaggi 1 e 2 e iniziare la configurazione dal passaggio 3.

## <a name="configure"></a>Passaggio 1: Creare un'area di lavoro

1. Nel [portale di Azure](https://portal.azure.com) cercare "Monitoraggio prestazioni rete" nell'elenco dei servizi nel **Marketplace**. Nell'elenco dei risultati fare clic per aprire la pagina **Monitoraggio prestazioni rete**.

  ![portal](.\media\how-to-npm\3.png)<br><br>
2. Nella parte inferiore della pagina principale **Monitoraggio prestazioni rete** fare clic su **Crea** per aprire la pagina **Monitoraggio prestazioni rete - Crea nuova soluzione**. Fare clic su **Area di lavoro di OMS - Selezionare un'area di lavoro** per aprire la pagina Aree di lavoro. Fare clic su **+Crea nuova area di lavoro** per aprire la pagina Area di lavoro.
3. Nella pagina **Area di lavoro OMS** selezionare **Crea nuovo** e configurare le impostazioni seguenti:

  * Area di lavoro OMS: immettere un nome per l'area di lavoro.
  * Sottoscrizione: in presenza di più sottoscrizioni, scegliere quella da associare alla nuova area di lavoro.
  * Gruppo di risorse: creare un gruppo di risorse o usarne uno esistente.
  * Posizione: è necessario selezionare un'[area supportata](#regions).
  * Piano tariffario: selezionare "Gratuito"
  
  >[!NOTE]
  >Il circuito ExpressRoute potrebbe trovarsi in qualsiasi posto, ma non deve essere nella stessa area dell'area di lavoro.
  >


  ![area di lavoro](.\media\how-to-npm\4.png)<br><br>
4. Fare clic su **OK** per salvare e distribuire il modello di impostazioni. Dopo la convalida del modello, fare clic su **Crea** per distribuire l'area di lavoro.
5. Dopo che l'area di lavoro è stata distribuita, passare alla risorsa **NetworkMonitoring(name)** che è stata creata. Convalidare le impostazioni e quindi fare clic su **La soluzione richiede una configurazione aggiuntiva**.

  ![configurazioni aggiuntive](.\media\how-to-npm\5.png)
6. Nella pagina **Monitoraggio prestazioni rete** selezionare **Usare TCP per le transazioni sintetiche** quindi fare clic su **Invia**. Le transazioni TCP vengono usate solo per creare e interrompere il collegamento. Su queste connessioni TCP non vengono inviati dati.

  ![TCP per le transazioni sintetiche](.\media\how-to-npm\6.png)

## <a name="agents"></a>Passaggio 2: Installare e configurare gli agenti

### <a name="download"></a>2.1: Scaricare il file di installazione dell'agente

1. Nella **pagina Configurazione del monitoraggio prestazioni di rete - Installazione TCP** per la risorsa, nella sezione **Installare gli agenti OMS**, fare clic sull'agente che corrisponde al processore del server e scaricare il file di installazione.

  >[!NOTE]
  >L'agente deve essere installato in un server Windows (2008 SP1 o versione successiva). Il monitoraggio dei circuiti ExpressRoute con il sistema operativo Windows Desktop e il sistema operativo Linux non è supportato. 
  >
  >
2. Quindi copiare l'**ID area di lavoro** e la **chiave primaria** nel Blocco note.
3. Nella sezione **Configurare gli agenti** scaricare lo script di Powershell. Lo script di PowerShell consente di aprire la porta firewall pertinente per le transazioni TCP.

  ![Script di PowerShell](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: Installare un agente di monitoraggio in ogni server di monitoraggio

Si consiglia di installare almeno due agenti su ogni lato della connessione ExpressRoute (ad esempio, in locale, reti virtuali di Azure) per la ridondanza. Usare la procedura seguente per installare gli agenti:

1. Eseguire **Installa** per installare l'agente in ogni server che si vuole usare per il monitoraggio di ExpressRoute. Il server che si usa per il monitoraggio può essere una VM o in locale e deve avere accesso a Internet. È necessario installare almeno un agente in locale e un agente in ogni segmento di rete che si vuole monitorare in Azure.
2. Nella pagina di **benvenuto** fare clic su **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina **Opzioni di installazione dell'agente** è possibile scegliere di connettere l'agente ad Azure Log Analytics (OMS) o Operations Manager. In alternativa è possibile omettere le scelte se si desidera configurare l'agente in un secondo momento. Dopo aver eseguito le selezioni, fare clic su **Avanti**.

  * Se si sceglie di connettersi ad **Azure Log Analytics (OMS)**, incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro** (chiave primaria) copiati nel Blocco note nella sezione precedente. Quindi fare clic su **Next**.

    ![ID e chiave](.\media\how-to-npm\8.png)
  * Se si sceglie di connettersi a **Operations Manager**, nella pagina **Configurazione gruppo di gestione** digitare il **nome del gruppo di gestione**, il **nome del server di gestione** e la **porta del server di gestione**. Quindi fare clic su **Next**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Nella pagina **Account azione agente** scegliere l'account di **sistema locale** o un **account di dominio o del computer locale**. Quindi fare clic su **Next**.

    ![Account](.\media\how-to-npm\10.png)
6. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi fare clic su **Installa**.
7. Nella pagina **Configurazione completata** fare clic su **Fine**.
8. Al termine verrà visualizzato Microsoft Monitoring Agent nel Pannello di controllo. È possibile rivedere la configurazione e verificare che l'agente sia connesso a Operational Insights (OMS). Quando si è connessi a OMS, l'agente visualizza un messaggio nel quale è indicato che **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite**.

### <a name="proxy"></a>2.3: Configurare le impostazioni proxy (facoltativo)

Se si usa un proxy Web per accedere a Internet, eseguire la procedura seguente per configurare le impostazioni proxy per Microsoft Monitoring Agent. Eseguire questa procedura per ogni server. Se è necessario configurare molti server, può risultare più semplice usare uno script per automatizzare il processo. In questo caso, vedere [Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite uno script](../log-analytics/log-analytics-windows-agent.md).

Per configurare le impostazioni proxy per Microsoft Monitoring Agent tramite il Pannello di controllo:

1. Aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent**.
3. Fare clic sulla scheda **Impostazioni proxy**.
4. Selezionare **Usa server proxy** e digitare l'URL e il numero di porta, se necessario. Se il server proxy richiede l'autenticazione, digitare il nome utente e la password per accedere al server proxy.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: Verificare la connettività dell'agente a OMS

È possibile verificare facilmente se gli agenti comunicano.

1. In un server con l'agente di monitoraggio, aprire il **Pannello di controllo**.
2. Aprire **Microsoft Monitoring Agent**.
3. Fare clic sulla scheda **Azure Log Analytics (OMS)**.
4. Nella colonna dello **stato** si dovrebbe vedere che l'agente si è connesso correttamente al servizio Operations Management Suite.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: Aprire le porte del firewall nei server dell'agente di monitoraggio

Per usare il protocollo TCP, è necessario aprire le porte del firewall per assicurarsi che gli agenti di monitoraggio possano comunicare.

È possibile eseguire uno script di PowerShell che crea le chiavi del Registro di sistema richieste da Monitoraggio prestazioni rete e crea anche le regole di Windows Firewall per consentire agli agenti di monitoraggio di creare connessioni TCP tra loro. Le chiavi del Registro di sistema create dallo script specificano anche se registrare i log di debug e il percorso al file dei log. Inoltre definiscono la porta TCP agente usata per la comunicazione. I valori per queste chiavi vengono impostati automaticamente dallo script, quindi non bisogna modificare manualmente le chiavi.

La porta 8084 è aperta per impostazione predefinita. È possibile usare una porta personalizzata fornendo il parametro "portNumber" allo script. In questo caso però è necessario specificare la stessa porta per tutti i server in cui viene eseguito lo script.

>[!NOTE]
>Lo script di PowerShell "EnableRules" configura le regole di Windows Firewall solo nel server in cui viene eseguito lo script. Se si dispone di un firewall di rete, è necessario assicurarsi che consenta il traffico destinato alla porta TCP usata dal monitoraggio delle prestazioni di rete.
>
>

Aprire i server degli agenti e aprire una finestra di PowerShell con privilegi amministrativi. Eseguire lo script di PowerShell [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) (che è stato scaricato in precedenza). Non utilizzare alcun parametro.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Passaggio 3: Configurare le regole del gruppo di sicurezza di rete

Per i server degli agenti di monitoraggio in Azure è necessario configurare regole del gruppo di sicurezza di rete per consentire il traffico TCP su una porta usata da NPM per le transazioni sintetiche. Per impostazione predefinita è la porta 8084. Questo consente a un agente di monitoraggio installato nella VM di Azure di comunicare con un agente di monitoraggio locale.

Per altre informazioni su NSG, vedere [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

## <a name="whitelist"></a>Passaggio 4: Richiedere di inserire l'area di lavoro nell'elenco elementi consentiti

>[!NOTE]
>Assicurarsi di aver installato gli agenti (sia l'agente server locale che l'agente server di Azure) e di aver eseguito lo script di PowerShell prima di procedere con questo passaggio.
>
>

Prima di iniziare a usare la funzionalità di monitoraggio di ExpressRoute di NPM, è necessario richiedere di inserire l'area di lavoro nell'elenco elementi consentiti. [Fare clic qui per passare alla pagina e compilare il modulo di richiesta](https://aka.ms/npmcohort). (Suggerimento: è consigliabile aprire questo collegamento in una nuova finestra o scheda). La procedura di inserimento nell'elenco elementi consentiti potrebbe richiedere un giorno lavorativo o più. Al termine dell'inserimento nell'elenco elementi consentiti verrà inviato un messaggio di posta elettronica.

## <a name="setupmonitor"></a>Passaggio 5: Configurare NPM per il monitoraggio di ExpressRoute

>[!WARNING]
>Proseguire solo quando l'area di lavoro è stata inserita nell'elenco elementi consentiti e si riceve il messaggio di conferma.
>
>

Dopo aver completato le sezioni precedenti e aver verificato che è avvenuto l'inserimento nell'elenco elementi consentiti, è possibile configurare il monitoraggio.

1. Passare al riquadro della panoramica di Monitoraggio prestazioni rete accedendo alla pagina **Tutte le risorse** e facendo clic sull'area di lavoro NPM inserita nell'elenco elementi consentiti.

  ![area di lavoro npm](.\media\how-to-npm\npm.png)
2. Fare clic sul riquadro della panoramica di **Monitoraggio prestazioni rete** per visualizzare il dashboard. Il dashboard contiene una pagina di ExpressRoute, indicante che ExpressRoute è in uno "stato non configurato". Fare clic su **Installazione della funzionalità** per aprire la pagina di configurazione di Monitoraggio prestazioni rete.

  ![installazione delle funzionalità](.\media\how-to-npm\npm2.png)
3. Nella pagina di configurazione passare alla scheda "Peering ExpressRoute" nel riquadro di sinistra. Fare clic su **Discover Now** (Individua).

  ![individua](.\media\how-to-npm\13.png)
4. Al termine del processo di individuazione vengono visualizzate le regole per il nome di circuito univoco e il nome della rete virtuale. Inizialmente queste regole sono disabilitate. Abilitare le regole, quindi selezionare gli agenti di monitoraggio e i valori di soglia.

  ![regole](.\media\how-to-npm\14.png)
5. Dopo aver attivato le regole e selezionato i valori e gli agenti che si desidera monitorare, c'è un'attesa di circa 30-60 prima che i valori inizino a popolarsi e i riquadri **Monitoraggio di ExpressRoute** diventino disponibili. Dopo aver visualizzato i riquadri di monitoraggio, i circuiti ExpressRoute e le risorse di connessione vengono monitorate da NPM.

  ![riquadri di monitoraggio](.\media\how-to-npm\15.png)

## <a name="explore"></a>Passaggio 6: Visualizzare i riquadri di monitoraggio

### <a name="dashboard"></a>Pagina Monitoraggio prestazioni rete

La pagina NPM contiene una pagina per ExpressRoute che mostra una panoramica dell'integrità dei circuiti e dei peering ExpressRoute.

  ![dashboard](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Elenco dei circuiti

Per visualizzare un elenco di tutti i circuiti ExpressRoute monitorati, fare clic sul riquadro **Circuiti ExpressRoute**. È possibile selezionare un circuito e visualizzare il relativo stato di integrità, i grafici di tendenza per la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza. I grafici sono interattivi. È possibile selezionare un intervallo di tempo personalizzato per tracciare i grafici. Si può trascinare il puntatore del mouse su un'area del grafico per eseguire lo zoom avanti e visualizzare i punti dati con granularità fine.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Tendenza di perdita, latenza e velocità effettiva

I grafici della larghezza di banda, la latenza e la perdita sono interattivi. Ogni sezione di questi grafici può essere ingrandita mediante i controlli del mouse. È possibile vedere anche i dati di larghezza di banda, latenza e perdita per altri intervalli, facendo clic su **Data/ora**, che si trova sotto il pulsante Azioni in alto a sinistra.

![tendenza](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Elenco dei peering

Facendo clic sul riquadro **Peering privati** nel dashboard viene visualizzato un elenco di tutte le connessioni alle reti virtuali su peering privato. È possibile selezionare una connessione di rete virtuale e visualizzare il relativo stato di integrità e i grafici di tendenza riguardanti la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza.

  ![elenco dei circuiti](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Topologia del circuito

Per visualizzare la topologia del circuito, fare clic sul riquadro **Topologia**. Verrà visualizzata la vista della topologia del circuito o peering. Il diagramma della topologia fornisce la latenza per ogni segmento della rete, e ogni hop di livello 3 è rappresentato da un nodo del diagramma. Facendo clic su un hop, vengono visualizzate altre informazioni sull'hop.
È possibile aumentare il livello di visibilità per includere hop locali spostando il dispositivo di scorrimento sotto a **Filtri**. Spostare il dispositivo di scorrimento a sinistra o a destra, aumenta o diminuisce il numero di hop nel grafico della topologia. È visibile la latenza per ogni segmento e questo consente di isolare più velocemente i segmenti ad alta latenza nella rete.

![filters](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Visualizzazione dettagliata della Topologia di un circuito

Questa visualizzazione mostra le connessioni della rete virtuale.
![topologia dettagliata](.\media\how-to-npm\17.png)
