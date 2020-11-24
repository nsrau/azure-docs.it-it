---
title: Risoluzione dei problemi relativi all'agente guest Linux di Azure
description: Risolvere i problemi dell'agente guest Linux di Azure non funziona
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550000"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Risoluzione dei problemi relativi all'agente guest Linux di Azure

[L'agente guest Linux di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) è un agente di macchine virtuali (VM). Consente alla macchina virtuale di comunicare con il controller di infrastruttura (il server fisico sottostante in cui è ospitata la macchina virtuale) nell'indirizzo IP 168.63.129.16. Questo indirizzo IP è un indirizzo IP pubblico virtuale che facilita la comunicazione. Per ulteriori informazioni, vedere la pagina relativa all' [indirizzo IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Verifica dello stato e della versione dell'agente

Vedere https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Risoluzione dei problemi relativi all'agente di macchine virtuali in stato non pronto

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Passaggio 1 controllare se il servizio agente guest Linux di Azure è in esecuzione

A seconda della distribuzione, il nome del servizio può essere walinuxagent o waagent:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Se è possibile visualizzare i servizi e sono in esecuzione, riavviare il servizio per verificare se il problema è stato risolto. Se i servizi vengono arrestati, avviarli e attendere alcuni minuti. Controllare quindi se lo **stato dell'agente** sta segnalando come **pronto**. Per ulteriori informazioni sulla risoluzione di questi problemi, contattare [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Passaggio 2 controllare se l'aggiornamento automatico è abilitato

Controllare questa impostazione in/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Per ulteriori informazioni su come aggiornare l'agente Linux di Azure, consultare https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Passaggio 3 controllare se la macchina virtuale è in grado di connettersi al controller di infrastruttura

Usare uno strumento come curl per verificare se la macchina virtuale è in grado di connettersi a 168.63.129.16 sulle porte 80, 32526 e 443. Se la macchina virtuale non si connette come previsto, controllare se la comunicazione in uscita sulle porte 80, 443 e 32526 è aperta nel firewall locale della macchina virtuale. Se l'indirizzo IP è bloccato, in vari scenari l'agente di macchine virtuali potrebbe avere un comportamento imprevisto.

## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Gli eventi per la risoluzione dei problemi dell'agente guest Linux di Azure vengono registrati nei file di log seguenti:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Impossibile connettersi a WireServer IP (host IP) 

Si notino le voci di errore seguenti in/var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Analisi**

La macchina virtuale non è in grado di raggiungere l'indirizzo IP WireServer nel server host.

**Soluzione**

1. Poiché l'IP WireServer non è raggiungibile, connettersi alla macchina virtuale tramite SSH, quindi provare ad accedere all'URL seguente da CURL: http://168.63.129.16/?comp=versions 
1. Verificare la presenza di problemi che potrebbero essere causati da un firewall, da un proxy o da un'altra origine che potrebbe bloccare l'accesso all'indirizzo IP 168.63.129.16.
1. Controllare se IPTables Linux o un firewall di terze parti sta bloccando l'accesso alle porte 80, 443 e 32526. Per ulteriori informazioni sui motivi per cui l'indirizzo non deve essere bloccato, vedere la pagina relativa all' [indirizzo IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione del problema relativo all'agente guest di Windows Azure non funzionante, [contattare il supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
