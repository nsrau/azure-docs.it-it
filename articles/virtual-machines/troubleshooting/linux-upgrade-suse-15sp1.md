---
title: Aggiornare una macchina virtuale di Azure con SUSE Linux Enterprise Server a SUSE 15 SP1 | Microsoft Docs
description: Questo articolo illustra la procedura generale per l'uso di SUSE Distribution Migration System (Sistema di migrazione della distribuzione SUSE) per aggiornare SUSE Linux Enterprise Server a SUSE 15 SP1 per una macchina virtuale di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 079574ab4c5846eae6266d1e13240baf99428a69
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449244"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Aggiornare la macchina virtuale di Azure con SLES 12 a SLES 15 SP1

Questo articolo illustra la procedura generale per l'aggiornamento di SUSE Linux Enterprise Server (SLES) 12 a SLES 15 SP1 per una macchina virtuale (VM) di Azure. Per altre informazioni, vedere [Usare SUSE Distribution Migration System](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) e [Guida all'aggiornamento a SUSE Linux Enterprise Server 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Percorsi di aggiornamento supportati
Per poter passare a SLES 15 SP1 è necessario che la versione SLES corrente sia SLES 12 SP4 o SLES 12 SP5.

![Screenshot del percorso di aggiornamento supportato](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Prerequisiti

- Pianificare l'attività di migrazione in base alla finestra di tempo di inattività approvata. La macchina virtuale viene riavviata durante la migrazione.
- Prima dell'attività di migrazione, eseguire un backup completo della macchina virtuale.
- Se il backup non è configurato, eseguire un backup di snapshot del disco del sistema operativo.
- [Controllare se la macchina virtuale è di generazione V1 o V2](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Eseguire l'aggiornamento da SUSE 12 SP4 o SP5 a SUSE 15 SP1

1. Installare il pacchetto più recente per la macchina virtuale:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. Al termine dell'installazione, riavviare la macchina virtuale.

3. Verificare la versione del kernel e del sistema operativo. Verificare che la versione sia SUSE 12 SP4 o SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Installare **suse-migration-sle15-activation**. Quando viene installato il pacchetto **suse-migration-sle15-activation**, anche un altro pacchetto, **SLES15-Migration** viene installato automaticamente come pacchetto di dipendenza. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. Al termine dell'installazione eseguire il comando `reboot` per riavviare la macchina virtuale.

6. Passare al [portale di Azure](https://portal.azure.com), selezionare la macchina virtuale e quindi selezionare **Console seriale**. Si noterà che il sistema si interrompe su "reboot: Restarting system" (riavvio: Riavvio del sistema in corso). Questo processo richiede circa 15-45 minuti. Per una macchina virtuale di seconda generazione, l'esecuzione potrebbe bloccarsi sulla schermata "reboot: Restarting system" (riavvio: Riavvio del sistema in corso). In questo caso, attendere 45 minuti. Se l'esecuzione resta bloccata, passare alla pagina **Panoramica** della macchina virtuale nel portale di Azure, arrestare la macchina virtuale e quindi riavviarla.

     ![Screenshot dei messaggi nella console seriale](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. Dopo il riavvio del sistema con nuovo kernel viene visualizzato il messaggio seguente.

     ![Screenshot dei messaggi nella console seriale](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Verificare la versione del kernel e del sistema operativo, per assicurarsi che il sistema sia stato aggiornato correttamente.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Verificare la versione di generazione di una macchina virtuale

Per verificare la versione di generazione, usare uno dei metodi seguenti:

- Nel terminale SLES eseguire il comando `dmidecode | grep -i hyper`. Se si tratta di una macchina virtuale di generazione V1 non viene restituito alcun output. Per le macchine virtuali di generazione V2 viene visualizzato l'output seguente:

     ![Screenshot dell'output per la macchina virtuale di seconda generazione](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- Nel [portale di Azure](https://portal.azure.com) passare a **Proprietà** per la macchina virtuale, quindi verificare il campo **Generazione macchina virtuale**.
