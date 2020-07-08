---
title: Non è possibile stabilire una connessione remota a macchine virtuali di Azure perché la scheda di interfaccia di rete è disabilitata | Microsoft Docs
description: Informazioni su come risolvere un errore RDP a causa della scheda di interfaccia di rete disabilitata nella macchina virtuale di Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 6bce1616ce0c7f7e42810a551acb2f02165ccf93
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078630"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Non è possibile stabilire una connessione Desktop remoto a Macchine virtuali di Azure perché la scheda di interfaccia di rete è disabilitata

Questo articolo spiega come risolvere un problema che impedisce di stabilire una connessione Desktop remoto a macchine virtuali Windows di Azure se l'interfaccia di rete è disabilitata.


## <a name="symptoms"></a>Sintomi

Non è possibile stabilire una connessione RDP o qualsiasi altro tipo di connessione a qualsiasi altra porta in una macchina virtuale in Azure perché l'interfaccia di rete nella macchina virtuale è disabilitata.

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per abilitare l'interfaccia per la macchina virtuale, usare il controllo seriale o [reimpostare l'interfaccia di rete](#reset-network-interface) per la macchina virtuale.

### <a name="use-serial-control"></a>Usare il controllo seriale

1. Connettersi alla [console seriale e aprire un'istanza CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se la console seriale non è abilitata nella macchina virtuale, vedere [Reimposta interfaccia di rete](#reset-network-interface).
2. Controllare lo stato dell'interfaccia di rete:

    ```console
    netsh interface show interface
    ```

    Prendere nota del nome dell'interfaccia di rete disabilitata.

3. Abilitare l'interfaccia di rete:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Ad esempio, se il nome dell'interfaccia di rete è "Ethernet 2", eseguire il comando seguente:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Controllare di nuovo lo stato dell'interfaccia di rete per assicurarsi che sia abilitata.

    ```console
    netsh interface show interface
    ```

    Non è necessario riavviare la macchina virtuale a questo punto. La VM sarà nuovamente raggiungibile.

5.  Connettersi alla macchina virtuale e verificare se il problema è stato risolto.

## <a name="reset-network-interface"></a>Reimpostare l'interfaccia di rete

Per reimpostare l'interfaccia di rete, cambiare l'indirizzo IP in un altro indirizzo IP disponibile nella subnet. A tale scopo usare il portale di Azure o Azure PowerShell. Per altre informazioni, vedere [Come reimpostare l'interfaccia di rete per la VM Windows di Azure](reset-network-interface.md).
