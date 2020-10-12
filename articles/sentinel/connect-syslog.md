---
title: Connettere i dati di syslog ad Azure Sentinel | Microsoft Docs
description: Connettere un computer o un dispositivo che supporta syslog ad Azure Sentinel usando un agente in un computer Linux tra il dispositivo e Sentinel. 
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7670d00a2dd25961a51d18c50c102e0f92b30975
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88566149"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Raccogliere dati da origini basate su Linux usando syslog

È possibile trasmettere gli eventi da computer o appliance di supporto Syslog basati su Linux in Azure Sentinel, usando l'agente di Log Analytics per Linux (noto in precedenza come agente OMS). Questa operazione può essere eseguita per qualsiasi computer che consente di installare l'agente di Log Analytics direttamente nel computer. Il daemon syslog nativo del computer raccoglierà gli eventi locali dei tipi specificati e li inoltrerà localmente all'agente, che li trasmetterà all'area di lavoro Log Analytics.

> [!NOTE]
> - Se l'Appliance supporta il **formato CEF (Common Event Format) su syslog**, viene raccolto un set di dati più completo e i dati vengono analizzati in corrispondenza della raccolta. È consigliabile scegliere questa opzione e seguire le istruzioni riportate in [connettere la soluzione esterna utilizzando CEF](connect-common-event-format.md).
>
> - Log Analytics supporta la raccolta di messaggi inviati dai daemon **rsyslog** o **syslog-ng** , dove rsyslog è il valore predefinito. Il daemon predefinito syslog nella versione 5 di Red Hat Enterprise Linux (RHEL), CentOS e Oracle Linux Version (**sysklog**) non è supportato per la raccolta di eventi syslog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il daemon rsyslog in modo da sostituire sysklog.

## <a name="how-it-works"></a>Funzionamento

**Syslog** è un protocollo di registrazione eventi comune a Linux. Quando l' **agente di log Analytics per Linux** è installato nella macchina virtuale o nel dispositivo, la routine di installazione configura il daemon syslog locale per l'invio di messaggi all'agente sulla porta TCP 25224. L'agente invia quindi il messaggio all'area di lavoro di Log Analytics su HTTPS, dove viene analizzato in una voce del registro eventi nella tabella syslog in **Azure Sentinel > log**.

