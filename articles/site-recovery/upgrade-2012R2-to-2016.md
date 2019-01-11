---
title: Eseguire l'aggiornamento a Windows Server 2016 degli host di Windows Server 2012 R2 e di SCVMM configurati con il servizio Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza in Azure per macchine virtuali di Azure Stack con il servizio Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: e7644128a3f0e0ea531933286e95b15149fdebd3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557863"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Eseguire l'aggiornamento a Windows Server 2016 e SCVMM 2016 degli host di Windows Server 2012 R2 e di SCVMM 2012 R2 configurati con il servizio Azure Site Recovery

Questo articolo descrive come aggiornare a Windows Server 2016 e SCVMM 2016 gli host di Windows Server 2012 R2 e di SCVMM 2012 R2 configurati con il servizio Azure Site Recovery

Site Recovery contribuisce alla strategia di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery). Il servizio assicura che i carichi di lavoro delle macchine virtuali rimangano disponibili quando si verificano interruzioni previste e impreviste.

> [!IMPORTANT]
> Per eseguire l'aggiornamento degli host di Windows Server 2012 R2 che sono già configurati per la replica con Azure Site Recovery, è necessario attenersi alla procedura illustrata in questo documento. Qualsiasi procedura alternativa può comportare stati non supportati e può causare un'interruzione della replica o l'impossibilità di eseguire il failover.


Questo articolo spiega come aggiornare le configurazioni seguenti nell'ambiente in uso:

> [!div class="checklist"]
> * **Host di Windows Server 2012 R2 che non sono gestiti da SCVMM** 
> * **Host di Windows Server 2012 R2 che sono gestiti da un server SCVMM 2012 R2 autonomo** 
> * **Host di Windows Server 2012 R2 che sono gestiti da un server SCVMM 2012 R2 a disponibilità elevata**


## <a name="prerequisites--factors-to-consider"></a>Prerequisiti e fattori da considerare

Prima di eseguire l'aggiornamento, tenere presente quanto segue:

- Se si dispone di host di Windows Server 2012 R2 che non sono gestiti da SCVMM e l'installazione è eseguita in un ambiente autonomo, il tentativo di aggiornamento causerà l'interruzione della replica.
- Se è stata selezionata l'opzione "*not store my Keys in Active Directory under Distributed Key Management*" (Non archiviare le chiavi in Active Directory in Gestione delle chiavi distribuite) all'inizio durante l'installazione di SCVMM 2012 R2, gli aggiornamenti non verranno completati.

- Se si usa System Center 2012 R2 VMM: 

    - Controllare le informazioni del database in VMM: **Console VMM** -> **Impostazioni** -> **Generale** -> **Connessione database**
    - Controllare gli account del servizio usati per il servizio agente System Center Virtual Machine Manager
    - Assicurarsi di disporre di un backup del database VMM.
    - Annotare il nome del database dei server SCVMM coinvolti. Questa operazione può essere eseguita accedendo a **Console VMM** -> **Impostazioni** -> **Generale** -> **Connessione database**
    - Annotare l'ID di VMM di entrambi i server VMM primario 2012 R2 e di ripristino. L'ID di VMM è reperibile nel Registro di sistema "HKLM:\SOFTWARE\Microsoft\Server Microsoft System Center Virtual Machine Manager\Setup".
    - Verificare che le nuove istanze di SCVMM aggiunte al cluster abbiano gli stessi nomi di prima. 

- Se si esegue la replica tra due siti gestiti da SCVMM su entrambi i lati, assicurarsi di aggiornare il lato del ripristino prima di aggiornare il lato primario.
> [!WARNING]
> Durante l'aggiornamento di SCVMM 2012 R2, in Gestione delle chiavi distribuite selezionare l'opzione per **archiviare le chiavi di crittografia in Active Directory**. Scegliere con attenzione le impostazioni per l'account del servizio e per la gestione delle chiavi distribuite. In base alla selezione, i dati crittografati, ad esempio le password nei modelli, possono non essere disponibili dopo l'aggiornamento e possono potenzialmente influire sulla replica con Azure Site Recovery.

> [!IMPORTANT]
> Fare riferimento alla documentazione dettagliata di SCVMM per i [prerequisiti](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations).

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Aggiornare host di Windows Server 2012 R2 che non sono gestiti da SCVMM 
L'elenco dei passaggi indicati di seguito si applica alla configurazione utente da [host Hyper-V ad Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) eseguita mediante i passaggi di questa [esercitazione](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial).

> [!WARNING]
> Come indicato nei prerequisiti, questi passaggi si applicano solo a uno scenario di ambiente cluster e non a una configurazione di host Hyper-V autonoma.

