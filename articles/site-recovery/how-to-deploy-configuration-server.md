---
title: Distribuire il server di configurazione per il ripristino di emergenza di VMware con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come distribuire un server di configurazione per il ripristino di emergenza di VMware con Azure Site Recovery
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2018
ms.author: anoopkv
ms.openlocfilehash: f7ec02cb4723d8a4ea0847810b1a1822cf20e957
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-a-configuration-server"></a>Distribuire un server di configurazione

Si distribuisce un server di configurazione locale quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure. Il server di configurazione coordina le comunicazioni tra VMware locale e Azure. Gestisce anche la replica dei dati. Questo articolo illustra i passaggi necessari per distribuire il server di configurazione quando si esegue la replica di macchine virtuali VMware in Azure. [Seguire le indicazioni di questo articolo](site-recovery-set-up-physical-to-azure.md) se è necessario configurare un server di configurazione per la replica di server fisici.

## <a name="prerequisites"></a>prerequisiti

È consigliabile distribuire il server di configurazione come una macchina virtuale VMware a disponibilità elevata. I requisiti hardware minimi sono riepilogati nella tabella seguente.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>Pianificazione della capacità

I requisiti di ridimensionamento del server di configurazione dipendono dalla frequenza potenziale di modifica dei dati. Usare questa tabella come riferimento.

| **CPU** | **Memoria** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 socket * 4 core a 2,5 GHz) |16 GB |300 GB |500 GB o inferiore |Replicare meno di 100 computer. |
| 12 vCPU (2 socket * 6 core a 2,5 GHz) |18 GB |600 GB |Da 500 GB a 1 TB |Replicare da 100 a 150 computer. |
| 16 vCPU (2 socket * 8 core a 2,5 GHz) |32 GB |1 TB |Da 1 TB a 2 TB |Replicare da 150 a 200 computer. |


Se si esegue la replica di macchine virtuali VMware, leggere altre [considerazioni sulla pianificazione della capacità](/site-recovery-plan-capacity-vmware.md). Eseguire lo [strumento di pianificazione della distribuzione](site-recovery-deployment-planner.md) per la replica VMware.



## <a name="download-the-template"></a>Scaricare il modello

Site Recovery fornisce un modello scaricabile per configurare il server di configurazione come una macchina virtuale VMware a disponibilità elevata. 

1. Nell'insieme di credenziali passare a **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** selezionare **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
4. Scaricare il modello OVF (Open Virtualization Format) per il server di configurazione.

  > [!TIP]
  È possibile scaricare l'ultima versione del modello del server di configurazione direttamente dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Importare il modello in VMware


1. Accedere al server VMware vCenter o all'host vSphere ESXi usando il client VMWare vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) per avviare la relativa procedura guidata.

     ![Modello OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale).
4. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
5. In **Ready to complete** (Completamento):

    * Per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Power on after deployment** (Accendi al termine della distribuzione) > **Finish** (Fine).

    * Per aggiungere un'altra interfaccia di rete, deselezionare **Power on after deployment** (Accendi al termine della distribuzione) e quindi selezionare **Finish** (Fine). Per impostazione predefinita, il modello del server di configurazione viene distribuito con una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.


## <a name="add-an-additional-adapter"></a>Aggiungere un'altra scheda

Se si vuole aggiungere un'altra scheda di interfaccia di rete al server di configurazione, eseguire questa operazione prima di registrare il server nell'insieme di credenziali. L'aggiunta di altre schede non è supportata dopo la registrazione.

1. Nell'inventario del client vSphere fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Edit Settings** (Modifica impostazioni).
2. In **Hardware** selezionare **Add** (Aggiungi) > **Ethernet Adapter** (Scheda Ethernet). Quindi selezionare **Avanti**.
3. Selezionare un tipo di scheda e una rete. 
4. Per connettere la scheda di interfaccia di rete virtuale all'accensione della macchina virtuale, selezionare **Connect at power on** (Connetti all'accensione). Quindi selezionare **Next** (Avanti) > **Finish** (Fine)  > **OK**.
 

## <a name="register-the-configuration-server"></a>Registrare il server di configurazione 

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore.
4. Al primo accesso verrà avviato lo strumento di configurazione di Azure Site Recovery.
5. Immettere un nome che verrà usato per registrare il server di configurazione in Site Recovery. Quindi selezionare **Avanti**.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.
7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente.

### <a name="configure-settings"></a>Configurare le impostazioni

1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività**. Selezionare la scheda NIC per ricevere il traffico di replica e quindi selezionare **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata.
2. In **Seleziona l'insieme di credenziali di Servizi di ripristino** selezionare la sottoscrizione di Azure e quindi il gruppo di risorse e l'insieme di credenziali pertinenti.
3. In **Installa software di terze parti** accettare il contratto di licenza. Selezionare **Scarica e installa** per installare Server MySQL.
4. Selezionare **Installa VMware PowerCLI**. Prima di eseguire questo passaggio, assicurarsi che tutte le finestre del browser siano chiuse. Selezionare quindi **Continua**.
5. In **Convalida la configurazione dell'appliance** i prerequisiti vengono verificati prima di continuare.
6. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui il server è in ascolto e un nome descrittivo per il server VMware nell'insieme di credenziali.
7. Immettere le credenziali che verranno usate dal server di configurazione per connettersi al server VMware. Site Recovery usa queste credenziali per individuare automaticamente le macchine virtuali VMware disponibili per la replica. Selezionare **Aggiungi** e quindi **Continua**.
8. In **Configura le credenziali della macchina virtuale** immettere il nome utente e la password che verranno usati per installare automaticamente il servizio Mobility di Azure Site Recovery nei computer quando la replica è abilitata. Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare. Per Linux, specificare i dettagli dell'account radice.
9. Selezionare **Finalizza configurazione** per terminare la registrazione. 
10. Al termine della registrazione, verificare nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. Fare quindi clic su **OK** per configurare le impostazioni della destinazione.


## <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Passaggi successivi

Configurare il ripristino di emergenza di [macchine virtuali VMware](tutorial-vmware-to-azure.md) in Azure.
