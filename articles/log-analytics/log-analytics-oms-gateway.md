---
title: Connettere i computer offline a Operations Management Suite tramite Gateway OMS | Documentazione Microsoft
description: Connettere i computer offline gestiti da Operations Management Suite e monitorati da System Center Operations Manager tramite il Gateway OMS per inviare dati al servizio Operations Management Suite.
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
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>Connettere i computer offline a Operations Management Suite tramite Gateway OMS

I computer gestiti da Operations Management Suite (OMS) e monitorati da System Center Operations Manager (Operations Manager) possono inviare i dati al servizio Operations Management Suite quando non hanno accesso a Internet. Il Gateway OMS è un proxy di inoltro HTTP che supporta il tunneling HTTP tramite il comando HTTP CONNECT. Il Gateway OMS può raccogliere dati e inviarli al servizio Operations Management Suite per conto dei computer offline.  

È possibile usare il Gateway OMS con:

* Ruolo di lavoro ibrido per runbook di Automazione di Azure.  
* I computer Windows che dispongono di Microsoft Monitoring Agent e che sono connessi direttamente a un'area di lavoro di Operations Management Suite.
* System Center Operations Manager 2012 SP1 con aggiornamento cumulativo (UR) 7, System Center Operations Manager 2012 R2 con UR3 o un gruppo di gestione di System Center Operations Manager 2016 integrato con Operations Management Suite.  

Alcuni criteri di protezione IT non consentono ai computer in rete di connettersi a Internet. Questo potrebbe essere vero per i dispositivi POS o per i server che supportano servizi IT. Tuttavia, è necessario connettere i computer a Operations Management Suite per gestirli e monitorarli. È possibile impostare i computer offline per comunicare direttamente con Gateway OMS. Il gateway riceve la configurazione e inoltra i dati per conto dei computer offline.  

Se si configurano i computer con agente Operations Management Suite per connettersi direttamente a un'area di lavoro di Operations Management Suite, tutti i computer comunicheranno invece tramite il gateway OMS. Il gateway trasferisce i dati dagli agenti direttamente a Operations Management Suite. Il gateway non analizza tutti i dati in transito.

Quando un gruppo di gestione di Operations Manager si integra con Operations Management Suite, è possibile impostare i server di gestione per la connessione al gateway OMS. I server ricevono informazioni di configurazione e quindi inviano i dati raccolti, a seconda della soluzione impostata. Gli agenti di Operations Manager inviano alcuni dati, ad esempio avvisi di Operations Manager, valutazioni della configurazione, spazio dell'istanza e dati sulla capacità, al server di gestione. Altri, dati di volumi elevati come ad esempio quelli relativi ai log di Internet Information Services (IS), alle prestazioni e agli eventi di sicurezza, vengono inviati direttamente al gateway OMS. 

Un server gateway di Operations Manager che viene distribuito in una rete perimetrale (nota anche come *DMZ*, *zona demilitarizzata* e *sottorete selezionata*) o in un'altra rete isolata per monitorare i sistemi non attendibili non può comunicare con il gateway OMS. I server gateway di Operations Manager possono fare riferimento solo a un server di gestione. Quando si imposta un gruppo di gestione di Operations Manager per la comunicazione con il gateway OMS, le informazioni di configurazione del proxy vengono automaticamente distribuite a ogni computer gestito tramite agente impostato per la raccolta dei dati per Azure Log Analytics. Le informazioni di configurazione del proxy vengono distribuite automaticamente anche se l'impostazione è vuota.    

Per offrire disponibilità elevata per gli agenti direttamente connessi o per i gruppi Operations Management che comunicano con Operations Management Suite tramite il gateway, è possibile usare il bilanciamento del carico di rete (NLB). Il bilanciamento del carico di rete reindirizza e distribuisce il traffico tra più server gateway. Se un server gateway si arresta, il traffico viene reindirizzato a un altro nodo disponibile.  

