---
title: Connettere computer usando il gateway Log Analitica | Microsoft Docs
description: Connettere i dispositivi e i computer monitorati da Operations Manager tramite il gateway Log Analitica per inviare dati al servizio di Log Analitica e di automazione di Azure quando non hanno accesso a internet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: 47b589d32accc4a699e7260b9e4b2de4cca58f2b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369616"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Connettere computer senza accesso a internet usando il gateway di Analitica di Log in Monitoraggio di Azure

>[!NOTE]
>Come Microsoft Operations Management Suite (OMS) esegue la transizione a monitoraggio di Microsoft Azure, in fase di modifica della terminologia. Questo articolo si riferisce al Gateway OMS come il gateway di Azure Log Analitica. 
>

Questo articolo descrive come configurare la comunicazione con automazione di Azure e monitoraggio di Azure tramite il gateway Log Analitica quando i computer che sono direttamente connessi o che sono monitorati da Operations Manager non dispone di alcun accesso a internet. 

Il gateway Log Analitica è un proxy di inoltro HTTP che supporta il tunneling HTTP tramite il comando HTTP CONNECT. Questo gateway è possibile raccogliere dati e inviarli all'automazione di Azure e un'area di lavoro di Log Analitica in Monitoraggio di Azure per conto dei computer che non sono connessi a internet.  

Il gateway di Log Analytics gli elementi seguenti:

* Creazione di report un massimo di Analitica Log quattro stesso agenti dell'area di lavoro che sono sottostante e che sono configurati con Hybrid Runbook Workers di automazione di Azure.  
* Computer Windows in cui Microsoft Monitoring Agent è direttamente connesso a un'area di lavoro di Log Analitica in Monitoraggio di Azure.
* Computer Linux in cui un agente di Log Analitica per Linux è direttamente connesso a un'area di lavoro di Log Analitica in Monitoraggio di Azure.  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3 o un gruppo di gestione in Operations Manager 2016 o versione successiva che viene integrato con Log Analitica.  

Alcuni criteri di sicurezza IT non consentono la connessione internet per i computer di rete. Questi computer non connessi possibile punto di vendita (POS) dispositivi o i server che supportano servizi IT, ad esempio. Per connettere questi dispositivi per automazione di Azure o un'area di lavoro di Log Analitica in modo da gestire e monitorare, configurarle per comunicare direttamente con il gateway Log Analitica. Il gateway Log Analitica può ricevere le informazioni di configurazione e inoltrare i dati per loro conto. Se i computer sono configurati con l'agente di Log Analitica per connettersi direttamente a un'area di lavoro di Log Analitica, i computer comunicheranno invece con il gateway Log Analitica.  

Il Log Analitica gateway trasferisce i dati dagli agenti al servizio direttamente. Senza analizzare i dati in transito.

Quando un gruppo di gestione di Operations Manager è integrato con Log Analitica, i server di gestione possono essere configurati per la connessione al gateway Log Analitica per ricevere informazioni di configurazione e inviare i dati raccolti, a seconda della soluzione che è stata abilitata .  Agenti di Operations Manager inviano alcuni dati al server di gestione. Gli agenti, ad esempio, potrebbero inviare avvisi di Operations Manager, i dati di valutazione della configurazione, i dati dello spazio dell'istanza e dati sulla capacità. Altri dati con volumi elevati, ad esempio Internet Information Services (IIS) log i dati sulle prestazioni e gli eventi di sicurezza, viene inviati direttamente al gateway Log Analitica. 

Se uno o più server Gateway di Operations Manager vengono distribuiti per monitorare i sistemi non attendibili in una rete perimetrale o in una rete isolata, tali server non possono comunicare con un gateway Log Analitica.  Server Gateway di gestione Operations possono fare riferimento solo a un server di gestione.  Quando un gruppo di gestione di Operations Manager è configurato per comunicare con il gateway di Analitica di Log, le informazioni di configurazione del proxy vengono automaticamente distribuite a ogni computer gestito tramite agente configurato per raccogliere dati di log per monitoraggio di Azure anche se l'impostazione è vuota.    