1. Seguire la procedura per [aggiornare il cluster in sequenza](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) per eseguire il processo di aggiornamento in sequenza del cluster.
2. Con ogni nuovo host di Windows Server 2016 che viene introdotto nel cluster, rimuovere il riferimento a un host di Windows Server 2012 R2 da Azure Site Recovery seguendo la procedura descritta [qui]. Deve trattarsi dell'host che si è scelto di svuotare e rimuovere dal cluster.
3. Dopo avere eseguito il comando *Update-VMVersion* per tutte le macchine virtuali, gli aggiornamenti risultano completati. 
4. Seguire la procedura descritta [qui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) per registrare il nuovo host di Windows Server 2016 in Azure Site Recovery. Il sito Hyper-V è già attivo ed è quindi sufficiente registrare il nuovo host nel cluster. 
5.  Passare al portale di Azure e verificare lo stato di integrità replicato all'interno di Servizi di ripristino

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Aggiornare host di Windows Server 2012 R2 gestiti da un server SCVMM 2012 R2 autonomo
Prima di aggiornare gli host di Windows Server 2012 R2, è necessario aggiornare SCVMM 2012 R2 a SCVMM 2016. Seguire questa procedura:

**Aggiornare SCVMM 2012 R2 autonomo a SCVMM 2016**

1.  Disinstallare il provider di Azure Site Recovery accedendo a Pannello di controllo -> Programmi -> Programmi e funzionalità ->Microsoft Azure Site Recovery e quindi fare clic su Disinstalla.
2. [Mantenere il database SCVMM e aggiornare il sistema operativo](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. In **Installazione applicazioni** selezionare **VMM** > **Disinstalla**. b. Selezionare **Rimuovi funzionalità** e quindi selezionare **Server di gestione VMM e Console VMM**. c. In **Opzioni di database** selezionare **Mantieni database**. d. Esaminare il riepilogo e fare clic su **Disinstalla**.

4. [Installare VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Avviare SCVMM e controllare lo stato di ogni host nella scheda **Infrastrutture**. Fare clic su **Aggiorna** per visualizzare lo stato più recente. Lo stato dovrebbe essere impostato su "Richiede attenzione". 
17. Installare la versione più recente del [Provider di Microsoft Azure Site Recovery](http://aka.ms/downloaddra) in SCVMM.
16. Installare la versione più recente dell'[agente Microsoft Azure Recovery Service (MARS)](http://aka.ms/latestmarsagent) in ogni host del cluster. Aggiornare la pagina per assicurarsi che SCVMM sia in grado di eseguire correttamente le query sugli host.

**Aggiornare gli host di Windows Server 2012 R2 a Windows Server 2016**

1. Attenersi alla procedura descritta [qui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) per eseguire il processo di aggiornamento in sequenza del cluster. 
2. Dopo aver aggiunto il nuovo host al cluster, aggiornare l'host dalla Console SCVMM per installare l'agente VMM nell'host aggiornato.
3. Eseguire il comando *Update-VMVersion* per aggiornare le versioni VM delle macchine virtuali. 
4.  Passare al portale di Azure e verificare lo stato di integrità replicato delle macchine virtuali all'interno dell'insieme di credenziali di Servizi di ripristino. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Aggiornare gli host di Windows Server 2012 R2 che sono gestiti da un server SCVMM 2012 R2 a disponibilità elevata
Prima di aggiornare gli host di Windows Server 2012 R2, è necessario aggiornare SCVMM 2012 R2 a SCVMM 2016. Per l'aggiornamento dei server SCVMM 2012 R2 configurati con Azure Site Recovery sono supportate le seguenti modalità: modalità mista senza server VMM aggiuntivi e modalità mista con server VMM aggiuntivi.

**Aggiornare SCVMM 2012 R2 a SCVMM 2016**

1.  Disinstallare il provider di Azure Site Recovery accedendo a Pannello di controllo -> Programmi -> Programmi e funzionalità ->Microsoft Azure Site Recovery e quindi fare clic su Disinstalla.
2. Seguire la procedura descritta [qui](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) in base alla modalità di aggiornamento scelta.
3. Avviare la Console SCVMM e controllare lo stato di ogni host nella scheda **Infrastrutture**. Fare clic su **Aggiorna** per visualizzare lo stato più recente. Lo stato dovrebbe essere impostato su "Richiede attenzione".
4. Installare la versione più recente del [Provider di Microsoft Azure Site Recovery](http://aka.ms/downloaddra) in SCVMM.
5. Aggiornare la versione più recente dell'[agente Microsoft Azure Recovery Service (MARS)](http://aka.ms/latestmarsagent) in ogni host del cluster. Aggiornare la pagina per assicurarsi che SCVMM sia in grado di eseguire correttamente le query sugli host.


**Aggiornare gli host di Windows Server 2012 R2 a Windows Server 2016**

1. Attenersi alla procedura descritta [qui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) per eseguire il processo di aggiornamento in sequenza del cluster.
2. Dopo aver aggiunto il nuovo host al cluster, aggiornare l'host dalla Console SCVMM per installare l'agente VMM nell'host aggiornato.
3. Eseguire il comando *Update-VMVersion* per aggiornare le versioni VM delle macchine virtuali. 
4.  Passare al portale di Azure e verificare lo stato di integrità replicato delle macchine virtuali all'interno dell'insieme di credenziali di Servizi di ripristino. 

## <a name="next-steps"></a>Passaggi successivi
Dopo avere completato l'aggiornamento degli host, è possibile eseguire un [failover di test](tutorial-dr-drill-azure.md) per verificare l'integrità degli stati di replica e di ripristino di emergenza.

