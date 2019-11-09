---
title: Connettere i computer tramite il gateway Log Analytics | Microsoft Docs
description: Connettere i dispositivi e i computer monitorati da Operations Manager usando il gateway Log Analytics per inviare i dati al servizio automazione di Azure e Log Analytics quando non hanno accesso a Internet.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/30/2019
ms.openlocfilehash: 7574f5c17c1b4598336b8db3108946164dc203f2
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847273"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Connettere i computer senza accesso a Internet usando il gateway Log Analytics in monitoraggio di Azure

>[!NOTE]
>Quando Microsoft Operations Management Suite (OMS) passa a Microsoft Azure monitoraggio, la terminologia sta cambiando. Questo articolo si riferisce al gateway OMS come gateway di Azure Log Analytics. 
>

Questo articolo descrive come configurare la comunicazione con automazione di Azure e monitoraggio di Azure usando il gateway Log Analytics quando i computer connessi direttamente o monitorati da Operations Manager non hanno accesso a Internet. 

Il gateway Log Analytics è un proxy di inoltro HTTP che supporta il tunneling HTTP tramite il comando HTTP CONNECT. Questo gateway invia dati ad automazione di Azure e un'area di lavoro Log Analytics in monitoraggio di Azure per conto dei computer che non possono connettersi direttamente a Internet. 

Il gateway di Log Analytics gli elementi seguenti:

* Creazione di report per le stesse aree di lavoro Log Analytics configurate in ogni agente sottostante e configurate con i ruoli di lavoro ibridi per Runbook di automazione di Azure.  
* Computer Windows in cui il Microsoft Monitoring Agent è direttamente connesso a un'area di lavoro Log Analytics in monitoraggio di Azure.
* Computer Linux in cui un agente di Log Analytics per Linux è direttamente connesso a un'area di lavoro Log Analytics in monitoraggio di Azure.  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3 o un gruppo di gestione in Operations Manager 2016 o versione successiva integrato con Log Analytics.  

Alcuni criteri di sicurezza IT non consentono la connessione Internet per i computer di rete. Questi computer non connessi potrebbero essere dispositivi POS o server che supportano servizi IT, ad esempio. Per connettere questi dispositivi ad automazione di Azure o a un'area di lavoro Log Analytics per gestirli e monitorarli, configurarli in modo che possano comunicare direttamente con il gateway di Log Analytics. Il gateway Log Analytics può ricevere informazioni di configurazione e trasmettere i dati per loro conto. Se i computer sono configurati con l'agente di Log Analytics per connettersi direttamente a un'area di lavoro Log Analytics, i computer comunicano con il gateway di Log Analytics.  

Il gateway Log Analytics trasferisce direttamente i dati dagli agenti al servizio. Non analizza i dati in transito e il gateway non memorizza nella cache i dati in caso di perdita della connettività con il servizio. Quando il gateway non è in grado di comunicare con il servizio, l'agente continua l'esecuzione e accoda i dati raccolti sul disco del computer monitorato. Quando la connessione viene ripristinata, l'agente invia i dati memorizzati nella cache raccolti a monitoraggio di Azure.

Quando un gruppo di gestione Operations Manager è integrato con Log Analytics, i server di gestione possono essere configurati per la connessione al gateway Log Analytics per ricevere informazioni di configurazione e inviare dati raccolti, a seconda della soluzione abilitata .  Operations Manager agenti inviano alcuni dati al server di gestione. Ad esempio, gli agenti potrebbero inviare Operations Manager avvisi, dati di valutazione della configurazione, dati dello spazio dell'istanza e dati sulla capacità. Altri dati di volumi elevati, ad esempio i log Internet Information Services (IIS), i dati sulle prestazioni e gli eventi di sicurezza, vengono inviati direttamente al gateway di Log Analytics. 

Se uno o più server gateway Operations Manager vengono distribuiti per monitorare i sistemi non attendibili in una rete perimetrale o in una rete isolata, questi server non possono comunicare con un gateway di Log Analytics.  I server gateway Operations Manager possono segnalare solo a un server di gestione.  Quando un gruppo di gestione di Operations Manager è configurato per comunicare con il gateway di Log Analytics, le informazioni di configurazione del proxy vengono automaticamente distribuite a ogni computer gestito tramite agente configurato per raccogliere i dati di log per monitoraggio di Azure. anche se l'impostazione è vuota.

