---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164184"
---
1. Copiare il programma di installazione in una cartella locale (ad esempio /tmp) sul server che si vuole proteggere. Eseguire i comandi seguenti in un terminale:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Per installare il servizio Mobility, eseguire il comando seguente:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Al termine dell'installazione è necessario registrare il servizio Mobility nel server di configurazione. Eseguire questo comando per registrare il servizio Mobility nel server di configurazione:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Riga di comando del programma di installazione del servizio Mobility

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parametro|type|DESCRIZIONE|Valori possibili|
|-|-|-|-|
|-r |Mandatory|Specifica se installare il servizio Mobility (MS) o MasterTarget (MT).|MS </br> MT|
|-d |Facoltativo|Percorso in cui viene installato il servizio Mobility.|/usr/local/ASR|
|-v|Mandatory|Specifica la piattaforma in cui viene installato il servizio Mobility. </br> </br>- **VMware**: usare questo valore se si installa il servizio Mobility in una macchina virtuale in esecuzione su *host VMware vSphere ESXi*, *host Hyper-V* e *server fisici*. </br> - **Azure**: usare questo valore se si installa un agente in una macchina virtuale IaaS di Azure.| VMware </br> Azure|
|-q|Facoltativo|Specifica l'esecuzione del programma di installazione in modalità non interattiva.| N/D|


#### <a name="mobility-service-configuration-command-line"></a>Riga di comando di configurazione del servizio Mobility

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parametro|type|DESCRIZIONE|Valori possibili|
|-|-|-|-|
|-i |Mandatory|Indirizzo IP del server di configurazione|Qualsiasi indirizzo IP valido|
|-P |Mandatory|Percorso completo del file in cui viene salvata la passphrase di connessione|Qualsiasi cartella valida|
