---
title: Connettere i dati CEF a Azure Sentinel Preview Documenti Microsoft
description: Informazioni su come connettere i dati CEF ad Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588349"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Connettere la soluzione esterna utilizzando il formato evento comune


Quando si connette una soluzione esterna che invia messaggi CEF, è necessario eseguire tre passaggi per connettersi con Azure Sentinel:When you connect an external solution that sends CEF messages, there are three steps to connect with Azure Sentinel:

Passaggio 1: [Connettere CEF distribuendo l'agente](connect-cef-agent.md) STEP 2: Eseguire i [passaggi specifici](connect-cef-solution-config.md) della soluzione STEP 3: [Verify connectivity](connect-cef-verify.md)

In questo articolo viene descritto il funzionamento della connessione, vengono forniti i prerequisiti e vengono illustrati i passaggi per la distribuzione dell'agente nelle soluzioni di protezione che inviano messaggi CEF (Common Event Format) in Access. 

> [!NOTE] 
> I dati vengono archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.Data is stored in the geographic location of the workspace on which you are running Azure Sentinel.

Per effettuare questa connessione, è necessario distribuire un agente in un computer Linux dedicato (VM o locale) per supportare la comunicazione tra l'appliance e Azure Sentinel. The following diagram describes the setup in the event of a Linux VM in Azure.

 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

In alternativa, questa configurazione esisterà se si usa una macchina virtuale in un altro cloud o in un computer locale. 

 ![CEF in locale](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerazioni sulla sicurezza

Assicurarsi di configurare la sicurezza del computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare la rete in modo che sia allineata con i criteri di sicurezza della rete aziendale e modificare le porte e i protocolli nel daemon in modo che siano in linea con i requisiti. È possibile usare le istruzioni seguenti per migliorare la configurazione della sicurezza del computer:  [Secure VM in Azure](../virtual-machines/linux/security-policy.md), Best practices for Network [security](../security/fundamentals/network-best-practices.md).

Per utilizzare la comunicazione TLS tra la soluzione di protezione e il computer Syslog, è necessario configurare il daemon Syslog (rsyslog o syslog-ng) per comunicare in TLS: Crittografia del [traffico Syslog con TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Crittografia dei messaggi di registro con TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Prerequisiti
Assicurarsi che il computer Linux utilizzato come proxy esegua uno dei seguenti sistemi operativi:

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
 
 - Versioni Daemon
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - RFC Syslog supportate
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Assicurarsi che la macchina soddisfi anche i seguenti requisiti: 
- Autorizzazioni
    - È necessario disporre di autorizzazioni elevate (sudo) nel computer. 
- Requisiti software
    - Assicurati di avere Python in esecuzione sul tuo computer



## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect CEF appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