Per garantire un'elevata disponibilità per i gruppi connessi direttamente o di Operations Management che comunicano con un'area di lavoro Log Analytics tramite il gateway, usare bilanciamento carico di rete per reindirizzare e distribuire il traffico tra più server gateway. In questo modo, se un server gateway diventa inattivo, il traffico viene reindirizzato a un altro nodo disponibile.  

Il computer su cui è in esecuzione il gateway Log Analytics richiede che l'agente di Log Analytics Windows identifichi gli endpoint di servizio con cui il gateway deve comunicare. L'agente deve anche indirizzare il gateway per segnalare alle stesse aree di lavoro in cui sono configurati gli agenti o il gruppo di gestione Operations Manager dietro il gateway. Questa configurazione consente al gateway e all'agente di comunicare con l'area di lavoro assegnata.

Un gateway può essere multihomed per un massimo di quattro aree di lavoro. Il numero totale di aree di lavoro supportate da un agente Windows.  

Ogni agente deve disporre di connettività di rete al gateway in modo che gli agenti possano trasferire automaticamente i dati da e verso il gateway. Evitare di installare il gateway in un controller di dominio.

Il diagramma seguente mostra il flusso di dati dagli agenti diretti, tramite il gateway, ad automazione di Azure e Log Analytics. La configurazione del proxy dell'agente deve corrispondere alla porta con cui è configurato il gateway Log Analytics.  

