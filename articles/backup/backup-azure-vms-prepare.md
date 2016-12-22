---
title: Preparazione dell&quot;ambiente per il backup di macchine virtuali di Azure | Microsoft Docs
description: Assicurarsi che l&quot;ambiente sia pronto per il backup di macchine virtuali in Azure
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: backup; eseguire il backup;
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: b5b18d063a5926ad4acb7d0aa3935978d0fedb8c
ms.openlocfilehash: dc7e2d041ee4e0aeb222578c2fec9525e24bb0d1


---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Preparare l'ambiente per il backup di macchine virtuali di Azure
> [!div class="op_single_selector"]
> * [Modello Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modello classico](backup-azure-vms-prepare.md)
>
>

Prima di eseguire il backup di una macchina virtuale (VM) di Azure, devono verificarsi tre condizioni.

* È necessario creare un insieme di credenziali di backup o identificare un insieme di credenziali di backup esistente *nella stessa area della VM*.
* Stabilire la connettività di rete tra gli indirizzi Internet pubblici di Azure e gli endpoint di archiviazione di Azure.
* Installare l'agente di VM sulla VM.

Se nell'ambiente esistono già queste condizioni, passare all'articolo [Eseguire il backup di macchine virtuali di Azure](backup-azure-vms.md). In caso contrario, continuare a leggere. Questo articolo illustra i passaggi per preparare l'ambiente per eseguire il backup di una VM di Azure.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitazioni durante il backup e il ripristino di una VM
> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). L'elenco seguente indica le limitazioni quando si distribuisce usando il modello classico.
>
>

* Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
* Il backup di macchine virtuali con un indirizzo IP riservato e nessun endpoint definito non è supportato.
* I dati di backup non includono le unità di rete montate collegate alla macchina virtuale.
* La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. È necessario eliminare prima la macchina virtuale esistente e gli eventuali dischi associati e quindi ripristinare i dati dal backup.
* L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
* Il backup di macchine virtuali tramite il servizio Backup di Azure è supportato in tutte le aree pubbliche di Azure (vedere l' [elenco di controllo](https://azure.microsoft.com/regions/#services) delle aree supportate). Se l'area che si sta cercando non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
* Il backup di macchine virtuali con il servizio Backup di Azure è supportato soltanto per versioni specifiche dei sistemi operativi:
  * **Linux**: Backup di Azure supporta [un elenco di distribuzioni approvate da Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , ad eccezione di CoreOS Linux. È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella macchina virtuale e sia configurato il supporto per Python.
  * **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.
* Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Altre informazioni sul [ripristino di un controller di dominio con più controller di dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Il ripristino delle macchine virtuali che presentano le seguenti configurazioni di rete speciali è supportato solo tramite PowerShell. Le macchine virtuali create usando il flusso di lavoro di ripristino nell'interfaccia utente non potranno avere queste configurazioni di rete al termine dell'operazione di ripristino. Per altre informazioni, vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Macchine virtuali con configurazione del servizio di bilanciamento del carico (interno ed esterno)
  * Macchine virtuali con più indirizzi IP riservati
  * Macchine virtuali con più schede di rete

## <a name="create-a-backup-vault-for-a-vm"></a>Creare un insieme di credenziali di backup per una VM
Un insieme di credenziali di backup è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali di backup contiene anche i criteri di backup che verranno applicati alle macchine virtuali di cui viene eseguito il backup.

Questa figura mostra le relazioni tra le diverse entità di Backup di Azure:     ![Entità e relazioni di Backup di Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Per creare un insieme di credenziali per il backup:

1. Accedere al [portale di Azure](http://manage.windowsazure.com/).
2. Nel portale di Azure fare clic su **Nuovo** > **Integrazione ibrida** > **Backup**. Quando si fa clic su **Backup**, si passa automaticamente al portale classico (visualizzato dopo la nota).

    ![Portale Ibiza](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

   > [!NOTE]
   > Se la sottoscrizione è stata usata l'ultima volta nel portale classico, può venire aperta nel portale classico. In questo caso, per creare un insieme di credenziali di backup fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali per il backup** > **Creazione rapida**. Vedere l'immagine seguente.
   >
   >

    ![Creare un insieme di credenziali per il backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)
3. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
4. In **Region**selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali deve trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere. Se si hanno macchine virtuali in più aree, è necessario creare un insieme di credenziali di backup in ogni area. Per archiviare i dati di backup, non è necessario specificare account di archiviazione perché l'insieme di credenziali per il backup e il servizio Backup di Azure gestiscono questa operazione in modo automatico.
5. Nel campo **Sottoscrizione** selezionare la sottoscrizione che si vuole associare all'insieme di credenziali di backup. Sono presenti scelte multiple solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.
6. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-azure-vms-prepare/creating-vault.png)
7. La creazione corretta dell'insieme di credenziali verrà confermata da un messaggio. L'insieme verrà elencato come **Attivo** nella pagina **Servizi di ripristino**. Subito dopo la creazione dell'insieme di credenziali, assicurarsi di scegliere l'opzione di ridondanza dell'archiviazione corretta. Leggere l'articolo relativo all'[impostazione dell'opzione di ridondanza nell'insieme di credenziali per il backup](backup-configure-vault.md#step-1-create-a-recovery-services-vault).

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)
8. Fare clic sull'insieme di credenziali per il backup per visualizzare la pagina **Avvio rapido** in cui sono riportate le istruzioni per il backup delle macchine virtuali di Azure.

    ![Istruzioni per il backup delle macchine virtuali nella pagina Dashboard](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## <a name="network-connectivity"></a>Connettività di rete
Per gestire gli snapshot di una macchina virtuale, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. Senza la connettività Internet appropriata, si verificherà il timeout delle richieste HTTP della macchina virtuale e l'operazione di backup non verrà eseguita. Se la distribuzione ha restrizioni di accesso, ad esempio, un gruppo di sicurezza di rete (NSG), scegliere una delle opzioni seguenti per specificare un percorso chiaro per il traffico di backup:

* [Microsoft Azure Datacenter IP Ranges](http://www.microsoft.com/en-us/download/details.aspx?id=41653) (Intervalli di indirizzi IP dei data center di Azure): leggere l'articolo per istruzioni su come includere gli indirizzi IP nell'elenco degli elementi consentiti.
* Distribuire un server proxy HTTP per eseguire il routing del traffico

Quando si decide quale opzione usare, i compromessi sono compresi tra facilità di gestione, controllo granulare e costo.

| Opzione | Vantaggi | Svantaggi: |
| --- | --- | --- |
| Aggiungere gli intervalli IP all'elenco elementi consentiti  |Senza costi aggiuntivi.<br><br>Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet <i>Set-AzureNetworkSecurityRule</i>. |Complessità di gestione perché gli intervalli IP interessati variano nel tempo.<br><br>Offre l'accesso a tutto l'ambiente di Azure, non solo al servizio di Archiviazione. |
| Proxy HTTP |È consentito il controllo granulare nel proxy sugli URL di archiviazione.<br>Singolo punto di accesso Internet alle macchine virtuali.<br>Non è soggetto alle modifiche degli indirizzi IP di Azure. |Costi aggiuntivi per l'esecuzione di una VM con il software proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Microsoft Azure Datacenter IP Ranges 
Per aggiungere gli intervalli IP dei data center di Azure all'elenco degli elementi consentiti, vedere il [sito Web di Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) per informazioni dettagliate sugli intervalli IP e per istruzioni.

### <a name="using-an-http-proxy-for-vm-backups"></a>Uso di un proxy HTTP per i backup delle VM
Quando si esegue il backup di una macchina virtuale, l'estensione di backup nella VM invia i comandi di gestione degli snapshot ad Archiviazione di Azure usando un'API HTTPS. Eseguire il routing del traffico di estensione del backup tramite il proxy HTTP perché è l'unico componente configurato per l'accesso a Internet pubblico.

> [!NOTE]
> Non esiste alcuna raccomandazione per il proxy da usare. Assicurarsi di scegliere un proxy compatibile con i passaggi di configurazione descritti di seguito.
>
>

L'immagine dell'esempio seguente visualizza i tre passaggi necessari per usare un proxy HTTP:

* La VM dell'app esegue il routing del traffico HTTP diretto alla rete Internet pubblica attraverso la VM del proxy.
* La VM del Proxy consente il traffico in entrata da macchine virtuali nella rete virtuale.
* Il gruppo di sicurezza di rete (NSG) denominato blocco NSF ha bisogno di una regola di sicurezza che consenta il traffico Internet in uscita dalla VM Proxy.

![Diagramma della distribuzione gruppo di sicurezza di rete con proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Per usare un proxy HTTP per la comunicazione con la rete Internet pubblica, attenersi alla procedura seguente:

#### <a name="step-1-configure-outgoing-network-connections"></a>Passaggio 1. Configurare le connessioni di rete in uscita
###### <a name="for-windows-machines"></a>Per macchine Windows
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

> [!NOTE]
> Se si nota il messaggio "(407) Autenticazione proxy obbligatoria" nel registro del server proxy, verificare che l'autenticazione sia impostata correttamente.
>
>

###### <a name="for-linux-machines"></a>Per macchine Linux
Aggiungere la riga seguente al file ```/etc/environment``` :

```
http_proxy=http://<proxy IP>:<proxy port>
```

Aggiungere le righe seguenti al file ```/etc/waagent.conf``` :

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Passaggio 2. Consentire le connessioni in entrata nel server proxy:
1. Aprire Windows Firewall nel server proxy. Il modo più semplice per accedere al firewall consiste nel cercare Windows Firewall con Sicurezza avanzata.

    ![Aprire il firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. Nella finestra di dialogo Windows Firewall fare clic con il pulsante destro del mouse su **Regole in ingresso** e scegliere **Nuova regola**.

    ![Creare una nuova regola](./media/backup-azure-vms-prepare/firewall-02.png)
3. In **Creazione guidata nuova regola connessioni in entrata** scegliere l'opzione **Personalizzata** per **Tipo di regola** e fare clic su **Avanti**.
4. Nella schermata di selezione del **Programma** scegliere **Tutti i programmi** e fare clic su **Avanti**.
5. Nella pagina **Protocollo e porte** specificare le informazioni seguenti e fare clic su **Avanti**:

    ![Creare una nuova regola](./media/backup-azure-vms-prepare/firewall-03.png)

   * Nel campo *Tipo di protocollo* scegliere *TCP*.
   * Nel campo *Porta locale* scegliere *Porte specifiche* e nel campo sottostante specificare la ```<Proxy Port>``` configurata.
   * Nel campo *Porta remota* selezionare *Tutte le porte*.

     Completare la procedura guidata facendo clic su Avanti fino al termine e assegnare un nome a questa regola.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Passaggio 3. Aggiungere una regola di eccezione al gruppo di sicurezza di rete (NSG):
In un prompt dei comandi di Azure PowerShell digitare il comando seguente:

Il comando aggiunge un'eccezione per il gruppo di sicurezza di rete (NSG). Questa eccezione consente il traffico TCP da qualsiasi porta da 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS). Se è necessario usare una porta specifica nell'area Internet pubblica, assicurarsi di aggiungerla anche a ```-DestinationPortRange``` .

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Assicurarsi di sostituire i nomi nell'esempio con quelli appropriati alla distribuzione in uso.*

## <a name="vm-agent"></a>Agente di macchine virtuali
Prima di eseguire il backup della macchina virtuale di Azure, è consigliabile assicurarsi che l'agente di macchine virtuali di Azure (agente VM) sia installato correttamente nella macchina virtuale. Poiché al momento della creazione della macchina virtuale l'agente VM è un componente opzionale, assicurarsi che la relativa casella di controllo sia selezionata prima di eseguire il provisioning della macchina virtuale.

### <a name="manual-installation-and-update"></a>Installazione e aggiornamento manuali
L'agente VM è già presente nelle VM create dalla raccolta di Azure. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali non è installato l'agente VM. Per queste VM è necessario installare esplicitamente l'agente VM. Altre informazioni sull' [installazione dell'agente di VM in una VM esistente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installazione dell'agente di VM |<li>Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore. <li>[Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. |<li> Installare l' [agente Linux](https://github.com/Azure/WALinuxAgent) più recente da GitHub. Per completare l'installazione sono necessari privilegi di amministratore. <li> [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. |
| Aggiornamento dell'agente di VM |L'aggiornamento dell'agente VM è semplice quanto la reinstallazione dei [file binari dell'agente VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |Seguire le istruzioni sull'[aggiornamento dell'agente di VM Linux ](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |
| Convalida dell'installazione dell'agente di VM |<li>Passare alla cartella *C:\WindowsAzure\Packages* nella VM di Azure, <li>che dovrebbe includere il file WaAppAgent.exe.<li> Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore. |N/D |

Per altre informazioni, vedere gli articoli sull'[agente di macchine virtuale](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e sulla relativa [installazione](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Estensione di backup
Per eseguire il backup della macchina virtuale, il servizio Backup di Azure installa un'estensione nell'agente di macchine virtuali. Il servizio Backup di Azure applica aggiornamenti e patch all'estensione di backup senza ulteriore intervento dell'utente.

L'estensione per il backup viene installata se la macchina virtuale è in esecuzione. Una macchina virtuale in esecuzione consente anche di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup di Azure continuerà tuttavia a eseguire il backup della macchina virtuale, anche se questa è spenta e non è stato possibile installare l'estensione (macchina virtuale offline). In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo* , come indicato in precedenza.

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato preparato l'ambiente per il backup della VM, il passaggio logico successivo consiste nel creare un backup. L'articolo sulla pianificazione offre informazioni più dettagliate sul backup delle VM.

* [Eseguire il backup di macchine virtuali](backup-azure-vms.md)
* [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
* [Gestire backup di macchine virtuali](backup-azure-manage-vms.md)



<!--HONumber=Nov16_HO5-->


