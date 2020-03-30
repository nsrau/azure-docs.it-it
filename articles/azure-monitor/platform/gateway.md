---
title: Connettere i computer utilizzando il gateway di Log Analytics Documenti Microsoft
description: Connettere i dispositivi e i computer monitorati da Operations Manager usando il gateway di Log Analytics per inviare dati al servizio Automazione di Azure e Log Analytics quando non dispongono dell'accesso a Internet.Connect your devices and Operations Manager-monitored computers by using the Log Analytics gateway to send data to the Azure Automation and Log Analytics service when they do not have internet access.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298344"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Connettere computer senza accesso a Internet usando il gateway di Log Analytics in Monitoraggio di AzureConnect computers without internet access by using the Log Analytics gateway in Azure Monitor

>[!NOTE]
>Man mano che Microsoft Operations Management Suite (OMS) passa a Microsoft Azure Monitor, la terminologia sta cambiando. Questo articolo fa riferimento a Gateway OMS come gateway di Azure Log Analytics.This article refers to OMS Gateway as the Azure Log Analytics gateway. 
>

Questo articolo descrive come configurare la comunicazione con Automazione di Azure e Monitoraggio di Azure usando il gateway di Log Analytics quando i computer connessi direttamente o monitorati da Operations Manager non dispongono di accesso a Internet.This article describes how to configure communication with Azure Automation and Azure Monitor by using the Log Analytics gateway when computers that are directly connected or that are monitored by Operations Manager have no internet access. 

Il gateway di Log Analytics è un proxy di inoltro HTTP che supporta il tunneling HTTP tramite il comando HTTP CONNECT. Questo gateway invia dati ad Automazione di Azure e a un'area di lavoro Log Analytics in Monitoraggio di Azure per conto dei computer che non possono connettersi direttamente a Internet.This gateway sends data to Azure Automation and a Log Analytics workspace in Azure Monitor per conto dei computer che non possono connettersi direttamente a Internet.This gateway sends data to Azure Automation and a Log Analytics workspace in Azure Monitor per conto dei computer che 

Il gateway di Log Analytics gli elementi seguenti:

* Creazione di report fino alle stesse aree di lavoro di Log Analytics configurate in ogni agente sottostante e configurate con i ruoli di lavoro per runbook ibridi di Azure Automation.Reporting up to the same Log Analytics workspaces configured on each agent behind it and that are configured with Azure Automation Hybrid Runbook Workers.  
* Windows computers on which the Microsoft Monitoring Agent is directly connected to a Log Analytics workspace in Azure Monitor.
* Linux computers on which a Log Analytics agent for Linux is directly connected to a Log Analytics workspace in Azure Monitor.  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3 o un gruppo di gestione in Operations Manager 2016 o versione successiva integrato con Log Analytics.  

Alcuni criteri di sicurezza IT non consentono la connessione a Internet per i computer di rete. Questi computer non connessi potrebbero essere dispositivi POS (POS) o server che supportano i servizi IT, ad esempio. Per connettere questi dispositivi ad Automazione di Azure o a un'area di lavoro di Log Analytics in modo da poterli gestire e monitorare, configurarli per comunicare direttamente con il gateway di Log Analytics.To connect these devices to Azure Automation or a Log Analytics workspace so you can manage and monitor them, configure them to communicate directly with the Log Analytics gateway. Il gateway di Log Analytics può ricevere informazioni di configurazione e inoltrare dati per loro conto. Se i computer sono configurati con l'agente di Log Analytics per connettersi direttamente a un'area di lavoro di Log Analytics, i computer comunicano invece con il gateway di Log Analytics.  

Il gateway di Log Analytics trasferisce direttamente i dati dagli agenti al servizio. Non analizza nessuno dei dati in transito e il gateway non memorizza i dati nella cache quando perde la connettività con il servizio. Quando il gateway non è in grado di comunicare con il servizio, l'agente continua a essere eseguito e accoda i dati raccolti sul disco del computer monitorato. Quando viene ripristinata la connessione, l'agente invia i dati memorizzati nella cache raccolti in Monitoraggio di Azure.When the connection is restored, the agent sends the cached data collected to Azure Monitor.

