---
title: Non è possibile stabilire una connessione Desktop remoto a Macchine virtuali di Azure a causa dell'indirizzo IP statico | Microsoft Docs
description: Informazioni su come risolvere un problema RDP causato dall'indirizzo IP statico in Microsoft Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141799"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Non è possibile stabilire una connessione Desktop remoto a Macchine virtuali di Azure a causa dell'indirizzo IP statico

Questo articolo descrive un problema che impedisce di stabilire una connessione Desktop remoto a Macchine virtuali di Azure dopo la configurazione di un indirizzo IP statico nella macchina virtuale.

> [!NOTE]
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'utilizzo del modello di distribuzione Resource Manager, che Microsoft consiglia di usare per le nuove distribuzioni in sostituzione del modello di distribuzione classica.

## <a name="symptoms"></a>Sintomi

Quando si effettua una connessione RDP a una macchina virtuale in Azure, viene visualizzato il messaggio di errore seguente:

**Il desktop remoto non può connettersi al computer remoto per uno di questi motivi:**

1. **L'accesso remoto al server non è abilitato**

2. **Il computer remoto è stato disattivato**

3. **Il computer remoto non è disponibile sulla rete**

**Verificare che il computer remoto sia acceso e connesso alla rete e che l'accesso remoto sia abilitato.**

Controllando lo screenshot in [Diagnostica di avvio](../troubleshooting/boot-diagnostics.md) nel portale di Azure, si vede che la macchina virtuale viene avviata normalmente e attende le credenziali nella schermata di accesso.

## <a name="cause"></a>Causa

La macchina virtuale ha un indirizzo IP statico definito nell'interfaccia di rete in Windows. Questo indirizzo IP è diverso dall'indirizzo definito nel portale di Azure.

## <a name="solution"></a>Soluzione

Prima di seguire questa procedura, creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

Per risolvere il problema, usare il controllo seriale per abilitare il DHCP o [reimpostare l'interfaccia di rete](reset-network-interface.md) per la macchina virtuale.

### <a name="use-serial-control"></a>Usare il controllo seriale

1. Connettersi alla [console seriale e aprire un'istanza CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se la console seriale non è abilitata nella macchina virtuale, vedere [Come reimpostare l'interfaccia di rete per la VM Windows di Azure](reset-network-interface.md).
2. Verificare se il DHCP è disabilitato nell'interfaccia di rete:

        netsh interface ip show config
3. Se il DHCP è disabilitato, reimpostare la configurazione dell'interfaccia di rete in modo che usi il DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Ad esempio, se il nome dell'interfaccia di rete è "Ethernet 2", eseguire il comando seguente:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Eseguire di nuovo una query sulla configurazione IP per verificare che l'interfaccia di rete sia impostata correttamente. Il nuovo indirizzo IP dovrebbe corrispondere a quello fornito da Azure.

        netsh interface ip show config

    Non è necessario riavviare la macchina virtuale a questo punto. La VM sarà nuovamente raggiungibile.

Se in seguito si vuole configurare l'indirizzo IP statico per la macchina virtuale, vedere [Configurare indirizzi IP privati per una VM mediante il portale di Azure](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).