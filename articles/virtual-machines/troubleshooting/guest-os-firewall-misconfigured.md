---
title: Il firewall del sistema operativo guest della macchina virtuale di Azure non è configurato correttamente | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711017"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Il firewall del sistema operativo guest della macchina virtuale di Azure non è configurato correttamente

Questo articolo illustra come correggere un errore di configurazione del firewall del sistema operativo guest nella macchina virtuale di Azure.

## <a name="symptoms"></a>Sintomi

1.  La schermata iniziale della macchina virtuale mostra che questa è completamente caricata.

2.  A seconda della configurazione del sistema operativo guest, è possibile che il traffico di rete non raggiunga o raggiunga solo in parte la macchina virtuale.

## <a name="cause"></a>Causa

Un errore di configurazione del firewall del sistema guest può bloccare tutti o solo alcuni tipi di traffico di rete verso la macchina virtuale.

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco di sistema della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per risolvere questo problema, usare la console seriale o [riparare la macchina virtuale in modalità offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) collegando il disco di sistema della macchina virtuale a una macchina virtuale di ripristino.

## <a name="online-mitigations"></a>Procedure di mitigazione online

Connettersi alla [console seriale e quindi aprire un'istanza di PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se la console seriale non è abilitata nella macchina virtuale, passare alla sezione "Riparare la macchina virtuale in modalità offline" dell'articolo di Azure seguente:

 [Si verifica un errore interno quando si prova a connettersi a una macchina virtuale di Azure tramite Desktop remoto](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Le regole seguenti possono essere modificate per abilitare l'accesso alla macchina virtuale (tramite RDP) o per offrire un'esperienza più semplice per la risoluzione dei problemi:

*   Desktop remoto (TCP-In): questa è la regola standard che fornisce l'accesso primario alla macchina virtuale consentendo connessioni RDP in Azure.

*   Gestione remota Windows (HTTP-In): questa regola consente di connettersi alla macchina virtuale usando PowerShell. In Azure questo tipo di accesso consente di usare funzionalità di scripting remoto e risoluzione dei problemi.

*   Condivisione file e stampanti (SMB-In): questa regola abilita l'accesso alle condivisioni di rete come opzione per la risoluzione dei problemi.

*   Condivisione file e stampanti (richiesta echo - ICMPv4-In): questa regola consente di effettuare il ping sulla macchina virtuale.

Nell'istanza di accesso della console seriale, è possibile eseguire una query sullo stato corrente della regola del firewall.

*   Query con il nome visualizzato come parametro:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Query con la porta locale usata dall'applicazione:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Query con l'indirizzo IP locale usato dall'applicazione:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Se si nota che la regola è disabilitata, è possibile abilitarla eseguendo il comando seguente:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Per risolvere il problema, è possibile impostare i profili firewall su OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Dopo aver eseguito questa operazione per impostare il firewall in modo corretto, riabilitare il firewall al termine della procedura di risoluzione dei problemi.

    > [!Note]
    > Non è necessario riavviare la macchina virtuale per applicare questa modifica.

*   Provare di nuovo a connettersi alla macchina virtuale tramite RDP.

### <a name="offline-mitigations"></a>Procedure di mitigazione offline

1.  Per abilitare o disabilitare le regole del firewall, fare riferimento a [abilitare o disabilitare una regola del firewall in un sistema operativo Guest della macchina virtuale di Azure](enable-disable-firewall-rule-guest-os.md).

2.  Controllare se si è verificato lo [scenario di blocco del traffico in ingresso da parte del firewall del sistema operativo guest](guest-os-firewall-blocking-inbound-traffic.md).

3.  Se si hanno ancora dubbi sul fatto che il firewall stia bloccando l'accesso, vedere [Disabilitare il firewall del sistema operativo guest nella macchina virtuale di Azure](disable-guest-os-firewall-windows.md) e quindi riabilitare il firewall del sistema guest usando le regole corrette.

