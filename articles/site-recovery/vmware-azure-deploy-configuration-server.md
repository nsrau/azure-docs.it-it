---
title: Distribuire il server di configurazione in Azure Site RecoveryDeploy the configuration server in Azure Site Recovery
description: Questo articolo descrive come distribuire un server di configurazione per il ripristino di emergenza di VMware con Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257381"
---
# <a name="deploy-a-configuration-server"></a>Distribuire un server di configurazione

Si distribuisce un server di configurazione locale quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure. Il server di configurazione coordina le comunicazioni tra VMware locale e Azure. Gestisce anche la replica dei dati. Questo articolo illustra i passaggi necessari per distribuire il server di configurazione quando si esegue la replica di macchine virtuali VMware in Azure. Se è necessario configurare un server di configurazione per la replica del server fisico, vedere Configurare il server di [configurazione per il ripristino di emergenza dei server fisici](physical-azure-set-up-source.md)in Azure.

> [!TIP]
> Per informazioni sul ruolo di un server di configurazione come parte dell'architettura di Azure Site Recovery, vedere [VMware to Azure disaster recovery architecture](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Distribuire un server di configurazione tramite un modello OVADeploy a configuration server through an OVA template

Il server di configurazione deve essere configurato come VM VMware a disponibilità elevata con determinati requisiti minimi di hardware e dimensionamento. Per una distribuzione semplice e comoda, Site Recovery fornisce un modello OVA (Open Virtualization Application) scaricabile per configurare il server di configurazione conforme a tutti i requisiti obbligatori elencati di seguito.

## <a name="prerequisites"></a>Prerequisiti

I requisiti hardware minimi per un server di configurazione sono riepilogati nelle sezioni seguenti.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisiti delle autorizzazioni di Azure Active DirectoryAzure Active Directory permission requirements

È necessario disporre di un utente con una delle autorizzazioni seguenti impostate in Azure Active Directory (Azure AD) per registrare il server di configurazione con i servizi di Azure Site Recovery.You must have a user with one of the following permissions set in Azure Active Directory (Azure AD) to register the configuration server with Azure Site Recovery services.

