---
title: Connettere i dati di syslog ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati syslog a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: b2be563efa3c09cffaf14dec2b871f3881af1a7a
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240042"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connettere la soluzione esterna usando syslog

È possibile connettere qualsiasi appliance locale che supporta syslog in Azure Sentinel. Questa operazione viene eseguita usando un agente basato su un computer Linux tra il dispositivo e la sentinella di Azure. Se il computer Linux è in Azure, è possibile trasmettere i log dal dispositivo o dall'applicazione a un'area di lavoro dedicata creata in Azure e connetterla. Se il computer Linux non è in Azure, è possibile trasmettere i log dall'appliance a una macchina virtuale locale dedicata o a un computer in cui si installa l'agente per Linux. 

> [!NOTE]
> Se il dispositivo supporta syslog CEF, la connessione è più completa ed è consigliabile scegliere questa opzione e seguire le istruzioni in [connessione dei dati da CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Come funziona

Syslog è un protocollo di registrazione di eventi comunemente usato in Linux. Le applicazioni inviano messaggi che possono essere archiviati nel computer locale o recapitati a un agente di raccolta di Syslog. Quando è installato, l'agente di Log Analytics per Linux configura il daemon Syslog locale per inoltrare i messaggi all'agente. Quest'ultimo invia quindi il messaggio a Monitoraggio di Azure, dove viene creato un record corrispondente.

Per altre informazioni, vedere [origini dati syslog in monitoraggio di Azure](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> L'agente può raccogliere log da più origini, ma deve essere installato nel computer proxy dedicato.

## <a name="connect-your-syslog-appliance"></a>Connettere l'appliance syslog

1. In Sentinel di Azure selezionare **connettori dati** , quindi selezionare il connettore **syslog** .

2. Nel pannello **syslog** selezionare **Apri connettore pagina**.

3. Installare l'agente Linux:
    
    - Se la macchina virtuale Linux è in Azure, selezionare **scaricare e installare l'agente nella macchina virtuale Linux di Azure**. Nel pannello **macchine virtuali** selezionare le macchine virtuali in cui installare l'agente e quindi fare clic su **Connetti**.
    - Se il computer Linux non è in Azure, selezionare **scaricare e installare l'agente in un computer Linux non Azure**. Nel pannello **agente diretto** copiare il comando per **scaricare e caricare l'agente per Linux** ed eseguirlo nel computer. 
    
   > [!NOTE]
   > Assicurarsi di configurare le impostazioni di sicurezza per questi computer in base ai criteri di sicurezza dell'organizzazione. È ad esempio possibile configurare le impostazioni di rete in modo che siano allineate con i criteri di sicurezza di rete dell'organizzazione e modificare le porte e i protocolli nel daemon per allinearli ai requisiti di sicurezza.

4. Selezionare **Apri la configurazione impostazioni avanzate dell'area di lavoro**.

5. Nel pannello **Impostazioni avanzate** selezionare**syslog**per **i dati** > . Aggiungere quindi le funzionalità per il connettore da raccogliere.
    
    Aggiungere le funzionalità incluse nell'appliance syslog nelle intestazioni dei log. È possibile visualizzare questa configurazione nel dispositivo syslog in **syslog-d** nella `/etc/rsyslog.d/security-config-omsagent.conf` cartella e in **r-syslog** da `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Se si vuole usare il rilevamento anomalo degli accessi SSH con i dati raccolti, aggiungere **AUTH** e **authpriv**. Per ulteriori informazioni, vedere la [sezione seguente](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

6. Quando sono state aggiunte tutte le funzionalità che si desidera monitorare e sono state modificate le opzioni di gravità per ciascuna di esse, selezionare la casella **di controllo applica la configurazione seguente alle macchine virtuali**.

7. Selezionare **Salva**. 

8. Nel dispositivo syslog verificare che siano state inviate le funzionalità specificate.

9. Per usare lo schema pertinente in monitoraggio di Azure per i log syslog, cercare **syslog**.

10. È possibile usare la funzione kusto descritta in [uso di funzioni nelle query log di monitoraggio di Azure](../azure-monitor/log-query/functions.md) per analizzare i messaggi syslog. È quindi possibile salvarli come una nuova funzione Log Analytics da usare come nuovo tipo di dati.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurare il connettore syslog per il rilevamento anomalo degli accessi SSH

> [!IMPORTANT]
> Il rilevamento anomalo degli accessi SSH è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel può applicare Machine Learning (ML) ai dati syslog per identificare l'attività di accesso anomalo Secure Shell (SSH). Gli scenari includono:

- Viaggio impossibile: quando due eventi di accesso riusciti si verificano da due posizioni che non sono raggiungibili entro l'intervallo di tempo dei due eventi di accesso.
- Percorso imprevisto: il percorso da cui si è verificato un evento di accesso riuscito è sospetto. Ad esempio, il percorso non è stato visualizzato di recente.
 
Questo rilevamento richiede una configurazione specifica del connettore dati syslog: 

1. Per il passaggio 5 della procedura precedente, assicurarsi che sia **AUTH** che **authpriv** siano selezionati come funzionalità da monitorare. Mantieni le impostazioni predefinite per le opzioni di gravità, in modo che siano tutte selezionate. Esempio:
    
    > [!div class="mx-imgBorder"]
    > ![Funzionalità necessarie per il rilevamento anomalo degli accessi SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Consente di ottenere tempo sufficiente per la raccolta delle informazioni syslog. Passare quindi ad **Azure Sentinel-logs**e copiare e incollare la query seguente:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Modificare l' **intervallo di tempo** , se necessario, e selezionare **Esegui**.
    
    Se il conteggio risultante è pari a zero, confermare la configurazione del connettore e verificare che l'attività di accesso dei computer monitorati sia riuscita per il periodo di tempo specificato per la query.
    
    Se il conteggio risultante è maggiore di zero, i dati syslog sono appropriati per il rilevamento anomalo degli accessi SSH. È possibile abilitare questo rilevamento dal**rilevamento di accessi SSH anomali**dei**modelli** > di regola di **analisi** >  (anteprima).

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le appliance syslog locali ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