Quando un gruppo di gestione di Operations Manager è integrato con Log Analytics, i server di gestione possono essere configurati per connettersi al gateway di Log Analytics per ricevere le informazioni di configurazione e inviare i dati raccolti, a seconda della soluzione abilitata .  Gli agenti di Operations Manager inviano alcuni dati al server di gestione. Ad esempio, gli agenti potrebbero inviare avvisi di Operations Manager, dati di valutazione della configurazione, dati relativi allo spazio dell'istanza e dati sulla capacità. Altri dati di volumi elevati, ad esempio i registri di Internet Information Services (IIS), i dati sulle prestazioni e gli eventi di sicurezza, vengono inviati direttamente al gateway di Log Analytics. 

Se uno o più server gateway di Operations Manager vengono distribuiti per monitorare i sistemi non attendibili in una rete perimetrale o in una rete isolata, tali server non possono comunicare con un gateway di Log Analytics.  I server gateway di Operations Manager possono riferire solo a un server di gestione.  Quando un gruppo di gestione di Operations Manager è configurato per comunicare con il gateway di Log Analytics, le informazioni di configurazione del proxy vengono distribuite automaticamente a ogni computer gestito tramite agente configurato per raccogliere i dati di log per Monitoraggio di Azure, anche se l'impostazione è vuota.

Per fornire disponibilità elevata per gruppi di gestione delle operazioni o connessi direttamente che comunicano con un'area di lavoro di Log Analytics tramite il gateway, usare il bilanciamento del carico di rete per reindirizzare e distribuire il traffico tra più server gateway. In questo modo, se un server gateway si arresta, il traffico viene reindirizzato a un altro nodo disponibile.  

Il computer che esegue il gateway di Log Analytics richiede l'agente di Windows di Log Analytics per identificare gli endpoint del servizio con cui il gateway deve comunicare. L'agente deve inoltre indicare al gateway di creare report per le stesse aree di lavoro con cui sono configurati gli agenti o il gruppo di gestione di Operations Manager dietro il gateway. Questa configurazione consente al gateway e all'agente di comunicare con l'area di lavoro assegnata.

Un gateway può essere multihomed fino a quattro aree di lavoro. Questo è il numero totale di aree di lavoro supportate da un agente Windows.  

