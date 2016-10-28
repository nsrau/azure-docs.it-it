<properties
	pageTitle="Preparazione dell’ambiente per il backup di macchine virtuali distribuite con Resource Manager | Microsoft Azure"
	description="Assicurarsi che l'ambiente sia pronto per il backup di macchine virtuali in Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="cfreeman"
	editor=""
	keywords="backup; eseguire il backup;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Preparare l’ambiente per il backup di macchine virtuali distribuite con Resource Manager

> [AZURE.SELECTOR]
- [Modello di Resource Manager](backup-azure-arm-vms-prepare.md)
- [Modello classico](backup-azure-vms-prepare.md)

Questo articolo illustra i passaggi per preparare un ambiente in cui eseguire il backup di una macchina virtuale (VM) distribuita con Resource Manager. I passaggi descritti nelle procedure usano il portale di Azure.

Il servizio Backup di Azure offre due tipi di insiemi di credenziali (insieme di credenziali di backup e insiemi di credenziali dei servizi di ripristino) per proteggere le macchine virtuali. L'insieme di credenziali di backup protegge le macchine virtuali distribuite usando il modello di distribuzione classico. L'insieme di credenziali dei servizi di ripristino protegge **le macchine virtuali distribuite sia con il modello di distribuzione classica sia con il modello di distribuzione Resource Manager**. È necessario usare un insieme di credenziali dei servizi di ripristino per proteggere una macchina virtuale distribuita con Resource Manager.

>[AZURE.NOTE] Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../resource-manager-deployment-model.md). Per informazioni dettagliate sull'utilizzo delle macchine virtuali con il modello di distribuzione classica, vedere [Preparare l'ambiente per il backup di macchine virtuali di Azure](backup-azure-vms-prepare.md).

Prima di proteggere o eseguire il backup di una macchina virtuale (VM) di Resource Manager, verificare che siano disponibili i prerequisiti seguenti:

- Creare un insieme di credenziali dei servizi di ripristino o identificarne uno esistente *nella stessa località della macchina virtuale*.
- Selezionare uno scenario, definire i criteri di backup e specificare gli elementi da proteggere.
- Verificare l'installazione dell'agente di macchine virtuali nella macchina virtuale.
- Verificare la connettività della rete

Se nell'ambiente esistono già queste condizioni, passare all'articolo [Eseguire il backup di macchine virtuali di Azure](backup-azure-vms.md). Se si vuole impostare o verificare qualsiasi prerequisito, questo articolo spiega la procedura di preparazione dei prerequisiti.


## Limitazioni durante il backup e il ripristino di una VM

Prima di preparare l'ambiente, è importante capire le limitazioni.

- Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
- Il backup di macchine virtuali con un indirizzo IP riservato e nessun endpoint definito non è supportato.
- Il backup delle macchine virtuali Linux con estensione Docker non è supportato.
- I dati di backup non includono le unità di rete montate collegate alla macchina virtuale.
- La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. Se si tenta di ripristinare una macchina virtuale che esiste, l'operazione di ripristino non viene eseguita.
- L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
- È possibile eseguire il backup di macchine virtuali in tutte le aree pubbliche di Azure. Vedere l'[elenco di controllo](https://azure.microsoft.com/regions/#services) delle aree supportate. Se l'area che si sta cercando non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
- È possibile eseguire il backup di macchine virtuali solo per specifiche versioni dei sistemi operativi seguenti:
  - **Linux**: Backup di Azure supporta [un elenco di distribuzioni approvate da Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md), ad eccezione di CoreOS Linux. È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella macchina virtuale e sia configurato il supporto per Python.
  - **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.
- Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Altre informazioni sul [ripristino di un controller di dominio con più controller di dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms).
- Il ripristino delle macchine virtuali che presentano le seguenti configurazioni di rete speciali è supportato solo tramite PowerShell. Le macchine virtuali create usando il flusso di lavoro di ripristino nell'interfaccia utente non avranno queste configurazioni di rete dopo l'operazione di ripristino. Per altre informazioni, vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
  - Macchine virtuali con configurazione del servizio di bilanciamento del carico (interno ed esterno)
  - Macchine virtuali con più indirizzi IP riservati
  - Macchine virtuali con più schede di rete