Per garantire un'elevata disponibilità per direttamente connessi o i gruppi di Operations Management che comunicano con un'area di lavoro di Log Analitica attraverso il gateway, usare il bilanciamento carico (NLB) per reindirizzare e distribuire il traffico tra più server gateway. In questo modo, se un server gateway si arresta, il traffico viene reindirizzato a un altro nodo disponibile.  

Il computer che esegue il gateway Log Analitica richiede l'agente di Log Analitica Windows identificare gli endpoint servizio che deve comunicare con il gateway. È anche l'agente indirizzare il gateway per segnalare le stesse aree di lavoro che gli agenti o il gruppo di gestione di Operations Manager dietro il gateway vengono configurati con. Questa configurazione consente il gateway e l'agente per comunicare con l'area di lavoro assegnato.

Un gateway può essere multihomed per fino a quattro aree di lavoro. Questo è il numero totale di aree di lavoro che supporta un agente di Windows.  

Ogni agente deve disporre di connettività di rete al gateway in modo che gli agenti possano trasferire automaticamente dati da e verso il gateway. Evitare di installare il gateway in un controller di dominio.

Il diagramma seguente mostra i dati provenienti dagli agenti diretti, tramite il gateway per automazione di Azure e Log Analitica. La configurazione del proxy dell'agente deve corrispondere alla porta configurata con il gateway Log Analitica.  