Ogni agente deve disporre della connettività di rete al gateway in modo che gli agenti possano trasferire automaticamente i dati da e verso il gateway. Evitare di installare il gateway in un controller di dominio. I computer Linux che si trovano dietro un server gateway non possono utilizzare il metodo di installazione dello [script wrapper](agent-linux.md#install-the-agent-using-wrapper-script) per installare l'agente Log Analytics per Linux. L'agente deve essere scaricato manualmente, copiato nel computer e installato manualmente perché il gateway supporta solo la comunicazione con i servizi di Azure menzionati in precedenza.

The following diagram shows data flowing from direct agents, through the gateway, to Azure Automation and Log Analytics. La configurazione del proxy dell'agente deve corrispondere alla porta con cui è configurato il gateway di Log Analytics.  

![Diagramma della comunicazione diretta dell'agente con i servizi](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Il diagramma seguente illustra il flusso di dati da un gruppo di gestione di Operations Manager a Log Analytics.   

![Diagramma della comunicazione di Operations Manager con Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurare il sistema

I computer designati per l'esecuzione del gateway di Log Analytics devono avere la configurazione seguente:

* Windows 10, Windows 8.1 o Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008
* Microsoft .NET Framework 4.5
* Almeno un processore a 4 core e 8 GB di memoria 
* Un [agente di Log Analytics per Windows](agent-windows.md) configurato per segnalare nella stessa area di lavoro gli agenti che comunicano tramite il gateway

### <a name="language-availability"></a>Lingue disponibili

Il gateway di Log Analytics è disponibile nelle lingue seguenti:The Log Analytics gateway is available in these languages:

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

Il gateway di Log Analytics supporta solo Transport Layer Security (TLS) 1.0, 1.1 e 1.2.  Non supporta SSL (Secure Sockets Layer).  Per garantire la sicurezza dei dati in transito a Log Analytics, configurare il gateway per l'utilizzo di almeno TLS 1.2. Le versioni precedenti di TLS o SSL sono vulnerabili. Anche se attualmente consentono la compatibilità con le versioni precedenti, evitare di utilizzarli.  

Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Numero di connessioni dell'agente supportate

Nella tabella seguente viene illustrato approssimativamente il numero di agenti che possono comunicare con un server gateway. Il supporto si basa su agenti che caricano circa 200 KB di dati ogni 6 secondi. Per ogni agente testato, il volume di dati è di circa 2,7 GB al giorno.

|Gateway |Agenti supportati (approssimativi)|  
|--------|----------------------------------|  
|CPU: Processore Intel Xeon E5-2660 v3 \@ 2,6 GHz 2 Core<br> Memoria: 4 GB<br> Larghezza di banda di rete: 1 Gbps| 600|  
|CPU: Processore Intel Xeon E5-2660 v3 \@ 2,6 GHz 4 Core<br> Memoria: 8 GB<br> Larghezza di banda di rete: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Scaricare il gateway di Log Analytics

Ottenere la versione più recente del file di installazione del gateway di Log Analytics dall'Area download Microsoft ( Collegamento per il download ) o dal portale di Azure.Get the latest version of the Log Analytics gateway Setup file from either Microsoft Download Center ([Download Link)](https://go.microsoft.com/fwlink/?linkid=837444)or the Azure portal.

Per ottenere il gateway di Log Analytics dal portale di Azure, eseguire la procedura seguente:To get the Log Analytics gateway from the Azure portal, follow these steps:

1. Esplorare l'elenco dei servizi e quindi selezionare **Log Analytics**. 
1. Selezionare un'area di lavoro.
1. Nel pannello dell'area di lavoro in **Generale** selezionare **Avvio rapido**. 
1. In **Scegliere un'origine dati da connettere all'area di lavoro** selezionare **Computer**.
1. Nel pannello **Agente diretto** selezionare Scarica gateway di **Log Analytics**.
 
   ![Screenshot della procedura per scaricare il gateway di Log Analytics](./media/gateway/download-gateway.png)

o 

1. Nel pannello dell'area di lavoro in **Impostazioni** selezionare **Impostazioni avanzate**.
1. Passare a >  **Origini connesse**Server**Windows** e selezionare Scarica gateway **Log Analytics**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Installare il gateway di Log Analytics tramite l'installazione guidataInstall Log Analytics gateway using setup wizard

Per installare un gateway utilizzando l'installazione guidata, attenersi alla seguente procedura. 

1. Nella cartelle di destinazione fare doppio clic su **Log Analytics gateway.msi**.
1. Nella pagina di **benvenuto** selezionare **Avanti**.

   ![Screenshot della pagina iniziale dell'Installazione guidata gateway](./media/gateway/gateway-wizard01.png)

1. Nella pagina **Contratto** di licenza selezionare **Accetto i termini del Contratto di Licenza** per accettare le Condizioni di licenza software Microsoft e quindi selezionare **Avanti**.
1. Nella pagina **Port and proxy address** (Indirizzo porta e proxy):

   a. Immettere il numero di porta TCP da utilizzare per il gateway. Il programma di installazione utilizza questo numero di porta per configurare una regola in ingresso in Windows Firewall.  Il valore predefinito è 8080.
      L'intervallo valido del numero di porta è compreso tra 1 e 65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.

   b. Se il server in cui è installato il gateway deve comunicare tramite un proxy, immettere l'indirizzo proxy a cui il gateway deve connettersi. Immettere ad esempio `http://myorgname.corp.contoso.com:80`.  Se si lascia vuoto questo campo, il gateway tenterà di connettersi direttamente a Internet.  Se il server proxy richiede l'autenticazione, immettere un nome utente e una password.

   c. Fare clic su **Avanti**.

   ![Screenshot della configurazione per il proxy del gateway](./media/gateway/gateway-wizard02.png)

1. Se Microsoft Update non è abilitato, viene visualizzata la pagina Microsoft Update ed è possibile scegliere di abilitarla. Effettuare una selezione e quindi selezionare **Avanti**. In caso contrario, continuare con il passaggio successivo.
1. Nella pagina Cartella di **destinazione,** lasciare la cartella predefinita C:, Programmi, Gateway OMS o immettere il percorso in cui si desidera installare il gateway. Quindi selezionare **Avanti**.
1. Nella pagina **Pronto per l'installazione** selezionare **Installa**. Se Controllo account utente richiede l'autorizzazione per l'installazione, selezionare **Sì**.
1. Al termine dell'installazione, selezionare **Fine**. Per verificare che il servizio sia in esecuzione, aprire lo snap-in services.msc e verificare che **Gateway OMS** sia visualizzato nell'elenco dei servizi e che il relativo stato sia **In esecuzione.**

   ![Screenshot dei servizi locali che mostra che Il gateway OMS è in esecuzione](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Installare il gateway di Log Analytics tramite la riga di comando

Il file scaricato per il gateway è un pacchetto di Windows Installer che supporta l'installazione invisibile all'utente dalla riga di comando o da un altro metodo automatizzato. Se non si ha familiarità con le opzioni della riga di comando standard per Windows Installer, vedere [Opzioni della riga di comando](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
Nella tabella seguente vengono evidenziati i parametri supportati dall'installazione.

|Parametri| Note|
|----------|------| 
|Portnumber | Numero di porta TCP su cui il gateway può essere in ascolto |
|Proxy | Indirizzo IP del server proxy |
|INSTALLDIR | Percorso completo per specificare la directory di installazione dei file del software gateway |
|USERNAME | ID utente per l'autenticazione con il server proxy |
|PASSWORD | Password dell'ID utente per l'autenticazione con proxy |
|LicenseAccepted | Specificare il valore **1** per verificare di accettare il contratto di licenza |
|HASAUTH | Specificare il valore **1** quando vengono specificati i parametri USERNAME/PASSWORD |
|HASPROXY | Specificare un valore pari a **1** quando si specifica l'indirizzo IP per il parametro **PROXY** |

Per installare automaticamente il gateway e configurarlo con un indirizzo proxy specifico, il numero di porta, digitare quanto segue:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

L'utilizzo dell'opzione della riga di comando /qn nasconde l'installazione, /qb mostra il programma di installazione durante l'installazione invisibile all'utente.  

Se è necessario fornire le credenziali per l'autenticazione con il proxy, digitare quanto segue:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Dopo l'installazione, è possibile confermare che le impostazioni vengono accettate (esclusi il nome utente e la password) utilizzando i cmdlet di PowerShell seguenti:

- **Get-OMSGatewayConfig:** restituisce la porta TCP su cui è configurato il gateway.
- **Get-OMSGatewayRelayProxy:** restituisce l'indirizzo IP del server proxy configurato per la comunicazione.

## <a name="configure-network-load-balancing"></a>Configurare il bilanciamento del carico di rete

È possibile configurare il gateway per la disponibilità elevata utilizzando il bilanciamento del carico di rete (NLB) utilizzando Microsoft [Network Load Balancing (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)o servizi di bilanciamento del carico basati su hardware. Il servizio di bilanciamento del carico gestisce il traffico reindirizzando le connessioni richieste dagli agenti di Log Analytics o dai server di gestione di Operations Manager nei nodi relativi. Se un server gateway si arresta, il traffico viene reindirizzato ad altri nodi.

### <a name="microsoft-network-load-balancing"></a>Bilanciamento carico di rete Microsoft

Per informazioni su come progettare e distribuire un cluster di bilanciamento del carico di rete di Windows Server 2016, vedere [Bilanciamento carico di rete](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). La procedura seguente illustra come configurare un cluster di bilanciamento del carico di rete Microsoft.  

1. Accedere al server Windows membro del cluster di bilanciamento del carico di rete con un account amministrativo.  
2. Aprire Gestione bilanciamento carico di rete in Server Manager, fare clic su **Strumenti** e quindi su **Gestione bilanciamento carico di rete**.
3. Per connettere un server gateway di Log Analytics all'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi fare clic su **Aggiungi host al cluster**. 

    ![Gestione bilanciamento carico di rete - Aggiungi host al cluster](./media/gateway/nlb02.png)
 
4. Immettere l'indirizzo IP del server gateway che si vuole connettere. 

    ![Gestione del bilanciamento del carico di rete – Aggiungi host al cluster: Connetti](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Per informazioni su come progettare e distribuire azure Load Balancer, vedere [Che cos'è Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Per distribuire un servizio di bilanciamento del carico di base, seguire i passaggi descritti in questa [guida introduttiva,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) escludendo i passaggi descritti nella sezione **Creare server back-end**.   

> [!NOTE]
> La configurazione del servizio di bilanciamento del carico di Azure tramite lo **SKU di base**richiede che le macchine virtuali di Azure appartengano a un set di disponibilità. Per altre informazioni sui set di disponibilità, vedere Gestire la disponibilità delle macchine virtuali Windows in Azure.To learn more about availability sets, see [Manage the availability of Windows virtual machines in Azure.](../../virtual-machines/windows/manage-availability.md) Per aggiungere macchine virtuali esistenti a un set di disponibilità, vedere [Impostare il set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)di disponibilità della macchina virtuale di Azure Resource Manager.To add existing virtual machines to an availability set, refer to Set Azure Resource Manager VM Availability Set .
> 

Dopo aver creato il servizio di bilanciamento del carico, è necessario creare un pool back-end, che distribuisce il traffico a uno o più server gateway. Seguire i passaggi descritti nella sezione Dell'articolo della Guida introduttiva relativa alla creazione di risorse per il servizio di [bilanciamento del carico.](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)  

>[!NOTE]
>Quando si configura il probe di integrità, è necessario configurarlo per l'utilizzo della porta TCP del server gateway. Il probe di integrità aggiunge o rimuove dinamicamente i server gateway dalla rotazione del bilanciamento del carico in base alla risposta ai controlli di integrità. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurare l'agente di Log Analytics e il gruppo di gestione di Operations Manager

In this section, you'll see how to configure directly connected Log Analytics agents, an Operations Manager management group, or Azure Automation Hybrid Runbook Workers with the Log Analytics gateway to communicate with Azure Automation or Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurare un agente di Log Analytics autonomoConfigure a standalone Log Analytics agent

Quando si configura l'agente di Log Analytics, sostituire il valore del server proxy con l'indirizzo IP del server gateway di Log Analytics e il relativo numero di porta. Se sono stati distribuiti più server gateway dietro un servizio di bilanciamento del carico, la configurazione del proxy dell'agente di Log Analytics è l'indirizzo IP virtuale del servizio di bilanciamento del carico.  

>[!NOTE]
>Per installare l'agente di Log Analytics nel gateway e nei computer Windows che si connettono direttamente a Log Analytics, vedere [Connettere computer Windows al servizio Log Analytics in Azure.](agent-windows.md) Per connettere computer Linux, vedere [Connettere computer Linux ad Azure Monitor.](agent-linux.md) 
>

Dopo aver installato l'agente nel server gateway, configurarlo per la creazione di report per gli agenti dell'area di lavoro o dell'area di lavoro che comunicano con il gateway. Se l'agente Windows di Log Analytics non è installato nel gateway, l'evento 300 viene scritto nel registro eventi del gateway di OMS, a indicare che l'agente deve essere installato. Se l'agente è installato ma non configurato per creare report nella stessa area di lavoro degli agenti che comunicano attraverso di esso, l'evento 105 viene scritto nello stesso registro, a indicare che l'agente nel gateway deve essere configurato per il report nella stessa area di lavoro degli agenti che comunicare con il gateway.

Dopo aver completato la configurazione, riavviare il servizio **Gateway OMS** per applicare le modifiche. In caso contrario, il gateway rifiuterà gli agenti che tentano di comunicare con Log Analytics e segnalerà l'evento 105 nel registro eventi del gateway OMS. Ciò si verifica anche quando si aggiunge o rimuove un'area di lavoro dalla configurazione dell'agente nel server gateway.

Per informazioni relative all'Automation Hybrid Runbook Worker, vedere [Automatizzare le risorse nel data center o nel cloud utilizzando Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurare Operations Manager, in cui tutti gli agenti utilizzano lo stesso server proxy

La configurazione del proxy di Operations Manager viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager, anche se l'impostazione è vuota.  

Per utilizzare Gateway OMS per supportare Operations Manager, è necessario disporre di:

* Microsoft Monitoring Agent (versione 8.0.10900.0 o successiva) installato nel server Gateway OMS e configurato con le stesse aree di lavoro di Log Analytics a cui il gruppo di gestione è configurato per la creazione di report.
* Connettività Internet. In alternativa, OMS Gateway deve essere connesso a un server proxy connesso a Internet.

> [!NOTE]
> Se non si specifica alcun valore per il gateway, i valori vuoti vengono inviati a tutti gli agenti.
>

Se il gruppo di gestione di Operations Manager esegue la registrazione con un'area di lavoro di Log Analytics per la prima volta, non verrà visualizzata l'opzione per specificare la configurazione proxy per il gruppo di gestione nella Console operatore. Questa opzione è disponibile solo se il gruppo di gestione è stato registrato con il servizio.  

Per configurare l'integrazione, aggiornare la configurazione del proxy di sistema utilizzando Netsh nel sistema in cui si esegue la Console operatore e in tutti i server di gestione nel gruppo di gestione. A tale scopo, seguire questa procedura:

1. Aprire un prompt dei comandi con privilegi elevati:Open an elevated command prompt:

   a. Selezionare **Start** e immettere **cmd**.  

   b. Fare clic con il pulsante destro del mouse su **Prompt dei comandi** e selezionare Esegui come **amministratore**.  

1. Immettere il comando seguente:

   `netsh winhttp set proxy <proxy>:<port>`

Dopo aver completato l'integrazione con Log `netsh winhttp reset proxy`Analytics, rimuovere la modifica eseguendo . Quindi, nella Console operatore, utilizzare l'opzione **Configura server proxy** per specificare il server gateway di Log Analytics. 

1. Nella console di Operations Manager, in **Operations Management Suite**, selezionare **Connessione**, quindi Configura **server proxy**.

   ![Screenshot di Operations Manager che mostra la selezione Configura server proxy](./media/gateway/scom01.png)

1. Selezionare **Usa un server proxy per accedere a Operations Management Suite,** quindi immettere l'indirizzo IP del server gateway di Log Analytics o l'indirizzo IP virtuale del servizio di bilanciamento del carico. Fare attenzione a iniziare `http://`con il prefisso .

   ![Screenshot di Operations Manager che mostra l'indirizzo del server proxy](./media/gateway/scom02.png)

1. Fare clic su **Fine**. Il gruppo di gestione di Operations Manager è ora configurato per comunicare tramite il server gateway con il servizio Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurare Operations Manager, in cui agenti specifici utilizzano un server proxy

Per ambienti complessi o di grandi dimensioni, è possibile utilizzare il server gateway di Log Analytics solo server specifici o gruppi.  Per questi server, non è possibile aggiornare direttamente l'agente Operations Manager perché questo valore viene sovrascritto dal valore globale per il gruppo di gestione.  Eseguire invece l'override della regola utilizzata per eseguire il push di questi valori.  

> [!NOTE] 
> Utilizzare questa tecnica di configurazione se si desidera consentire più server gateway di Log Analytics nell'ambiente.  Ad esempio, è possibile richiedere che specifici server gateway di Log Analytics vengano specificati su base regionale.
>

Per configurare server o gruppi specifici per l'utilizzo del server gateway di Log Analytics: 

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Creazione**.  
1. Nell'area di lavoro Creazione selezionare **Regole**. 
1. Nella barra degli strumenti di Operations Manager, selezionare il pulsante **Ambito.** Se questo pulsante non è disponibile, assicurarsi di aver selezionato un oggetto, non una cartella, nel riquadro **Monitoraggio.** La finestra di dialogo **Crea ambito oggetti Management Pack** visualizza un elenco di classi, gruppi o oggetti di destinazione comuni. 
1. Nel campo **Cerca** immettere **Servizio sanitario** e selezionarlo dall'elenco. Selezionare **OK**.  
1. Cercare la regola di **impostazione del proxy dell'advisor**. 
1. Nella barra degli strumenti di Operations Manager, selezionare **Sostituzioni,** quindi scegliere **Sostituisci regola - Per un oggetto specifico della classe: Servizio integrità** e selezionare un oggetto dall'elenco.  In alternativa, creare un gruppo personalizzato contenente l'oggetto servizio integrità dei server a cui si desidera applicare questa sostituzione. Applicare quindi la sostituzione al gruppo personalizzato.
1. Nella finestra di dialogo **Proprietà di sostituzione** aggiungere un segno di spunta nella colonna **Sostituisci** accanto al parametro **WebProxyAddress.**  Nel campo **Sostituisci valore** immettere l'URL del server gateway di Log Analytics. Fare attenzione a iniziare `http://`con il prefisso .  

    >[!NOTE]
    > Non è necessario attivare la regola. È già gestito automaticamente con una sostituzione nel Management Pack Microsoft System Center Advisor Secure Reference Override destinato al gruppo Monitoring Server di Microsoft System Center Advisor.
    > 

1. Selezionare un Management Pack dall'elenco Seleziona Management Pack di **destinazione oppure** creare un nuovo Management Pack non bloccato selezionando **Nuovo**. 
1. Al termine, selezionare **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurare per i ruoli di runbook ibridi di automazioneConfigure for Automation Hybrid Runbook Workers

Se si dispone di componenti di lavoro Runbook ibridi di automazione nel proprio ambiente, attenersi alla seguente procedura per configurare il gateway per supportare i ruoli di lavoro.

Fare riferimento alla sezione [Configurare la rete](../../automation/automation-hybrid-runbook-worker.md#network-planning) della documentazione di automazione per trovare l'URL per ogni area.

Se il computer è registrato automaticamente come lavoratore ibrido per runbook, ad esempio se la soluzione di gestione degli aggiornamenti è abilitata per una o più macchine virtuali, attenersi alla seguente procedura:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Riavviare il servizio gateway di Log Analytics usando il cmdlet di PowerShell seguente: `Restart-Service OMSGatewayService`

Se il computer viene aggiunto ad Automazione di Azure usando il cmdlet di registrazione Hybrid Runbook Worker, attenersi alla seguente procedura:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Riavviare il servizio gateway di Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili

È possibile utilizzare i cmdlet per completare le attività per aggiornare le impostazioni di configurazione del gateway di Log Analytics. Prima di utilizzare i cmdlet, assicurarsi di:

1. Installare il gateway Log Analytics (Microsoft Windows Installer).
1. Aprire una finestra della console di PowerShell.
1. Importare il modulo digitando questo comando:`Import-Module OMSGateway`
1. Se non si verifica alcun errore nel passaggio precedente, il modulo è stato importato ed è possibile usare i cmdlet. Immettere `Get-Module OMSGateway`
1. Dopo aver utilizzato i cmdlet per apportare modifiche, riavviare il servizio Gateway di OMS.

Un errore nel passaggio 3 indica che il modulo non è stato importato. L'errore potrebbe verificarsi quando PowerShell non riesce a trovare il modulo. È possibile trovare il modulo nel percorso di installazione di OMS Gateway: *C:*.

| **Cmdlet** | **Parametri** | **Descrizione** | **Esempio** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Indirizzo<br> Username<br> Password (stringa protetta) |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. Impostare un proxy di inoltro e le credenziali:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Impostare un proxy di inoltro che non necessita dell'autenticazione:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Cancellare l'impostazione del proxy di inoltro:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Ottiene l'host attualmente consentito (solo l'host consentito configurato localmente, non gli host consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene i soggetti del certificato client attualmente consentiti (solo i soggetti consentiti configurati localmente, non i soggetti consentiti scaricati automaticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per raccogliere gli eventi registrati dal gateway, è necessario che sia installato l'agente di Log Analytics.To collect events logged by the gateway, you should have the Log Analytics agent installed.

![Screenshot dell'elenco del Visualizzatore eventi nel registro del gateway di Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>ID e descrizioni degli eventi del gateway di Log Analytics

Nella tabella seguente vengono illustrati gli ID evento e le descrizioni per gli eventi del log del gateway di Log Analytics.

| **Id** | **Descrizione** |
| --- | --- |
| 400 |Qualsiasi errore dell'applicazione che non ha un ID specifico. |
| 401 |Configurazione errata. Ad esempio, listenPort - "text" anziché un numero intero. |
| 402 |Eccezione durante l'analisi dei messaggi di handshake TLS. |
| 403 |Errore di rete. Ad esempio, non è possibile connettersi al server di destinazione. |
| 100 |Informazioni generali. |
| 101 |Il servizio è stato avviato. |
| 102 |Il servizio è stato arrestato. |
| 103 |È stato ricevuto un comando HTTP CONNECT dal client. |
| 104 |Non è stato ricevuto un comando HTTP CONNECT. |
| 105 |Il server di destinazione non è presente nell'elenco dei server consentiti o la porta di destinazione non è protetta (443). <br> <br> Verificare che l'agente MMA nel server gateway OMS e gli agenti che comunicano con Gateway OMS siano connessi alla stessa area di lavoro di Log Analytics. |
| 105 |ERRORE TcpConnection – Certificato client non valido: CN - Gateway. <br><br> Assicurarsi di utilizzare OMS Gateway versione 1.0.395.0 o successiva. Assicurarsi inoltre che l'agente MMA sul server Gateway OMS e gli agenti che comunicano con Gateway OMS siano connessi alla stessa area di lavoro di Log Analytics. |
| 106 |Versione del protocollo TLS/SSL non supportata.<br><br> Il gateway di Log Analytics supporta solo TLS 1.0, TLS 1.1 e 1.2. Non supporta SSL.|
| 107 |La sessione TLS è stata verificata. |

### <a name="performance-counters-to-collect"></a>Contatori delle prestazioni da raccogliere

La tabella seguente mostra i contatori delle prestazioni disponibili per il gateway di Log Analytics. Utilizzare Performance Monitor per aggiungere i contatori.

| **Nome** | **Descrizione** |
| --- | --- |
| Connessione tra il gateway di Log Analytics e il client attivo |Numero di connessioni di rete client attive (TCP) |
| Gateway di Log Analytics/Numero di errori |Numero di errori |
| Gateway di Log Analytics/Client connesso |Numero di client connessi |
| Gateway di Log Analytics/Numero di rifiuti |Numero di rifiuti a causa di errori di convalida TLS |

![Screenshot dell'interfaccia del gateway di Log Analytics che mostra i contatori delle prestazioni](./media/gateway/counters.png)

## <a name="assistance"></a>Assistenza

Dopo aver eseguito l'accesso al portale di Azure, è possibile ottenere assistenza per il gateway di Log Analytics o qualsiasi altra funzionalità o servizio di Azure.When you're signed in to the Azure portal, you can get help with the Log Analytics gateway or any other Azure service or feature.
Per ottenere assistenza, selezionare l'icona del punto interrogativo nell'angolo superiore destro del portale e selezionare **Nuova richiesta**di supporto . Compilare quindi il nuovo modulo di richiesta di supporto.

![Screenshot di una nuova richiesta di supporto](./media/gateway/support.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere origini dati](../../azure-monitor/platform/agent-data-sources.md) per raccogliere dati da origini connesse e archiviare i dati nell'area di lavoro di Log Analytics.Add data sources to collect data from connected sources, and store the data in your Log Analytics workspace.