## Creare l'insieme di credenziali dei servizi di ripristino per una macchina virtuale

Un insieme di credenziali dei servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino si di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere **Esplora** dal menu Hub e digitare **Servizi di ripristino** nell'elenco di risorse. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insieme di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino.

3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    Si apre il pannello degli insiemi di credenziali dei servizi di ripristino in cui viene chiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)

4. Nel campo **Nome** digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Fare clic su **Sottoscrizione** per visualizzare l'elenco di sottoscrizioni disponibili. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono presenti scelte multiple solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

6. Fare clic su **Gruppo di risorse** per visualizzare l'elenco dei gruppi di risorse disponibili oppure fare clic su **Nuovo** per crearne uno nuovo. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md)

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali **deve** trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere.

    >[AZURE.IMPORTANT] Se si non è certi della località della macchina virtuale, chiudere la finestra di dialogo di creazione dell'insieme di credenziali e passare all'elenco di macchine virtuali nel portale. Se si hanno macchine virtuali in più aree, sarà necessario creare un insieme di credenziali dei servizi di ripristino backup in ogni area. Creare l'insieme di credenziali nella prima località prima di passare a quella successiva. Per archiviare i dati di backup, non è necessario specificare account di archiviazione perché l'insieme di credenziali dei servizi di ripristino e il servizio Backup di Azure gestiscono questa operazione in modo automatico.

8. Fare clic su **Crea**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. L'insieme di credenziali, dopo essere stato creato, viene visualizzato negli insiemi di credenziali di Servizi di ripristino.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

    Ora che l'insieme di credenziali è stato creato, è possibile apprendere come impostare la replica di archiviazione.

## Impostare la replica di archiviazione

L'opzione della replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con [ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e con [ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), vedere la panoramica in [Replica di archiviazione di Azure](../storage/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

1. Selezionare l'insieme di credenziali per aprire il dashboard dell'insieme di credenziali e il pannello Impostazioni. Se il pannello **Impostazioni** non si apre, fare clic su **Tutte le impostazioni** nel dashboard dell'insieme di credenziali.

2. Nel pannello **Impostazioni** fare clic su **Infrastruttura di backup** > **Configurazione backup** per aprire il pannello **Configurazione backup**. Nel pannello **Configurazione backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la macchina virtuale all'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.


## Selezionare un obiettivo di backup, impostare i criteri e definire gli elementi da proteggere

Prima di registrare una macchina virtuale in un insieme di credenziali, eseguire il processo di individuazione per verificare che vengano identificate le eventuali nuove macchine virtuali aggiunte alla sottoscrizione. Il processo esegue una query su Azure per ottenere l'elenco delle macchine virtuali disponibili nella sottoscrizione, insieme ad altre informazioni come il nome del servizio cloud e l'area. Nel portale di Azure lo scenario fa riferimento a ciò che si inserirà nell'insieme di credenziali dei servizi di ripristino. I criteri determinano la pianificazione relativa alla frequenza e al momento in cui acquisiti i punti di ripristino. I criteri includono anche il periodo di mantenimento dati per i punti di ripristino.

1. Se un insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio 2. Se non è aperto alcun insieme di credenziali di Servizi di ripristino, ma ci si trova nel portale di Azure, scegliere **Sfoglia** dal menu Hub.

  - Nell'elenco di risorse digitare **Servizi di ripristino**.
  - Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzata l'opzione, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
  - Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

    Viene aperto il dashboard dell'insieme di credenziali selezionato.

    ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-vms-first-look-arm/vault-settings.png)

2. Scegliere **Backup** dal menu del dashboard dell'insieme di credenziali per aprire il pannello Backup.

    ![Pannello Backup aperto](./media/backup-azure-vms-first-look-arm/backup-button.png)

    Quando si apre il pannello, il servizio Backup cerca le nuove macchine virtuali nella sottoscrizione.

    ![Individuare le VM](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)

