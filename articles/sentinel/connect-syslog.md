---
title: Connettere i dati di Syslog ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati Syslog ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588077"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connettere la soluzione esterna utilizzando Syslog

È possibile connettere qualsiasi appliance locale che supporta Syslog ad Azure Sentinel.You can connect any on-premises appliance that supports Syslog to Azure Sentinel. Questa operazione viene eseguita utilizzando un agente basato su un computer Linux tra l'appliance e Azure Sentinel.This is done by using an agent based on a Linux machine between the appliance and Azure Sentinel. Se il computer Linux è in Azure, è possibile trasmettere i log dall'appliance o dall'applicazione a un'area di lavoro dedicata creata in Azure e connetterla. Se il computer Linux non è in Azure, è possibile trasmettere i log dall'appliance a una macchina virtuale o a un computer locale dedicato in cui si installa l'agente per Linux.If your Linux machine is not in Azure, you can stream the logs from your appliance to a dedicated on premises VM or machine on which you install the Agent for Linux. 

> [!NOTE]
> Se l'appliance supporta Syslog CEF, la connessione è più completa ed è consigliabile scegliere questa opzione e seguire le istruzioni in [Connessione dei dati da CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Funzionamento

Syslog è un protocollo di registrazione di eventi comunemente usato in Linux. Le applicazioni inviano messaggi che possono essere archiviati nel computer locale o recapitati a un agente di raccolta di Syslog. Quando è installato, l'agente di Log Analytics per Linux configura il daemon Syslog locale per inoltrare i messaggi all'agente. Quest'ultimo invia quindi il messaggio a Monitoraggio di Azure, dove viene creato un record corrispondente.

Per altre informazioni, vedere [Origini dati Syslog in Monitoraggio di Azure.For](../azure-monitor/platform/data-sources-syslog.md)more information, see Syslog data sources in Azure Monitor .

> [!NOTE]
> - L'agente può raccogliere log da più origini, ma deve essere installato in un computer proxy dedicato.
> - Se si desidera supportare i connettori per CEF e Syslog nella stessa macchina virtuale, eseguire la procedura seguente per evitare la duplicazione dei dati:
>    1. Seguire le istruzioni per [collegare il CEF](connect-common-event-format.md).
>    2. Per connettere i dati Syslog, passare a **Impostazioni impostazioni** > **dell'area di lavoro** > **Impostazioni avanzate** > **Syslog** > **Syslog** dei dati e impostare le strutture e le relative priorità in modo che non siano le stesse strutture e proprietà utilizzate nella configurazione CEF. <br></br>Se si seleziona **Applica sotto la configurazione ai computer,** queste impostazioni vengono applicate a tutte le macchine virtuali connesse a questa area di lavoro.


## <a name="connect-your-syslog-appliance"></a>Collegare l'appliance Syslog

1. In Azure Sentinel selezionare Connettori dati e quindi selezionare il connettore **Syslog.In** Azure Sentinel, select **Data connectors** and then select the Syslog connector.

2. Nel pannello **Syslog** selezionare **Apri pagina connettore**.

3. Installare l'agente Linux:
    
    - Se la macchina virtuale Linux è in Azure, selezionare **Scarica e installa agente nella macchina virtuale Linux di Azure.** Nel pannello **Macchine virtuali** selezionare le macchine virtuali in cui installare l'agente e quindi fare clic su **Connetti**.
    - Se il computer Linux non è in Azure, selezionare Scarica e installa agente in computer non Azure Linux.If your Linux machine isn't in Azure, select **Download and install agent on Linux non-Azure machine**. Nel pannello **Agente diretto** copiare il comando per DOWNLOAD **E ONBOARD AGENT FOR LINUX** ed eseguirlo nel computer. 
    
   > [!NOTE]
   > Assicurarsi di configurare le impostazioni di sicurezza per questi computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare le impostazioni di rete in modo che siano in linea con i criteri di sicurezza di rete dell'organizzazione e modificare le porte e i protocolli nel daemon in modo che siano allineati ai requisiti di sicurezza.

4. Selezionare **Apri configurazione impostazioni avanzate area di lavoro**.

5. Nel pannello **Impostazioni avanzate** selezionare **Data** > **Syslog**. Aggiungere quindi le strutture per il connettore da raccogliere.
    
    Aggiungere le strutture incluse nell'appliance syslog nelle intestazioni di log. È possibile visualizzare questa configurazione nell'appliance Syslog `/etc/rsyslog.d/security-config-omsagent.conf` in **Syslog-d** nella `/etc/syslog-ng/security-config-omsagent.conf`cartella e in **r-Syslog** da .
    
    Se si desidera utilizzare il rilevamento anomalo dell'accesso SSH con i dati raccolti, aggiungere **auth** e **authpriv**. Vedere la [sezione seguente](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) per ulteriori dettagli.

6. Dopo aver aggiunto tutte le strutture che si desidera monitorare e aver modificato le opzioni di gravità per ognuna di esse, selezionare la casella di controllo Applica sotto la **configurazione alle macchine**.

7. Selezionare **Salva**. 

8. Nell'appliance syslog, assicurarsi di inviare le strutture specificate.

9. Per usare lo schema pertinente in Monitoraggio di Azure per i log syslog, cercare **Syslog**.

10. È possibile usare la funzione Kusto descritta in Uso delle funzioni nelle query di log di Monitoraggio di Azure per analizzare i messaggi Syslog.You can use the Kusto function described in [Using functions in Azure Monitor log queries](../azure-monitor/log-query/functions.md) to parse your Syslog messages. È quindi possibile salvarli come nuova funzione di Log Analytics da usare come nuovo tipo di dati.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurare il connettore Syslog per il rilevamento anomalo dell'accesso SSH

> [!IMPORTANT]
> Il rilevamento dell'accesso SSH anomalo è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel può applicare l'apprendimento automatico (ML) ai dati syslog per identificare l'attività di accesso sSH (Secure Shell) anomala. Gli scenari includono:

- Viaggi impossibili: quando si verificano due eventi di accesso riusciti da due posizioni impossibili da raggiungere entro il periodo di tempo dei due eventi di accesso.
- Posizione imprevista: la posizione da cui si è verificato un evento di accesso riuscito è sospetta. Ad esempio, la posizione non è stata visualizzata di recente.
 
Questo rilevamento richiede una configurazione specifica del connettore dati Syslog: 

1. Per il passaggio 5 della procedura precedente, assicurarsi che **sia auth** che **authpriv** siano selezionati come strutture da monitorare. Mantenere le impostazioni predefinite per le opzioni di gravità, in modo che siano tutte selezionate. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![Strutture necessarie per il rilevamento anomalo dell'accesso SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Concedere tempo sufficiente per la raccolta di informazioni syslog. Passare quindi a **Azure Sentinel - Logs**e copiare e incollare la query seguente:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Modificare **l'intervallo** di tempo, se necessario, e selezionare **Esegui**.
    
    Se il conteggio risultante è zero, verificare la configurazione del connettore e che i computer monitorati abbiano un'attività di accesso corretta per il periodo di tempo specificato per la query.
    
    Se il conteggio risultante è maggiore di zero, i dati syslog sono adatti per il rilevamento anomalo dell'accesso SSH. Questo rilevamento viene abilitato dai**modelli** > di regole di **analisi** >  **(anteprima) Anomalous SSH Login Detection**.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Syslog on-premises appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

