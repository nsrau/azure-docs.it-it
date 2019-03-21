---
title: Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino con Backup di Azure
description: Informazioni su come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino con Backup di Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: c6d6e380cded18a089f624f90d998477a89293be
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259042"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino

Questo articolo descrive come eseguire il backup di macchine virtuali di Azure con [Backup di Azure](backup-overview.md)distribuendo e abilitando il backup in un insieme di credenziali di Servizi di ripristino.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Verificare i prerequisiti e gli scenari supportati.
> * Preparare le macchine virtuali di Azure. Installare, se necessario, l'agente di macchine virtuali di Azure e verificare l'accesso in uscita per macchine virtuali.
> * Creare un insieme di credenziali.
> * Configurare l'archiviazione per l'insieme di credenziali
> * Individuare le macchine virtuali, configurare le impostazioni di backup e i criteri.
> * Abilitare il backup per macchine virtuali di Azure


> [!NOTE]
   > Questo articolo descrive come eseguire il backup di macchine virtuali di Azure configurando un insieme di credenziali e selezionando le macchine virtuali per il backup. È utile per eseguire il backup di più macchine virtuali. È anche possibile [eseguire il backup di macchine virtuali](backup-azure-vms-first-look-arm.md) di Azure direttamente dalle relative impostazioni.

## <a name="before-you-start"></a>Prima di iniziare

Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer.