![Diagramma della comunicazione degli agenti diretti con i servizi](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Il diagramma seguente illustra il flusso di dati da un gruppo di gestione di Operations Manager a Log Analytics.   

![Diagramma della comunicazione di Operations Manager con Log Analitica](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configura il sistema

I computer designati per l'esecuzione del gateway di Analitica di Log devono avere la configurazione seguente:

* Windows 10, Windows 8.1 o Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008
* Microsoft .NET Framework 4.5
* Almeno un processore a 4 core e 8 GB di memoria 
* Oggetto [agente di Log Analitica per Windows](agent-windows.md) configurata per il report alla stessa area di lavoro come gli agenti che comunicano tramite il gateway

### <a name="language-availability"></a>Lingue disponibili

Il gateway di Analitica di Log è disponibile nelle seguenti lingue:

- Cinese (semplificato)
- Cinese (tradizionale)
- Ceco
- Olandese
- Inglese
- Francese
- Tedesco
- Ungherese
- Italiano
- Giapponese
- Coreano
- Polacco
- Portoghese (Brasile)
- Portoghese (Portogallo)
- Russo
- Spagnolo (internazionale)

### <a name="supported-encryption-protocols"></a>Protocolli di crittografia supportati
Il gateway Log Analitica supporta solo Transport Layer Security (TLS) 1.0, 1.1 e 1.2.  Non supporta Secure Sockets Layer (SSL).  Per garantire la sicurezza dei dati in transito verso Log Analitica, configurare il gateway per usare almeno TLS 1.2. Le versioni precedenti di TLS o SSL sono vulnerabili. Anche se attualmente consentono di garantire la compatibilità, evitare di usarle.  

Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Numero di connessioni dell'agente supportate
La tabella seguente mostra approssimativamente quante gli agenti possano comunicare con un server gateway. Il supporto è basato su agenti che caricano circa 200 KB di dati ogni 6 secondi. Per ogni agente di test, volume di dati è circa 2,7 GB al giorno.

|Gateway |Agenti supportati (approssimativa)|  
|--------|----------------------------------|  
|CPU: Intel Xeon v3 E5 2660 processore \@ core da 2,6 GHz 2<br> Memoria: 4 GB<br> Larghezza di banda di rete: 1 Gbps| 600|  
|CPU: Intel Xeon v3 E5 2660 processore \@ core da 2,6 GHz 4<br> Memoria: 8 GB<br> Larghezza di banda di rete: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Scaricare il gateway di Log Analytics

Ottenere la versione più recente del file di installazione di gateway Log Analitica dalla [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) o il portale di Azure.

Per ottenere il gateway di Analitica di Log dal portale di Azure, seguire questa procedura:

1. Esplorare l'elenco dei servizi e quindi selezionare **Log Analytics**. 
1. Selezionare un'area di lavoro.
1. Nel pannello dell'area di lavoro in **Generale** selezionare **Avvio rapido**. 
1. In **Scegliere un'origine dati da connettere all'area di lavoro** selezionare **Computer**.
1. Nel **degli agenti diretti** pannello, seleziona **gateway di scaricare Log Analitica**.
 
   ![Screenshot dei passaggi per scaricare il gateway Log Analitica](./media/gateway/download-gateway.png)

oppure 

1. Nel pannello dell'area di lavoro in **Impostazioni** selezionare **Impostazioni avanzate**.
1. Passare a **Connected Sources** > **Windows Server** e selezionare **gateway scaricare Log Analitica**.

## <a name="install-the-log-analytics-gateway"></a>Installare il gateway di Log Analytics

Per installare un gateway, seguire questa procedura.  (Se è installata una versione precedente denominata utilità di inoltro di Log Analitica, verrà aggiornata a questa versione.)

1. Nella cartelle di destinazione fare doppio clic su **Log Analytics gateway.msi**.
1. Nella pagina di **benvenuto** selezionare **Avanti**.

   ![Schermata di paginainiziale della procedura guidata configurazione del Gateway](./media/gateway/gateway-wizard01.png)

1. Nel **contratto di licenza** pagina, selezionare **accetto i termini del contratto di licenza** per accettare le condizioni di licenza Software Microsoft e quindi selezionare **Next**.
1. Nella pagina **Port and proxy address** (Indirizzo porta e proxy):

   a. Immettere il numero di porta TCP da utilizzare per il gateway. Il programma di installazione Usa questo numero di porta per configurare una regola in ingresso nel Firewall di Windows.  Il valore predefinito è 8080.
      L'intervallo valido del numero di porta è 1 e 65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.

   b. Se il server in cui è installato il gateway deve comunicare tramite un proxy, immettere l'indirizzo del proxy in cui il gateway deve connettersi. Ad esempio, immettere `http://myorgname.corp.contoso.com:80`.  Se si lascia vuoto questo campo, il gateway tenterà di connettersi direttamente a internet.  Se il server proxy richiede l'autenticazione, immettere un nome utente e una password.

   c. Selezionare **Avanti**.

   ![Screenshot della configurazione per il proxy del gateway](./media/gateway/gateway-wizard02.png)

1. Se non è abilitato Microsoft Update, verrà visualizzata la pagina di Microsoft Update e si può scegliere di abilitarlo. Effettuare una selezione e quindi selezionare **successivo**. In caso contrario, continuare con il passaggio successivo.
1. Nel **cartella di destinazione** pagina, è possibile lasciare la cartella predefinita c:\programmi\oms Gateway c:\Programmi\Microsoft oppure immettere il percorso in cui si desidera installare il gateway. Quindi selezionare **Avanti**.
1. Nella pagina **Pronto per l'installazione** selezionare **Installa**. Se controllo dell'Account utente richiede l'autorizzazione per installare, selezionare **Sì**.
1. Al termine dell'installazione, selezionare **fine**. Per verificare che il servizio sia in esecuzione, aprire lo snap-in Services. msc e verificare che **Gateway OMS** viene visualizzato nell'elenco dei servizi e che il suo stato sia **esecuzione**.

   ![Screenshot dei servizi locali, che mostra che il Gateway OMS è in esecuzione](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Configurare il bilanciamento del carico di rete 
È possibile configurare il gateway per la disponibilità elevata con bilanciamento carico di rete (NLB) tramite Microsoft [carico bilanciamento rete (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md), o servizi di bilanciamento del carico basato su hardware. Il servizio di bilanciamento del carico gestisce il traffico reindirizzando le connessioni richieste dagli agenti di Log Analytics o dai server di gestione di Operations Manager nei nodi relativi. Se un server gateway si arresta, il traffico viene reindirizzato ad altri nodi.

### <a name="microsoft-network-load-balancing"></a>Bilanciamento carico di rete Microsoft
Per informazioni su come progettare e distribuire un cluster di bilanciamento del carico di rete di Windows Server 2016, vedere [Bilanciamento carico di rete](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). La procedura seguente illustra come configurare un cluster di bilanciamento del carico di rete Microsoft.  

1. Accedere al server Windows membro del cluster di bilanciamento del carico di rete con un account amministrativo.  
2. Aprire Gestione bilanciamento carico di rete in Server Manager, fare clic su **Strumenti** e quindi su **Gestione bilanciamento carico di rete**.
3. Per connettere un server gateway di Log Analytics all'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi fare clic su **Aggiungi host al cluster**. 

    ![Carico di rete Gestione bilanciamento – Aggiungi Host al Cluster](./media/gateway/nlb02.png)
 
4. Immettere l'indirizzo IP del server gateway che si vuole connettere. 

    ![Gestione bilanciamento carico di rete - Aggiunta dell'host al cluster: Connettere](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Per informazioni su come progettare e distribuire un servizio di bilanciamento del carico di Azure, vedere [What ' s Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Per distribuire un servizio di bilanciamento del carico di base, seguire i passaggi descritti in questo [quickstart](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) esclusi i passaggi descritti nella sezione **crea server back-end**.   

> [!NOTE]
> Configurazione di bilanciamento del carico di Azure tramite il **SKU Basic**, richiede che le macchine virtuali di Azure appartengono a un Set di disponibilità. Per altre informazioni sui set di disponibilità, vedere [gestire la disponibilità delle macchine virtuali Windows in Azure](../../virtual-machines/windows/manage-availability.md). Per aggiungere le macchine virtuali esistenti a un set di disponibilità, consultare [Set Azure Resource Manager VM Set di disponibilità](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Dopo aver creato il servizio di bilanciamento del carico, un pool back-end deve essere creato, che distribuisce il traffico a uno o più server gateway. Seguire i passaggi descritti nella sezione dell'articolo Guida introduttiva [creare le risorse di bilanciamento del carico](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Quando si configura il probe di integrità deve essere configurato per usare la porta TCP del server gateway. Il probe di integrità in modo dinamico aggiunge o rimuove il server gateway dalla rotazione del bilanciamento del carico in base alla loro risposta ai controlli di integrità. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurare l'agente di Log Analitica e gruppo di gestione di Operations Manager
In questa sezione verrà illustrato come configurare gli agenti, un gruppo di gestione di Operations Manager o Azure Automation Hybrid Runbook Workers Log Analitica connesse direttamente con il gateway Log Analitica per comunicare con automazione di Azure o Log Analitica.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurare un agente di Log Analitica autonomo
Quando si configura l'agente di Log Analitica, sostituire il valore del server proxy con l'indirizzo IP del server gateway Log Analitica e il numero di porta. Se sono stati distribuiti più server gateway dietro un bilanciamento del carico, la configurazione di proxy dell'agente di Log Analitica è l'indirizzo IP virtuale del bilanciamento del carico.  

>[!NOTE]
>Per installare l'agente di Log Analitica nei computer Windows che si connettono direttamente a Log Analitica e gateway, vedere [i computer Windows di connettersi al servizio in Azure Log Analitica](agent-windows.md). Per connettere i computer Linux, vedere [configurare un agente di Log Analitica per i computer Linux in un ambiente ibrido](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Dopo aver installato l'agente nel server gateway, configurarlo per segnalare all'area di lavoro o gli agenti dell'area di lavoro che comunicano con il gateway. Se l'agente di Log Analitica Windows non è installato sul gateway, evento 300 viene scritto nel registro eventi di Gateway di OMS, che indica che l'agente deve essere installato. Se l'agente è installato ma non configurato per inviare report alla stessa area di lavoro come gli agenti che comunicano attraverso di esso, l'evento 105 viene scritto per lo stesso log, che indica che l'agente nel gateway deve essere configurato per inviare report alla stessa area di lavoro come gli agenti che co mmunicate con il gateway.

Dopo aver completato di configurazione, riavviare il servizio Gateway OMS per applicare le modifiche. In caso contrario, il gateway rifiuterà gli agenti che tentano di comunicare con Log Analitica e verranno segnalato l'evento 105 nel registro eventi del Gateway OMS. Questo si verifica anche quando si aggiungono o rimuove un'area di lavoro dalla configurazione dell'agente nel server gateway.   

Per informazioni correlate a di Hybrid Runbook Workers di automazione, vedere [automatizzare le risorse nel Data Center o cloud con Hybrid Runbook Workers](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurare Operations Manager, in cui tutti gli agenti usano lo stesso server proxy
La configurazione del proxy di Operations Manager viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager, anche se l'impostazione è vuota.  

Per usare il Gateway OMS per supportare Operations Manager, è necessario disporre di:

* Microsoft Monitoring Agent (versione 8.0.10900.0 o versione successiva) installato nel server Gateway OMS e configurato con le stesse aree di lavoro di Log Analitica che il gruppo di gestione sia configurato per inviare report a.
* Connettività Internet. In alternativa, il Gateway di OMS deve essere connesso a un server proxy che è connesso a internet.

> [!NOTE]
> Se non si specifica alcun valore per il gateway, a tutti gli agenti vengono inseriti i valori vuoti.
>

Se il gruppo di gestione di Operations Manager è la registrazione con un'area di lavoro di Log Analitica per la prima volta, si non verrà visualizzata l'opzione per specificare la configurazione del proxy per il gruppo di gestione nella console operatore. Questa opzione è disponibile solo se il gruppo di gestione è stato registrato con il servizio.  

Per configurare l'integrazione, aggiornare la configurazione del proxy del sistema tramite Netsh nel sistema in cui si esegue la console operatore e in tutti i server di gestione nel gruppo di gestione. A tale scopo, seguire questa procedura:

1. Aprire un prompt dei comandi con privilegi elevati:

   a. Selezionare **avviare** e immettere **cmd**.  

   b. Fare doppio clic su **prompt dei comandi** e selezionare **Esegui come amministratore**.  

1. Immettere il comando seguente:

   `netsh winhttp set proxy <proxy>:<port>`

Dopo aver completato l'integrazione con Log Analitica, rimuovere la modifica eseguendo `netsh winhttp reset proxy`. Quindi, nella console operatore, usare il **Configura server proxy** opzione per specificare il server gateway Log Analitica. 

1. Nella console di Operations Manager, sotto **Operations Management Suite**, selezionare **connessione**, quindi selezionare **configura Server Proxy**.

   ![Schermata di Operations Manager, che mostra la selezione di configura Server Proxy](./media/gateway/scom01.png)

1. Selezionare **usare un server proxy per accedere a Operations Management Suite** e quindi immettere l'indirizzo IP del server gateway Log Analitica o indirizzo IP virtuale del servizio di bilanciamento del carico. Prestare attenzione a iniziare con il prefisso `http://`.

   ![Schermata di Operations Manager, che mostra il proxy di indirizzo del server](./media/gateway/scom02.png)

1. Selezionare **Fine**. Il gruppo di gestione di Operations Manager è ora configurato per comunicare tramite il server gateway con il servizio Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurare Operations Manager, in cui agenti specifici usano un server proxy
Per gli ambienti di grandi dimensioni o complessi, è possibile solo specifici server (o gruppi) usino il server gateway Log Analitica.  Per questi server, è possibile aggiornare l'agente Operations Manager direttamente perché questo valore viene sovrascritto dal valore globale per il gruppo di gestione.  In alternativa, sostituire la regola usata per il push di questi valori.  

> [!NOTE] 
> Usare questa tecnica di configurazione se si vuole consentire per più server gateway Log Analitica nell'ambiente in uso.  Ad esempio, è possibile richiedere determinati server gateway Log Analitica per essere specificato in base a livello di area.
>

Per configurare il server specifici o gruppi di utilizzare il server gateway Log Analitica: 

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Creazione**.  
1. Nell'area di lavoro Creazione selezionare **Regole**. 
1. Sulla barra degli strumenti di Operations Manager, selezionare la **ambito** pulsante. Se questo pulsante non è disponibile, verificare che è stato selezionato un oggetto, non una cartella nel **Monitoring** riquadro. La finestra di dialogo **Crea ambito oggetti Management Pack** visualizza un elenco di classi, gruppi o oggetti di destinazione comuni. 
1. Nel **ricercare** immettere **servizio integrità** e selezionarlo dall'elenco. Selezionare **OK**.  
1. Cercare **regola di impostazione Proxy di Advisor**. 
1. Sulla barra degli strumenti di Operations Manager, selezionare **esegue l'override** e quindi **eseguire l'Override di regola\oggetto specifico della classe: Servizio integrità** e selezionare un oggetto dall'elenco.  Oppure creare un gruppo personalizzato che contiene l'oggetto servizio di integrità dei server di cui di che si desidera applicare questa sostituzione consente. Applicare quindi la sostituzione al gruppo personalizzato.
1. Nel **proprietà di sostituzione** finestra di dialogo, aggiungere un segno di spunta nel **eseguire l'Override** accanto alla colonna il **WebProxyAddress** parametro.  Nel **valore di sostituzione** immettere l'URL del server gateway Log Analitica. Prestare attenzione a iniziare con il prefisso `http://`.  

    >[!NOTE]
    > Non è necessario attivare la regola. È già gestita automaticamente con una sostituzione nel management pack di Microsoft System Center Advisor Secure Reference Override che fa riferimento a Microsoft System Center Advisor Monitoring Server Group.
    > 

1. Selezionare un management pack dal **seleziona management pack di destinazione** elencare o creare un nuovo management pack non bloccato selezionando **New**. 
1. Al termine, selezionare **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurare per Hybrid Runbook Workers di automazione
Se si dispone di Hybrid Runbook Workers di automazione nell'ambiente in uso, seguire questi passaggi per le soluzioni alternative manuali e temporanee configurare il Gateway OMS per supportare i ruoli di lavoro.

Per seguire la procedura descritta in questa sezione, è necessario conoscere l'area di Azure in cui si trova l'account di automazione. Per trovare tale posizione:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare il servizio Automazione di Azure.
1. Selezionare l'account appropriato di Automazione di Azure.
1. Visualizzarne l'area in **Località**.

   ![Screenshot della posizione dell'account di automazione nel portale di Azure](./media/gateway/location.png)

Usare le tabelle seguenti per identificare l'URL per ogni posizione.

**URL del servizio dati di Runtime del processo**

| **Posizione** | **URL** |
| --- | --- |
| Stati Uniti centro-settentrionali |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net |
| Asia sudorientale |sea-jobruntimedata-prod-su1.azure-automation.net |
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Giappone |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia |ase-jobruntimedata-prod-su1.azure-automation.net |

**URL del servizio agente**

| **Posizione** | **URL** |
| --- | --- |
| Stati Uniti centro-settentrionali |ncus-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-agentservice-prod-1.azure-automation.net |
| Asia sudorientale |sea-agentservice-prod-1.azure-automation.net |
| India centrale |cid-agentservice-prod-1.azure-automation.net |
| Giappone |jpe-agentservice-prod-1.azure-automation.net |
| Australia |ase-agentservice-prod-1.azure-automation.net |

Se il computer viene registrato automaticamente come ruolo di lavoro Runbook ibridi, usare la soluzione gestione aggiornamenti per gestire la patch. A tale scopo, seguire questa procedura:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Riavviare il servizio gateway di Log Analytics usando il cmdlet di PowerShell seguente: `Restart-Service OMSGatewayService`

Se il computer è unito in automazione di Azure usando il cmdlet di registrazione di lavoro ibrido per Runbook, seguire questa procedura:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Riavviare il servizio gateway di Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili
È possibile usare i cmdlet per completare le attività per aggiornare le impostazioni di configurazione del gateway Log Analitica. Prima di usare i cmdlet, assicurarsi di:

1. Installare il gateway di Log Analitica (programma di installazione di Microsoft Windows).
1. Aprire una finestra della console di PowerShell.
1. Importare il modulo digitando questo comando: `Import-Module OMSGateway`
1. Se non si verifica alcun errore nel passaggio precedente, il modulo è stato importato ed è possibile usare i cmdlet. Immettere `Get-Module OMSGateway`
1. Dopo aver usato il cmdlet per apportare modifiche, riavviare il servizio Gateway OMS.

Un errore nel passaggio 3 indica che il modulo non è stato importato. L'errore può verificarsi quando PowerShell non è possibile trovare il modulo. È possibile trovare il modulo nel percorso di installazione di Gateway OMS: *C:\Programmi\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametri** | **Descrizione** | **Esempio** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> Valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Indirizzo<br> Username<br> Password |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. Impostare un proxy di inoltro e le credenziali:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Impostare un proxy di inoltro che non richiede autenticazione: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Deselezionare l'impostazione del proxy di inoltro:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Ottiene gli host attualmente consentiti (solo gli host consentiti configurati localmente, non automaticamente scaricati consentito host) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene oggetti del certificato client attualmente consentiti (solo oggetti consentiti di oggetti consentiti configurati localmente, scaricati automaticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>risoluzione dei problemi
Per raccogliere gli eventi registrati dal gateway, si deve avere installato l'agente di Log Analitica.

![Screenshot dell'elenco di Visualizzatore eventi nel log di gateway Log Analitica](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>ID evento gateway Analitica di log e descrizioni

La tabella seguente illustra gli ID evento e le descrizioni degli eventi di log di Log Analitica gateway.

| **ID** | **Descrizione** |
| --- | --- |
| 400 |Qualsiasi errore dell'applicazione che non dispone di alcun ID specifico. |
| 401 |Configurazione errata. Ad esempio, listenPort = "text" anziché un numero intero. |
| 402 |Eccezione durante l'analisi dei messaggi di handshake TLS. |
| 403 |Errore di rete. Ad esempio, non è possibile connettersi al server di destinazione. |
| 100 |Informazioni generali. |
| 101 |Il servizio è stato avviato. |
| 102 |Il servizio è stato arrestato. |
| 103 |È stato ricevuto un comando HTTP CONNECT dal client. |
| 104 |Non è stato ricevuto un comando HTTP CONNECT. |
| 105 |Server di destinazione non è presente nell'elenco elementi consentiti o porta di destinazione non è sicura (443). <br> <br> Assicurarsi che l'agente MMA sul server Gateway OMS e gli agenti che comunicano con OMS Gateway siano connessi alla stessa area di lavoro di Log Analitica. |
| 105 |ERRORE TcpConnection – Certificato client non valido: CN = Gateway. <br><br> Assicurarsi di usare il Gateway OMS versione 1.0.395.0 o versione successiva. Assicurarsi anche che l'agente MMA sul server Gateway OMS e gli agenti che comunicano con OMS Gateway siano connessi alla stessa area di lavoro di Log Analitica. |
| 106 |Versione del protocollo TLS/SSL non supportata.<br><br> Il gateway Log Analitica supporta solo TLS 1.0, TLS 1.1 e 1.2. Non supporta SSL.|
| 107 |La sessione TLS è stata verificata. |

### <a name="performance-counters-to-collect"></a>Contatori delle prestazioni da raccogliere

La tabella seguente mostra i contatori delle prestazioni disponibili per il gateway di Log Analytics. Utilizzare Performance Monitor per aggiungere i contatori.

| **Nome** | **Descrizione** |
| --- | --- |
| Connessione tra il gateway di Log Analytics e il client attivo |Numero di connessioni di rete client attive (TCP) |
| Gateway di Log Analytics/Numero di errori |Numero di errori |
| Gateway di Log Analytics/Client connesso |Numero di client connessi |
| Gateway di Log Analytics/Numero di rifiuti |Numero di rifiuti a causa di errori di convalida TLS |

![Interfaccia a gateway Analitica di schermata del Log che mostra i contatori delle prestazioni](./media/gateway/counters.png)

## <a name="assistance"></a>Assistenza
Quando è connessi al portale di Azure, è possibile ottenere supporto con il gateway Log Analitica o qualsiasi altro servizio di Azure o funzionalità.
Per ottenere assistenza, selezionare l'icona punto interrogativo nell'angolo superiore sinistro del portale e selezionare **nuova richiesta di supporto**. Completare quindi il nuovo modulo di richiesta di supporto.

![Screenshot di una nuova richiesta di supporto](./media/gateway/support.png)

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere le origini dati](../../azure-monitor/platform/agent-data-sources.md) per raccogliere i dati da origini connesse e archiviare i dati nell'area di lavoro di Log Analitica.