---
title: Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino tramite Backup di Azure
description: Viene descritto come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino tramite Backup di Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3342b15511305ab337d9b5032080e205e36150d3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049814"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino

Questo articolo descrive come eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino usando il [Backup di Azure](backup-overview.md) servizio. 

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Verificare i prerequisiti per il backup e il supporto.
> * Preparare le macchine virtuali di Azure. Installare, se necessario, l'agente di macchine virtuali di Azure e verificare l'accesso in uscita per macchine virtuali.
> * Creare un insieme di credenziali.
> * Individuare le macchine virtuali e configurare un criterio di backup.
> * Abilitare il backup per macchine virtuali di Azure.


> [!NOTE]
> Questo articolo descrive come configurare un insieme di credenziali e selezionare le macchine virtuali per eseguire il backup. È utile per eseguire il backup di più macchine virtuali. In alternativa, è possibile [eseguire il backup di una singola macchina virtuale Azure](backup-azure-vms-first-look-arm.md) direttamente dalle impostazioni della macchina virtuale.

## <a name="before-you-start"></a>Prima di iniziare


- [Revisione](backup-architecture.md#architecture-direct-backup-of-azure-vms) l'architettura del backup delle macchine Virtuali di Azure.
- [Informazioni su](backup-azure-vms-introduction.md) backup delle macchine virtuali di Azure ed estensione di backup.
- [Verificare la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.


## <a name="prepare-azure-vms"></a>Preparare le macchine virtuali di Azure

In alcuni casi, potrebbe essere necessario configurare l'agente di macchine Virtuali di Azure nelle VM di Azure o consentire l'accesso in uscita in modo esplicito in una macchina virtuale.

### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali 

Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la VM è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina locale, è necessario installare l'agente manualmente, come riepilogato nella tabella.

**VM** | **Dettagli**
--- | ---
**Windows** | 1. [Scaricare e installare](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) il file MSI per l'agente.<br/><br/> 2. Eseguire l'installazione sul computer con autorizzazioni di amministratore.<br/><br/> 3. Verificare l'installazione. Nelle *C:\WindowsAzure\Packages* nella macchina virtuale, fare doppio clic su **WaAppAgent.exe** > **proprietà**. Nel **informazioni dettagliate** della scheda **versione prodotto** deve essere 2.6.1198.718 o versione successiva.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che non siano in esecuzione operazioni di backup, e [reinstallare l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installare usando un RPM o un pacchetto DEB dal repository di pacchetti della distribuzione. Questo è il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository. L'agente è disponibile in [GitHub](https://github.com/Azure/WALinuxAgent), ma non è consigliabile installarlo direttamente da questa posizione.<br/><br/> Se si sta aggiornando l'agente, assicurarsi che nessuna operazione di backup sono in esecuzione e aggiornare i file binari.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

L'estensione di backup in esecuzione nella macchina virtuale richiede accesso in uscita agli indirizzi IP pubblici di Azure.

In genere, non è necessario consentire in modo esplicito l'accesso alla rete in uscita per una macchina virtuale di Azure in modo che possa comunicare con Backup di Azure.
Se non è possibile connettere le macchine virtuali e se viene visualizzato l'errore **ExtensionSnapshotFailedNoNetwork**, è consigliabile consentire in modo esplicito l'accesso. L'estensione di backup può quindi comunicare con Azure indirizzi IP pubblici per il traffico di backup.


#### <a name="explicitly-allow-outbound-access"></a>Consentire l'accesso in uscita in modo esplicito

Se una macchina virtuale non può connettersi al servizio di Backup, consentono in modo esplicito l'accesso in uscita tramite un metodo riepilogato nella tabella.

**Opzione** | **Azione** | **Dettagli** 
--- | --- | --- 
**Configurare le regole NSG** | consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Invece di consentire e la gestione di ogni intervallo di indirizzi, è possibile aggiungere una regola del gruppo (NSG) di sicurezza di rete che consenta l'accesso al servizio Backup di Azure usando un [tag del servizio](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). [Altre informazioni](../virtual-network/security-overview.md#service-tags)<br/><br/> Non sono previsti costi aggiuntivi.<br/><br/> Le regole sono semplici da gestire con i tag di servizio.
**Distribuire un proxy** | Distribuire un server proxy HTTP per eseguire il routing del traffico | Questo metodo offre l'accesso a tutto l'ambiente di Azure e l'archiviazione non solo.<br/><br/> Possibilità di controllo granulare sugli URL di archiviazione.<br/><br/> È presente un singolo punto di accesso internet per le macchine virtuali.<br/><br/> Vi sono costi aggiuntivi per un proxy.
**Configurare il Firewall di Azure** | Consentire il traffico attraverso il Firewall di Azure nella macchina virtuale, tramite un tag di nome di dominio completo per il servizio Backup di Azure. |  Questo metodo è semplice da utilizzare se è configurato il Firewall di Azure nella subnet di una rete virtuale.<br/><br/> È possibile creare il proprio tag del nome di dominio completo o modificare gli FQDN in un tag.<br/><br/> Se si usa Azure Managed Disks, può essere necessario aprire una porta aggiuntiva (8443) nei firewall.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurare una regola del gruppo di sicurezza di rete per consentire l'accesso in uscita ad Azure

Se un gruppo di sicurezza consente di gestire l'accesso alle macchine Virtuali, consentire l'accesso in uscita per l'archiviazione dei backup per le porte e gli intervalli necessari.

1. Nelle proprietà della VM > **Networking**, selezionare **Aggiungi regola porta in uscita**.
2. Nelle **Aggiungi regola di sicurezza in uscita**, selezionare **avanzate**.
3. In **Origine** selezionare **Rete virtuale**.
4. Nelle **intervalli di porte di origine**, immettere un asterisco (*) per consentire l'accesso in uscita da qualsiasi porta.
5. In **Destinazione** selezionare **Service Tag** (Tag del servizio). Selezionare **Storage.region** dall'elenco. L'area è dove si trovano l'insieme di credenziali e le macchine virtuali che si desidera eseguire il backup.
6. In **Intervalli di porte di destinazione** selezionare la porta.
    - Macchina virtuale non gestita con account di archiviazione non crittografato: 80
    - Macchina virtuale non gestita con account di archiviazione crittografato: 443 (impostazione predefinita)
    - Macchina virtuale gestita: 8443
7. In **Protocollo** selezionare **TCP**.
8. In **Priorità** specificare un valore di priorità inferiore a tutte le regole di negazione di livello superiore.
   
   Se si dispone di una regola che neghi l'accesso, il nuovo Consenti regola deve essere superiore. Se ad esempio la priorità di una regola **Deny_All** è impostata su 1000, la nuova regola deve essere impostata su un valore minore di 1000.
9. Specificare un nome e una descrizione per la regola e selezionare **OK**.

È possibile applicare la regola del gruppo di sicurezza di rete a più macchine virtuali per consentire l'accesso in uscita. Questo video illustra la procedura descritta.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Instradare il traffico di backup attraverso un proxy

È possibile instradare il traffico di backup attraverso un proxy e quindi concedere al proxy l'accesso agli intervalli di Azure necessari. Configurare il proxy di macchina virtuale per consentire quanto segue:

- La macchina virtuale di Azure deve instradare tutto il traffico HTTP diretto alla rete Internet pubblica attraverso il proxy.
- Il proxy deve consentire il traffico in ingresso dalle macchine virtuali nella rete virtuale applicabile.
- Il gruppo di sicurezza di rete **NSF-lockdown** necessita di una regola che consenta il traffico Internet in uscita dalla macchina virtuale del proxy.

###### <a name="set-up-the-proxy"></a>Configurare il proxy

Se non si ha un proxy di account di sistema, configurarne uno come indicato di seguito:

1. Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Eseguire **PsExec.exe -i -s cmd.exe** per eseguire il prompt dei comandi con un account di sistema.
3. Eseguire il browser nel contesto di sistema. Ad esempio, usare **%PROGRAMFILES%\Internet Explorer\iexplore.exe** per Internet Explorer.  
4. Definire le impostazioni del proxy.
   - Nei computer Linux:
     - Aggiungere questa riga nel file **/etc/environment**:
       - **http_proxy = http:\//proxy IP: proxy indirizzo porta**
     - Aggiungere queste righe nel file **/etc/waagent.conf**:
         - **HttpProxy.Host=proxy IP address**
         - **Porta HttpProxy.Port=proxy**
   - Nelle impostazioni del browser dei computer Windows specificare che deve essere usato un proxy. Se attualmente si usa un proxy per un account utente, è possibile usare questo script per applicare l'impostazione a livello di account di sistema.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Consentire le connessioni in ingresso sul proxy

Consentire le connessioni in ingresso nelle impostazioni del proxy.

1. In Windows Firewall, aprire **Windows Firewall con sicurezza avanzata**.
2. Fare clic con il pulsante destro del mouse su **Regole in entrata** > **Nuova regola**.
3. Nelle **tipo di regola**, selezionare **Custom** > **Avanti**.
4. In **Programma** selezionare **Tutti i programmi** > **Avanti**.
5. Nelle **protocolli e porte**:
   - Impostare il tipo su **TCP**.
   - Impostare **porte locali** al **porte specifiche**.
   - Impostare **porta remota** al **tutte le porte**.
  
6. Completare la procedura guidata e specificare un nome per la regola.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Aggiungere una regola al gruppo di sicurezza di rete per il proxy

Nel gruppo di sicurezza di rete **NSF-lockdown** consentire il traffico da qualsiasi porta su 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS).

Lo script PowerShell seguente contiene un esempio per consentire il traffico.
Invece di consentire in uscita a tutti gli indirizzi internet pubblici, è possibile specificare un intervallo di indirizzi IP (`-DestinationPortRange`), oppure usare il tag di servizio storage.region.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

### <a name="allow-firewall-access-by-using-an-fqdn-tag"></a>Consentire l'accesso di firewall tramite un tag di nome di dominio completo

È possibile impostare backup Firewall di Azure per consentire l'accesso in uscita per il traffico di rete di Backup di Azure.

- [Informazioni](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) sulla distribuzione di Firewall di Azure.
- [Informazioni](https://docs.microsoft.com/azure/firewall/fqdn-tags) sui tag FQDN.

## <a name="modify-storage-replication-settings"></a>Modificare le impostazioni di replica di archiviazione

Per impostazione predefinita, l'insieme di credenziali prevede l'[archiviazione con ridondanza geografica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). È consigliabile l'archiviazione con ridondanza geografica per il backup primario. È possibile usare [archiviazione con ridondanza locale (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per un'opzione più economica.

Modificare il tipo di replica di archiviazione come segue:

1. Nel portale, selezionare il nuovo insieme di credenziali. Sotto **le impostazioni**, selezionare **proprietà**.
2. Nelle **delle proprietà**, in **configurazione Backup**, selezionare **Update**.
3. Selezionare il tipo di replica di archiviazione e selezionare **salvare**.

![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

Individuare le macchine virtuali nella sottoscrizione e configurare il backup.

1. Nell'insieme di credenziali > **Overview**, selezionare **+ Backup**.

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Verranno visualizzati i riquadri **Backup** e **Obiettivo del backup**.

2. In **Obiettivo del backup** > **Posizione di esecuzione del carico di lavoro** selezionare **Azure**. In **Elementi di cui eseguire il backup** selezionare **Macchina virtuale** >  **OK**.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Questo passaggio registra l'estensione della VM nell'insieme di credenziali. Il riquadro **Obiettivo del backup** verrà chiuso e verrà visualizzato il riquadro **Criteri di backup**.

3. In **Criteri di backup** selezionare i criteri da associare all'insieme di credenziali. Selezionare **OK**.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.
    - Selezionare **Crea nuovo** per creare un criterio. [Altre informazioni](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) sulla definizione di un criterio.

    ![Riquadri Backup e Criteri di backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Nel **selezionare le macchine virtuali** riquadro, selezionare le macchine virtuali che useranno il criterio di backup specificato > **OK**.

   La macchina virtuale selezionata viene convalidata. È possibile selezionare solo macchine virtuali presenti nella stessa area dell'insieme di credenziali. Il backup delle macchine virtuali può essere eseguito in un solo insieme di credenziali.

   ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. In **Backup** selezionare **Abilita backup**.

   Questo passaggio distribuisce i criteri nell'insieme di credenziali e alle macchine virtuali. Inoltre, viene installato l'estensione di backup nell'agente di macchine Virtuali in esecuzione nella VM di Azure.
   
   Questo passaggio non consente di creare il punto di ripristino iniziale per la macchina virtuale.

   ![Pulsante Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo aver abilitato il backup:

- Un backup iniziale viene eseguito in base alla pianificazione del backup.
- Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno.

Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione. Il backup della macchina virtuale, tuttavia, viene eseguito anche se la macchina è spenta e non è possibile installare l'estensione. È noto come una macchina virtuale offline. In questo caso, il punto di ripristino sarà coerente con l'arresto anomalo del sistema.
    
> [!NOTE]
> Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Modificare manualmente i criteri di backup in base alle necessità.

## <a name="run-the-initial-backup"></a>Eseguire il backup iniziale

Il backup iniziale verrà eseguito in base alla pianificazione, a meno che l'utente non lo esegua immediatamente in modo manuale. Eseguirlo manualmente come indicato qui:

1. Nel menu dell'insieme di credenziali, selezionare **elementi di Backup**.
2. Nelle **elementi di Backup**, selezionare **macchina virtuale di Azure**.
3. Nel **elementi di Backup** , selezionare i puntini di sospensione (**...** ).
4. Selezionare **Esegui Backup ora**.
5. Nelle **Esegui Backup ora**, usare il controllo calendario per selezionare l'ultimo giorno che deve essere mantenuto il punto di ripristino > **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, creazione del backup iniziale potrebbe richiedere qualche minuto.



## <a name="next-steps"></a>Passaggi successivi

- Risolvere eventuali problemi relativi [Azure VM agents](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) oppure [backup delle macchine Virtuali di Azure](backup-azure-vms-troubleshoot.md).
- [Ripristinare](backup-azure-arm-restore-vms.md) macchine virtuali di Azure.

