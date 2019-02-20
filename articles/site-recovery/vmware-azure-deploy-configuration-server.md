---
title: Distribuire il server di configurazione per il ripristino di emergenza di VMware con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come distribuire un server di configurazione per il ripristino di emergenza di VMware con Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2018
ms.author: ramamill
ms.openlocfilehash: b7454226b96ff2f6a76285d708a7ce2ad1c3a6de
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235887"
---
# <a name="deploy-a-configuration-server"></a>Distribuire un server di configurazione

Si distribuisce un server di configurazione locale quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure. Il server di configurazione coordina le comunicazioni tra VMware locale e Azure. Gestisce anche la replica dei dati. Questo articolo illustra i passaggi necessari per distribuire il server di configurazione quando si esegue la replica di macchine virtuali VMware in Azure. [Seguire le indicazioni di questo articolo](physical-azure-set-up-source.md) se è necessario configurare un server di configurazione per la replica di server fisici.

>[!TIP]
Per informazioni sul ruolo del server di configurazione nell'ambito dell'architettura di Azure Site Recovery, fare clic [qui](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Distribuzione del server di configurazione tramite il modello OVA

Il server di configurazione deve essere configurato come macchina virtuale VMware a disponibilità elevata con determinati requisiti hardware e di dimensione. Per facilitare la distribuzione, Site Recovery fornisce un modello OVA (Open Virtualization Application) scaricabile che consente di configurare il server di configurazione conformemente a tutti i requisiti obbligatori elencati di seguito.

## <a name="prerequisites"></a>Prerequisiti

I requisiti hardware minimi per un server di configurazione sono riepilogati nella tabella seguente.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Pianificazione della capacità

I requisiti di ridimensionamento del server di configurazione dipendono dalla frequenza potenziale di modifica dei dati. Usare questa tabella come riferimento.

| **CPU** | **Memoria** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 socket * 4 core \@ 2,5 GHz) |16 GB |300 GB |500 GB o inferiore |Replicare meno di 100 computer. |
| 12 vCPU (2 socket * 6 core \@ 2,5 GHz) |18 GB |600 GB |Da 500 GB a 1 TB |Replicare da 100 a 150 computer. |
| 16 vCPU (2 socket * 8 core \@ 2,5 GHz) |32 GB |1 TB |Da 1 TB a 2 TB |Replicare da 150 a 200 computer. |

Se si esegue la replica di più macchine virtuali VMware, leggere le [considerazioni sulla pianificazione della capacità](site-recovery-plan-capacity-vmware.md). Eseguire lo [strumento di pianificazione della distribuzione](site-recovery-deployment-planner.md) per la replica VMware.

## <a name="download-the-template"></a>Scaricare il modello

1. Nell'insieme di credenziali passare a **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** selezionare **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
4. Scaricare il modello OVA (Open Virtualization Application) per il server di configurazione.

  > [!TIP]
>È anche possibile scaricare l'ultima versione del modello del server di configurazione direttamente dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).

>[!NOTE]
La licenza fornita con il modello OVA è una licenza di valutazione valida per 180 giorni. Passato questo periodo, il cliente deve attivare le finestre con una licenza acquistata.

## <a name="import-the-template-in-vmware"></a>Importare il modello in VMware

1. Accedere al server VMware vCenter o all'host vSphere ESXi usando il client VMWare vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) per avviare la relativa procedura guidata.

     ![Modello OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale). L'utilizzo dell'opzione di thin provisioning potrebbe influire sulle prestazioni del server di configurazione.
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Ready to complete** (Completamento):

    * Per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Power on after deployment** (Accendi al termine della distribuzione) > **Finish** (Fine).

    * Per aggiungere un'altra interfaccia di rete, deselezionare **Power on after deployment** (Accendi al termine della distribuzione) e quindi selezionare **Finish** (Fine). Per impostazione predefinita, il modello del server di configurazione viene distribuito con una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.

> [!IMPORTANT]
> Non modificare le configurazioni delle risorse (restrizioni per CPU/memoria/core), modificare/eliminare i servizi o i file installati nel server di configurazione dopo la distribuzione. Ciò avrà impatto sulla registrazione del server di configurazione per i servizi di Azure e sulle prestazioni del server di configurazione.