![Diagramma della comunicazione diretta degli agenti con i servizi](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Il diagramma seguente illustra il flusso di dati da un gruppo di gestione di Operations Manager a Log Analytics.   

![Diagramma della comunicazione Operations Manager con Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurare il sistema

I computer designati per l'esecuzione del gateway Log Analytics devono disporre della configurazione seguente:

* Windows 10, Windows 8.1 o Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008
* Microsoft .NET Framework 4.5
* Almeno un processore a 4 core e 8 GB di memoria 
* Un [agente log Analytics per Windows](agent-windows.md) configurato per la segnalazione alla stessa area di lavoro degli agenti che comunicano tramite il gateway

### <a name="language-availability"></a>Lingue disponibili

Il gateway Log Analytics è disponibile nelle lingue seguenti:

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

Il gateway Log Analytics supporta solo Transport Layer Security (TLS) 1,0, 1,1 e 1,2.  Non supporta Secure Sockets Layer (SSL).  Per garantire la sicurezza dei dati in transito per Log Analytics, configurare il gateway per l'uso di almeno TLS 1,2. Le versioni precedenti di TLS o SSL sono vulnerabili. Sebbene consentano attualmente la compatibilità con le versioni precedenti, evitarne l'uso.  

Per altre informazioni, vedere [Invio dei dati in modo sicuro tramite TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Numero di connessioni dell'agente supportate

Nella tabella seguente viene illustrato approssimativamente il numero di agenti che è in grado di comunicare con un server gateway. Il supporto è basato su agenti che caricano circa 200 KB di dati ogni 6 secondi. Per ogni agente testato, il volume di dati è di circa 2,7 GB al giorno.

|Gateway |Agenti supportati (approssimativa)|  
|--------|----------------------------------|  
|CPU: processore Intel Xeon E5-2660 V3 \@ 2,6 GHz 2 Core<br> Memoria: 4 GB<br> Larghezza di banda di rete: 1 Gbps| 600|  
|CPU: processore Intel Xeon E5-2660 V3 \@ 2,6 GHz 4 core<br> Memoria: 8 GB<br> Larghezza di banda di rete: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Scaricare il gateway di Log Analytics

Ottenere la versione più recente del file di installazione del gateway Log Analytics dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=54443) o dalla portale di Azure.

Per ottenere il gateway Log Analytics dal portale di Azure, seguire questa procedura:

1. Esplorare l'elenco dei servizi e quindi selezionare **Log Analytics**. 
1. Selezionare un'area di lavoro.
1. Nel pannello dell'area di lavoro in **Generale** selezionare **Avvio rapido**. 
1. In **Scegliere un'origine dati da connettere all'area di lavoro** selezionare **Computer**.
1. Nel pannello **agente diretto** selezionare **Scarica log Analytics gateway**.
 
   ![Screenshot dei passaggi per scaricare il gateway Log Analytics](./media/gateway/download-gateway.png)

oppure 

1. Nel pannello dell'area di lavoro in **Impostazioni** selezionare **Impostazioni avanzate**.
1. Passare a **origini connesse** > **server Windows** e selezionare **Scarica log Analytics gateway**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Installare Log Analytics gateway mediante installazione guidata

Per installare un gateway usando l'installazione guidata, seguire questa procedura. 

1. Nella cartelle di destinazione fare doppio clic su **Log Analytics gateway.msi**.
1. Nella pagina di **benvenuto** selezionare **Avanti**.

   ![Screenshot della pagina iniziale nell'installazione guidata del gateway](./media/gateway/gateway-wizard01.png)

1. Nella pagina **contratto di licenza** selezionare Accetto **i termini del contratto di licenza** per accettare le condizioni di licenza software Microsoft e quindi fare clic su **Avanti**.
1. Nella pagina **Port and proxy address** (Indirizzo porta e proxy):

   a. Immettere il numero di porta TCP da usare per il gateway. Il programma di installazione utilizza questo numero di porta per configurare una regola in ingresso in Windows Firewall.  Il valore predefinito è 8080.
      L'intervallo valido del numero di porta è compreso tra 1 e 65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.

   b. Se il server in cui è installato il gateway deve comunicare tramite un proxy, immettere l'indirizzo proxy a cui il gateway deve connettersi. Ad esempio, immettere `http://myorgname.corp.contoso.com:80`.  Se si lascia vuoto questo campo, il gateway tenterà di connettersi direttamente a Internet.  Se il server proxy richiede l'autenticazione, immettere un nome utente e una password.

   c. Selezionare **Avanti**.

   ![Screenshot della configurazione per il proxy del gateway](./media/gateway/gateway-wizard02.png)

1. Se Microsoft Update non è abilitato, viene visualizzata la pagina Microsoft Update ed è possibile scegliere di abilitarla. Effettuare una selezione e quindi fare clic su **Avanti**. In caso contrario, continuare con il passaggio successivo.
1. Nella pagina **cartella di destinazione** , lasciare la cartella predefinita C:\Program c:\programmi\oms gateway o immettere il percorso in cui si vuole installare il gateway. Quindi selezionare **Avanti**.
1. Nella pagina **Pronto per l'installazione** selezionare **Installa**. Se il controllo dell'account utente richiede l'autorizzazione per l'installazione, selezionare **Sì**.
1. Al termine dell'installazione, fare clic su **fine**. Per verificare che il servizio sia in esecuzione, aprire lo snap-in Services. msc e verificare che il **gateway OMS** sia visualizzato nell'elenco dei servizi e che lo stato sia in **esecuzione**.

   ![Screenshot dei servizi locali, che indica che il gateway OMS è in esecuzione](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Installare il gateway Log Analytics tramite la riga di comando
Il file scaricato per il gateway è un pacchetto di Windows Installer che supporta l'installazione invisibile all'utente dalla riga di comando o da un altro metodo automatico. Se non si ha familiarità con le opzioni della riga di comando standard per Windows Installer, vedere [Opzioni della riga di comando](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
Nella tabella seguente vengono evidenziati i parametri supportati dal programma di installazione.

|Parametri| Note|
|----------|------| 
|NumeroPorta | Numero di porta TCP su cui è in ascolto il gateway |
|PROXY | Indirizzo IP del server proxy |
|INSTALLDIR | Percorso completo per specificare la directory di installazione dei file software del gateway |
|NOME utente | ID utente da autenticare con il server proxy |
|PASSWORD | Password dell'ID utente da autenticare con il proxy |
|LicenseAccepted | Specificare il valore **1** per verificare l'accettazione del contratto di licenza |
|HASAUTH | Specificare il valore **1** quando si specificano i parametri di nome utente/password |
|HASPROXY | Specificare il valore **1** quando si specifica l'indirizzo IP per il parametro **proxy** |

Per installare automaticamente il gateway e configurarlo con un indirizzo proxy specifico, numero di porta, digitare quanto segue:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Se si usa l'opzione della riga di comando/qn,/QB Visualizza il programma di installazione durante l'installazione invisibile all'utente.  

Se è necessario fornire le credenziali per l'autenticazione con il proxy, digitare quanto segue:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Dopo l'installazione, è possibile verificare che le impostazioni siano accettate (stato introdotto bisonte il nome utente e la password) usando i cmdlet di PowerShell seguenti:

- **Get-OMSGatewayConfig** : restituisce la porta TCP su cui è configurato il gateway per l'ascolto.
- **Get-OMSGatewayRelayProxy** : restituisce l'indirizzo IP del server proxy con cui è stata configurata la comunicazione.

## <a name="configure-network-load-balancing"></a>Configurare il bilanciamento del carico di rete 
È possibile configurare il gateway per la disponibilità elevata utilizzando bilanciamento carico di rete (NLB) utilizzando bilanciamento carico di [rete](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)Microsoft, [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)o i bilanciamenti del carico basati su hardware. Il servizio di bilanciamento del carico gestisce il traffico reindirizzando le connessioni richieste dagli agenti di Log Analytics o dai server di gestione di Operations Manager nei nodi relativi. Se un server gateway si arresta, il traffico viene reindirizzato ad altri nodi.

### <a name="microsoft-network-load-balancing"></a>Bilanciamento carico di rete Microsoft
Per informazioni su come progettare e distribuire un cluster di bilanciamento del carico di rete di Windows Server 2016, vedere [Bilanciamento carico di rete](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). La procedura seguente illustra come configurare un cluster di bilanciamento del carico di rete Microsoft.  

1. Accedere al server Windows membro del cluster di bilanciamento del carico di rete con un account amministrativo.  
2. Aprire Gestione bilanciamento carico di rete in Server Manager, fare clic su **Strumenti** e quindi su **Gestione bilanciamento carico di rete**.
3. Per connettere un server gateway di Log Analytics all'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi fare clic su **Aggiungi host al cluster**. 

    ![Gestione bilanciamento carico di rete-Aggiungi host al cluster](./media/gateway/nlb02.png)
 
4. Immettere l'indirizzo IP del server gateway che si vuole connettere. 

    ![Gestione del bilanciamento del carico di rete – Aggiungi host al cluster: Connetti](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Servizio di bilanciamento del carico di Azure
Per informazioni su come progettare e distribuire un Azure Load Balancer, vedere [che cos'è Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Per distribuire un servizio di bilanciamento del carico di base, seguire i passaggi illustrati in questa [Guida introduttiva](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) , esclusi i passaggi descritti nella sezione **creare server back-end**.   

> [!NOTE]
> Per configurare la Azure Load Balancer usando lo **SKU Basic**, è necessario che le macchine virtuali di Azure appartengano a un set di disponibilità. Per altre informazioni sui set di disponibilità, vedere [gestire la disponibilità delle macchine virtuali Windows in Azure](../../virtual-machines/windows/manage-availability.md). Per aggiungere macchine virtuali esistenti a un set di disponibilità, fare riferimento a [imposta Azure Resource Manager set di disponibilità della macchina](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)virtuale.
> 

Dopo aver creato il servizio di bilanciamento del carico, è necessario creare un pool back-end che distribuisce il traffico a uno o più server gateway. Seguire i passaggi descritti nella sezione dell'articolo introduttivo [creare risorse per il servizio di bilanciamento del carico](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Quando si configura il probe di integrità, è necessario configurarlo per l'utilizzo della porta TCP del server gateway. Il probe di integrità aggiunge o rimuove in modo dinamico i server gateway dalla rotazione del servizio di bilanciamento del carico in base alla risposta ai controlli di integrità. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurare l'agente di Log Analytics e il gruppo di gestione Operations Manager
In questa sezione viene illustrato come configurare gli agenti Log Analytics connessi direttamente, un gruppo di gestione Operations Manager o i ruoli di lavoro ibridi per Runbook di automazione di Azure con il gateway di Log Analytics per comunicare con automazione di Azure o Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurare un agente di Log Analytics autonomo
Quando si configura l'agente di Log Analytics, sostituire il valore del server proxy con l'indirizzo IP del server gateway Log Analytics e il relativo numero di porta. Se sono stati distribuiti più server gateway dietro un servizio di bilanciamento del carico, la configurazione del proxy di Log Analytics Agent è l'indirizzo IP virtuale del servizio di bilanciamento del carico.  

>[!NOTE]
>Per installare l'agente di Log Analytics sul gateway e sui computer Windows che si connettono direttamente a Log Analytics, vedere [connettere computer Windows al servizio log Analytics in Azure](agent-windows.md). Per connettere i computer Linux, vedere [configurare un agente log Analytics per computer Linux in un ambiente ibrido](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Dopo aver installato l'agente nel server gateway, configurarlo in modo che venga segnalato all'area di lavoro o agli agenti dell'area di lavoro che comunicano con il gateway. Se il Log Analytics agente Windows non è installato nel gateway, l'evento 300 viene scritto nel registro eventi del gateway OMS, a indicare che l'agente deve essere installato. Se l'agente è installato ma non configurato per la segnalazione alla stessa area di lavoro degli agenti che comunicano tramite esso, l'evento 105 viene scritto nello stesso log, a indicare che l'agente sul gateway deve essere configurato per segnalare la stessa area di lavoro degli agenti che co mmunicate con il gateway.

Al termine della configurazione, riavviare il servizio gateway OMS per applicare le modifiche. In caso contrario, il gateway rifiuterà gli agenti che tentano di comunicare con Log Analytics e segnalerà l'evento 105 nel registro eventi del gateway OMS. Questo problema si verifica anche quando si aggiunge o rimuove un'area di lavoro dalla configurazione dell'agente nel server gateway.   

Per informazioni correlate al ruolo di lavoro ibrido per Runbook di automazione, vedere [automatizzare le risorse nel Data Center o nel cloud usando Hybrid Runbook Workers](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurare Operations Manager, in cui tutti gli agenti usano lo stesso server proxy

La configurazione del proxy di Operations Manager viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager, anche se l'impostazione è vuota.  

Per usare il gateway OMS per supportare Operations Manager, è necessario disporre di:

* Microsoft Monitoring Agent (versione 8.0.10900.0 o successiva) installata nel server gateway OMS e configurata con le stesse aree di lavoro Log Analytics a cui il gruppo di gestione è configurato per la segnalazione.
* Connettività Internet. In alternativa, il gateway OMS deve essere connesso a un server proxy connesso a Internet.

> [!NOTE]
> Se non si specifica alcun valore per il gateway, viene effettuato il push di valori vuoti a tutti gli agenti.
>

Se il gruppo di gestione Operations Manager viene registrato con un'area di lavoro di Log Analytics per la prima volta, non verrà visualizzata l'opzione per specificare la configurazione proxy per il gruppo di gestione nella console operatore. Questa opzione è disponibile solo se il gruppo di gestione è stato registrato con il servizio.  

Per configurare l'integrazione, aggiornare la configurazione del proxy di sistema tramite Netsh nel sistema in cui viene eseguita la console operatore e in tutti i server di gestione del gruppo di gestione. A tale scopo, seguire questa procedura:

1. Aprire un prompt dei comandi con privilegi elevati:

   a. Selezionare **Start** e immettere **cmd**.  

   b. Fare clic con il pulsante destro del mouse su **prompt dei comandi** e scegliere **Esegui come amministratore**.  

1. Immettere il comando seguente:

   `netsh winhttp set proxy <proxy>:<port>`

Dopo aver completato l'integrazione con Log Analytics, rimuovere la modifica eseguendo `netsh winhttp reset proxy`. Quindi, nella console operatore, utilizzare l'opzione **Configura server proxy** per specificare il server gateway log Analytics. 

1. Nella console di Operations Manager, in **Operations Management Suite**, selezionare **connessione**, quindi fare clic su **Configura server proxy**.

   ![Screenshot del Operations Manager, che mostra la selezione configurare il server proxy](./media/gateway/scom01.png)

1. Selezionare **Usa un server proxy per accedere a Operations Management Suite** e quindi immettere l'indirizzo IP del server gateway log Analytics o dell'indirizzo IP virtuale del servizio di bilanciamento del carico. Prestare attenzione per iniziare con il prefisso `http://`.

   ![Screenshot di Operations Manager, che mostra l'indirizzo del server proxy](./media/gateway/scom02.png)

1. Selezionare **Fine**. Il gruppo di gestione di Operations Manager è ora configurato per comunicare tramite il server gateway con il servizio Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurare Operations Manager, dove gli agenti specifici usano un server proxy

Per ambienti complessi o di grandi dimensioni, è possibile che si desideri utilizzare il server gateway Log Analytics solo specifici server (o gruppi).  Per questi server, non è possibile aggiornare direttamente l'agente di Operations Manager perché questo valore viene sovrascritto dal valore globale del gruppo di gestione.  Eseguire invece l'override della regola utilizzata per effettuare il push di questi valori.  

> [!NOTE] 
> Utilizzare questa tecnica di configurazione se si desidera consentire più server gateway Log Analytics nell'ambiente in uso.  Ad esempio, è possibile richiedere specifici server gateway Log Analytics essere specificati a livello di area.
>

Per configurare server o gruppi specifici per l'utilizzo del server gateway Log Analytics: 

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Creazione**.  
1. Nell'area di lavoro Creazione selezionare **Regole**. 
1. Sulla barra degli strumenti Operations Manager selezionare il pulsante **ambito** . Se questo pulsante non è disponibile, assicurarsi di aver selezionato un oggetto, non una cartella, nel riquadro **monitoraggio** . La finestra di dialogo **Crea ambito oggetti Management Pack** visualizza un elenco di classi, gruppi o oggetti di destinazione comuni. 
1. Nel campo **Cerca** immettere **servizio integrità** e selezionarlo dall'elenco. Selezionare **OK**.  
1. Cercare la **regola di impostazione proxy di Advisor**. 
1. Sulla barra degli strumenti Operations Manager selezionare **sostituzioni** , quindi scegliere **Sostituisci Rule\For un oggetto specifico della classe: servizio integrità** e selezionare un oggetto dall'elenco.  In alternativa, creare un gruppo personalizzato che contenga l'oggetto servizio integrità dei server a cui si desidera applicare la sostituzione. Applicare quindi la sostituzione al gruppo personalizzato.
1. Nella finestra di dialogo **proprietà di sostituzione** aggiungere un segno di spunta nella colonna **sostituzione** accanto al parametro **WebProxyAddress** .  Nel campo **valore di sostituzione** immettere l'URL del server gateway log Analytics. Prestare attenzione per iniziare con il prefisso `http://`.  

    >[!NOTE]
    > Non è necessario attivare la regola. Questa funzione è già gestita automaticamente con una sostituzione nel riferimento protetto di Microsoft System Center Advisor Management Pack che è destinato al gruppo di server di monitoraggio di Microsoft System Center Advisor.
    > 

1. Selezionare una Management Pack dall'elenco **seleziona Management Pack di destinazione** o creare un nuovo Management Pack non sealed selezionando **nuovo**. 
1. Al termine, selezionare **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurare per i ruoli di lavoro ibridi per Runbook di automazione

Se nel proprio ambiente sono presenti ruoli di lavoro ibridi per Runbook di automazione, attenersi alla procedura seguente per configurare il gateway per supportare i thread di lavoro.

Vedere la sezione [configurare la rete](../../automation/automation-hybrid-runbook-worker.md#network-planning) della documentazione di automazione per trovare l'URL per ogni area.

Se il computer è registrato automaticamente come ruolo di lavoro ibrido per Runbook, ad esempio se la soluzione Gestione aggiornamenti è abilitata per una o più macchine virtuali, attenersi alla procedura seguente:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Riavviare il servizio gateway di Log Analytics usando il cmdlet di PowerShell seguente: `Restart-Service OMSGatewayService`

Se il computer è stato aggiunto ad automazione di Azure usando il cmdlet di registrazione del ruolo di lavoro ibrido per Runbook, seguire questa procedura:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway di Log Analytics. Ad esempio: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Riavviare il servizio gateway di Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili

È possibile usare i cmdlet di per completare le attività per aggiornare le impostazioni di configurazione del gateway Log Analytics. Prima di usare i cmdlet di, assicurarsi di:

1. Installare il gateway Log Analytics (Microsoft Windows Installer).
1. Aprire una finestra della console di PowerShell.
1. Importare il modulo digitando questo comando: `Import-Module OMSGateway`
1. Se non si verifica alcun errore nel passaggio precedente, il modulo è stato importato ed è possibile usare i cmdlet. Immettere `Get-Module OMSGateway`
1. Dopo aver usato i cmdlet per apportare modifiche, riavviare il servizio gateway OMS.

Un errore nel passaggio 3 indica che il modulo non è stato importato. L'errore potrebbe verificarsi quando PowerShell non riesce a trovare il modulo. Il modulo è reperibile nel percorso di installazione del gateway OMS: c:\Programmi\Microsoft *OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parameters** (Parametri) | **Descrizione** | **Esempio** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> Valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Indirizzo<br> Nome utente<br> Password |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. impostare un proxy di inoltro e le credenziali:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. impostare un proxy di inoltro che non richiede l'autenticazione: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. deselezionare l'impostazione proxy di inoltro:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Ottiene l'host attualmente consentito (solo l'host consentito configurato localmente, non gli host consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene gli oggetti del certificato client attualmente consentiti (solo gli oggetti consentiti configurati localmente, non scaricati automaticamente gli oggetti consentiti) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per raccogliere gli eventi registrati dal gateway, è necessario che sia installato l'agente Log Analytics.

![Screenshot dell'elenco di Visualizzatore eventi nel registro Log Analytics gateway](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>ID e descrizioni degli eventi del gateway Log Analytics

La tabella seguente Mostra gli ID evento e le descrizioni per gli eventi del log del gateway Log Analytics.

| **ID** | **Descrizione** |
| --- | --- |
| 400 |Qualsiasi errore dell'applicazione che non dispone di un ID specifico. |
| 401 |Configurazione errata. Ad esempio, listenPort = "Text" invece di un Integer. |
| 402 |Eccezione durante l'analisi dei messaggi di handshake TLS. |
| 403 |Errore di rete. Ad esempio, non è in grado di connettersi al server di destinazione. |
| 100 |Informazioni generali. |
| 101 |Il servizio è stato avviato. |
| 102 |Il servizio è stato arrestato. |
| 103 |È stato ricevuto un comando HTTP CONNECT dal client. |
| 104 |Non è stato ricevuto un comando HTTP CONNECT. |
| 105 |Il server di destinazione non è presente nell'elenco dei consentiti o la porta di destinazione non è sicura (443). <br> <br> Assicurarsi che l'agente MMA nel server gateway OMS e gli agenti che comunicano con il gateway OMS siano connessi alla stessa area di lavoro Log Analytics. |
| 105 |ERRORE TcpConnection – certificato client non valido: CN = gateway. <br><br> Verificare che sia in uso il gateway OMS versione 1.0.395.0 o successiva. Assicurarsi anche che l'agente MMA nel server gateway OMS e gli agenti che comunicano con il gateway OMS siano connessi alla stessa area di lavoro Log Analytics. |
| 106 |Versione del protocollo TLS/SSL non supportata.<br><br> Il gateway Log Analytics supporta solo TLS 1,0, TLS 1,1 e 1,2. Non supporta SSL.|
| 107 |La sessione TLS è stata verificata. |

### <a name="performance-counters-to-collect"></a>Contatori delle prestazioni da raccogliere

La tabella seguente mostra i contatori delle prestazioni disponibili per il gateway di Log Analytics. Utilizzare Performance Monitor per aggiungere i contatori.

| **Nome** | **Descrizione** |
| --- | --- |
| Connessione tra il gateway di Log Analytics e il client attivo |Numero di connessioni di rete client attive (TCP) |
| Gateway di Log Analytics/Numero di errori |Numero di errori |
| Gateway di Log Analytics/Client connesso |Numero di client connessi |
| Gateway di Log Analytics/Numero di rifiuti |Numero di rifiuti a causa di errori di convalida TLS |

![Screenshot dell'interfaccia del gateway Log Analytics, che mostra i contatori delle prestazioni](./media/gateway/counters.png)

## <a name="assistance"></a>Assistenza

Dopo aver eseguito l'accesso alla portale di Azure, è possibile ottenere assistenza per il gateway Log Analytics o qualsiasi altra funzionalità o servizio di Azure.
Per ottenere assistenza, selezionare l'icona del punto interrogativo nell'angolo in alto a destra del portale e selezionare **nuova richiesta di supporto**. Completare quindi il nuovo modulo di richiesta di supporto.

![Screenshot di una nuova richiesta di supporto](./media/gateway/support.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere origini dati](../../azure-monitor/platform/agent-data-sources.md) per raccogliere dati da origini connesse e archiviare i dati nell'area di lavoro log Analytics.
