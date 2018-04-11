---
title: Connettere computer usando il gateway OMS | Microsoft Docs
description: Per inviare dati ad Automazione di Azure e Log Analytics quando non è disponibile l'accesso a Internet, connettere i dispositivi e i computer monitorati da Operations Manager al gateway OMS.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Connettere computer senza accesso a Internet usando il gateway OMS
Questo documento descrive come configurare la comunicazione con Automazione di Azure e Log Analytics usando il gateway OMS quando i computer connessi direttamente o monitorati da Operations Manager non hanno accesso a Internet. Il gateway OMS è un proxy di inoltro HTTP che supporta il tunneling HTTP tramite il comando HTTP CONNECT. È in grado di raccogliere i dati e inviarli ad Automazione di Azure e Log Analytics per conto dei computer senza accesso a Internet.  

Il gateway OMS supporta:

* Funzionalità Hybrid Runbook Workers di Automazione di Azure  
* Computer Windows con Microsoft Monitoring Agent direttamente connesso a un'area di lavoro di Log Analytics
* Computer Linux con l'agente di Operations Management Suite Agent per Linux direttamente connesso a un'area di lavoro di Log Analytics  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3, Operations Manager 2016 e il gruppo di gestione di Operations Manager versione 1801 integrato con Log Analytics  

Se i criteri di sicurezza IT non consentono a determinati computer della rete di connettersi a Internet, ad esempio nel caso di dispositivi POS o server che supportano servizi IT, ma è necessario connetterli ad Automazione di Azure o Log Analytics per gestirli e monitorarli, è possibile configurarli in modo che comunichino direttamente con il gateway OMS.

 Se questi computer sono configurati con l'agente OMS per connettersi direttamente a un'area di lavoro di Log Analytics, tutti i computer comunicheranno invece con il gateway OMS. Il gateway OMS trasferisce i dati dagli agenti direttamente al servizio, senza analizzare i dati in transito.

Quando un gruppo di gestione di Operations Manager viene integrato con Log Analytics, i server di gestione possono essere configurati per connettersi al gateway OMS per ricevere informazioni di configurazione e inviare i dati raccolti. Gli agenti di Operations Manager inviano alcuni dati, ad esempio avvisi di Operations Manager, valutazioni della configurazione, spazio delle istanze e dati sulla capacità, al server di gestione. Altri dati con volumi elevati, ad esempio log, informazioni sulle prestazioni ed eventi di sicurezza di IIS, vengono inviati direttamente al gateway OMS.  

Se uno o più server gateway di Operations Manager sono stati distribuiti in una rete perimetrale o in un'altra rete isolata per monitorare i sistemi non attendibili, non possono comunicare con un gateway OMS. I server gateway di Operations Manager possono fare riferimento solo a un server di gestione. 

Quando un gruppo di gestione di Operations Manager viene configurato per la comunicazione con il gateway OMS, le informazioni di configurazione del proxy vengono automaticamente distribuite a ogni computer gestito tramite agente configurato per la raccolta dei dati per Log Analytics, anche se l'impostazione è vuota.    

Per offrire disponibilità elevata per i gruppi connessi diretti o di Operations Management che comunicano con Log Analytics tramite il gateway OMS, è possibile usare il bilanciamento del carico di rete per reindirizzare e distribuire il traffico tra più server gateway OMS. Se un server gateway si arresta, il traffico viene reindirizzato a un altro nodo disponibile.  

È consigliabile installare l'agente OMS nel computer che esegue il software gateway OMS per eseguire il monitoraggio e analizzare i dati sulle prestazioni o sugli eventi. L'agente consente anche al gateway OMS di identificare gli endpoint del servizio con cui deve comunicare.

Tutti gli agenti devono avere connettività di rete al gateway, in modo da poter trasferire automaticamente dati da e verso il gateway. Non è consigliabile installare un gateway in un controller di dominio.

