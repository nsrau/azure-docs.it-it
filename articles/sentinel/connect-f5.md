---
title: Connettere i dati F5 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati F5 a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 9e7ceee07cfc81953709e3077a6af14388e40e99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475816"
---
# <a name="connect-f5-to-azure-sentinel"></a>Connettere F5 ad Azure Sentinel

Questo articolo illustra come connettere il dispositivo F5 a Sentinel di Azure. F5 Data Connector consente di connettere facilmente i log F5 con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di F5 in Sentinel di Azure offrirà un maggior numero di informazioni sull'uso di Internet dell'organizzazione e migliorerà le sue funzionalità di sicurezza. 


## <a name="how-it-works"></a>Funzionamento

È necessario distribuire un agente in un computer Linux dedicato (VM o locale) per supportare la comunicazione tra F5 e Sentinel di Azure. Il diagramma seguente illustra la configurazione in caso di una macchina virtuale Linux in Azure.

 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

In alternativa, questa configurazione sarà disponibile se si usa una VM in un altro cloud o un computer locale. 

 ![CEF in locale](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Assicurarsi di configurare la sicurezza del computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare la rete in modo che venga allineata ai criteri di sicurezza della rete aziendale e modificare le porte e i protocolli nel daemon per allinearli ai propri requisiti. È possibile usare le istruzioni seguenti per migliorare la configurazione della sicurezza del computer:  [Secure VM in Azure](../virtual-machines/linux/security-policy.md), procedure consigliate [per la sicurezza di rete](../security/fundamentals/network-best-practices.md).

Per usare la comunicazione TLS tra la soluzione di sicurezza e il computer syslog, è necessario configurare il daemon syslog (rsyslog o syslog-ng) per la comunicazione in TLS: [crittografia del traffico syslog con TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [crittografia dei messaggi di log con TLS- syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Prerequisiti
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
 
 
1. Nel portale di Azure Sentinel fare clic su **connettori dati** , quindi premere **F5** e quindi aprire la **pagina del connettore**. 

1. In **Install and configure the syslog Agent**selezionare il tipo di computer, Azure, other cloud o in locale. 
   > [!NOTE]
   > Poiché lo script nel passaggio successivo installa l'agente di Log Analytics e connette il computer all'area di lavoro di Azure Sentinel, verificare che il computer non sia connesso ad altre aree di lavoro.
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python nel computer usando il comando seguente: `python –version`

1. Eseguire lo script seguente nel computer proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Mentre lo script è in esecuzione, assicurarsi che non vengano visualizzati messaggi di errore o di avviso.


## <a name="step-2-configure-your-f5-to-send-cef-messages"></a>PASSAGGIO 2: configurare F5 per l'invio di messaggi CEF

1. Passare a [F5 configurazione della registrazione degli eventi di sicurezza dell'applicazione](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)e seguire le istruzioni per configurare la registrazione remota, usando le linee guida seguenti:
   - Impostare il **tipo di archiviazione remota** su **CEF**.
   - Impostare il **protocollo** su **TCP**.
   - Impostare l' **indirizzo IP** sull'indirizzo IP del server syslog.
   - Impostare il **numero di porta** su **514**o la porta impostata per l'uso da parte dell'agente.
   - È possibile impostare le **dimensioni massime della stringa di query** sulle dimensioni impostate nell'agente.

1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>PASSAGGIO 3: convalidare la connettività

1. Aprire Log Analytics per assicurarsi che i log vengano ricevuti con lo schema CommonSecurityLog.<br> Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

1. Prima di eseguire lo script, è consigliabile inviare messaggi dalla soluzione di sicurezza per assicurarsi che vengano inoltrati al computer proxy syslog configurato. 
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python nel computer usando il comando seguente: `python –version`
1. Eseguire lo script seguente per verificare la connettività tra l'agente, Azure Sentinel e la soluzione di sicurezza. Verifica che l'invio del daemon sia configurato correttamente, sia in ascolto sulle porte corrette e che nulla blocchi la comunicazione tra il daemon e l'agente di Log Analytics. Lo script invia anche i messaggi fittizi ' TestCommonEventFormat ' per controllare la connettività end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere F5 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