## <a name="add-an-additional-adapter"></a>Aggiungere un'altra scheda

Se si vuole aggiungere un'altra scheda di interfaccia di rete al server di configurazione, eseguire questa operazione prima di registrare il server nell'insieme di credenziali. L'aggiunta di altre schede non è supportata dopo la registrazione.

1. Nell'inventario del client vSphere fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Edit Settings** (Modifica impostazioni).
2. In **Hardware** selezionare **Add** (Aggiungi) > **Ethernet Adapter** (Scheda Ethernet). Quindi selezionare **Avanti**.
3. Selezionare un tipo di scheda e una rete.
4. Per connettere la scheda di interfaccia di rete virtuale all'accensione della macchina virtuale, selezionare **Connect at power on** (Connetti all'accensione). Quindi selezionare **Next** (Avanti) > **Finish** (Fine)  > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrare il server di configurazione con i servizi Azure Site Recovery

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore.
4. Al primo accesso verrà avviato lo strumento di configurazione di Azure Site Recovery entro pochi secondi.
5. Immettere un nome che verrà usato per registrare il server di configurazione in Site Recovery. Quindi selezionare **Avanti**.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.
7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata **automaticamente** entro pochi secondi.

### <a name="configure-settings"></a>Configurare le impostazioni

1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività** e quindi la scheda di interfaccia di rete utilizzata dal server di elaborazione per ricevere il traffico di replica. Selezionare quindi **Salva**. Una volta configurata, questa impostazione non può essere modificata. È consigliabile non modificare l'indirizzo IP del server di configurazione. Verificare che l'indirizzo IP assegnato al server di configurazione sia un indirizzo IP STATICO e non DHCP.
2. In **Seleziona l'insieme di credenziali di Servizi di ripristino** accedere a Microsoft Azure, selezionare la sottoscrizione di Azure e quindi il gruppo di risorse e l'insieme di credenziali pertinenti.

    > [!NOTE]
    > Dopo la registrazione, non è più possibile cambiare l'insieme di credenziali di Servizi di ripristino.

3. In **Installa software di terze parti**

    |Scenario   |Procedura da seguire  |
    |---------|---------|
    |È possibile scaricare e installare MySQL manualmente?     |  Sì. Scaricare l'applicazione MySQL e inserirla nella cartella **C:\Temp\ASRSetup**, quindi installarla manualmente. Se dopo aver accettato le condizioni si fa clic su **Scarica e installa**, nel portale viene visualizzato l'avviso *Già installato*. È possibile procedere con il passaggio successivo.       |
    |È possibile evitare di scaricare MySQL online?     |   Sì. Inserire l'applicazione di installazione di MySQL nella cartella **C:\Temp\ASRSetup**. Accettare le condizioni e fare clic su **Scarica e installa**. Il portale userà il programma di installazione aggiunto dall'utente e installerà l'applicazione. È possibile procedere con il passaggio successivo all'installazione.    |
    |Come si può scaricare e installare MySQL tramite Azure Site Recovery?     |  Accettare il contratto di licenza e fare clic su **Scarica e installa**. È quindi possibile procedere con il passaggio successivo all'installazione.       |
4. In **Convalida la configurazione dell'appliance** i prerequisiti vengono verificati prima di continuare.
5. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui è in ascolto il server. Immettere un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
6. Immettere le credenziali che verranno usate dal server di configurazione per connettersi al server VMware. Site Recovery usa queste credenziali per individuare automaticamente le macchine virtuali VMware disponibili per la replica. Selezionare **Aggiungi** e quindi **Continua**. Le credenziali immesse qui vengono salvate in locale.
7. In **Configura le credenziali della macchina virtuale** immettere il nome utente e la password delle macchine virtuali per installare automaticamente il servizio Mobility durante la replica. Per le macchine virtuali **Windows**, l'account deve avere privilegi di amministratore locale nelle macchine virtuali da replicare. Per **Linux**, specificare i dettagli dell'account radice.
8. Selezionare **Finalizza configurazione** per completare la registrazione.
9. Al termine della registrazione, aprire il portale di Azure e verificare che il server di configurazione e il server VMware siano elencati in **Insiemi di credenziali dei servizi di ripristino** > **Gestisci** > **Infrastruttura di Site Recovery** > **Server di configurazione**.

