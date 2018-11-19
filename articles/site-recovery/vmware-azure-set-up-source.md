---
title: Configurare l'ambiente di origine e il server di configurazione per il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare l'ambiente locale e il server di configurazione per il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566313"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>Configurare l'ambiente di origine e il server di configurazione

Questo articolo descrive come configurare l'ambiente locale di origine per il ripristino di emergenza di macchine virtuali VMware in Azure con [Azure Site Recovery](site-recovery-overview.md). Include i passaggi per l'impostazione di un computer locale come server di configurazione di Site Recovery e l'individuazione automatica delle macchine virtuali locali. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver eseguito le seguenti operazioni

- Pianificato la distribuzione del ripristino di emergenza usando [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Il Planner consente di assicurarsi di avere risorse e larghezza di banda sufficienti per il ripristino di emergenza e il punto di recupero dell'oggetto (RPO) all'interno dell'organizzazione.
- [Configurare le risorse di Azure ](tutorial-prepare-azure.md) nel [portale di Azure](http://portal.azure.com).
- [Impostare le risorse VMware locali](vmware-azure-tutorial-prepare-on-premises.md), tra cui un account dedicato per l'individuazione automatica di macchine virtuali VMware locali.
- È possibile [esaminare domande comuni](vmware-azure-common-questions.md#configuration-server) sulla distribuzione e sulla gestione del server di configurazione.


## <a name="choose-protection-goals"></a>Scegliere gli obiettivi della protezione

1. In **Insiemi di credenziali dei servizi di ripristino** selezionare il nome dell'insieme di credenziali. 
2. In **Attività iniziali** selezionare su Site Recovery. Selezionare quindi **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Nella casella **In quale destinazione si vuole eseguire la replica dei computer** selezionare **In Azure**.
5. In **I computer sono virtuali** selezionare **Sì con VMware vSphere Hypervisor**. Selezionare **OK**.

## <a name="about-the-configuration-server"></a>Informazioni sul server di configurazione

Si distribuisce un server di configurazione locale quando viene impostato il ripristino di emergenza di macchine virtuali VMware in Azure.

- Il server di configurazione coordina le comunicazioni tra VMware locale e Azure. Gestisce anche la replica dei dati.
- Distribuire il server di configurazione per la macchina virtuale VMware.
- Site Recovery fornisce un modello OVA da scaricare dal portale di Azure e da importare nel server vCenter per impostare la macchina virtuale del server di configurazione.
- [Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.


>[!NOTE]
Non usare queste istruzioni se si distribuisce il server di configurazione per il ripristino di emergenza di computer fisici locali in Azure. Per questo scenario, seguire [questo articolo](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>Prima di distribuire il server di configurazione

Se si installa il server di configurazione come macchina virtuale VMware usando il modello OVA, la macchina virtuale verrà installata con tutti i prerequisiti. Se si desidera esaminare i prerequisiti, usare gli articoli seguenti.

- [Informazioni](vmware-azure-configuration-server-requirements.md) su hardware, software e requisiti di capacità per il server di configurazione.
- Se si esegue la replica di macchine virtuali VMware, rivedere le [considerazioni sulla pianificazione della capacità](site-recovery-plan-capacity-vmware.md) ed eseguire lo strumento di [pianificazione della distribuzione di Azure Site Recovery](site-recovery-deployment-planner.md) per la replica VMWare.
- La licenza di Windows per il modello OVA è una licenza di valutazione valida per 180 giorni. Trascorso questo periodo di tempo, è necessario attivare Windows con una licenza valida. 
- Il modello OVA fornisce un modo semplice per configurare il server di configurazione come macchina virtuale VMware. Se per qualche motivo è necessario impostare la macchina virtuale VMware senza il modello, esaminare i prerequisiti e seguire le istruzioni manuali in [questo articolo](physical-azure-set-up-source.md).
- L'account di Azure necessita di autorizzazioni per creare app di Azure AD.


>[!IMPORTANT]
Il server di configurazione deve essere distribuito come descritto in questo articolo. Non è supportata la copia o la clonazione di un server di configurazione esistente.

### <a name="set-up-azure-account-permissions"></a>Impostare le autorizzazioni dell'account di Azure

Un amministratore globale/tenant può assegnare le autorizzazioni per creare app di Azure AD nell'account o assegnare il ruolo di sviluppatore di applicazioni (che include le autorizzazioni) nell'account.


L'amministratore globale/tenant può concedere le autorizzazioni per l'account come indicato di seguito:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**.

 > [!NOTE]
 > Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

In alternativa, l'amministratore globale/tenant dispone delle autorizzazioni per assegnare il ruolo all'account. [Altre informazioni](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 


## <a name="download-the-ova-template"></a>Scaricare il modello OVA

1. Nell'insieme di credenziali passare a **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** selezionare **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
4. Scaricare il modello OVA (Open Virtualization Application) per il server di configurazione.

  > [!TIP]
>È anche possibile scaricare l'ultima versione del modello del server di configurazione dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Importare il modello OVA in VMware

1. Accedere al server VMware vCenter o all'host vSphere ESXi con il client VMWare vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) per avviare la relativa procedura guidata.

     ![Modello OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale).
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Pronto per completare**, per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Accendi al termine della distribuzione** > **Fine**.
9. Per impostazione predefinita la macchina virtuale viene distribuita con una singola scheda di interfaccia di rete. Se si desidera aggiungere un'altra scheda di interfaccia di rete, deselezionare **Accendi al termine della distribuzione** e fare clic su **Fine**. Seguire quindi la procedura successiva.

## <a name="add-an-adapter-to-the-configuration-server"></a>Aggiungere un adattatore al server di configurazione

Se si vuole aggiungere un'altra scheda di interfaccia di rete al server di configurazione, eseguire questa operazione prima di registrare il server nell'insieme di credenziali. L'aggiunta di altre schede non è supportata dopo la registrazione.

1. Nell'inventario del client vSphere fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Edit Settings** (Modifica impostazioni).
2. In **Hardware** selezionare **Add** (Aggiungi) > **Ethernet Adapter** (Scheda Ethernet). Quindi selezionare **Avanti**.
3. Selezionare un tipo di scheda e una rete.
4. Per connettere la scheda di interfaccia di rete virtuale all'accensione della macchina virtuale, selezionare **Connect at power on** (Connetti all'accensione). Quindi selezionare **Next** (Avanti) > **Finish** (Fine)  > **OK**.

## <a name="register-the-configuration-server"></a>Registrare il server di configurazione 
Accendere la macchina virtuale e registrare il server di configurazione nell'insieme di credenziali di Site Recovery.

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore.



## <a name="set-up-the-configuration-server"></a>Configurare il server di configurazione

Come parte della distribuzione, è necessario installare MySQL nella macchina virtuale del server di configurazione. È possibile procedere in due modi:

- Consentire a Site Recovery di scaricare e installare MySQL. Se si desidera utilizzare questa opzione, non è necessario eseguire alcuna operazione prima di avviare l'installazione del server di configurazione.
- Scaricare e installare MySQL manualmente: prima di configurare il server di configurazione, scaricare il programma di installazione MySQL e inserirlo nella cartella **C:\Temp\ASRSetup**. Installare quindi MySQL. 
- Scaricare manualmente e consentire l'installazione a Site Recovery. Prima di configurare il server di configurazione, scaricare il programma di installazione MySQL e inserirlo nella cartella **C:\Temp\ASRSetup**.


1. Al primo accesso nella macchina virtuale, verrà avviato lo strumento di configurazione di Azure Site Recovery.
2. Specificare un nome che verrà usato per registrare il server di configurazione nell'insieme di credenziali di Site Recovery. Quindi selezionare **Avanti**.
3. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure. Si noti che l'account deve avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.
4. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
5. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente entro pochi secondi.
6. Nella procedura guidata, selezionare **Configurare la connettività**.
7. Selezionare la scheda di interfaccia di rete (in esecuzione per impostazione predefinita nel server di configurazione) utilizzata dal server di elaborazione per ricevere il traffico di replica dalle macchine virtuali.
8. Selezionare quindi **Salva**. Si noti che non è possibile modificare le impostazioni dell'insieme di credenziali dopo aver registrato il server di configurazione. 
9. In **Seleziona l'insieme di credenziali di Servizi di ripristino** accedere a Microsoft Azure, selezionare la sottoscrizione di Azure e quindi il gruppo di risorse e l'insieme di credenziali pertinenti. 
10. In **Installa software di terze parti**, installare MySQL:
     - Se Site Recovery gestisce il download e l'installazione di MySQL, fare clic su **Scarica e installa**. Attendere che l'installazione venga completata, quindi procedere con la procedura guidata.
     - Se è stato scaricato MySQL che verrà installato da Site Recovery, fare clic su **Scarica e installa**. Attendere che l'installazione venga completata e procedere con la procedura guidata.
     - Se è stato scaricato e installato MySQL manualmente, fare clic su **Scarica e installa**. Nell'app verrà visualizzato come **Già installato**. Procedere con la procedura guidata.
11. In **Convalida la configurazione dell'appliance** vengono verificati i prerequisiti prima di continuare. 
12. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui il server è in ascolto e specificare un nome descrittivo per il server VMware nell'insieme di credenziali.
13. Immettere le credenziali che verranno usate dal server di configurazione per connettersi al server VMware e rilevare automaticamente le macchine virtuali VMware disponibili per la replica. Selezionare **Aggiungi** >  **Continua**. Le credenziali vengono salvate in locale.
14. In **Configura le credenziali della macchina virtuale** specificare le credenziali che verranno usate da Site Recovery per installare automaticamente il servizio Mobility nei computer quando verrà abilitata la replica per una macchina virtuale.
    - Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare.
    - Per Linux, specificare i dettagli dell'account radice.
15. Selezionare **Finalizza configurazione** per completare la registrazione.
16. Al termine della registrazione, aprire il portale di Azure e verificare che il server di configurazione e il server VMware siano visualizzati in **Insiemi di credenziali dei servizi di ripristino** > **Gestisci** > **Infrastruttura di Site Recovery** > **Server di configurazione**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>Escludere antivirus nel server di configurazione

Se il software antivirus è in esecuzione nel server di configurazione della macchina virtuale, assicurarsi che le cartelle seguenti siano escluse dalle operazioni antivirus. Ciò garantisce che la replica e la connettività funzionino come previsto: 

- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\Programmi\Provider di Servizi di ripristino di Microsoft Azure
- C:\Programmi\Gestione configurazione di Servizi di ripristino di Microsoft Azure
- C:\Programmi\Error Collection Tool di Servizi di ripristino di Microsoft Azure
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Programmi (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Directory di installazione di Site Recovery. Ad esempio: E:\Programmi (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Passaggi successivi
- Se si verificano problemi, esaminare [domande frequenti](vmware-azure-common-questions.md#configuration server) e [risoluzione dei problemi](vmware-azure-troubleshoot-configuration-server.md) per il server di configurazione.
- Ora, [Configura l'ambiente di destinazione](./vmware-azure-set-up-target.md) in Azure.