Per altre informazioni, vedere [origini dati syslog in monitoraggio di Azure](../azure-monitor/platform/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Configurare la raccolta syslog

### <a name="configure-your-linux-machine-or-appliance"></a>Configurare il computer o l'appliance Linux

1. In Sentinel di Azure selezionare **connettori dati** , quindi selezionare il connettore **syslog** .

1. Nel pannello **syslog** selezionare **Apri connettore pagina**.

1. Installare l'agente Linux. In **scegliere il percorso di installazione dell'agente:**
    
    **Per una VM Linux di Azure:**
      
    1. Selezionare **Installa agente nella macchina virtuale Linux di Azure**.
    
    1. Fare clic sul collegamento **scarica & installa agente per macchine virtuali Linux di Azure >** . 
    
    1. Nel pannello **macchine virtuali** fare clic su una macchina virtuale in cui installare l'agente, quindi fare clic su **Connetti**. Ripetere questo passaggio per ogni macchina virtuale che si vuole connettere.
    
    **Per qualsiasi altro computer Linux:**

    1. Selezionare **Installa agente in un computer Linux non Azure**

    1. Fare clic sul collegamento **scarica & installa agente per computer Linux non Azure >** . 

    1. Nel pannello **Gestione agenti** fare clic sulla scheda **server Linux** , quindi copiare il comando per **scaricare e caricare l'agente per Linux** ed eseguirlo nel computer Linux. 
    
   > [!NOTE]
   > Assicurarsi di configurare le impostazioni di sicurezza per questi computer in base ai criteri di sicurezza dell'organizzazione. È ad esempio possibile configurare le impostazioni di rete in modo che siano allineate con i criteri di sicurezza di rete dell'organizzazione e modificare le porte e i protocolli nel daemon per allinearli ai requisiti di sicurezza.

### <a name="configure-the-log-analytics-agent"></a>Configurare l'agente di Log Analytics

1. Nella parte inferiore del pannello del connettore syslog fare clic sul collegamento **aprire la configurazione impostazioni avanzate dell'area di lavoro >** .

1. Nel pannello **Impostazioni avanzate** selezionare syslog per **i dati**  >  **Syslog**. Aggiungere quindi le funzionalità per il connettore da raccogliere.
    
    - Aggiungere le funzionalità incluse nell'appliance syslog nelle intestazioni dei log. 
    
    - Se si vuole usare il rilevamento anomalo degli accessi SSH con i dati raccolti, aggiungere **AUTH** e **authpriv**. Per ulteriori informazioni, vedere la [sezione seguente](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

1. Quando sono state aggiunte tutte le funzionalità che si desidera monitorare e sono state modificate le opzioni di gravità per ciascuna di esse, selezionare la casella **di controllo applica la configurazione seguente alle macchine virtuali**.

1. Selezionare **Salva**. 

1. Nella macchina virtuale o nel dispositivo verificare che siano state inviate le funzionalità specificate.

1. Per eseguire una query sui dati del log syslog nei **log**, digitare `Syslog` nella finestra query.

1. È possibile usare i parametri di query descritti in [uso delle funzioni nelle query log di monitoraggio di Azure](../azure-monitor/log-query/functions.md) per analizzare i messaggi syslog. È quindi possibile salvare la query come nuova funzione Log Analytics e utilizzarla come nuovo tipo di dati.

> [!NOTE]
> **Uso dello stesso computer per l'invio di messaggi syslog *e* CEF normali**
>
>
> È possibile utilizzare il [computer del server d'avanzamento del log CEF](connect-cef-agent.md) esistente per raccogliere e trasmettere log da origini syslog semplici. Tuttavia, è necessario eseguire la procedura seguente per evitare di inviare eventi in entrambi i formati ad Azure Sentinel, in quanto ciò comporta la duplicazione degli eventi.
>
>    La configurazione della [raccolta dati dalle origini CEF](connect-common-event-format.md)è già stata configurata e l'agente di log Analytics è stato configurato come descritto in precedenza:
>
> 1. In ogni computer che invia log in formato CEF è necessario modificare il file di configurazione Syslog per rimuovere le funzionalità usate per inviare i messaggi CEF. In questo modo, le funzionalità inviate in CEF non verranno inviate anche in syslog. Per istruzioni dettagliate su come eseguire questa operazione, vedere [configurare syslog nell'agente Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. È necessario eseguire il comando seguente in questi computer per disabilitare la sincronizzazione dell'agente con la configurazione di syslog in Sentinel di Azure. In questo modo si garantisce che la modifica della configurazione apportata nel passaggio precedente non venga sovrascritta.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurare il connettore syslog per il rilevamento anomalo degli accessi SSH

> [!IMPORTANT]
> Il rilevamento anomalo degli accessi SSH è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel può applicare Machine Learning (ML) ai dati syslog per identificare l'attività di accesso anomalo Secure Shell (SSH). Gli scenari includono:

- Viaggio impossibile: quando due eventi di accesso riusciti si verificano da due posizioni che non sono raggiungibili entro l'intervallo di tempo dei due eventi di accesso.
- Percorso imprevisto: il percorso da cui si è verificato un evento di accesso riuscito è sospetto. Ad esempio, il percorso non è stato visualizzato di recente.
 
Questo rilevamento richiede una configurazione specifica del connettore dati syslog: 

1. Per il passaggio 5 della procedura precedente, assicurarsi che sia **AUTH** che **authpriv** siano selezionati come funzionalità da monitorare. Mantieni le impostazioni predefinite per le opzioni di gravità, in modo che siano tutte selezionate. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![Funzionalità necessarie per il rilevamento anomalo degli accessi SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Consente di ottenere tempo sufficiente per la raccolta delle informazioni syslog. Passare quindi ad **Azure Sentinel-logs**e copiare e incollare la query seguente:
    
    ```console
    Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    ```
    
    Modificare l' **intervallo di tempo** , se necessario, e selezionare **Esegui**.
    
    Se il conteggio risultante è pari a zero, confermare la configurazione del connettore e verificare che l'attività di accesso dei computer monitorati sia riuscita per il periodo di tempo specificato per la query.
    
    Se il conteggio risultante è maggiore di zero, i dati syslog sono appropriati per il rilevamento anomalo degli accessi SSH. È possibile abilitare questo rilevamento **Analytics**dal  >   rilevamento di accessi SSH anomali dei modelli di**regola**di analisi  >  **(anteprima)**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le appliance syslog locali ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