1. [Verificare](backup-architecture.md#architecture-direct-backup-of-azure-vms) l'architettura del backup delle macchine virtuali di Azure.
[Informazioni su](backup-azure-vms-introduction.md) backup delle macchine virtuali di Azure ed estensione di backup.
2. [Verificare la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.
3. Preparare le macchine virtuali di Azure. Se non è presente, installare l'agente di macchine virtuali e verificare l'accesso in uscita per le macchine virtuali di cui si vuole eseguire il backup.


## <a name="prepare-azure-vms"></a>Preparare le macchine virtuali di Azure

Installare, se necessario, l'agente di macchine virtuali e verificare l'accesso in uscita dalle macchine virtuali.

### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali
Se necessario, installare l'agente come indicato di seguito.

**VM** | **Dettagli**
--- | ---
**Macchine virtuali di Windows** | [Scaricare e installare](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) il file MSI per l'agente. Eseguire l'installazione sul computer con autorizzazioni di amministratore.<br/><br/> Per verificare l'installazione, in *C:\WindowsAzure\Packages* nella macchina virtuale fare clic con il pulsante destro del mouse su WaAppAgent.exe > **Proprietà** > scheda **Dettagli**. Il campo **Versione prodotto** deve essere impostato su 2.6.1198.718 o su una versione superiore.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non venga eseguita nessuna operazione di backup e [reinstallare l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Macchine virtuali di Linux** | Il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure prevede l'installazione tramite un pacchetto RPM o DEB dal repository di pacchetti della distribuzione. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository. L'agente è disponibile in [GitHub](https://github.com/Azure/WALinuxAgent), ma non è consigliabile installarlo direttamente da questa posizione.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non venga eseguita nessuna operazione di backup e aggiornare i file binari.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

L'estensione di backup in esecuzione nella macchina virtuale deve avere accesso in uscita agli indirizzi IP pubblici di Azure.

- Alla macchina virtuale di Azure non è richiesto nessun accesso esplicito alla rete in uscita per comunicare con il servizio Backup di Azure.
- Tuttavia, alcune macchine virtuali meno recenti potrebbero riscontrare problemi e generare l'errore **ExtensionSnapshotFailedNoNetwork** durante il tentativo di connessione. In questo caso, usare una delle opzioni seguenti per consentire all'estensione di backup di comunicare con gli indirizzi IP pubblici di Azure per il traffico di backup.

   **Opzione** | **Azione** | **Vantaggi** | **Svantaggi**
   --- | --- | --- | ---
   **Configurare le regole del gruppo di sicurezza di rete** | consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/>  È possibile aggiungere una regola che consenta l'accesso al servizio Backup di Azure usando un [tag di servizio](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure), anziché consentire e gestire singolarmente ogni intervallo di indirizzi. [Altre informazioni](../virtual-network/security-overview.md#service-tags) sui tag di servizio. | Senza costi aggiuntivi. Semplicità di gestione tramite tag del servizio.
   **Distribuire un proxy** | Distribuire un server proxy HTTP per eseguire il routing del traffico | Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di archiviazione. Possibilità di controllo granulare sugli URL di archiviazione.<br/><br/> Singolo punto di accesso Internet per le macchine virtuali.<br/><br/> Costi aggiuntivi per il proxy.<br/><br/>
   **Configurare il Firewall di Azure** | consentire il traffico attraverso Firewall di Azure nella macchina virtuale usando un tag FQDN per il servizio Backup di Azure.|  Semplicità d'uso se è configurato Firewall di Azure in una subnet di rete virtuale. | Impossibile creare tag FQDN personalizzati o modificare FQDN in un tag.<br/><br/> Se si usa Azure Managed Disks, può essere necessario aprire una porta aggiuntiva (8443) nei firewall.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurare una regola del gruppo di sicurezza di rete per consentire l'accesso in uscita ad Azure

Se l'accesso della macchina virtuale di Azure è gestito da un gruppo di sicurezza di rete, è necessario consentire l'accesso in uscita per l'archiviazione di backup sulle porte e sugli intervalli necessari.

1. Nella macchina virtuale > **Rete** fare clic su **Aggiungi regola porta in uscita**.
2. In **Aggiungi regola di sicurezza in uscita** fare clic su **Avanzate**.
3. In **Origine** selezionare **Rete virtuale**.
4. In **Intervalli di porte di origine** immettere un asterisco (*) per consentire l'accesso in uscita da qualsiasi porta.
5. In **Destinazione** selezionare **Service Tag** (Tag del servizio). Selezionare **Storage.region** dall'elenco. L'area è quella in cui si trovano l'insieme di credenziali e le macchine virtuali di cui si vuole eseguire il backup.
6. In **Intervalli di porte di destinazione** selezionare la porta.
    - Macchina virtuale non gestita con account di archiviazione non crittografato: 80
    - Macchina virtuale non gestita con account di archiviazione crittografato: 443 (impostazione predefinita)
    - Macchina virtuale gestita: 8443
7. In **Protocollo** selezionare **TCP**.
8. In **Priorità** specificare un valore di priorità inferiore a tutte le regole di negazione di livello superiore. Se è configurata una regola che nega l'accesso, la nuova regola di assenso deve avere un valore superiore. Se ad esempio la priorità di una regola **Deny_All** è impostata su 1000, la nuova regola deve essere impostata su un valore minore di 1000.
9. Specificare un nome e una descrizione per la regola e fare clic su **OK**.

È possibile applicare la regola del gruppo di sicurezza di rete a più macchine virtuali per consentire l'accesso in uscita.

Questo video illustra la procedura descritta.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>Instradare il traffico di backup attraverso un proxy

È possibile instradare il traffico di backup attraverso un proxy e quindi concedere al proxy l'accesso agli intervalli di Azure necessari.
È necessario configurare la macchina virtuale del proxy per consentire quanto segue:

- La macchina virtuale di Azure deve instradare tutto il traffico HTTP diretto alla rete Internet pubblica attraverso il proxy.
- Il proxy deve consentire il traffico in ingresso dalle macchine virtuali nella rete virtuale (VNet) applicabile.
- Il gruppo di sicurezza di rete **NSF-lockdown** necessita di una regola che consenta il traffico Internet in uscita dalla macchina virtuale del proxy.

##### <a name="set-up-the-proxy"></a>Configurare il proxy
Se non si ha un proxy di account di sistema, configurarne uno come indicato di seguito:

1. Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553).

2. Eseguire **PsExec.exe -i -s cmd.exe** per eseguire il prompt dei comandi con un account di sistema.
3. Eseguire il browser nel contesto di sistema. Ad esempio: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** per Internet Explorer.  
4. Definire le impostazioni del proxy.
   - Nei computer Linux:
     - Aggiungere questa riga nel file **/etc/environment**:
       - **http_proxy = http:\//proxy IP: proxy indirizzo porta**
     - Aggiungere queste righe nel file **/etc/waagent.conf**:
         - **HttpProxy.Host=indirizzo IP proxy**
         - **HttpProxy.Port=porta proxy**
   - Nelle impostazioni del browser dei computer Windows specificare che deve essere usato un proxy. Se attualmente si usa un proxy per un account utente, è possibile usare questo script per applicare l'impostazione a livello di account di sistema.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

##### <a name="allow-incoming-connections-on-the-proxy"></a>Consentire le connessioni in ingresso sul proxy

Consentire le connessioni in ingresso nelle impostazioni del proxy.

- Ad esempio, aprire **Windows Firewall con sicurezza avanzata**.
    - Fare clic con il pulsante destro del mouse su **Regole in entrata** > **Nuova regola**.
    - In **Tipo di regola** selezionare **Personalizzate** > **Avanti**.
    - In **Programma** selezionare **Tutti i programmi** > **Avanti**.
    - In **Protocolli e porte** impostare il tipo su **TCP**, **Porte locali** su **Porte specifiche** e **Porta remota** su **Tutte le porte**.
    - Completare la procedura guidata e specificare un nome per la regola.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Aggiungere una regola al gruppo di sicurezza di rete per il proxy

Nel gruppo di sicurezza di rete **NSF-lockdown** consentire il traffico da qualsiasi porta su 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS).

