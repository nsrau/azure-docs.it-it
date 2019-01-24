---
title: Preparare il backup delle macchine virtuali di Azure con Backup di Azure
description: Descrive come preparare le macchine virtuali di Azure per il backup con il servizio Backup di Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: 65e4c6d66e410e8cd761128028b7a47e21db86eb
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354502"
---
# <a name="prepare-to-back-up-azure-vms"></a>Preparare il backup delle macchine virtuali di Azure

Questo articolo descrive come preparare per il backup una macchina virtuale di Azure usando un insieme di credenziali di Servizi di ripristino di [Backup di Azure](backup-introduction-to-azure-backup.md). La preparazione per il backup include:


> [!div class="checklist"]
> * Prima di iniziare, esaminare gli scenari supportati e le limitazioni.
> * Verificare i prerequisiti, inclusi i requisiti delle macchine virtuali di Azure e la connettività di rete.
> * Creare un insieme di credenziali.
> * Selezionare la modalità di replica della risorsa di archiviazione.
> * Individuare le macchine virtuali, configurare le impostazioni di backup e i criteri.
> * Abilitare il backup per alcune macchine virtuali.


> [!NOTE]
   > Questo articolo descrive come eseguire il backup di macchine virtuali di Azure configurando un insieme di credenziali e selezionando le macchine virtuali per il backup. È utile per eseguire il backup di più macchine virtuali. È anche possibile eseguire il backup di una macchina virtuale di Azure direttamente dalle relative impostazioni. [Altre informazioni](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Prima di iniziare

1. [Ottenere una panoramica](backup-azure-vms-introduction.md) di Backup di Azure per le macchine virtuali di Azure.
2. Esaminare i dettagli relativi al supporto e le limitazioni seguenti.

   **Supporto/Limitazione** | **Dettagli**
   --- | ---
   **Sistema operativo Windows** | Windows Server 2008 R2 a 64 bit o versione successiva.<br/><br/> Windows Client 7 a 64 bit o versione successiva.
   **Sistema operativo Linux** | È possibile eseguire il backup delle distribuzioni Linux a 64 bit [supportate da Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ad eccezione di CoreOS Linux.<br/><br/> Esaminare i sistemi operativi Linux che [supportano il ripristino di file](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Possono essere usate altre distribuzioni Linux a condizione che nella macchina virtuale sia disponibile l'agente di macchine virtuali e che sia presente il supporto per Python. Tuttavia, queste distribuzioni non sono supportate.
   **Area** | È possibile eseguire il backup delle macchine virtuali di Azure in tutte le [aree supportate](https://azure.microsoft.com/regions/#services). Se un'area non è supportata, non sarà possibile selezionarla quando si crea l'insieme di credenziali.<br/><br/> Non è possibile eseguire il backup e il ripristino tra aree di Azure, ma solo all'interno di una singola area.
   **Limite dischi dati** | Non è possibile eseguire il backup di macchine virtuali con più di 16 dischi dati.
   **Archiviazione condivisa** | Non è consigliabile eseguire il backup delle macchine virtuali usando CSV o File server di scalabilità orizzontale. I writer CSV potrebbero non essere eseguiti correttamente.
   **Crittografia Linux** | Il backup di macchine virtuali Linux crittografate con Linux Unified Key Setup (LUKS) non è supportato.
   **Coerenza delle macchine virtuali** | Backup di Azure non supporta la coerenza tra più macchine virtuali.
   **Rete** | I dati di backup non includono le unità di rete collegate a una macchina virtuale.<br/><br/>
   **Snapshot** | L'acquisizione di snapshot su un disco abilitato per l'acceleratore di scrittura non è supportata. Questa limitazione impedisce a Backup di Azure di acquisire uno snapshot coerente con l'app di tutti i dischi delle macchine virtuali.
   **PowerShell** | Alcune azioni che sono disponibili solo con PowerShell:<br/><br/> - Il ripristino di macchine virtuali gestite da servizi di bilanciamento del carico interni ed esterni o con più adapter o indirizzi IP riservati. [Altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> - Il ripristino di una macchina virtuale del controller di dominio in una configurazione con più controller di dominio. [Altre informazioni](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)
   **Ora di sistema** | Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Modificare manualmente i criteri di backup in base alle necessità.
   **Account di archiviazione** | Se si usa un account di archiviazione di rete con restrizioni, abilitare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** in modo che il servizio Backup di Azure possa accedere all'account. Il ripristino a livello di elemento non è supportato per gli account di archiviazione di rete con restrizioni.<br/><br/> In un account di archiviazione verificare che le impostazioni di **Firewall e reti virtuali** consentano l'accesso da **Tutte le reti**.


## <a name="prerequisites"></a>Prerequisiti

- È necessario creare l'insieme di credenziali nella stessa area in cui si trovano le macchine virtuali di Azure di cui si vuole eseguire il backup.
- Controllare le aree delle macchine virtuali di Azure prima di iniziare.
    - Se sono presenti macchine virtuali in più aree, creare un insieme di credenziali in ogni area.
    - Non è necessario specificare gli account di archiviazione per archiviare i dati di backup. L'insieme di credenziali e il servizio Backup di Azure gestiscono questo aspetto automaticamente.
- Verificare che l'agente di macchine virtuali sia installato nelle macchine virtuali di Azure di cui si vuole eseguire il backup.

### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

Per abilitare il backup, Backup di Azure installa un'estensione di backup (VM Snapshot o VM Snapshot Linux) nell'agente di macchine virtuali eseguito nella macchina virtuale di Azure.
    -  L'agente di macchine virtuali di Azure viene installato per impostazione predefinita in qualsiasi macchina virtuale di Windows distribuita da un'immagine del Marketplace di Azure. Quando si distribuisce un'immagine di Marketplace di Azure dal portale, da PowerShell, dall'interfaccia della riga di comando o da un modello di Azure Resource Manager, viene installato anche l'agente di macchine Virtuali di Azure.
    - Se è stata eseguita la migrazione di una macchina virtuale da un sistema locale, l'agente non viene installato ed è necessario installarlo prima di abilitare il backup della macchina virtuale.

Se necessario, installare l'agente come indicato di seguito.

**VM** | **Dettagli**
--- | ---
**Macchine virtuali di Windows** | [Scaricare e installare](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) l'agente nel computer con autorizzazioni di amministratore.<br/><br/> Per verificare l'installazione, in *C:\WindowsAzure\Packages* nella macchina virtuale fare clic con il pulsante destro del mouse su WaAppAgent.exe > **Proprietà** > scheda **Dettagli**. Il campo **Versione prodotto** deve essere impostato su 2.6.1198.718 o su una versione superiore.
**Macchine virtuali di Linux** | Il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure prevede l'installazione tramite un pacchetto RPM o DEB dal repository di pacchetti della distribuzione. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository. L'agente è disponibile in [GitHub](https://github.com/Azure/WALinuxAgent), ma non è consigliabile installarlo direttamente da questa posizione.
In caso di problemi con il backup della VM di Azure, usare la tabella seguente per verificare che l'agente di macchine virtuali di Azure sia installato correttamente nella VM. La tabella contiene informazioni aggiuntive sull'agente di macchine virtuali per VM Windows e Linux.

### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

L'estensione di backup in esecuzione nella macchina virtuale deve avere accesso in uscita agli indirizzi IP pubblici di Azure.

> [!NOTE]
> Alla macchina virtuale di Azure non è richiesto nessun accesso esplicito alla rete in uscita per comunicare con il servizio Backup di Azure. Tuttavia, alcune macchine virtuali meno recenti potrebbero riscontrare problemi e generare l'errore **ExtensionSnapshotFailedNoNetwork**; per risolvere questo errore, scegliere una delle opzioni seguenti per consentire all'estensione backup di comunicare con gli indirizzi IP pubblici di Azure al fine di fornire un percorso chiaro per il traffico di backup.

- **Regole del gruppo di sicurezza di rete**: consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). È possibile aggiungere una regola che consenta l'accesso al servizio Backup di Azure usando un [tag del servizio](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure), anziché consentire singolarmente ogni intervallo di indirizzi e gestire tali indirizzi nel tempo. Per altre informazioni sul tag di servizio vedere questo [articolo](../virtual-network/security-overview.md#service-tags).
- **Proxy**: Distribuire un server proxy HTTP per eseguire il routing del traffico
- **Firewall di Azure**: Consentire il traffico attraverso Firewall di Azure nella macchina virtuale usando un tag FQDN per il servizio Backup di Azure

Nel valutare le opzioni, prendere in considerazione vantaggi e svantaggi.

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
**Gruppo di sicurezza di rete** | Senza costi aggiuntivi. Semplicità di gestione tramite tag del servizio. | Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di archiviazione. |
**Proxy HTTP** | Possibilità di controllo granulare sugli URL di archiviazione.<br/><br/> Singolo punto di accesso Internet per le macchine virtuali.<br/><br/> Costi aggiuntivi per il proxy.
**Tag FQDN** | Semplicità d'uso se è configurato Firewall di Azure in una subnet di rete virtuale. | Impossibile creare tag FQDN personalizzati o modificare FQDN in un tag.

Se si usa Azure Managed Disks, può essere necessario aprire una porta aggiuntiva (8443) nei firewall.

### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurare una regola del gruppo di sicurezza di rete per consentire l'accesso in uscita ad Azure

Se l'accesso della macchina virtuale di Azure è gestito da un gruppo di sicurezza di rete, è necessario consentire l'accesso in uscita per l'archiviazione di backup sulle porte e sugli intervalli necessari.

1. Nella macchina virtuale > **Rete** fare clic su **Aggiungi regola porta in uscita**.

  - Se è configurata una regola che nega l'accesso, la nuova regola di assenso deve avere un valore superiore. Se ad esempio la priorità di una regola **Deny_All** è impostata su 1000, la nuova regola deve essere impostata su un valore minore di 1000.
2. In **Aggiungi regola di sicurezza in uscita** fare clic su **Avanzate**.
3. In **Origine** selezionare **Rete virtuale**.
4. In **Intervalli di porte di origine** immettere un asterisco (*) per consentire l'accesso in uscita da qualsiasi porta.
5. In **Destinazione** selezionare **Service Tag** (Tag del servizio). Nell'elenco selezionare Archiviazione.<region> L'area è quella in cui si trovano l'insieme di credenziali e le macchine virtuali di cui si vuole eseguire il backup.
6. In **Intervalli di porte di destinazione** selezionare la porta.

    - Macchina virtuale con dischi non gestiti e account di archiviazione non crittografato: 80
    - Macchina virtuale con dischi non gestiti e account di archiviazione crittografato: 443 (impostazione predefinita)
    - Macchina virtuale gestita: 8443
7. In **Protocollo** selezionare **TCP**.
8. In **Priorità** assegnare un valore di priorità inferiore a tutte le regole di negazione di livello superiore.
9. Specificare un nome e una descrizione per la regola e fare clic su **OK**.

È possibile applicare la regola del gruppo di sicurezza di rete a più macchine virtuali per consentire a Backup di Azure l'accesso in uscita ad Azure.

Questo video illustra la procedura descritta.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> I tag del servizio di archiviazione sono in versione di anteprima. Sono disponibili solo in aree specifiche. Per un elenco delle aree, vedere [Tag di servizio per l'archiviazione](../virtual-network/security-overview.md#service-tags).

### <a name="route-backup-traffic-through-a-proxy"></a>Instradare il traffico di backup attraverso un proxy

È possibile instradare il traffico di backup attraverso un proxy e quindi concedere al proxy l'accesso agli intervalli di Azure necessari.
È necessario configurare la macchina virtuale del proxy per consentire quanto segue:

- La macchina virtuale di Azure deve instradare tutto il traffico HTTP diretto alla rete Internet pubblica attraverso il proxy.
- Il proxy deve consentire il traffico in ingresso dalle macchine virtuali nella rete virtuale (VNet) applicabile.
- Il gruppo di sicurezza di rete **NSF-lockdown** necessita di una regola che consenta il traffico Internet in uscita dalla macchina virtuale del proxy.

Ecco come deve essere configurato il proxy. I valori di esempio usati devono essere sostituiti con valori personalizzati.

#### <a name="set-up-a-system-account-proxy"></a>Configurare un proxy di account di sistema
Se non si ha un proxy di account di sistema, configurarne uno come indicato di seguito:

1. Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Eseguire **PsExec.exe -i -s cmd.exe** per eseguire il prompt dei comandi con un account di sistema.
3. Eseguire il browser nel contesto di sistema. Ad esempio:  **PROGRAMFILES%\Internet Explorer\iexplore.exe** per Internet Explorer.  
4. Definire le impostazioni del proxy.
    - Nei computer Linux:
        - Aggiungere questa riga nel file **/etc/environment**:
            - **http_proxy=http://proxy indirizzo IP:porta proxy**
        - Aggiungere queste righe nel file **/etc/waagent.conf**:
            - **HttpProxy.Host=indirizzo IP proxy**
            - **HttpProxy.Port=porta proxy**
    - Nelle impostazioni del browser dei computer Windows specificare che deve essere usato un proxy. Se attualmente si usa un proxy per un account utente, è possibile usare questo script per applicare l'impostazione a livello di account di sistema.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>Consentire le connessioni in ingresso sul proxy

Consentire le connessioni in ingresso nelle impostazioni del proxy.

- Ad esempio, aprire **Windows Firewall con sicurezza avanzata**.
    - Fare clic con il pulsante destro del mouse su **Regole in entrata** > **Nuova regola**.
    - In **Tipo di regola** selezionare **Personalizzate** > **Avanti**.
    - In **Programma** selezionare **Tutti i programmi** > **Avanti**.
    - In **Protocolli e porte** impostare il tipo su **TCP**, **Porte locali** su **Porte specifiche** e **Porta remota** su **Tutte le porte**.
    - Completare la procedura guidata e specificare un nome per la regola.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Aggiungere una regola di eccezione al gruppo di sicurezza di rete (NSG)

Nel gruppo di sicurezza di rete **NSF-lockdown** consentire il traffico da qualsiasi porta su 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS).

- Lo script PowerShell seguente contiene un esempio per consentire il traffico.
- Anziché consentire il traffico in uscita verso tutti gli indirizzi Internet pubblici, è possibile specificare un intervallo di indirizzi IP (-DestinationPortRange) oppure usare il tag del servizio storage.region.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Consentire l'accesso firewall con il tag FQDN

È possibile configurare Firewall di Azure per consentire l'accesso in uscita del traffico di rete a Backup di Azure.

- [Informazioni](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sulla distribuzione di Firewall di Azure.
- [Informazioni](https://docs.microsoft.com/azure/firewall/fqdn-tags) sui tag FQDN.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

Un insieme di credenziali di Servizi di ripristino per Backup consente di archiviare i backup e i punti di ripristino creati nel corso del tempo, nonché i criteri di backup associati ai computer sottoposti a backup. Creare un insieme di credenziali nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu **Hub** selezionare **Sfoglia** e digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Digitazione nella casella e selezione di "Insiemi di credenziali dei servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
3. Nel menu di **Insiemi di credenziali dei servizi di ripristino** selezionare **Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Verrà visualizzato il riquadro **Insiemi di credenziali dei servizi di ripristino**, in cui viene richiesto di specificare le informazioni relative a **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Riquadro "Insiemi di credenziali dei servizi di ripristino"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali.
    - Il nome deve essere univoco per la sottoscrizione di Azure.
    - Può contenere da 2 a 50 caratteri.
    - Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
5. Selezionare **Sottoscrizione** per visualizzare l'elenco delle sottoscrizioni disponibili. Se non si è certi della sottoscrizione da usare, usare quella predefinita (o suggerita). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
6. Selezionare **Gruppo di risorse** per visualizzare l'elenco dei gruppi di risorse disponibili oppure **Nuovo** per crearne uno nuovo. [Altre informazioni](../azure-resource-manager/resource-group-overview.md) sui gruppi di risorse.
7. Selezionare **Località** per selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali *deve* trovarsi nella stessa area delle macchine virtuali di cui si vuole eseguire il backup.
8. Selezionare **Create**.
    - La creazione dell'insieme di credenziali può richiedere alcuni minuti.
    - Monitorare le notifiche di stato nell'area superiore destra del portale.
    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Al termine della creazione, l'insieme di credenziali verrà visualizzato nell'elenco degli insiemi di credenziali dei servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

## <a name="set-up-storage-replication"></a>Configurare la replica di archiviazione

Per impostazione predefinita, l'insieme di credenziali prevede l'[archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). È consigliabile l'archiviazione con ridondanza geografica per il backup primario, ma è possibile usare l'[archiviazione con ridondanza locale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per un'opzione più economica.

Modificare la replica di archiviazione nel modo seguente:

1. Nell'insieme di credenziali > **Infrastruttura di backup** fare clic su **Configurazione di backup**.

   ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. In **Configurazione di backup** modificare il metodo di ridondanza dell'archiviazione in base alle esigenze e selezionare **Salva**.


## <a name="configure-backup"></a>Configurare il backup

Individuare le macchine virtuali nella sottoscrizione e configurare il backup.

1. Nell'insieme di credenziali > **Panoramica** fare clic su **+ Backup**.

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Verranno visualizzati i riquadri **Backup** e **Obiettivo del backup**.

2. In **Obiettivo del backup**> **Posizione di esecuzione del carico di lavoro** selezionare **Azure**. In **Elementi di cui eseguire il backup** selezionare **Macchina virtuale** >  **OK**. In questo modo viene registrata l'estensione delle macchine virtuali nell'insieme di credenziali.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Questo passaggio registra l'estensione della VM nell'insieme di credenziali. Il riquadro **Obiettivo del backup** verrà chiuso e verrà visualizzato il riquadro **Criteri di backup**.

3. In **Criteri di backup** selezionare i criteri da associare all'insieme di credenziali. Fare quindi clic su **OK**.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.
    - Fare clic su **Crea nuovo oggetto** per creare un criterio. [Altre informazioni](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) sulla definizione di un criterio.

    ![Riquadri Backup e Criteri di backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Nel riquadro **Seleziona macchine virtuali** selezionare le macchine virtuali che useranno il criterio di backup specificato > **OK**.

    - La macchina virtuale selezionata viene convalidata.
    - È possibile selezionare solo macchine virtuali presenti nella stessa area dell'insieme di credenziali. Il backup delle macchine virtuali può essere eseguito in un solo insieme di credenziali.

   ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. In **Backup** selezionare **Abilita backup**.

   - Questa operazione consente di distribuire i criteri nell'insieme di credenziali e nelle macchine virtuali e installare l'estensione di backup nell'agente di macchine virtuali in esecuzione nella macchina virtuale di Azure.
   - Questo passaggio non consente di creare il punto di ripristino iniziale per la macchina virtuale.

   ![Pulsante Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo l'abilitazione del backup:

- Il criterio di backup viene eseguito in base alla pianificazione del backup.
- Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno.
    - Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione.
    -  Il backup della macchina virtuale, tuttavia, viene eseguito anche se la macchina è spenta e non è possibile installare l'estensione. Questa situazione è detta *VM offline*. In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*.
- Se si vuole generare subito un backup su richiesta per la macchina virtuale, in **Elementi di backup** fare clic sui puntini di sospensione (...) accanto alla macchina virtuale > **Esegui Backup ora**.


## <a name="next-steps"></a>Passaggi successivi

- Risolvere eventuali problemi che si verificano con gli [agenti di macchine virtuali di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) o con il [backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md).
- [Eseguire un backup delle VM di Azure](backup-azure-vms-first-look-arm.md)
