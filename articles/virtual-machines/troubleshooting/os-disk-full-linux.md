---
title: Problemi con un disco del sistema operativo completo in una macchina virtuale Linux
description: Come risolvere i problemi relativi a un disco del sistema operativo completo in una macchina virtuale Linux
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523535"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problemi con un disco del sistema operativo completo in una macchina virtuale Linux

Quando il disco del sistema operativo di una macchina virtuale (VM) Linux diventa pieno, questo può causare problemi con il funzionamento corretto della VM.

## <a name="symptom"></a>Sintomo

Quando si tenta di creare un nuovo file, viene visualizzato il messaggio seguente:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Più daemon indicano quindi che non sono in grado di creare file temporanei durante la sessione di avvio.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Causa

Questo messaggio di errore può verificarsi per diversi motivi:

1. Il disco potrebbe essere pieno.
1. Il file System potrebbe avere esaurito gli iNode.
1. È possibile che un disco dati venga montato su una directory esistente, causando la nascosta dei file.
1. File aperti da un processo e quindi eliminati.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino.
1. Liberare spazio sul disco.
1. Ricreare la macchina virtuale.

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. Risolvere il problema in modalità offline per risolvere il problema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
1. Utilizzando SSH, connettersi alla macchina virtuale di ripristino.

### <a name="free-up-space-on-the-disk"></a>Liberare spazio sul disco

Per risolvere il problema:

- Ridimensionare il disco fino a 1 TB se non è già alla dimensione massima di 1 TB.
- Liberare spazio su disco.

1. Controllare se il disco è pieno. Se le dimensioni del disco sono inferiori a 1 TB, espanderle fino a un massimo di 1 TB [usando l'interfaccia](../linux/expand-disks.md)della riga di comando di Azure.
1. Se il disco è già 1 TB, sarà necessario liberare spazio su disco.
1. Al termine del ridimensionamento e della pulizia, procedere alla ricompilazione della macchina virtuale.

### <a name="rebuild-the-vm"></a>Ricreare la macchina virtuale

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per ricreare la macchina virtuale.