1. L'utente deve disporre di un ruolo di sviluppatore dell'applicazione per creare un'applicazione.
    - Per verificare, accedere al portale di Azure.To verify, sign in to the Azure portal.</br>
    - Passare a**Ruoli e amministratori**di Azure Active **Directory** > .</br>
    - Verificare che il ruolo di sviluppatore dell'applicazione sia assegnato all'utente. In caso contrario, utilizzare un utente con questa autorizzazione o contattare un [amministratore per abilitare l'autorizzazione.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)
    
2. Se il ruolo di sviluppatore dell'applicazione non può essere assegnato, assicurarsi che il flag **Gli utenti possono registrare** le applicazioni sia impostato su **true** affinché l'utente crei un'identità. Per abilitare queste autorizzazioni:
    - Accedere al portale di Azure.
    - Passare a**Impostazioni utente** **di Azure Active Directory** > .
    - In **Registrazioni app**, **Gli utenti possono registrare le applicazioni**, selezionare **Sì**.

      ![AD_application_permission di AzureAzure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services *non è supportato.* Usare un account gestito tramite [Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

## <a name="download-the-template"></a>Scaricare il modello

1. Nell'insieme di credenziali passare a **Preparare l'origine dell'infrastruttura** > **Source**.
2. In **Prepara origine** selezionare **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
4. Scaricare il modello OVA per il server di configurazione.

   > [!TIP]
   >È inoltre possibile scaricare la versione più recente del modello di server di configurazione direttamente [dall'Area download Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> La licenza fornita con un modello OVA è una licenza di valutazione valida per 180 giorni. Dopo questo periodo, è necessario acquistare una licenza.

## <a name="import-the-template-in-vmware"></a>Importare il modello in VMware

1. Accedere al server VMware vCenter o all'host vSphere ESXi usando il client VMWare vSphere.
2. Scegliere **Distribuisci modello OVF** dal menu **File** per avviare la procedura guidata **Distribuisci modello OVF.**

     ![Distribuisci modello OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale). L'utilizzo dell'opzione di thin provisioning potrebbe influire sulle prestazioni del server di configurazione.
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Ready to complete** (Completamento):

    * Per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Accensione al** > termine della distribuzione **.**
    * Per aggiungere un'altra interfaccia di rete, deselezionare **Power on after deployment** (Accendi al termine della distribuzione) e quindi selezionare **Finish** (Fine). Per impostazione predefinita, il modello del server di configurazione viene distribuito con una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.

> [!IMPORTANT]
> Non modificare le configurazioni delle risorse, ad esempio memoria, core e limitazione della CPU, né modificare o eliminare i servizi o i file installati nel server di configurazione dopo la distribuzione. Questi tipi di modifiche influiscono sulla registrazione del server di configurazione con i servizi di Azure e sulle prestazioni del server di configurazione.

## <a name="add-an-additional-adapter"></a>Aggiungere un'altra scheda

> [!NOTE]
> Due schede di interfaccia di rete sono necessarie se si prevede di mantenere gli indirizzi IP dei computer di origine in caso di failover e si desidera eseguire il failback in un secondo momento. Una scheda di interfaccia di rete è connessa ai computer di origine e l'altra viene usata per la connettività di Azure.One NIC is connected to source machines, and the other NIC is used for Azure connectivity.

Se si vuole aggiungere un'altra scheda di interfaccia di rete al server di configurazione, eseguire questa operazione prima di registrare il server nell'insieme di credenziali. L'aggiunta di altre schede non è supportata dopo la registrazione.

1. Nell'inventario del client vSphere fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Edit Settings** (Modifica impostazioni).
2. In **Hardware** selezionare **Add** (Aggiungi) > **Ethernet Adapter** (Scheda Ethernet). Quindi selezionare **Avanti**.
3. Selezionare un tipo di scheda e una rete.
4. Per connettere la scheda di interfaccia di rete virtuale all'accensione della macchina virtuale, selezionare **Connect at power on** (Connetti all'accensione). Quindi selezionare**Fine** >  **successiva** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrare il server di configurazione con i servizi Azure Site Recovery

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore.
4. La prima volta che si accede, in pochi secondi viene avviato lo strumento Configurazione di Azure Site Recovery.
5. Immettere un nome che verrà usato per registrare il server di configurazione in Site Recovery. Quindi selezionare **Avanti**.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure.</br>
    a. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.</br>
    b. Verificare che l'account utente scelto disponga dell'autorizzazione per creare un'applicazione in Azure.Ensure that the chosen user account has permission to create an application in Azure. Per abilitare le autorizzazioni necessarie, seguire le linee guida nella sezione Requisiti di autorizzazione di [Azure Active Directory](#azure-active-directory-permission-requirements).
7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Accedere di nuovo al computer. La procedura guidata di gestione del server di configurazione viene avviata automaticamente in pochi secondi.

### <a name="configure-settings"></a>Configurare le impostazioni

1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività**. Dalle caselle di riepilogo a discesa, selezionare innanzitutto la scheda di interfaccia di rete utilizzata dal server di elaborazione incorporato per l'individuazione e l'installazione push del servizio mobility nei computer di origine. Selezionare quindi la scheda di interfaccia di rete usata dal server di configurazione per la connettività con Azure.Then select the NIC that the configuration server uses for connectivity with Azure. Selezionare **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata. Non modificare l'indirizzo IP di un server di configurazione. Assicurarsi che l'IP assegnato al server di configurazione sia un indirizzo IP statico e non un indirizzo IP DHCP.
2. In Seleziona insieme di **credenziali di Servizi di ripristino**accedere a Microsoft Azure con le credenziali usate nel passaggio 6 di Registrare il server di configurazione con i servizi di Azure Site [Recovery.](#register-the-configuration-server-with-azure-site-recovery-services)
3. Dopo l'accesso, selezionare la sottoscrizione di Azure e il gruppo di risorse e l'insieme di credenziali pertinenti.

    > [!NOTE]
    > Dopo la registrazione, non c'è flessibilità per modificare l'insieme di credenziali dei servizi di ripristino.
    > La modifica di un insieme di credenziali dei servizi di ripristino richiede la dissociazione del server di configurazione dall'insieme di credenziali corrente e la replica di tutte le macchine virtuali protette nel server di configurazione viene interrotta. Per ulteriori informazioni, vedere Gestire il server di configurazione per il ripristino di [emergenza delle macchine virtuali VMware](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. In **installa software di terze parti**:

    |Scenario   |Procedura da seguire  |
    |---------|---------|
    |Posso scaricare e installare MySQL manualmente?     |  Sì. Scaricare l'applicazione MySQL, inserirla nella cartella **C:**, quindi installarla manualmente. Dopo aver accettato i termini e selezionato **Scarica e installa**, il portale dice Già *installato*. È possibile procedere con il passaggio successivo.       |
    |È possibile evitare di scaricare MySQL online?     |   Sì. Inserire l'applicazione di installazione di MySQL nella cartella **C:\Temp\ASRSetup**. Accettare i termini, selezionare **Scarica e installa**e il portale utilizza il programma di installazione aggiunto per installare l'applicazione. Al termine dell'installazione, procedere al passaggio successivo.    |
    |Si desidera scaricare e installare MySQL tramite Azure Site Recovery.    |  Accettare il contratto di licenza e selezionare **Scarica e installa**. Al termine dell'installazione, procedere al passaggio successivo.       |

5. In **Convalida configurazione appliance**, i prerequisiti vengono verificati prima di continuare.
6. In **Configura server vCenter Server/vSphere ESXi**immettere il nome di dominio completo o l'indirizzo IP del server vCenter o l'host vSphere, in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui è in ascolto il server. Immettere un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
7. Immettere le credenziali che verranno usate dal server di configurazione per connettersi al server VMware. Site Recovery usa queste credenziali per individuare automaticamente le macchine virtuali VMware disponibili per la replica. Selezionare **Aggiungi** > **continuazione**. Le credenziali immesse qui vengono salvate in locale.
8. In **Configura credenziali macchina virtuale**immettere il nome utente e la password delle macchine virtuali per installare automaticamente il servizio per dispositivi mobili durante la replica. Per le macchine virtuali **Windows**, l'account deve avere privilegi di amministratore locale nelle macchine virtuali da replicare. Per **Linux**, specificare i dettagli dell'account radice.
9. Selezionare **Finalizza configurazione** per completare la registrazione.
10. Al termine della registrazione, aprire il portale di Azure e verificare che il server di configurazione e il server VMware siano elencati in **Recovery Services Vault** > **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.

## <a name="upgrade-the-configuration-server"></a>Aggiornare il server di configurazione

Per aggiornare il server di configurazione alla versione più recente, vedere Gestire il server di configurazione per il ripristino di [emergenza delle macchine virtuali VMware](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Per istruzioni su come aggiornare tutti i componenti di Site Recovery, vedere [Aggiornamenti del servizio in Site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gestire il server di configurazione

Per evitare interruzioni nella replica in corso, assicurarsi che l'indirizzo IP del server di configurazione non venga modificato dopo la registrazione del server di configurazione in un insieme di credenziali. Per altre informazioni sulle attività comuni di gestione del server di configurazione, vedere Gestire il server di configurazione per il ripristino di [emergenza delle macchine virtuali VMware.](vmware-azure-manage-configuration-server.md)

## <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

Consulta il nostro [articolo sulla risoluzione dei problemi](vmware-azure-troubleshoot-configuration-server.md) per risolvere i problemi di distribuzione & connettività.

## <a name="faqs"></a>Domande frequenti

* Per quanto tempo è valida la licenza fornita su un server di configurazione tramite OVF? Cosa succede se non riattivi la licenza?

    La licenza fornita con un modello OVA è una licenza di valutazione valida per 180 giorni. Prima della scadenza, è necessario attivare la licenza. In caso contrario, può causare l'arresto frequente del server di configurazione e causare un ostacolo alle attività di replica. Per ulteriori informazioni, vedere Gestire il server di configurazione per il ripristino di [emergenza delle macchine virtuali VMware](vmware-azure-manage-configuration-server.md#update-windows-license).

* È possibile usare la macchina virtuale in cui è installato il server di configurazione per scopi diversi?

    No. Usare la macchina virtuale per l'unico scopo del server di configurazione. Assicurarsi di seguire tutte le specifiche indicate in [Prerequisiti](#prerequisites) per una gestione efficiente del ripristino di emergenza.
* È possibile sostituire l'insieme di credenziali già registrate nel server di configurazione con un insieme di credenziali nuovo?

    No. Dopo che un vault è stato registrato con il server di configurazione, non può essere modificato.
* È possibile utilizzare lo stesso server di configurazione per proteggere sia le macchine fisiche che le macchine virtuali?

    Sì. Lo stesso server di configurazione può essere utilizzato per replicare macchine fisiche e virtuali. Tuttavia, è possibile eseguire il failback del computer fisico solo in una macchina virtuale VMware.However, the physical machine can be failed back back to a VMware VM.
* Qual è lo scopo di un server di configurazione e dove viene utilizzato?

    Per altre informazioni sul server di configurazione e sulle relative funzionalità, vedere Architettura di replica da [VMware ad Azure.](vmware-azure-architecture.md)
* Dove posso trovare la versione più recente del server di configurazione?

    Per informazioni sui passaggi da eseguire per aggiornare il server di configurazione tramite il portale, vedere [Aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Per istruzioni su come aggiornare tutti i componenti di Site Recovery, vedere [Aggiornamenti del servizio in Site Recovery](https://aka.ms/asr_how_to_upgrade).
* Dove è possibile scaricare la passphrase per il server di configurazione?

    Per scaricare la passphrase, vedere Gestire il server di configurazione per il ripristino di [emergenza delle macchine virtuali VMware](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* È possibile modificare la passphrase?

    No. Non modificare la passphrase del server di configurazione. Una modifica nella passphrase interrompe la replica dei computer protetti e porta a uno stato di integrità critico.
* Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

    In **Vault di Servizi di ripristino**selezionare **Gestisci** > server di**configurazione****dell'infrastruttura** > di Site Recovery . In **Server** selezionare **Scarica chiave di registrazione** per scaricare il file di credenziali dell'insieme di credenziali.
* È possibile clonare un server di configurazione esistente e utilizzarlo per l'orchestrazione di replica?

    No. L'utilizzo di un componente server di configurazione clonato non è supportato. La clonazione di un server di elaborazione con scalabilità orizzontale è anche uno scenario non supportato. La clonazione dei componenti di Site Recovery influisce sulle repliche in corso.

* È possibile modificare l'IP di un server di configurazione?

    No. Non modificare l'indirizzo IP di un server di configurazione. Assicurarsi che tutti gli indirizzi IP assegnati al server di configurazione siano indirizzi IP statici e non indirizzi IP DHCP.
* È possibile configurare un server di configurazione in Azure?

    Configurare un server di configurazione in un ambiente locale con un line-of-sight diretto con v-Center e ridurre al minimo le latenze di trasferimento dei dati. È possibile eseguire backup pianificati del server di configurazione [a scopo di failback](vmware-azure-manage-configuration-server.md#failback-requirements).

* È possibile modificare il driver della cache in un server di configurazione o in un server di elaborazione con scalabilità orizzontale?

    No, il driver della cache non può essere modificato una volta completata la configurazione.

Per altre domande frequenti sui server di configurazione, vedere [Domande frequenti](vmware-azure-common-questions.md#configuration-server)sul server di configurazione .

## <a name="next-steps"></a>Passaggi successivi

Configurare il ripristino di emergenza di [macchine virtuali VMware](vmware-azure-tutorial.md) in Azure.
