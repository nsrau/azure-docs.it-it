---
title: Connettere computer a OMS usando il gateway OMS | Microsoft Docs
description: "Connettere i dispositivi gestiti OMS e i computer monitorati da Operations Manager al gateway OMS per inviare dati al servizio OMS quando non è disponibile l&quot;accesso a Internet."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: ce2f9311775389366c66323070254f721f0896ab
ms.contentlocale: it-it
ms.lasthandoff: 04/22/2017

---

# <a name="connect-computers-without-internet-access-to-oms-using-the-oms-gateway"></a>Connettere computer senza accesso a Internet a OMS usando il gateway OMS

Questo documento descrive in che modo i computer gestiti OMS e monitorati da System Center Operations Manager possono inviare dati al servizio OMS quando non è disponibile l'accesso a Internet. Il gateway OMS, che è un proxy di inoltro HTTP che supporta il tunneling HTTP con il comando HTTP CONNECT, può raccogliere dati e inviarli al servizio OMS per conto dei computer.  

Il gateway OMS supporta:

* Funzionalità Hybrid Runbook Workers di Automazione di Azure  
* Computer Windows con Microsoft Monitoring Agent direttamente connesso a un'area di lavoro OMS
* Computer Linux con l'agente OMS per Linux direttamente connesso a un'area di lavoro OMS  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3 o il gruppo di gestione di Operations Manager 2016 integrato con OMS.  

Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, ad esempio dispositivi POS o server che supportano servizi IT, ma è necessario connetterli a OMS per gestirli e monitorarli, è possibile configurarli per comunicare direttamente con il gateway OMS per poter ricevere la configurazione e inoltrare i dati per conto dei computer.  Se questi computer vengono configurati con l'agente OMS per connettersi direttamente a un'area di lavoro OMS, tutti i computer comunicheranno invece con il gateway OMS.  Il gateway trasferisce i dati dagli agenti direttamente a OMS senza analizzare i dati in transito.

Quando un gruppo di gestione di Operations Manager viene integrato con OMS, i server di gestione possono essere configurati per la connessione al gateway OMS per poter ricevere informazioni di configurazione e inviare i dati raccolti a seconda della soluzione abilitata.  Gli agenti di Operations Manager inviano alcuni dati, ad esempio avvisi di Operations Manager, valutazioni della configurazione, spazi dell'istanza e dati sulla capacità, al server di gestione. Altri dati di volumi elevati, ad esempio quelli relativi a log, prestazioni ed eventi di sicurezza di IIS, vengono inviati direttamente al gateway OMS.  Se uno o più server gateway di Operations Manager sono stati distribuiti in una rete perimetrale o in un'altra rete isolata per monitorare i sistemi non attendibili, tali server non possono comunicare con un gateway OMS.  I server gateway di Operations Manager possono fare riferimento solo a un server di gestione.  Quando un gruppo di gestione di Operations Manager viene configurato per la comunicazione con il gateway OMS, le informazioni di configurazione del proxy vengono automaticamente distribuite a ogni computer gestito tramite agente configurato per la raccolta dei dati per Log Analytics, anche se l'impostazione è vuota.    

Per offrire disponibilità elevata per i gruppi connessi diretti o di Operations Management che comunicano con OMS tramite il gateway, è possibile usare il bilanciamento del carico di rete per reindirizzare e distribuire il traffico tra più server gateway.  Se un server gateway si arresta, il traffico viene reindirizzato a un altro nodo disponibile.  

È consigliabile installare l'agente OMS nel computer che esegue il software del gateway OMS e analizzare i dati sulle prestazioni o sugli eventi. L'agente consente anche al gateway OMS di identificare gli endpoint del servizio con cui deve comunicare.

Ogni agente deve avere connettività di rete per il gateway, in modo che gli agenti possano trasferire automaticamente dati da e verso il gateway. Non è consigliabile installare il gateway in un controller di dominio.

Il diagramma seguente illustra il flusso di dati dagli agenti diretti a OMS tramite il server gateway.  La configurazione del proxy degli agenti deve corrispondere alla stessa porta con cui il gateway OMS è configurato per comunicare con OMS.  

