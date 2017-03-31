---
title: Connettere computer e dispositivi a OMS tramite il gateway OMS | Documentazione Microsoft
description: "Connettere i dispositivi gestiti OMS e i computer monitorati da Operations Manager al gateway OMS per inviare dati al servizio OMS quando non è disponibile l&quot;accesso a Internet."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: de2e6d201ba54774697356e1cd55c4881664a401
ms.lasthandoff: 02/28/2017


---
# <a name="connect-computers-and-devices-without-internet-access-to-oms-using-the-oms-gateway"></a>Connettere computer e dispositivi senza accesso a Internet a OMS tramite il gateway OMS

Questo documento descrive in che modo i dispositivi gestiti OMS e i computer monitorati da System Center Operations Manager (SCOM) possono inviare dati al servizio OMS quando non è disponibile l'accesso a Internet. Il gateway OMS può raccogliere i dati e inviarli al servizio OMS per conto dei dispositivi.

Il gateway è un proxy di inoltro HTTP che supporta il tunneling HTTP tramite il comando HTTP CONNECT. Il gateway può gestire fino a 2000 dispositivi OMS connessi contemporaneamente quando vengono eseguiti su una CPU a 4 core e un server da 16 GB che esegue Windows.

Ad esempio, l'organizzazione o l'azienda può avere server con connettività di rete ma può non disporre della connettività Internet. In un altro esempio, possono essere disponibili molti dispositivi POS senza alcuna possibilità di un monitoraggio diretto. E in un altro esempio ancora, Operations Manager può usare il gateway OMS come server proxy. In questi esempi, il gateway OMS può trasferire dati dagli agenti installati in questi server o dispositivi POS a OMS.

Anziché ogni singolo agente che invia dati direttamente a OMS e richiede una connessione Internet diretta, tutti i dati dell'agente vengono inviati tramite un singolo computer con una connessione Internet. Tale computer è lo stesso in cui si installa e si usa il gateway. In questo scenario, è possibile installare gli agenti in qualsiasi computer in cui si vuole raccogliere i dati. Il gateway quindi trasferisce i dati dagli agenti direttamente a OMS senza analizzare i dati trasferiti.

È necessario installare l'agente OMS nel computer in cui è installato anche il gateway. In questo modo è possibile monitorare il gateway OMS e analizzare i dati relativi alle prestazioni oppure a un evento per il server in cui è installato. L'agente consente anche al gateway OMS di identificare gli endpoint del servizio con cui deve comunicare.

Il gateway deve avere accesso a Internet per caricare i dati a OMS. Ogni agente deve anche disporre di connettività di rete per il gateway, in modo che gli agenti possano trasferire automaticamente dati da e verso il gateway. Per ottenere risultati ottimali, non installare il gateway in un computer che agisce anche da controller di dominio.

Di seguito è riportato un diagramma che illustra il flusso di dati dagli agenti diretti a OMS.