3. Nel pannello Backup fare clic su **Obiettivo del backup** per aprire il pannello corrispondente.

    ![Pannello Scenario aperto](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)

4. Nel pannello Obiettivo del backup impostare **Posizione di esecuzione del carico di lavoro** su Azure ed **Elementi di cui eseguire il backup** su Macchina virtuale, quindi fare clic su **OK**.

    Il pannello Obiettivo di backup si chiude e viene visualizzato il pannello Criterio di backup.

    ![Pannello Scenario aperto](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)

5. Nel pannello Criterio di backup selezionare il criterio di backup che si vuole applicare all'insieme di credenziali e fare clic su **OK**.

    ![Selezionare il criterio di backup](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    I dettagli dei criteri predefiniti vengono elencati nei dettagli. Per creare un nuovo criterio, selezionare **Crea nuovo** dal menu a discesa. Il menu a discesa contiene anche un'opzione per impostare l'orario in cui lo snapshot viene acquisito sulle 19. Per istruzioni sulla definizione di un criterio di backup, vedere [Definizione di un criterio di backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Dopo aver fatto clic su **OK**, il criterio di backup viene associato all'insieme di credenziali.

    Scegliere quindi le VM da associare all'insieme di credenziali.

6. Scegliere le macchine virtuali da associare al criterio specificato e fare clic su **Seleziona**.

    ![Selezionare il carico di lavoro](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    Se la VM desiderata non è visibile, controllare che esista nella stessa località di Azure dell'insieme di credenziali dei servizi di ripristino.

7. Dopo aver definito tutte le impostazioni per l'insieme di credenziali, nel pannello Backup fare clic su **Abilita backup** nella parte inferiore della pagina. Il criterio verrà distribuito nell'insieme di credenziali e nelle VM.

    ![Abilitare il backup](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

La fase successiva di preparazione è l'installazione dell'agente di macchine virtuali o la verifica che l'agente di macchine virtuali sia installato.


## Installare l'agente di macchine virtuali nella macchina virtuale

Per il funzionamento dell'estensione di backup, l'agente di macchine virtuali deve essere installato nella macchina virtuale di Azure. Se la macchina virtuale è stata creata dalla raccolta di Azure, l'agente di macchine virtuali è già installato. Queste informazioni riguardano le situazioni in cui *non* si usa una macchina virtuale creata dalla raccolta di Azure, ad esempio una macchina virtuale di cui è stata eseguita la migrazione da un data center locale. In tal caso, è necessario installare l'agente di macchine virtuali per proteggerla.

Per altre informazioni, leggere gli articoli relativi all'[agente di macchine virtuali](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e all'[installazione dell'agente di macchine virtuali](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

In caso di problemi con il backup della macchina virtuale di Azure, assicurarsi che l'agente di macchine virtuali di Azure sia installato correttamente nella macchina virtuale. Vedere in proposito la tabella seguente. Se si è creata una macchina virtuale personalizzata, [assicurarsi che la casella di controllo **Installa l'agente di macchine virtuali** sia selezionata](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) prima che ne venga effettuato il provisioning.

La tabella seguente fornisce informazioni aggiuntive sull'agente di macchine virtuali per macchine virtuali Windows e Linux.

| **Operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installazione dell'agente di macchine virtuali | <li>Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore. <li>[Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. | <li> Installare l'[agente Linux](https://github.com/Azure/WALinuxAgent) più recente da GitHub. Per completare l'installazione sono necessari privilegi di amministratore. <li> [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. |
| Aggiornamento dell'agente di VM | L'aggiornamento dell'agente di VM è semplice quanto la reinstallazione dei [file binari dell'agente di VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente di VM. | Seguire le istruzioni sull'[aggiornamento dell'agente di VM Linux](../virtual-machines-linux-update-agent.md). <br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente di VM. |
| Convalida dell'installazione dell'agente di macchine virtuali | <li>Passare alla cartella *C:\\WindowsAzure\\Packages* nella macchina virtuale di Azure. <li>La cartella dovrebbe includere il file WaAppAgent.exe.<li> Fare clic con il pulsante destro del mouse sul file, passare a **Proprietà** e quindi scegliere la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore. | N/D |


### Estensione di backup

Dopo aver installato l'agente di macchine virtuali nella macchina virtuale, il servizio Backup di Azure installa l'estensione di backup nell'agente di macchine virtuali. Il servizio Backup di Azure si aggiorna automaticamente e applica patch all'estensione di backup.

L'estensione di backup viene installata dal servizio Backup indipendentemente dal fatto che la macchina virtuale sia in esecuzione o meno. Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup di Azure continua tuttavia a eseguire il backup della macchina virtuale, anche se questa è spenta e non è stato possibile installare l'estensione. Questa situazione è detta macchina virtuale offline. In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*.


## Connettività di rete

Per gestire gli snapshot di una macchina virtuale, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. Senza la connettività Internet appropriata, si verificherà il timeout delle richieste HTTP della macchina virtuale e l'operazione di backup non verrà eseguita. Se la distribuzione ha restrizioni di accesso, ad esempio, un gruppo di sicurezza di rete (NSG), scegliere una delle opzioni seguenti per specificare un percorso chiaro per il traffico di backup:

- [Microsoft Azure Datacenter IP Ranges ](http://www.microsoft.com/it-IT/download/details.aspx?id=41653) (Intervalli di indirizzi IP dei data center di Azure): leggere l'articolo per istruzioni su come includere gli indirizzi IP nell'elenco degli elementi consentiti.
- Distribuire un server proxy HTTP per eseguire il routing del traffico

Quando si decide quale opzione usare, i compromessi sono compresi tra facilità di gestione, controllo granulare e costo.

|Opzione|Vantaggi|Svantaggi:|
|------|----------|-------------|
|Aggiungere gli intervalli IP all'elenco elementi consentiti| Nessun costo aggiuntivo. <br><br>Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet <i>Set-AzureNetworkSecurityRule</i>. | Complessità di gestione perché gli intervalli IP interessati variano nel tempo.<br><br>Offre l'accesso a tutto l'ambiente di Azure, non solo al servizio di Archiviazione.|
|Proxy HTTP| È consentito il controllo granulare nel proxy sugli URL di archiviazione.<br>Singolo punto di accesso a Internet alle VM.<br>Non è soggetto alle modifiche degli indirizzi IP di Azure.| Costi aggiuntivi per l'esecuzione di una VM con il software proxy.|

### Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti.

Per aggiungere gli intervalli IP dei data center di Azure all'elenco degli elementi consentiti, vedere il [sito Web di Azure](http://www.microsoft.com/it-IT/download/details.aspx?id=41653) per informazioni dettagliate sugli intervalli IP e per istruzioni.

### Uso di un proxy HTTP per i backup delle VM
Quando si esegue il backup di una macchina virtuale, l'estensione di backup nella VM invia i comandi di gestione degli snapshot ad Archiviazione di Azure usando un'API HTTPS. Eseguire il routing del traffico di estensione del backup tramite il proxy HTTP perché è l'unico componente configurato per l'accesso a Internet pubblico.

>[AZURE.NOTE] Non esiste alcuna raccomandazione per il proxy da usare. Assicurarsi di scegliere un proxy compatibile con i passaggi di configurazione descritti di seguito.

L'immagine dell'esempio seguente visualizza i tre passaggi necessari per usare un proxy HTTP:

- La VM dell'app esegue il routing del traffico HTTP diretto alla rete Internet pubblica attraverso la VM del proxy.
- La VM del Proxy consente il traffico in entrata da macchine virtuali nella rete virtuale.
- Il gruppo di sicurezza di rete (NSG) denominato blocco NSF ha bisogno di una regola di sicurezza che consenta il traffico Internet in uscita dalla VM Proxy.

![Diagramma della distribuzione gruppo di sicurezza di rete con proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Per usare un proxy HTTP per la comunicazione con la rete Internet pubblica, attenersi alla procedura seguente:

#### Passaggio 1. Configurare le connessioni di rete in uscita

###### Per macchine Windows
Questa procedura consente di impostare la configurazione del server proxy per l'account del sistema locale.

1. Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Eseguire il comando seguente dal prompt dei comandi con privilegi elevati,

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Si apre la finestra di Explorer.
3. Scegliere Strumenti -> Opzioni Internet -> Connessioni -> Impostazioni LAN.
4. Verificare le impostazioni del proxy per l'account di sistema. Impostare l'IP del Proxy e la porta.
5. Chiudere Internet Explorer.

Questo comando esegue una configurazione proxy a livello di computer e verrà usato per tutto il traffico HTTP/HTTPS in uscita.

Se è stato installato un server proxy in un account utente corrente (non in un account di sistema locale), usare lo script seguente per applicare le impostazioni a SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

>[AZURE.NOTE] Se si nota il messaggio "(407) Autenticazione proxy obbligatoria" nel log del server proxy, verificare che l'autenticazione sia impostata correttamente.

######Per macchine Linux

Aggiungere la riga seguente al file ```/etc/environment```:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Aggiungere le righe seguenti al file ```/etc/waagent.conf```:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### Passaggio 2. Consentire le connessioni in entrata nel server proxy:

1. Aprire Windows Firewall nel server proxy. Il modo più semplice per accedere al firewall consiste nel cercare Windows Firewall con Sicurezza avanzata.

    ![Aprire il firewall](./media/backup-azure-vms-prepare/firewall-01.png)

2. Nella finestra di dialogo Windows Firewall fare clic con il pulsante destro del mouse su **Regole in ingresso** e scegliere **Nuova regola**.

    ![Creare una nuova regola](./media/backup-azure-vms-prepare/firewall-02.png)

3. In **Creazione guidata nuova regola connessioni in entrata** scegliere l'opzione **Personalizzata** per **Tipo di regola** e fare clic su **Avanti**.
4. Nella schermata di selezione del **Programma** scegliere **Tutti i programmi** e fare clic su **Avanti**.

5. Nella pagina **Protocollo e porte** specificare le informazioni seguenti e fare clic su **Avanti**:

    ![Creare una nuova regola](./media/backup-azure-vms-prepare/firewall-03.png)

    - Nel campo *Tipo di protocollo* scegliere *TCP*.
    - Nel campo *Porta locale* scegliere *Porte specifiche* e nel campo sottostante specificare la ```<Proxy Port>``` configurata.
    - Nel campo *Porta remota* selezionare *Tutte le porte*.

    Completare la procedura guidata facendo clic su Avanti fino al termine e assegnare un nome a questa regola.

#### Passaggio 3. Aggiungere una regola di eccezione al gruppo di sicurezza di rete (NSG):

In un prompt dei comandi di Azure PowerShell digitare il comando seguente:

Il comando aggiunge un'eccezione per il gruppo di sicurezza di rete (NSG). Questa eccezione consente il traffico TCP da qualsiasi porta da 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS). Se è necessario usare una porta specifica nell'area Internet pubblica, assicurarsi di aggiungerla anche a ```-DestinationPortRange```.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*Questi passaggi includono nomi e valori di esempio. Usare nomi e valori appropriati alla distribuzione specifica quando si aggiungono, tagliano e incollano i dettagli nel codice.*


Dopo aver stabilito la connettività di rete, è possibile eseguire il backup di una macchina virtuale. Vedere [Eseguire il backup di macchine virtuali distribuite con il modello di distribuzione Resource Manager](backup-azure-arm-vms.md).

## Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi
Ora che è stato preparato l'ambiente per il backup della VM, il passaggio logico successivo consiste nel creare un backup. L'articolo sulla pianificazione offre informazioni più dettagliate sul backup delle VM.

- [Eseguire il backup di macchine virtuali](backup-azure-vms.md)
- [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
- [Gestire backup di macchine virtuali](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0831_2016-->