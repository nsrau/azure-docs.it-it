---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671190"
---
1. Installare dapl, rdmacm, ibverbs e mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. In /etc/waagent.conf abilitare RDMA rimuovendo il commento dalle righe di configurazione seguenti. Per modificare questo file è necessario l'accesso alla radice.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Aggiungere o modificare le impostazioni della memoria seguenti espresse in KB nel file /etc/security/limits.conf. Per modificare questo file è necessario l'accesso alla radice. A scopo di test è possibile impostare memlock su unlimited. Ad esempio: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Installare la libreria Intel MPI. [Acquistare e scaricare](https://software.intel.com/intel-mpi-library/) la libreria da Intel o scaricare la [versione di valutazione gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Sono supportati solo i runtime Intel MPI 5.x.
 
   Per la procedura di installazione, vedere [Intel MPI Library installation guide](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) (Guida all'installazione di Intel MPI Library).

5. Abilitare ptrace per i processi non di debugger non radice (necessari per le versioni più recenti di Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