![Diagramma dell'agente diretto](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Di seguito è riportato un diagramma che illustra il flusso di dati da Operations Manager a OMS.

![Diagramma di Operations Manager](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="language-availability"></a>Lingue disponibili

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

Per ottenere il file di configurazione del gateway OMS, sono disponibili tre modi diversi.

### <a name="microsoft-download-center"></a>Area download Microsoft

- Scaricare la versione più recente del gateway OMS dall'[Area download Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).

### <a name="oms-portal"></a>Portale OMS

1.    Accedere all'area di lavoro OMS.
2.    Selezionare **Settings** > **Connected Sources** > **Windows Servers** (Impostazioni, Origini connesse, Server Windows).
3.    Fare clic su **Download OMS Gateway** (Scarica gateway OMS).


### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com), eseguire l'accesso, visualizzare l'elenco dei servizi e quindi selezionare **Log Analytics**.
2. Selezionare un'area di lavoro.
3. Nel pannello dell'area di lavoro, in **General** fare clic su **Avvio rapido**.
4. In **Choose a data source to connect to the workspace** (Scegliere un'origine dati per connettersi all'area di lavoro) fare clic su **Computer**.
4. Nel pannello **Agente diretto** fare clic su **Download OMS Gateway** (Scarica gateway OMS).  
    ![Scaricare il gateway OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Installare il gateway OMS
L'installazione del gateway sostituisce le versioni del gateway precedentemente installate (server d'inoltro di Log Analytics).

Prerequisiti: .NET Framework 4.5, Windows Server 2012 R2 SP1 e versione successiva


1. Per avviare l'installazione, fare doppio clic su **OMS Gateway.msi**.
2. Nella pagina di benvenuto fare clic su **Avanti**.  
    ![Configurazione guidata gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)
3. Nella pagina del contratto di licenza selezionare **Accetto i termini del contratto di licenza** per accettare le condizioni di licenza e quindi **Avanti**.
4. Nella pagina degli indirizzi della porta e del proxy:
   1. Digitare il numero di porta TCP da usare per il gateway. Il programma di installazione visualizza il numero di porta da Windows Firewall. Il valore predefinito è 8080.
      L'intervallo valido del numero di porta è 1-65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.
   2. In alternativa, se il server in cui è installato il gateway deve usare un proxy, digitare l'indirizzo del proxy in cui il gateway deve connettersi. Ad esempio, `http://myorgname.corp.contoso.com:80`. Se vuoto, il gateway tenterà di connettersi direttamente a Internet. In caso contrario, il gateway si connette al proxy. Se il server proxy richiede l'autenticazione, digitare il nome utente e la password.  
       ![Configurazione guidata del proxy del gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. Fare clic su **Avanti**
5. Se Microsoft Update non è abilitato, viene visualizzata la pagina di Microsoft Update in cui è possibile scegliere di abilitare questo servizio. Effettuare una selezione e quindi fare clic su **Avanti**. In caso contrario, continuare con il passaggio successivo.
6. Nella pagina Cartella di destinazione lasciare la cartella predefinita folder c:\ProgramFiles\OMS Gateway oppure digitare il percorso in cui si vuole installare il gateway e quindi fare clic su **Avanti**.
7. Nella pagina Pronto per l'installazione fare clic su **Installa**. È possibile che venga visualizzato Controllo account utente per la richiesta dell'autorizzazione all'installazione. In questo caso fare clic su **Sì**.
8. Al termine dell'installazione fare clic su **Fine**. È possibile verificare se il servizio è in esecuzione aprendo lo snap-in services.msc e verificare che il **gateway OMS** viene visualizzato nell'elenco dei servizi.  
    ![Servizi – Gateway OMS](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Installare un agente nei dispositivi
Se necessario, vedere [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md) per informazioni su come installare direttamente gli agenti connessi. L'articolo descrive come è possibile installare l'agente tramite una procedura guidata o la riga di comando.

## <a name="configure-oms-agents"></a>Configurare gli agenti di OMS
Per informazioni sulla configurazione di un agente per l'uso di un server proxy, che in questo caso è il gateway, vedere [Configurare le impostazioni proxy e firewall con Microsoft Monitoring Agent](log-analytics-proxy-firewall.md).

Gli agenti di Operations Manager inviano alcuni dati, ad esempio avvisi di Operations Manager, valutazioni della configurazione, spazi dell'istanza e dati sulla capacità, tramite il server di gestione. Altri dati di volumi elevati, ad esempio quelli relativi a log, prestazioni e sicurezza di IIS, vengono inviati direttamente al gateway OMS. Per un elenco completo dei dati inviati tramite ogni canale, vedere [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).

> [!NOTE]
> Se si prevede di usare il gateway con il bilanciamento del carico di rete, vedere [Optionally configure network load balancing](#optionally-configure-network-load-balancing) (Configurare facoltativamente il bilanciamento del carico di rete).
>
>

## <a name="configure-a-scom-proxy-server"></a>Configurare un server proxy SCOM
Configurare Operations Manager per aggiungere il gateway in modo che agisca da server proxy. Quando viene aggiornata, la configurazione del proxy viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager.

Per usare il gateway per il supporto di Operations Manager, è necessario disporre di:

* Microsoft Monitoring Agent (versione dell'agente **8.0.10900.0** e successiva) installato nel server gateway e configurato per le aree di lavoro di OMS con cui si vuole comunicare.
* Il gateway deve disporre di connettività Internet o essere connesso a un server proxy che dispone di tale connettività.

### <a name="to-configure-scom-for-the-gateway"></a>Per configurare SCOM per il gateway
1. Aprire la console di Operations Manager e in **Operations Management Suite** fare clic su **Connessione** e quindi su **Configura server proxy**:  
    ![Operations Manager – Configurare il server proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Selezionare **Usa un server proxy per accedere a Operations Management Suite** e quindi digitare l'indirizzo IP del server gateway OMS. Assicurarsi di iniziare con il prefisso `http://`:  
    ![Operations Manager – Indirizzo del server proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Fare clic su **Fine**. Il server di Operations Manager è collegato all'area di lavoro di OMS.

## <a name="configure-network-load-balancing"></a>Configurare il bilanciamento del carico di rete
È possibile configurare il gateway per la disponibilità elevata usando il bilanciamento del carico di rete mediante la creazione di un cluster. Il cluster gestisce il traffico dagli agenti reindirizzando le connessioni richieste da Microsoft Monitoring Agent attraverso i nodi. Se un server gateway si arresta, il traffico viene reindirizzato ad altri nodi.

1. Aprire Gestione bilanciamento carico di rete e creare un cluster.
2. Fare clic con il pulsante destro del mouse sul cluster prima di aggiungere i gateway e scegliere **Proprietà cluster.** Configurare il cluster in modo che abbia il proprio indirizzo IP:  
    ![Gestione del bilanciamento del carico di rete – Indirizzi IP del cluster](./media/log-analytics-oms-gateway/nlb01.png)
3. Per connettere un server gateway OMS all'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi fare clic su **Aggiungi host al cluster**.  
    ![Gestione del bilanciamento del carico di rete – Aggiungi host al cluster](./media/log-analytics-oms-gateway/nlb02.png)
4. Immettere l'indirizzo IP del server del gateway che si vuole connettere:  
    ![Gestione del bilanciamento del carico di rete – Aggiungi host al cluster: Connetti](./media/log-analytics-oms-gateway/nlb03.png)
5. Nei computer che non dispongono di connettività Internet, assicurarsi di usare l'indirizzo IP del cluster quando si configurano le **proprietà di Microsoft Monitoring Agent**:  
    ![Proprietà di Microsoft Monitoring Agent –Impostazioni proxy](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configurare per i ruoli di lavoro ibridi di automazione
Se nell'ambiente sono disponibili ruoli di lavoro ibridi di automazione, i passaggi seguenti offrono soluzioni alternative manuali e temporanee per configurare il supporto tramite il gateway.

Nei passaggi seguenti, è necessario conoscere l'area di Azure in cui risiede l'account di Automazione. Per individuare la località:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il servizio Automazione di Azure.
3. Selezionare l'account appropriato di Automazione di Azure.
4. Visualizzarne l'area in **Località**.  
    ![Portale di Azure – Località dell'account di Automazione](./media/log-analytics-oms-gateway/location.png)

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

Se il computer è registrato come ruolo di lavoro ibrido automaticamente, per l'applicazione di patch tramite la soluzione di gestione degli aggiornamenti, usare i passaggi seguenti:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway OMS. Ad esempio:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Riavviare il servizio del gateway OMS usando il cmdlet PowerShell seguente: `Restart-Service OMSGatewayService`

Se il computer viene caricato in Automazione di Azure tramite il cmdlet di registrazione del ruolo di lavoro ibrido, usare i passaggi seguenti:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco di host consentiti nel gateway OMS. Ad esempio: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Aggiungere gli URL del servizio dati del processo di runtime all'elenco di host consentiti nel gateway OMS. Ad esempio:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Riavviare il servizio del gateway OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili
I cmdlet consentono di completare le attività necessarie per aggiornare le impostazioni di configurazione del gateway OMS. Prima di usarli, assicurarsi di:

1. Installare il gateway OMS (MSI).
2. Aprire la finestra di PowerShell.
3. Per importare il modulo, digitare il comando `Import-Module OMSGateway`.
4. Se non si verifica alcun errore nel passaggio precedente, il modulo è stato importato ed è possibile usare i cmdlet. Digitare `Get-Module OMSGateway`.
5. Dopo aver apportato le modifiche usando i cmdlet, assicurarsi di riavviare il servizio del gateway.

Se si verifica un errore nel passaggio 3, il modulo non è stato importato. L'errore può verificarsi quando PowerShell non riesce a trovare il modulo. È possibile trovarlo nel percorso di installazione del gateway: C:\Program Files\Microsoft OMS Gateway\PowerShell.

| **Cmdlet** | **Parametri** | **Descrizione** | **esempi** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> Valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Indirizzo <br> Nome utente <br> Password |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. Impostare un proxy di risposta e le credenziali: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Impostare un proxy di risposta che non richiede l'autenticazione: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Deselezionare l'impostazione del proxy di risposta, ovvero non è necessario un proxy di risposta: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Ottiene gli host attualmente consentiti (solo gli host consentiti configurati localmente, non includere gli host consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene gli oggetti del certificato client attualmente consentiti (solo gli oggetti consentiti configurati localmente, non includere gli oggetti consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Risoluzione dei problemi
È necessario installare l'agente OMS nei computer in cui è installato anche il gateway. È quindi possibile usare l'agente per raccogliere gli eventi registrati dal gateway.

![Visualizzatore eventi – Log del gateway OMS](./media/log-analytics-oms-gateway/event-viewer.png)

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