![Diagramma della comunicazione degli agenti diretti con OMS](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Il diagramma seguente illustra il flusso di dati da un gruppo di gestione di Operations Manager a OMS.   

![Diagramma della comunicazione di Operations Manager con OMS](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>Prerequisiti

Quando si designa un computer per l'esecuzione del gateway OMS, il computer presenta i requisiti seguenti:

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

## <a name="download-the-oms-gateway"></a>Scaricare il gateway OMS

Per ottenere la versione più recente del file di configurazione del gateway OMS, sono disponibili tre modi diversi.

1. Download dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Download dal portale di OMS.  Dopo avere effettuato l'accesso all'area di lavoro di OMS, passare a **Impostazioni** > **Origini connesse** > **Server Windows** e fare clic su **Scarica gateway OMS**.

3. Download dal [portale di Azure](https://portal.azure.com).  Dopo avere effettuato l'accesso:  

   1. Esplorare l'elenco dei servizi e quindi selezionare **Log Analytics**.  
   2. Selezionare un'area di lavoro.
   3. Nel pannello dell'area di lavoro, in **General** fare clic su **Avvio rapido**.
   4. In **Choose a data source to connect to the workspace** (Scegliere un'origine dati per connettersi all'area di lavoro) fare clic su **Computer**.
   5. Nel pannello **Agente diretto** fare clic su **Download OMS Gateway** (Scarica gateway OMS).<br><br> ![Scaricare il gateway OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Installare il gateway OMS

Per installare un gateway, eseguire questa procedura.  Se è installata una versione precedente, chiamata *Server d'inoltro di Log Analytics*, verrà aggiornata a questa versione.  

1. Dalla cartella di destinazione fare doppio clic su **OMS Gateway.msi**.
2. Nella pagina di **benvenuto** fare clic su **Avanti**.<br><br> ![Configurazione guidata gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br>
3. Nella pagina **Contratto di licenza** selezionare **Accetto i termini del contratto di licenza** per accettare le condizioni di licenza e quindi fare clic su **Avanti**.
4. Nella pagina **Port and proxy address** (Indirizzo porta e proxy):
   1. Digitare il numero di porta TCP da usare per il gateway. Viene configurata una regola in ingresso con questo numero di porta in Windows Firewall.  Il valore predefinito è 8080.
      L'intervallo valido del numero di porta è 1-65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.
   2. In alternativa, se il server in cui è installato il gateway deve comunicare tramite un proxy, digitare l'indirizzo del proxy in cui il gateway deve connettersi. Ad esempio: `http://myorgname.corp.contoso.com:80`.  Se vuoto, il gateway tenterà di connettersi direttamente a Internet.  Se il server proxy richiede l'autenticazione, immettere un nome utente e una password.<br><br> ![Configurazione guidata del proxy del gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Fare clic su **Avanti**.
5. Se Microsoft Update non è abilitato, viene visualizzata la pagina di Microsoft Update in cui è possibile scegliere di abilitarlo. Effettuare una selezione e quindi fare clic su **Avanti**. In caso contrario, continuare con il passaggio successivo.
6. Nella pagina **Cartella di destinazione** lasciare la cartella predefinita C:\Programmi\OMS Gateway oppure digitare il percorso in cui si vuole installare il gateway e quindi fare clic su **Avanti**.
7. Nella pagina **Pronto per l'installazione** fare clic su **Installa**. È possibile che venga visualizzato Controllo account utente per la richiesta dell'autorizzazione all'installazione. In questo caso fare clic su **Sì**.
8. Al termine dell'installazione fare clic su **Fine**. È possibile verificare se il servizio è in esecuzione aprendo lo snap-in services.msc e verificare che il **gateway OMS** venga visualizzato nell'elenco dei servizi e che lo stato sia **In esecuzione**.<br><br> ![Servizi – Gateway OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configurare il bilanciamento del carico di rete
È possibile configurare il gateway per la disponibilità elevata con il bilanciamento del carico di rete tramite Bilanciamento carico di rete Microsoft o servizi di bilanciamento del carico basati su hardware.  Il servizio di bilanciamento del carico gestisce il traffico reindirizzando le connessioni richieste dagli agenti OMS o dai server di gestione di Operations Manager nei nodi. Se un server gateway si arresta, il traffico viene reindirizzato ad altri nodi.

Per informazioni su come progettare e distribuire un cluster di bilanciamento del carico di rete di Windows Server 2016, vedere [Bilanciamento carico di rete](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  La procedura seguente illustra come configurare un cluster di bilanciamento del carico di rete Microsoft.  

1.  Accedere al server Windows membro del cluster di bilanciamento del carico di rete con un account amministrativo.  
2.  Aprire Gestione bilanciamento carico di rete in Server Manager, fare clic su **Strumenti** e quindi su **Gestione bilanciamento carico di rete**.
3. Per connettere un server gateway OMS all'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi fare clic su **Aggiungi host al cluster**.<br><br> ![Gestione del bilanciamento del carico di rete – Aggiungi host al cluster](./media/log-analytics-oms-gateway/nlb02.png)<br>
4. Immettere l'indirizzo IP del server gateway che si vuole connettere.<br><br> ![Gestione del bilanciamento del carico di rete – Aggiungi host al cluster: Connetti](./media/log-analytics-oms-gateway/nlb03.png)

## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Configurare l'agente OMS e il gruppo di gestione di Operations Manager
La sezione seguente include i passaggi per configurare gli agenti OMS connessi direttamente, un gruppo di gestione di Operations Manager o i ruoli di lavoro ibridi per runbook di Automazione di Azure con il gateway OMS per comunicare con OMS.  

Per conoscere i requisiti e la procedura di installazione dell'agente OMS nei computer Windows che si connettono direttamente a OMS, vedere [Connettere computer Windows a OMS](log-analytics-windows-agents.md) o, per i computer Linux, vedere [Connettere computer Linux a OMS](log-analytics-linux-agents.md).

### <a name="configuring-the-oms-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Configurazione dell'agente OMS e di Operations Manager per usare il gateway OMS come server proxy

### <a name="configure-standalone-oms-agent"></a>Configurare un agente OMS autonomo
Per informazioni sulla configurazione di un agente per l'uso di un server proxy, che in questo caso è il gateway, vedere [Configurare le impostazioni proxy e firewall con Microsoft Monitoring Agent](log-analytics-proxy-firewall.md).  Se sono stati distribuiti più server gateway dietro un servizio di bilanciamento del carico di rete, la configurazione del proxy dell'agente OMS corrisponde all'indirizzo IP virtuale del servizio di bilanciamento del carico di rete:<br><br> ![Proprietà di Microsoft Monitoring Agent –Impostazioni proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Configurare Operations Manager: tutti gli agenti usano lo stesso server proxy
Configurare Operations Manager per aggiungere il server gateway.  La configurazione del proxy di Operations Manager viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager, anche se l'impostazione è vuota.

Per usare il gateway per il supporto di Operations Manager, è necessario avere:

* Microsoft Monitoring Agent (versione dell'agente **8.0.10900.0** e successiva) installato nel server gateway e configurato per le aree di lavoro di OMS con cui si vuole comunicare.
* Il gateway deve disporre di connettività Internet o essere connesso a un server proxy che dispone di tale connettività.

> [!NOTE]
> Se non si specifica un valore per il gateway, viene effettuato il push di valori vuoti a tutti gli agenti.


1. Aprire la console di Operations Manager e in **Operations Management Suite** fare clic su **Connessione** e quindi su **Configura server proxy**.<br><br> ![Operations Manager – Configurare il server proxy](./media/log-analytics-oms-gateway/scom01.png)<br>
2. Selezionare **Usa un server proxy per accedere a Operations Management Suite** e quindi digitare l'indirizzo IP del server gateway OMS o l'indirizzo IP virtuale del servizio di bilanciamento del carico di rete. Assicurarsi di iniziare con il prefisso `http://`.<br><br> ![Operations Manager – Indirizzo del server proxy](./media/log-analytics-oms-gateway/scom02.png)<br>
3. Fare clic su **Fine**. Il server di Operations Manager è collegato all'area di lavoro di OMS.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Configurare Operations Manager: agenti specifici usano il server proxy
È possibile che in ambienti complessi o di grandi dimensioni solo specifici server (o gruppi) debbano usare il server gateway OMS.  Per questi server, non è possibile aggiornare direttamente l'agente Operations Manager perché questo valore viene sovrascritto dal valore globale del gruppo di gestione.  È invece necessario eseguire l'override della regola usata per effettuare il push di questi valori.

> [!NOTE]
> Questa stessa tecnica di configurazione può essere usata per consentire l'uso di più server gateway OMS in un ambiente.  È ad esempio possibile fare in modo che vengano specificati determinati server gateway OMS per ogni area.

1. Aprire la console di Operations Manager e selezionare l'area di lavoro **Creazione**.  
2. Nell'area di lavoro Creazione selezionare **Regole** e fare clic sul pulsante **Ambito** sulla barra degli strumenti di Operations Manager. Se questo pulsante non è disponibile, verificare che nel riquadro Monitoraggio sia selezionato un oggetto e non una cartella. La finestra di dialogo **Crea ambito oggetti Management Pack** visualizza un elenco di classi, gruppi o oggetti di destinazione comuni.
3. Digitare **Servizio integrità** nel campo **Cerca** e selezionarlo dall'elenco.  Fare clic su **OK**.  
4. Cercare la regola **Advisor Proxy Setting Rule** (Regola di impostazione proxy di Advisor) e nella barra degli strumenti della console operatore fare clic su **Override** e quindi scegliere**Sostituisci regola\Oggetto specifico della classe: Servizio integrità** e selezionare un oggetto specifico dall'elenco.  Facoltativamente, è possibile creare un gruppo personalizzato contenente l'oggetto Servizio integrità dei server a cui si vuole applicare questo override e quindi applicare l'override a tale gruppo.
5. Nella finestra di dialogo **Proprietà di sostituzione** fare clic per inserire un segno di spunta nella colonna **Override** accanto al parametro **WebProxyAddress**.  Nel campo **Valore di sostituzione** immettere l'URL del server gateway OMS per assicurarsi di iniziare con il prefisso `http://`.
   >[!NOTE]
   > Non è necessario abilitare la regola perché viene già automaticamente gestita con un override contenuto nel Management Pack Microsoft System Center Advisor Secure Reference Override che specifica come destinazione Microsoft System Center Advisor Monitoring Server Group (Gruppo di server di monitoraggio di Microsoft System Center Advisor).
   >
6. Selezionare un Management Pack dall'elenco **Seleziona Management Pack di destinazione** o creare un nuovo Management Pack non bloccato facendo clic su **Nuovo**.
7. Dopo avere completato le modifiche, fare clic su **OK**.

### <a name="configure-for-automation-hybrid-workers"></a>Configurare per i ruoli di lavoro ibridi di automazione
Se nell'ambiente sono disponibili ruoli di lavoro ibridi per runbook di Automazione, i passaggi seguenti offrono soluzioni alternative manuali e temporanee per configurare il supporto tramite il gateway.

Nei passaggi seguenti, è necessario conoscere l'area di Azure in cui risiede l'account di Automazione. Per individuare la località:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il servizio Automazione di Azure.
3. Selezionare l'account appropriato di Automazione di Azure.
4. Visualizzarne l'area in **Località**.<br><br> ![Portale di Azure – Località dell'account di Automazione](./media/log-analytics-oms-gateway/location.png)  

Usare le tabelle seguenti per identificare l'URL di ogni località:

**URL del servizio dati del processo di runtime**

| **Località** | **URL** |
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

| **Località** | **URL** |
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

Se il computer è registrato come ruolo di lavoro ibrido per runbook automaticamente, per l'applicazione di patch tramite la soluzione di gestione degli aggiornamenti, seguire questa procedura:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway OMS. Ad esempio:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Riavviare il servizio del gateway OMS usando il cmdlet di PowerShell seguente: `Restart-Service OMSGatewayService`

Se il computer viene caricato in Automazione di Azure usando il cmdlet di registrazione del ruolo di lavoro ibrido per runbook, seguire questa procedura:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco di host consentiti nel gateway OMS. Ad esempio: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway OMS. Ad esempio:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Riavviare il servizio del gateway OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili
I cmdlet consentono di completare le attività necessarie per aggiornare le impostazioni di configurazione del gateway OMS. Prima di usarli, assicurarsi di:

1. Installare il gateway OMS (MSI).
2. Aprire una finestra della console di PowerShell.
3. Per importare il modulo, digitare il comando `Import-Module OMSGateway`.
4. Se non si verifica alcun errore nel passaggio precedente, il modulo è stato importato ed è possibile usare i cmdlet. Digitare `Get-Module OMSGateway`.
5. Dopo aver apportato le modifiche usando i cmdlet, assicurarsi di riavviare il servizio del gateway.

Se si verifica un errore nel passaggio 3, il modulo non è stato importato. L'errore può verificarsi quando PowerShell non riesce a trovare il modulo. È possibile trovarlo nel percorso di installazione del gateway: *C:\Programmi\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet** | **Parametri** | **Descrizione** | **Esempio** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> Valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Indirizzo<br> Nome utente<br> Password |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. Impostare un proxy di inoltro e le credenziali:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Impostare un proxy di inoltro che non richiede autenticazione: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Deselezionare l'impostazione del proxy di inoltro:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Ottiene gli host attualmente consentiti (solo gli host consentiti configurati localmente, non include gli host consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene gli oggetti del certificato client attualmente consentiti (solo gli oggetti consentiti configurati localmente, non include gli oggetti consentiti scaricati automaticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Risoluzione dei problemi
Per raccogliere gli eventi registrati dal gateway, è necessario che sia installato anche l'agente OMS.<br><br> ![Visualizzatore eventi – Log del gateway OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**ID e descrizioni dell'evento del gateway OMS**

La tabella seguente illustra gli ID evento e le descrizioni degli eventi di log del gateway OMS.

| **ID** | **Descrizione** |
| --- | --- |
| 400 |Qualsiasi errore dell'applicazione che non dispone di un ID specifico |
| 401 |Configurazione errata. Ad esempio: listenPort = "text" anziché un intero |
| 402 |Eccezione durante l'analisi dei messaggi di handshake TLS |
| 403 |Errore di rete. Ad esempio: non è possibile connettersi al server di destinazione |
| 100 |Informazioni generali |
| 101 |Servizio avviato |
| 102 |Servizio arrestato |
| 103 |Ricevuto un comando HTTP CONNECT dal client |
| 104 |Non è stato ricevuto un comando HTTP CONNECT |
| 105 |Il server di destinazione non è presente nell'elenco dei valori consentiti o la porta di destinazione non è sicura (443) <br> <br> Assicurarsi che l'agente MMA sul server gateway e gli agenti che comunicano con il gateway siano connessi alla stessa area di lavoro di Log Analytics. |
| 105 |ERRORE TcpConnection – Certificato client non valido: CN=Gateway <br><br> Assicurarsi che: <br>    <br> &#149; Si usi un gateway con il numero di versione 1.0.395.0 o successiva. <br> &#149; L'agente MMA sul server gateway e gli agenti che comunicano con il gateway siano connessi alla stessa area di lavoro di Log Analytics. |
| 106 |La sessione TLS è sospetta e viene rifiutata |
| 107 |La sessione TLS è stata verificata |

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
Per richiedere supporto, fare clic sul simbolo del punto interrogativo nell'angolo superiore destro del portale e quindi fare clic su **Nuova richiesta di supporto**. Completare quindi il nuovo modulo di richiesta di supporto.

![Nuova richiesta di supporto](./media/log-analytics-oms-gateway/support.png)

È anche possibile lasciare commenti e suggerimenti su OMS o Log Analytics nel [forum di Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere le origini dati](log-analytics-data-sources.md) per raccogliere i dati dalle origini connesse nell'area di lavoro di OMS e archiviarli nel repository OMS.