Il diagramma seguente illustra il flusso di dati dagli agenti diretti ad Automazione di Azure e Log Analytics tramite il server gateway. La configurazione del proxy dell'agente deve usare la stessa porta usata dal gateway OMS per comunicare con il servizio.  

![Diagramma della comunicazione degli agenti diretti con i servizi](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Il diagramma seguente illustra il flusso di dati da un gruppo di gestione di Operations Manager a Log Analytics.   

![Diagramma della comunicazione di Operations Manager con Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>prerequisiti

Quando si specifica un computer per l'esecuzione del gateway OMS, assicurarsi che abbia quanto segue:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET Framework 4.5
* Almeno un processore a 4 core e 8 GB di memoria 

### <a name="language-availability"></a>Lingue disponibili

Il gateway OMS è disponibile nelle lingue seguenti:

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
Il gateway OMS supporta solo TLS (Transport Layer Security) 1.0, 1.1 e 1.2.  Non supporta SSL (Secure Sockets Layer).

### <a name="supported-number-of-agent-connections"></a>Numero di connessioni dell'agente supportate
La tabella seguente indica il numero supportato di agenti che comunicano con un server gateway. Questo supporto è basato su agenti che caricano circa 200 KB di dati ogni 6 secondi. Il volume di dati per agente testato è di circa 2,7 GB al giorno.

|Gateway |Numero approssimativo di agenti supportati|  
|--------|----------------------------------|  
|- CPU: CPU Intel XEON E5-2660 v3 da 2,6 GHz 2 core<br> - Memoria: 4 GB<br> - Larghezza di banda: 1 Gbps| 600|  
|- CPU: CPU Intel XEON E5-2660 v3 da 2,6 GHz 4 core<br> - Memoria: 8 GB<br> - Larghezza di banda: 1 Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>Scaricare il gateway OMS

Per ottenere la versione più recente del file di configurazione del gateway OMS sono disponibili due modi diversi.

1. Scaricarla dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Eseguire il download dal portale di Azure. Dopo l'accesso al portale di Azure, seguire questa procedura:  

   1. Esplorare l'elenco dei servizi e quindi selezionare **Log Analytics**.  
   2. Selezionare un'area di lavoro.
   3. Nel pannello dell'area di lavoro in **Generale** selezionare **Avvio rapido**.
   4. In **Scegliere un'origine dati da connettere all'area di lavoro** selezionare **Computer**.
   5. Nel pannello **Agente diretto** selezionare **Scarica gateway OMS**.
   
    ![Scaricare il gateway OMS](./media/log-analytics-oms-gateway/download-gateway.png)

- OPPURE - 

   1. Nel pannello dell'area di lavoro in **Impostazioni** selezionare **Impostazioni avanzate**.
   2. Selezionare **Origini connesse** > **Server Windows**. Selezionare quindi **Scarica gateway OMS**.

## <a name="install-the-oms-gateway"></a>Installare il gateway OMS

Per installare un gateway, seguire questa procedura. Se è installata una versione precedente, denominata *Server d'inoltro di Log Analytics*, verrà eseguito l'aggiornamento a questa versione.  

1. Nella cartella di destinazione selezionare **OMS Gateway.msi**.
2. Nella pagina di **benvenuto** selezionare **Avanti**.

 ![Configurazione guidata del gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. Nella pagina **Contratto di licenza** selezionare **Accetto le condizioni del contratto di licenza**. Quindi selezionare **Avanti**.

4. Nella pagina **Port and proxy address** (Indirizzo porta e proxy):

   a. Digitare il numero di porta TCP da usare per il gateway. Viene configurata una regola in ingresso con questo numero di porta in Windows Firewall. Il valore predefinito è 8080. L'intervallo valido del numero di porta è 1-65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.

   b. In alternativa, se il server in cui è installato il gateway deve comunicare tramite un proxy, specificare il proxy a cui il gateway deve connettersi. Specificare, ad esempio, `http://myorgname.corp.contoso.com:80`, come illustrato nello screenshot seguente. Se si lascia vuoto questo campo, il gateway prova a connettersi direttamente a Internet.  Se il server proxy richiede l'autenticazione, immettere un nome utente e una password.
   
    ![Configurazione guidata del proxy del gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Selezionare **Avanti**.

5. Se il servizio Microsoft Update non è abilitato, viene visualizzata la pagina di Microsoft Update in cui è possibile scegliere di abilitarlo. Selezionare un'opzione e quindi **Avanti**. In caso contrario, continuare con il passaggio successivo.

6. Nella pagina **Cartella di destinazione** lasciare la cartella predefinita **C:\Programmi\OMS Gateway** oppure digitare il percorso in cui si vuole installare il gateway OMS. Quindi selezionare **Avanti**.

7. Nella pagina **Pronto per l'installazione** selezionare **Installa**. È possibile che venga visualizzato Controllo account utente per la richiesta dell'autorizzazione all'installazione. Se si riceve una richiesta di autorizzazione, selezionare **Sì**.

8. Al termine dell'installazione, selezionare **Fine**. È possibile verificare se il servizio è in esecuzione aprendo lo snap-in services.msc e verificando che la voce **Gateway OMS** sia presente nell'elenco di servizi e che il relativo stato sia **In esecuzione**.

    ![Servizi - Gateway OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configurare il bilanciamento del carico di rete 
È possibile configurare il gateway per la disponibilità elevata usando il bilanciamento del carico di rete. Usare Bilanciamento carico di rete Microsoft o servizi di bilanciamento del carico basati su hardware.  Il servizio di bilanciamento del carico gestisce il traffico reindirizzando le connessioni richieste dagli agenti OMS o dai server di gestione di Operations Manager nei suoi nodi. Se un server gateway si arresta, il traffico viene reindirizzato agli altri nodi.

Per informazioni su come progettare e distribuire un cluster di bilanciamento del carico di rete di Windows Server 2016, vedere [Bilanciamento carico di rete](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  La procedura seguente illustra come configurare un cluster di bilanciamento del carico di rete Microsoft.  

1.  Accedere al server Windows con un account amministrativo membro del cluster di bilanciamento del carico di rete.  

2.  In Server Manager, aprire Gestione bilanciamento carico di rete. Selezionare **Strumenti** e quindi selezionare **Gestione bilanciamento carico di rete**.

3. Per connettere un server gateway OMS all'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi scegliere **Add Host to Cluster** (Aggiungi host al cluster).

 ![Gestione bilanciamento carico di rete - Aggiunta dell'host al cluster](./media/log-analytics-oms-gateway/nlb02.png)

4. Immettere l'indirizzo IP del server gateway a cui ci si vuole connettere.

    ![Gestione bilanciamento carico di rete - Aggiunta dell'host al cluster: connessione](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Configurare l'agente OMS e il gruppo di gestione di Operations Manager
Le sezioni seguenti di questo articolo includono i passaggi per configurare gli agenti OMS connessi direttamente, un gruppo di gestione di Operations Manager o i ruoli di lavoro ibridi per runbook di Automazione di Azure con il gateway OMS per comunicare con Automazione di Azure o Log Analytics.  

Per comprendere i requisiti e i passaggi per l'installazione dell'agente OMS in computer Windows che si connettono direttamente a Log Analytics, vedere [Raccogliere dati dai computer dell'ambiente con Log Analytics](log-analytics-concept-hybrid.md#prerequisites).

 Per i computer Linux, vedere [Connettere i computer Linux a Log Analytics](log-analytics-quick-collect-linux-computer.md). Per informazioni correlate per il ruolo di lavoro ibrido per runbook di Automazione, vedere [Distribuire il ruolo di lavoro ibrido per runbook](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Configurare l'agente OMS autonomo
Per informazioni sulla configurazione di un agente per l'uso di un server proxy, che in questo caso è il gateway, vedere [Raccogliere dati dai computer dell'ambiente con Log Analytics](log-analytics-concept-hybrid.md#prerequisites). Se sono stati distribuiti più server gateway dietro un servizio di bilanciamento del carico di rete, la configurazione del proxy dell'agente OMS corrisponde all'indirizzo IP virtuale del servizio di bilanciamento del carico di rete:

![Proprietà di Microsoft Monitoring Agent - Impostazioni proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Configurare Operations Manager: tutti gli agenti usano lo stesso server proxy
Configurare Operations Manager per aggiungere il server gateway.  La configurazione del proxy di Operations Manager viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager, anche se l'impostazione è vuota.  

Per usare il gateway OMS per supportare Operations Manager, sono necessari i componenti seguenti:

* Microsoft Monitoring Agent (versione dell'agente **8.0.10900.0** o successiva) installato nel server gateway e configurato per le aree di lavoro di Log Analytics con cui si vuole comunicare.

* Un gateway con connettività Internet o una connessione a un server proxy che ha tale connettività.

> [!NOTE]
> Se non si specifica un valore per il gateway, viene eseguito il push di valori vuoti a tutti gli agenti.
> 

Se è la prima volta che il gruppo di gestione di Operations Manager esegue la registrazione in un'area di lavoro di Log Analytics, l'opzione per specificare la configurazione proxy per il gruppo di gestione non è disponibile nella Console operatore. 

Il gruppo di gestione deve essere registrato correttamente con il servizio prima che questa opzione sia disponibile. Aggiornare la configurazione del proxy di sistema tramite Netsh nel sistema in cui sono in esecuzione la Console operatore e tutti i server di gestione nel gruppo di gestione.

1. Aprire un prompt dei comandi con privilegi elevati.

    a. Passare a **Start**. Digitare quindi **cmd**.
    
    b. Fare clic con il pulsante destro del mouse su **Prompt dei comandi**. Scegliere quindi **Esegui come amministratore**.
    
2. Immettere il comando seguente e premere **INVIO**:

    `netsh winhttp set proxy <proxy>:<port>`

Dopo aver completato l'integrazione con Log Analytics, è possibile rimuovere la modifica eseguendo `netsh winhttp reset proxy`. Usare quindi l'opzione **Configura server proxy** nella Console operatore per specificare il server gateway OMS. 

1. Aprire la console di Operations Manager. In **Operations Management Suite** selezionare **Connessione**. Selezionare quindi **Configura server proxy**.

    ![Operations Manager - Configurazione del server proxy](./media/log-analytics-oms-gateway/scom01.png)

2. Selezionare **Usa un server proxy per accedere a Operations Management Suite**. Immettere quindi l'indirizzo IP del server gateway OMS o l'indirizzo IP virtuale del servizio di bilanciamento del carico di rete. Assicurarsi di iniziare con il prefisso `http://`.

    ![Operations Manager - Indirizzo del server proxy](./media/log-analytics-oms-gateway/scom02.png)

3. Selezionare **Fine**. Il gruppo di gestione di Operations Manager è ora configurato per comunicare tramite il server gateway con il servizio Log Analytics.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Configurare Operations Manager: agenti specifici usano il server proxy
È possibile che in ambienti complessi o di grandi dimensioni si voglia che solo specifici server (o gruppi) usino il server gateway OMS. Per questi server, non è possibile aggiornare l'agente Operations Manager direttamente, perché questo valore viene sovrascritto dal valore globale del gruppo di gestione. Eseguire invece l'override della regola che viene usata per il push di questi valori.  

> [!NOTE] 
> Questa stessa tecnica di configurazione può essere usata per consentire l'uso di più server gateway OMS in un ambiente. È ad esempio possibile fare in modo che vengano specificati server gateway OMS specifici per ogni area.
>  

1. Aprire la console di Operations Manager e quindi selezionare l'area di lavoro **Creazione**.

2. Nell'area di lavoro Creazione selezionare **Regole**. Fare quindi clic sul pulsante **Ambito** sulla barra degli strumenti di Operations Manager. Se questo pulsante non è disponibile, verificare che nel riquadro **Monitoraggio** sia selezionato un oggetto e non una cartella. La finestra di dialogo **Crea ambito oggetti Management Pack** visualizza un elenco di classi, gruppi o oggetti di destinazione comuni. 

3. Nel campo **Look for** (Cerca) digitare **Servizio integrità**. Selezionare il servizio nell'elenco. Selezionare **OK**.  

4. Cercare la regola **Advisor Proxy Setting Rule** (Regola di impostazione proxy di Advisor). Sulla barra degli strumenti della Console operatore selezionare **Sostituzioni**. Selezionare quindi **Override the Rule\For a specific object of class: Health Service** (Eseguire l'override della regola\Per un oggetto specifico della classe: Servizio integrità). 

5. Selezionare un oggetto specifico nell'elenco. 

6. Facoltativamente, è possibile creare un gruppo personalizzato che contiene l'oggetto servizio integrità dei server a cui si vuole applicare questa sostituzione. Applicare quindi la sostituzione al gruppo.

7. Nella finestra di dialogo **Proprietà di sostituzione** fare clic per inserire un segno di spunta nella colonna **Override** accanto al parametro **WebProxyAddress**.  Nel campo **Valore di sostituzione** immettere l'URL del server gateway OMS, assicurandosi che inizi con il prefisso `http://`.  

    >[!NOTE]
    > Non è necessario attivare la regola. La regola è infatti già gestita automaticamente con un override contenuto nel Management Pack di sostituzione del riferimento protetto di Microsoft System Center Advisor per il gruppo di server di monitoraggio di Microsoft System Center Advisor.
    >   

8. Selezionare un Management Pack nell'elenco **Seleziona Management Pack di destinazione** o creare un nuovo Management Pack non bloccato selezionando **Nuovo**. 

9. Dopo avere completato le modifiche, selezionare **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Configurare il gateway OMS per i ruoli di lavoro ibrido per runbook di Automazione
Se nell'ambiente ci sono ruoli di lavoro ibridi per runbook di Automazione, i passaggi seguenti offrono soluzioni alternative manuali e temporanee per configurare il supporto tramite il gateway OMS.

Nei passaggi seguenti, è necessario conoscere l'area di Azure in cui risiede l'account di Automazione. Per trovare la posizione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il servizio Automazione di Azure.
3. Selezionare l'account appropriato di Automazione di Azure.
4. In **Località** esaminare l'area dell'account.

    ![Portale di Azure - Località dell'account di Automazione](./media/log-analytics-oms-gateway/location.png)  

Usare le tabelle seguenti per identificare l'URL di ogni località:

**URL del servizio dati del processo di runtime**

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

Se il computer è registrato automaticamente come ruolo di lavoro ibrido per runbook, per l'applicazione di patch tramite la soluzione Gestione aggiornamenti procedere come segue:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway OMS. Digitare, ad esempio, quanto segue: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Riavviare il servizio del gateway OMS usando il cmdlet di PowerShell seguente: `Restart-Service OMSGatewayService`

In caso di onboarding del computer in Automazione di Azure usando il cmdlet di registrazione del ruolo di lavoro ibrido per runbook, seguire questa procedura:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco di host consentiti nel gateway OMS. Digitare, ad esempio, quanto segue: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway OMS. Digitare, ad esempio, quanto segue: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Riavviare il servizio gateway OMS: `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili
I cmdlet consentono di completare le attività necessarie per l'aggiornamento delle impostazioni di configurazione del gateway OMS. Prima di usarli, seguire questa procedura:

1. Installare il gateway OMS (MSI).
2. Aprire una finestra della console di PowerShell.
3. Importare il modulo digitando il comando: `Import-Module OMSGateway`.
4. Se non si verifica alcun errore nel passaggio precedente, il modulo è stato importato ed è possibile usare i cmdlet. Digitare `Get-Module OMSGateway`.
5. Dopo avere apportato le modifiche usando i cmdlet, assicurarsi di riavviare il servizio gateway OMS.

Se si verifica un errore nel passaggio 3, il modulo non è stato importato. L'errore può verificarsi quando PowerShell non riesce a trovare il modulo. È possibile trovarlo nel percorso di installazione del gateway OMS: *C:\Programmi\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet** | **Parametri** | **Descrizione** | **Esempio** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> Valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Indirizzo<br> Username<br> Password |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. Impostare un proxy di inoltro e le credenziali:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Impostare un proxy di inoltro che non richiede autenticazione: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Deselezionare l'impostazione del proxy di inoltro:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Ottiene gli host attualmente consentiti (solo gli host consentiti configurati localmente, non include gli host consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene gli oggetti del certificato client attualmente consentiti (solo gli oggetti consentiti configurati localmente, non include gli oggetti consentiti scaricati automaticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>risoluzione dei problemi
Per raccogliere gli eventi registrati dal gateway, è necessario che sia installato anche l'agente OMS.

![Visualizzatore eventi - Log del gateway OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**ID e descrizioni degli eventi del gateway OMS**

La tabella seguente illustra gli ID e le descrizioni degli eventi di log del gateway OMS:

| **ID** | **Descrizione** |
| --- | --- |
| 400 |Qualsiasi errore dell'applicazione che non ha un ID specifico. |
| 401 |Configurazione errata. Ad esempio: listenPort = "testo" anziché un valore integer. |
| 402 |Eccezione durante l'analisi dei messaggi di handshake TLS. |
| 403 |Errore di rete. Ad esempio: non è possibile connettersi al server di destinazione. |
| 100 |Informazioni generali. |
| 101 |Il servizio è stato avviato. |
| 102 |Il servizio è stato arrestato. |
| 103 |È stato ricevuto un comando HTTP CONNECT dal client. |
| 104 |Non è stato ricevuto un comando HTTP CONNECT. |
| 105 |Il server di destinazione non è presente nell'elenco elementi consentiti o la porta di destinazione non è sicura (443). <br> <br> Assicurarsi che l'agente MMA nel server gateway e gli agenti che comunicano con il gateway siano connessi alla stessa area di lavoro di Log Analytics. |
| 105 |ERRORE TcpConnection – Certificato client non valido: CN=Gateway <br><br> Assicurarsi che: <br>    <br> - Sia in uso un gateway con numero di versione 1.0.395.0 o successivo. <br> - L'agente MMA nel server gateway e gli agenti che comunicano con il gateway siano connessi alla stessa area di lavoro di Log Analytics. |
| 106 |Il gateway OMS supporta solo TLS 1.0, 1.1 e 1.2.  Non supporta SSL. Per qualsiasi versione del protocollo TLS/SSL non supportata, il gateway OMS genera l'ID evento 106.|
| 107 |La sessione TLS è stata verificata. |

**Contatori delle prestazioni da raccogliere**

La tabella seguente mostra i contatori delle prestazioni disponibili per il gateway OMS. È possibile aggiungere i contatori usando il monitor di prestazioni.

| **Nome** | **Descrizione** |
| --- | --- |
| Gateway OMS/Connessione client attiva |Numero di connessioni di rete client attive (TCP) |
| Gateway OMS/Numero di errori |Numero di errori |
| Gateway OMS/Client connesso |Numero di client connessi |
| Gateway OMS/Numero di rifiuti |Numero di rifiuti a causa di errori di convalida TLS |

![Contatori delle prestazioni del gateway OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Ottenere supporto
Dopo aver effettuato l'accesso al portale di Azure, è possibile creare una richiesta di supporto con il gateway OMS o qualsiasi altro servizio di Azure o funzionalità di un servizio.

Per richiedere assistenza, selezionare il simbolo di punto interrogativo nell'angolo superiore destro del portale. Selezionare quindi **Nuova richiesta di supporto**. Completare il nuovo modulo di richiesta di supporto.

![Nuova richiesta di supporto](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere origini dati](log-analytics-data-sources.md) per raccogliere i dati dalle origini connesse e archiviarli nell'area di lavoro di Log Analytics.
