---
title: Connettere i dati di zScaler ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di zScaler a Sentinel di Azure.
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 45351cc29b2b7028863aff06ab5a511674604d6f
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048948"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Connettere l'accesso a Internet di zScaler ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati zScaler in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come connettere il dispositivo zScaler Internet Access ad Azure Sentinel. ZScaler Data Connector consente di connettere facilmente i log di zScaler Internet Access (ZIA) con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di zScaler in Sentinel di Azure offrirà maggiori informazioni sull'uso di Internet dell'organizzazione e migliorerà le funzionalità di sicurezza. 


## <a name="how-it-works"></a>Funzionamento

È necessario distribuire un agente in un computer Linux dedicato (VM o locale) per supportare la comunicazione tra zScaler Internet Access e Azure Sentinel. Il diagramma seguente illustra la configurazione in caso di una macchina virtuale Linux in Azure.

 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

In alternativa, questa configurazione sarà disponibile se si usa una VM in un altro cloud o un computer locale. 

 ![CEF in locale](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Assicurarsi di configurare la sicurezza del computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare la rete in modo che venga allineata ai criteri di sicurezza della rete aziendale e modificare le porte e i protocolli nel daemon per allinearli ai propri requisiti. È possibile usare le istruzioni seguenti per migliorare la configurazione della sicurezza del computer:  [Secure VM in Azure](../virtual-machines/linux/security-policy.md), procedure consigliate [per la sicurezza di rete](../security/fundamentals/network-best-practices.md).

Per usare la comunicazione TLS tra la soluzione di sicurezza e il computer syslog, è necessario configurare il daemon syslog (rsyslog o syslog-ng) per la comunicazione in TLS: [crittografia del traffico syslog con TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [crittografia dei messaggi di log con TLS- syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>prerequisiti
Verificare che il computer Linux usato come proxy esegua uno dei sistemi operativi seguenti:

- 64 bit
  - CentOS 6 e 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 e 7
  - Red Hat Enterprise Linux Server 6 e 7
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bit
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 e 9
   - Ubuntu Linux 14.04 LTS e 16.04 LTS
 
 - Versioni del daemon
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - RFC di syslog supportate
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Verificare che il computer soddisfi anche i requisiti seguenti: 
- autorizzazioni
    - È necessario disporre di autorizzazioni elevate (sudo) nel computer. 
- Requisiti software
    - Assicurarsi che Python sia in esecuzione nel computer
## <a name="step-1-deploy-the-agent"></a>PASSAGGIO 1: distribuire l'agente

In questo passaggio è necessario selezionare il computer Linux che fungerà da proxy tra Azure Sentinel e la soluzione di sicurezza. Sarà necessario eseguire uno script nel computer proxy che:
- Installa l'agente di Log Analytics e lo configura in base alle esigenze per l'ascolto dei messaggi syslog sulla porta 514 su TCP e l'invio dei messaggi CEF all'area di lavoro di Azure Sentinel.
- Configura il daemon syslog in modo che inoltri i messaggi CEF all'agente Log Analytics usando la porta 25226.
- Imposta l'agente syslog per raccogliere i dati e inviarli in modo sicuro per Log Analytics, dove vengono analizzati e arricchiti.
 
 
1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **zScaler** e quindi aprire la **pagina del connettore**. 

1. In **Install and configure the syslog Agent**selezionare il tipo di computer, Azure, other cloud o in locale. 
   > [!NOTE]
   > Poiché lo script nel passaggio successivo installa l'agente di Log Analytics e connette il computer all'area di lavoro di Azure Sentinel, verificare che il computer non sia connesso ad altre aree di lavoro.
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python nel computer usando il comando seguente: `python –version`

1. Eseguire lo script seguente nel computer proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Mentre lo script è in esecuzione, assicurarsi che non vengano visualizzati messaggi di errore o di avviso.


## <a name="step-2-configure-your-zscaler-to-send-cef-messages"></a>PASSAGGIO 2: configurare il zScaler per l'invio di messaggi CEF

1. Nell'appliance zScaler è necessario impostare questi valori in modo che l'appliance invii i log necessari nel formato necessario all'agente syslog di Azure Sentinel, in base all'agente Log Analytics. È possibile modificare questi parametri nel dispositivo, purché vengano modificati anche nel daemon syslog nell'agente Sentinel di Azure.
    - Protocollo = TCP
    - Porta = 514
    - Formato = CEF
    - Indirizzo IP: assicurarsi di inviare i messaggi CEF all'indirizzo IP della macchina virtuale dedicata a questo scopo.
 Per altre informazioni, vedere la [Guida alla distribuzione di zScaler e Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Questa soluzione supporta syslog RFC 3164 o RFC 5424.


1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>PASSAGGIO 3: convalidare la connettività

1. Aprire Log Analytics per assicurarsi che i log vengano ricevuti con lo schema CommonSecurityLog.<br> Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

1. Prima di eseguire lo script, è consigliabile inviare messaggi dalla soluzione di sicurezza per assicurarsi che vengano inoltrati al computer proxy syslog configurato. 
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python nel computer usando il comando seguente: `python –version`
1. Eseguire lo script seguente per verificare la connettività tra l'agente, Azure Sentinel e la soluzione di sicurezza. Verifica che l'invio del daemon sia configurato correttamente, sia in ascolto sulle porte corrette e che nulla blocchi la comunicazione tra il daemon e l'agente di Log Analytics. Lo script invia anche i messaggi fittizi ' TestCommonEventFormat ' per controllare la connettività end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere zScaler Internet Access ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).

