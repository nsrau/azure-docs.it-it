---
title: Preparare le macchine virtuali VMware che esegue Linux per la migrazione ad Azure con la migrazione di Server eseguire la migrazione di Azure | Microsoft Docs
description: Viene descritto come preparare una VM Linux per la migrazione ad Azure con la migrazione di Server eseguire la migrazione di Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811791"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Preparare le macchine virtuali VMware Linux per la migrazione ad Azure 

Questo articolo descrive come preparare le macchine virtuali VMware che esegue Linux quando si desidera eseguirne la migrazione ad Azure usando [Azure Migrate](migrate-overview.md). 

> [!NOTE]
> Migrazione del Server eseguire la migrazione di Azure è attualmente in anteprima pubblica. È possibile usare la versione GA esistente di Azure Migrate per individuare e valutare le macchine virtuali per la migrazione, ma l'effettiva migrazione non è supportata nella versione GA esistente.

Preparare le macchine Linux come segue:

1. Installare Hyper-V Linux Integration Services. Le versioni più recenti delle distribuzioni di Linux potrebbero essere installato per impostazione predefinita).
2. Ricompilare l'immagine Linux in init in modo che contenga i driver necessari di Hyper-V e, in modo che eseguirà l'avvio della macchina virtuale in Azure (obbligatorio per alcune distribuzioni).
3. Abilitare la registrazione della console seriale per la risoluzione dei problemi. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)
4. Aggiornare il file di mappa di dispositivo con nome del dispositivo per le associazioni del volume, usare gli identificatori dei dispositivi permanenti.
5. Aggiornare le voci fstab per utilizzare gli identificatori di volume permanente.
6. Rimuovere tutte le regole udev che riserva i nomi delle interfacce basati sull'indirizzo MAC e così via.
7. Interfacce di rete di Update per ricevere gli indirizzi IP DHCP.
8. Assicurarsi che ssh sia abilitato. Verificare che il servizio sshd viene impostato per avviarsi automaticamente al riavvio del sistema.
9. Assicurarsi che in ingresso ssh le richieste di connessione non sono bloccate dal firewall del sistema operativo o le regole della tabella IP.

[Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) su queste modifiche nelle distribuzioni Linux più diffuse.

## <a name="sample-script"></a>Script di esempio

Questo script (preparare-per-azure.sh) fornisce un esempio per la preparazione delle macchine Linux. Lo script potrebbe non funzionare per tutte le distribuzioni e ambienti, ma è un utile punto di partenza.

Nel script viene illustrato come: 

- Rigenerare l'immagine di init Linux con i driver necessari, se necessario.
- Aggiornare le voci fstab per utilizzare gli identificatori di volume permanente.
- Reindirizzare i log della console alla porta seriale.
- Abilitare la console Azure accesso seriale
- Rimuovere le regole udev di rete
- Inserire un'esecuzione degli script di avvio che viene eseguito quando si avvia la macchina virtuale. Controlla se il computer è in esecuzione in Azure. Se si tratta, aggiorna la configurazione di rete nella macchina virtuale e imposta la prima interfaccia ethernet per usare DHCP per acquisire un indirizzo IP.

### <a name="before-you-start"></a>Prima di iniziare

- Lo script di esempio contiene i passaggi di esempio. Questo non deve essere eseguito nei sistemi di produzione. Potrebbe danneggiare o danneggiare la macchina virtuale in cui viene eseguito.
- È consigliabile che eseguirlo in una macchina virtuale di test. Prima di iniziare, eseguire un backup delle macchine Virtuali o uno snapshot in modo che sia possibile ripristinare la macchina virtuale se necessario. 
- Lo script funziona quando la macchina virtuale è in esecuzione una di queste distribuzioni di Linux:
    - Red Hat Enterprise Linux 6.5+, 7.1+
    - Sistema operativo cento 6.5 +, 7.1 +
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Esecuzione dello script

1. Copia lo script in Linux eseguire il test della macchina virtuale tramite sftp o un client scp, ad esempio FileZilla o WinScp.
2. Accedere tramite SSH alla macchina Linux usando un account amministratore.
3. Passare alla directory di script.
4. Per rendere disponibile lo script in un file eseguibile, eseguire **sudo chmod 777 preparare-per-azure.sh**.
5. Eseguire lo script con **./prepare-for-azure.sh**.

Ecco come viene eseguito lo script:

![Script di Linux](./media/how-to-prepare-linux-for-migration/script1.png)
![script di Linux](./media/how-to-prepare-linux-for-migration/script2.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script3.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script5.png)
![script Linux ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Script di Linux](./media/how-to-prepare-linux-for-migration/script7.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script8.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script10.png)
![script di Linux ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Script di Linux](./media/how-to-prepare-linux-for-migration/script12.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script13.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script15.png)
![script di Linux ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Script di Linux](./media/how-to-prepare-linux-for-migration/script17.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script18.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script20.png)
![script di Linux ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Script di Linux](./media/how-to-prepare-linux-for-migration/script22.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script23.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script24.png)
![script di Linux](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md) per creare gruppi più attendibili.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