- Lo script PowerShell seguente contiene un esempio per consentire il traffico.
- Anziché consentire il traffico in uscita verso tutti gli indirizzi Internet pubblici, è possibile specificare un intervallo di indirizzi IP (-DestinationPortRange) oppure usare il tag del servizio storage.region.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Consentire l'accesso firewall con il tag FQDN

È possibile configurare Firewall di Azure per consentire l'accesso in uscita del traffico di rete a Backup di Azure.

- [Informazioni](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sulla distribuzione di Firewall di Azure.
- [Informazioni](https://docs.microsoft.com/azure/firewall/fqdn-tags) sui tag FQDN.

## <a name="set-up-storage-replication"></a>Configurare la replica di archiviazione

Per impostazione predefinita, l'insieme di credenziali prevede l'[archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). È consigliabile l'archiviazione con ridondanza geografica per il backup primario, ma è possibile usare l'[archiviazione con ridondanza locale](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per un'opzione più economica.

Backup di Azure gestisce automaticamente l'archiviazione per l'insieme di credenziali. È necessario specificare una modalità di replica archiviazione.
Modificare la replica di archiviazione nel modo seguente:

1. Nel pannello **Insieme di credenziali dei servizi di ripristino** fare clic sul nuovo insieme di credenziali. Sotto il **le impostazioni** fare clic su **proprietà**.
2. Nelle **delle proprietà**, in **configurazione Backup**, fare clic su **Update**.

3. Selezionare il tipo di replica di archiviazione e fare clic su **salvare**.

      ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

Individuare le macchine virtuali nella sottoscrizione e configurare il backup.

1. Nell'insieme di credenziali > **Panoramica** fare clic su **+ Backup**.

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Verranno visualizzati i riquadri **Backup** e **Obiettivo del backup**.

2. In **Obiettivo del backup**> **Posizione di esecuzione del carico di lavoro** selezionare **Azure**. In **Elementi di cui eseguire il backup** selezionare **Macchina virtuale** >  **OK**. In questo modo viene registrata l'estensione delle macchine virtuali nell'insieme di credenziali.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Questo passaggio registra l'estensione della VM nell'insieme di credenziali. Il riquadro **Obiettivo del backup** verrà chiuso e verrà visualizzato il riquadro **Criteri di backup**.

3. In **Criteri di backup** selezionare i criteri da associare all'insieme di credenziali. Fare quindi clic su **OK**.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.
    - Fare clic su **Crea nuovo oggetto** per creare un criterio. [Altre informazioni](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) sulla definizione di un criterio.

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

- Un backup iniziale viene eseguito in base alla pianificazione del backup.
- Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno.
    - Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione.
    -  Il backup della macchina virtuale, tuttavia, viene eseguito anche se la macchina è spenta e non è possibile installare l'estensione. Questa situazione è detta *VM offline*. In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*.
    Si noti che Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Modificare manualmente i criteri di backup in base alle necessità.

## <a name="run-the-initial-backup"></a>Eseguire il backup iniziale

Il backup iniziale verrà eseguito in base alla pianificazione, a meno che l'utente non lo esegua immediatamente in modo manuale. Eseguirlo manualmente come indicato qui:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup** fare clic su **Macchine virtuali di Azure**.
3. In **Elementi di backup** fare clic sui puntini di sospensione **...**.
4. Fare clic su **Esegui backup ora**.
5. In **Esegui backup ora** usare il comando del calendario per selezionare l'ultimo giorno di conservazione del punto di ripristino > **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.



## <a name="next-steps"></a>Passaggi successivi

- Risolvere eventuali problemi che si verificano con gli [agenti di macchine virtuali di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) o con il [backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md).
- [Eseguire un backup delle VM di Azure](backup-azure-vms-first-look-arm.md)