Si consiglia di installare l'agente Operations Management Suite nel computer in cui è in esecuzione il software del gateway OMS. In questo modo, è possibile monitorare il gateway OMS e analizzare i dati degli eventi o delle prestazioni. L'agente consente anche al gateway OMS di identificare gli endpoint del servizio con cui deve comunicare.

Ogni agente deve avere connettività di rete per il gateway, in modo che gli agenti possano trasferire automaticamente dati da e verso il gateway. Si consiglia di non installare il gateway in un controller di dominio.

Il diagramma seguente illustra il flusso di dati dagli agenti diretti a Operations Management Suite tramite il server gateway. La configurazione del proxy per gli agenti deve corrispondere alla porta in cui il gateway OMS è configurato per comunicare con Operations Management Suite.  

![comunicazione diretta dell'agente con il diagramma Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Il diagramma seguente mostra il flusso di dati da un gruppo di gestione di Operations Manager a Operations Management Suite.   

![La comunicazione di Operation Manager con il diagramma del diagramma di Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>Prerequisiti

Il computer in cui si è scelto di eseguire Gateway OMS deve soddisfare questi requisiti:

* Sistema operativo Windows 10, Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008
* Microsoft .NET Framework 4.5
* Processore di almeno 4 core
* Almeno 8 GB di memoria 

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

## <a name="download-oms-gateway"></a>Scaricare il gateway OMS

Sono disponibili tre opzioni per ottenere la versione più recente del gateway OMS:

* Scaricarla dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

* Scaricarla dal portale di Operations Management Suite. Dopo aver avuto accesso all'area di lavoro di Operations Management Suite:

    1. Passare a **Impostazioni** > **Origini connesse** > **Server Windows**. 
    2. Selezionare **Scarica gateway OMS**.

* Scaricarlo dal [portale di Azure](https://portal.azure.com). Dopo avere effettuato l'accesso:  

   1. Nell'elenco dei servizi selezionare **Log Analytics**.  
   2. Selezionare un'area di lavoro.
   3. Nel pannello dell'area di lavoro in **Generale** selezionare **Avvio rapido**.
   4. In **Scegliere un'origine dati da connettere all'area di lavoro** selezionare **Computer**.
   5. Nel pannello **Agente diretto** selezionare **Scarica gateway OMS**.<br><br> ![Scaricare il gateway OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>Installare il gateway OMS

Per installare il gateway, completare i passaggi seguenti. 

> [!NOTE]
> Se è installata una versione precedente del gateway (noto in precedenza come server d'inoltro di Log Analytics), la versione precedente viene aggiornata alla versione corrente del gateway OMS.
>

1. Nella cartella di destinazione fare doppio clic su **OMS Gateway.msi**.
2. Nella pagina di **benvenuto** selezionare **Avanti**.<br><br> ![Configurazione guidata gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Nella pagina **Contratto di licenza**, per accettare le condizioni di licenza di Microsoft Software, selezionare **Accetto i termini del contratto di licenza**. 
4. Nella pagina **Port and proxy address** (Indirizzo porta e proxy):

   1. Inserire il numero di porta TCP da usare per il gateway. Viene configurata una regola in ingresso con questo numero di porta in Windows Firewall. Il valore predefinito è 8080.
      L'intervallo valido per il numero di porta è da 1 a 65535. Se l'input non è compreso in questo intervallo, viene visualizzato un messaggio di errore.
   2. In alternativa, se il server in cui è installato il gateway deve comunicare tramite un proxy, inserire l'indirizzo del proxy in cui il gateway deve connettersi. Ad esempio, **http://myorgname.corp.contoso.com:80**. Se la casella di indirizzo proxy è vuota, il gateway tenta di connettersi direttamente a Internet. Se il server proxy richiede l'autenticazione, immettere un nome utente e una password.<br><br> ![Configurazione guidata del proxy del gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. Se Microsoft Update non è abilitato nel computer, verrà visualizzata la pagina **Microsoft Update**. È possibile scegliere di abilitarlo in questa pagina. In caso contrario, continuare con il passaggio successivo.
6. Nella pagina **Cartella di destinazione** è possibile lasciare la cartella predefinita C:\Programmi\OMS Gateway oppure inserire il percorso in cui si vuole installare il gateway.
7. Nella pagina **Pronto per l'installazione** selezionare **Installa**. Potrebbe essere visualizzata una finestra di dialogo che richiede l'autorizzazione per l'installazione. Selezionare **Sì**.
8. Selezionare **Fine**. Per verificare se il servizio è in esecuzione, aprire lo snap-in Services.msc e controllare che il **gateway OMS** sia visualizzato nell'elenco dei servizi. Lo stato deve essere **In esecuzione**.<br><br> ![Verificare i servizi per il gateway OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>Impostare il bilanciamento del carico di rete 
È possibile impostare il gateway OMS per la disponibilità elevata tramite il bilanciamento del carico di rete. È possibile usare la funzionalità Bilanciamento carico di rete in Windows Server o nei servizi di bilanciamento del carico basati su hardware. Per gestire il traffico, il bilanciamento del carico reindirizza le connessioni richieste dagli agenti Operations Management Suite o dai server di gestione di Operations Manager nei nodi. Se un server gateway si arresta, il traffico viene reindirizzato ad altri nodi.

Per informazioni su come progettare e distribuire un cluster di bilanciamento del carico di rete di Windows Server 2016, vedere [Bilanciamento carico di rete](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  

Per configurare un cluster di bilanciamento del carico di rete di Windows Server, completare i passaggi seguenti:  

1. Usando un account amministratore, accedere al server di Windows che è un membro del cluster del bilanciamento del carico di rete.  
2. In Server Manager, aprire Gestione bilanciamento carico di rete.
3. Selezionare **Strumenti** e quindi selezionare **Gestione bilanciamento carico di rete**.
4. Per connettere un server gateway OMS con l'istanza di Microsoft Monitoring Agent installata, fare clic con il pulsante destro del mouse sull'indirizzo IP del cluster e quindi selezionare **Aggiungi host al cluster**.<br><br> ![Gestione bilanciamento carico di rete - Aggiungi host al cluster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. Immettere l'indirizzo IP del server gateway che si vuole connettere.<br><br> ![Gestione bilanciamento carico di rete - Aggiungi host al cluster: Connetti](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>Configurare l'agente Operations Management Suite e un gruppo di gestione di Operations Manager
In questa sezione viene descritto come configurare gli agenti di Operations Management Suite direttamente connessi e un gruppo di gestione di Operations Manager. È anche possibile impostare il ruolo di lavoro ibrido per runbook di Automazione di Azure per usare il gateway OMS per comunicare con Operations Management Suite.  

Per informazioni sui requisiti e i passaggi per installare l'agente Operations Management Suite nei computer Windows che sono connessi direttamente a Operations Management Suite, vedere [Connect Windows computers to Operations Management Suite](log-analytics-windows-agents.md) (Connettere i computer Windows a Operations Management Suite). Per i computer Linux, vedere [Connect Linux computers to Operations Management Suite](log-analytics-linux-agents.md) (Connettere i computer Linux a Operations Management Suite). 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Impostare l'agente di Operations Management Suite e di Operations Manager per usare il gateway OMS come server proxy

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>Impostare un agente singolo di Operations Management Suite
Per informazioni sulla configurazione di un agente per l'uso di un server proxy, vedere [Configure proxy and firewall settings with Microsoft Monitoring Agent](log-analytics-proxy-firewall.md) (Configurare le impostazioni proxy e firewall con Microsoft Monitoring Agent). In questo caso, il server proxy è il gateway. Se sono stati distribuiti più server gateway dietro un servizio di bilanciamento del carico di rete, la configurazione del proxy dell'agente di Operations Management Suite corrisponde all'indirizzo IP virtuale del servizio di bilanciamento del carico di rete:<br><br> ![Proprietà di Microsoft Monitoring Agent: Impostazioni proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>Impostare Operations Manager (tutti gli agenti usano lo stesso server proxy)
Impostare Operations Manager per aggiungere il server gateway. La configurazione del proxy di Operations Manager viene applicata automaticamente a tutti gli agenti che creano report in Operations Manager, anche se l'impostazione è vuota.

Per usare il gateway OMS con Operations Manager, è necessario soddisfare i requisiti seguenti:

* Microsoft Monitoring Agent (versione dell'agente **8.0.10900.0** e versioni successive) deve essere installato nel server del gateway. Deve essere configurato per le aree di lavoro dell'agente di Operations Management Suite con cui si desidera comunicare.
* Il gateway deve disporre di connettività Internet o essere connesso a un server proxy che dispone di tale connettività.

> [!NOTE]
> Se non si specifica un valore per il gateway, viene effettuato il push di valori vuoti a tutti gli agenti.

Per connettere il server di Operations Manager all'area di lavoro dell'agente di Operations Management Suite:

1. Nella console di Operations Manager, passare a **Operations Management Suite** > **Connessione** > **Configura server proxy**.<br><br> ![Operations Manager: Configura server proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Selezionare **Usa un server proxy per accedere a Operations Management Suite**. Immettere l'indirizzo IP del server del gateway di OMS o l'indirizzo IP virtuale del bilanciamento del carico di rete. Assicurarsi di iniziare con il prefisso **http://**.<br><br> ![Operations Manager: Indirizzo del server proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Selezionare **Fine**. 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>Impostare Operations Manager (agenti specifici usano il server proxy)
È possibile che in ambienti complessi o di grandi dimensioni si desidera che solo specifici server (o gruppi) usino il server gateway OMS. Per questi server, non è possibile aggiornare direttamente l'agente di Operations Manager. Il valore viene sovrascritto dal valore globale per il gruppo di gestione. Eseguire invece l'override della regola che viene usata per eseguire il push di questi valori.

> [!NOTE] 
> È possibile usare la stessa tecnica di configurazione per eseguire più server gateway OMS in un ambiente. È ad esempio possibile fare in modo che vengano specificati determinati server gateway OMS per ogni area.

1. Nella console di System Center Operations Manager, selezionare l'area di lavoro **Creazione**.  
2. Selezionare **Regole** e sulla barra degli strumenti di System Center Operations Manage, selezionare il pulsante **Ambito**. Se questo pulsante non è disponibile, verificare che nel riquadro **Monitoraggio** sia selezionato un oggetto e non una cartella. La finestra di dialogo **Crea ambito oggetti Management Pack** visualizza un elenco di classi, gruppi o oggetti di destinazione comuni. 
3. Nella casella **Cerca** inserire **Servizio integrità**. Selezionarlo dall'elenco. Selezionare **OK**.  
4. Sulla barra degli strumenti della console di Operations Manager, cercare la regola **Advisor Proxy Setting Rule** (Regola delle impostazioni di Advisor Proxy). Selezionare **Sostituzioni**e quindi **Override the Rule\For a specific object of class: Health Service** (Eseguire l'Override della Regola\Per un oggetto specifico della classe: Servizio di integrità). Selezionare un oggetto specifico dall'elenco. Facoltativamente, è possibile creare un gruppo personalizzato che ha l'oggetto del servizio integrità dei server a cui si desidera applicare questa sostituzione. Applicare quindi la sostituzione al gruppo.
5. Nella finestra di dialogo **Proprietà di sostituzione** accanto al parametro **WebProxyAddress** selezionare la colonna **Sostituzione**. Nella casella **Valore di sostituzione** inserire l'URL del server gateway OMS. Assicurarsi di iniziare con il prefisso **http://**.

   >[!NOTE]
   > Non è necessario attivare la regola. La regola viene gestita automaticamente con una sostituzione nel management pack delle sostituzioni di System Center Advisor Secure Reference. Il management pack è destinato al gruppo di server di monitoraggio di System Center Advisor.
   > 

6. Per specificare un management pack, eseguire una delle operazioni seguenti:
    * Nell'elenco**Seleziona Management Pack di destinazione:** selezionare un management pack.
    * Per creare un nuovo management pack non bloccato, selezionare **Nuovo**. 
7. Selezionare **OK**. 

### <a name="set-up-automation-hybrid-workers"></a>Impostare ruolo di lavoro ibrido di Automazione
Se si dispone della funzionalità dei ruoli di lavoro ibridi per runbook di Automazione di Azure nell'ambiente in uso, è possibile creare una soluzione alternativa manuale, temporanea in modo che il gateway possa supportare i ruoli.

Per i passaggi seguenti, è necessario conoscere l'area di Azure in cui risiede l'account di Automazione. Per trovare la posizione:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il servizio Automazione di Azure.
3. Selezionare l'account pertinente di Automazione di Azure.
4. In **Posizione** vedere l'area.<br><br> ![Portale di Azure: posizione dell'account di Automazione](./media/log-analytics-oms-gateway/location.png)  

Usare le tabelle seguenti per identificare l'URL di ogni località:

**URL del servizio dati del processo di runtime**

| Percorso | URL |
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

| Percorso | URL |
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

Se il computer è registrato automaticamente come un ruolo di lavoro ibrido per runbook, per l'applicazione di patch tramite la soluzione di gestione degli aggiornamenti, completare questa procedura:

1. Aggiungere gli URL del servizio dati del processo di runtime all'elenco **Allowed Host** (Host consentiti) nel gateway OMS. Ad esempio:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Riavviare il servizio del gateway OMS usando il cmdlet di Azure PowerShell seguente: `Restart-Service OMSGatewayService`

Se il computer viene caricato in Automazione di Azure usando il cmdlet di registrazione del ruolo di lavoro ibrido per runbook, completare questa procedura:

1. Aggiungere l'URL di registrazione del servizio agente all'elenco **Allowed Host** (Host consentiti) nel gateway OMS. Ad esempio: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Aggiungere gli URL del servizio dati del processo di runtime all'elenco **Allowed Host** (Host consentiti) nel gateway OMS. Ad esempio:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Riavviare il servizio del gateway OMS:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlet PowerShell utili
I cmdlet consentono di aggiornare le impostazioni del gateway OMS. Prima di usare i cmdlet di PowerShell, assicurarsi di:

1. Installare il gateway OMS (MSI).
2. Aprire una finestra della console di PowerShell.
3. Per importare il modulo, immettere il comando `Import-Module OMSGateway`. Se non si verifica alcun errore, il modulo può essere importato con successo ed è possibile usare i cmdlet. 
4. Immettere `Get-Module OMSGateway`.
5. Dopo aver apportato le modifiche usando i cmdlet, riavviare il servizio del gateway OMS.

Se si verifica un errore nel passaggio 3, il modulo non è stato importato. Questo errore può verificarsi se PowerShell non è in grado di trovare il modulo. Il modulo è elencato nel percorso di installazione del gateway: C:\Programmi\Microsoft OMS Gateway\PowerShell.

| Cmdlet | Parametri | Descrizione | esempi |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Chiave (obbligatorio) <br> Valore |Modifica la configurazione del servizio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Chiave |Ottiene la configurazione del servizio |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Indirizzo <br> Nome utente <br> Password |Imposta l'indirizzo (e le credenziali) del proxy di inoltro (upstream) |1. Impostare un proxy di risposta e le credenziali: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Impostare un proxy di risposta che non richiede l'autenticazione: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Deselezionare l'impostazione del proxy di risposta, ovvero non è necessario un proxy di risposta: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Ottiene l'indirizzo del proxy di inoltro (upstream) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Host (obbligatorio) |Aggiunge l'host all'elenco dei valori consentiti |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Host (obbligatorio) |Rimuove l'host dall'elenco dei valori consentiti |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Ottiene gli host attualmente consentiti (solo gli host consentiti configurati localmente, non include gli host consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Aggiunge l'oggetto del certificato client all'elenco dei valori consentiti |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Oggetto (obbligatorio) |Rimuove l'oggetto del certificato client dall'elenco dei valori consentiti |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Ottiene gli oggetti del certificato client attualmente consentiti (solo gli oggetti consentiti configurati localmente, non include gli oggetti consentiti scaricati automaticamente) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>Risoluzione dei problemi
Per raccogliere gli eventi registrati dal gateway, è necessario che sia installato anche l'agente Operations Management Suite.<br><br> ![Visualizzatore eventi: Log del gateway OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**ID e descrizioni dell'evento del gateway OMS**

La tabella seguente elenca gli ID evento e le descrizioni degli eventi di log del gateway OMS:

| ID | Descrizione |
| --- | --- |
| 400 |Qualsiasi errore dell'applicazione che non dispone di un ID specifico |
| 401 |Configurazione errata. Ad esempio: listenPort = "\<text\>" invece di un intero |
| 402 |Eccezione durante l'analisi dei messaggi di handshake di Transport Layer Security (TLS) |
| 403 |Errore di rete. Ad esempio: non è possibile connettersi al server di destinazione |
| 100 |Informazioni generali |
| 101 |Servizio avviato |
| 102 |Servizio arrestato |
| 103 |Ricevuto un comando HTTP CONNECT dal client |
| 104 |Non è stato ricevuto un comando HTTP CONNECT |
| 105 |Il server di destinazione non è presente nell'elenco dei valori consentiti o la porta di destinazione non è sicura (443) <br> <br> Assicurarsi che l'agente Microsoft Monitoring Agent sul server gateway e gli agenti che comunicano con il gateway siano connessi alla stessa area di lavoro di Log Analytics. |
| 105 |ERRORE TcpConnection – Certificato client non valido: CN=Gateway <br><br> Assicurarsi che: <br>    <br> &#149; Sia in uso la versione del gateway OSM 1.0.395.0 o versione successiva. <br> &#149; L'agente Microsoft Monitoring Agent sul server gateway e gli agenti che comunicano con il gateway siano connessi alla stessa area di lavoro di Log Analytics. |
| 106 |La sessione TLS è sospetta e viene rifiutata |
| 107 |La sessione TLS è stata verificata |

**Contatori delle prestazioni da raccogliere**

La tabella seguente elenca i contatori delle prestazioni che sono disponibili per il gateway OMS. È possibile aggiungere i contatori usando il monitor di prestazioni di Windows.

| Nome | Descrizione |
| --- | --- |
| Gateway OMS/Connessione client attiva |Numero di connessioni di rete client attive (TCP) |
| Gateway OMS/Numero di errori |Numero di errori |
| Gateway OMS/Client connesso |Numero di client connessi |
| Gateway OMS/Numero di rifiuti |Numero di rifiuti a causa di errori di convalida TLS |

![Contatori delle prestazioni del gateway OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Ottenere supporto
Nel portale di Azure, dopo aver eseguito l'accesso, è possibile creare una richiesta di supporto con il gateway OMS o qualsiasi altro servizio di Azure o funzionalità di servizio.
Per richiedere supporto, selezionare il simbolo del punto interrogativo nell'angolo superiore destro del portale e quindi selezionare **Nuova richiesta di supporto**. Completare il nuovo modulo di richiesta di supporto.

![Nuova richiesta di supporto](./media/log-analytics-oms-gateway/support.png)

È anche possibile lasciare commenti e suggerimenti su Operations Management Suite o Log Analytics nel [Forum di commenti e suggerimenti su Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Passaggi successivi
Per raccogliere i dati dalle origini connesse nell'area di lavoro Operations Management Suite, è possibile [aggiungere origini dati](log-analytics-data-sources.md). È possibile archiviare la data nell'archivio di Operations Management Suite.