## <a name="upgrade-the-configuration-server"></a>Aggiornare il server di configurazione

Per aggiornare il server di configurazione alla versione più recente, eseguire i seguenti [passaggi](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Per istruzioni dettagliate su come aggiornare tutti i componenti di Site Recovery, fare clic [qui](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gestire il server di configurazione

Per evitare interruzioni nella replica in corso, verificare che l'indirizzo IP del server di configurazione non cambi dopo aver registrato il server di configurazione per un insieme di credenziali. Atre informazioni sulle attività comuni di gestione del server di configurazione sono disponibili [qui](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Domande frequenti

1. Per quanto tempo è valida la licenza fornita sul server di configurazione distribuito tramite OVF? Cosa accade se non si riattiva la licenza?

    La licenza fornita con il modello OVA è una licenza di valutazione valida per 180 giorni. Prima della scadenza, è necessario attivare la licenza. In caso contrario, questo può comportare l'arresto frequente del server di configurazione, pregiudicando le attività di replica.

2. È possibile usare la macchina virtuale, in cui è installato il server di configurazione, per scopi diversi?

    **No**, è consigliabile usare la macchina virtuale solo per il server di configurazione. Assicurarsi di seguire tutte le specifiche indicate in [Prerequisiti](#prerequisites) per una gestione efficiente del ripristino di emergenza.
3. È possibile sostituire l'insieme di credenziali già registrate nel server di configurazione con un insieme di credenziali nuovo?

    **No**, una volta registrato con il server di configurazione, l'insieme di credenziali non può più essere cambiato.
4. È possibile usare lo stesso server di configurazione per proteggere sia i computer fisici che le macchine virtuali?

    **Sì**, lo stesso server di configurazione può essere usato per la replica di computer fisici e macchine virtuali. Tuttavia, è possibile eseguire il failback del computer fisico solo in una macchina virtuale VMware.
5. Qual è lo scopo di un server di configurazione e dove viene usato?

    Per altre informazioni sul server di configurazione e sulle sue funzionalità, fare riferimento a [VMware per l'architettura di replica di Azure](vmware-azure-architecture.md).
6. Dove è possibile trovare la versione più recente del server di configurazione?

    Per informazioni sui passaggi da eseguire per aggiornare il server di configurazione tramite il portale, vedere [Aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Per istruzioni dettagliate su come aggiornare tutti i componenti di Site Recovery, vedere [qui](https://aka.ms/asr_how_to_upgrade).
7. Dove è possibile scaricare la passphrase per il server di configurazione?

    Fare riferimento a [questo articolo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) per scaricare la passphrase.
8. È possibile modificare la passphrase?

    **No**. Si **consiglia vivamente di non modificare la passphrase** del server di configurazione. La modifica della passphrase interrompe la replica delle macchine protette e porta a uno stato di integrità critico.
9. Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

    Nelle **credenziali di Servizi di ripristino**, **Gestisci** > **Infrastruttura di Site Recovery** > **Server di configurazione**. In Server, selezionare **Scarica chiave di registrazione** per scaricare il file di credenziali dell'insieme di credenziali.
10. È possibile clonare un server di configurazione esistente e usarlo per l'orchestrazione della replica?

    **No**, l'uso di un componente server di configurazione clonato non è supportato.

11. È possibile cambiare l'indirizzo IP del server di configurazione?

    **No**, è consigliabile non modificare l'indirizzo IP di un server di configurazione. Verificare che gli indirizzi IP assegnati al server di configurazione siano indirizzi IP STATICI e non DHCP.
12. È possibile impostare il server di configurazione in Azure?

    È consigliabile impostare il server di configurazione nell'ambiente locale con visuale diretta su vCenter per ridurre al minimo le latenze di trasferimento dei dati. È possibile eseguire backup pianificati del server di configurazione [a scopo di failback](vmware-azure-manage-configuration-server.md#failback-requirements).

Per altre domande frequenti sul server di configurazione, fare riferimento alla [documentazione sulle domande comuni per i server di configurazione](vmware-azure-common-questions.md#configuration-server).

## <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Passaggi successivi

Configurare il ripristino di emergenza di [macchine virtuali VMware](vmware-azure-tutorial.md) in Azure.