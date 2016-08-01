Questo articolo illustra un insieme di procedure consolidate per l'esecuzione di una macchina virtuale (VM) Windows in Azure, con particolare attenzione agli aspetti di scalabilità, disponibilità, gestibilità e sicurezza.

> [AZURE.NOTE] Azure offre due diversi modelli di distribuzione, ovvero [Azure Resource Manager][resource-manager-overview] e la distribuzione classica. Questo articolo usa Azure Resource Manager, consigliato da Microsoft per le nuove distribuzioni.

Non è consigliabile utilizzare una singola VM per carichi di lavoro di produzione, poiché non esiste alcun contratto di servizio che disciplini il tempo di attività delle singole VM in Azure. Per ottenere il contratto di servizio, è necessario distribuire più VM in un set di disponibilità. Per ulteriori informazioni, vedere l'articolo sull'[esecuzione di più VM Windows in Azure][multi-vm].

## Diagramma dell'architettura

Il provisioning di una VM in Azure coinvolge altri componenti mobili oltre alla VM in sé. Sono presenti elementi di calcolo, rete e archiviazione.

![[0]][0]

- **Gruppo di risorse.** Un [gruppo di risorse][resource-manager-overview] è un contenitore in cui risiedono le risorse correlate. Creare un gruppo di risorse in cui includere le risorse per questa VM.

- **VM**. È possibile effettuare il provisioning di una VM da un elenco di immagini pubblicate oppure da un file VHD caricato nell'archivio BLOB di Azure.

- **Disco del sistema operativo.** Il disco del sistema operativo è un VHD archiviato nell'[Archiviazione di Azure][azure-storage]. Viene quindi salvato in modo permanente anche se il computer host si arresta.

- **Disco temporaneo.** La VM viene creata con un disco temporaneo, ovvero l'unità `D:` in Windows. Questo disco viene archiviato in un'unità fisica nel computer host. _Non_ viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della VM. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio.

- **Dischi dati.** Un [disco dati][data-disk] è un VHD persistente usato per i dati dell'applicazione. I dischi dati vengono archiviati nell'Archiviazione di Azure, come il disco del sistema operativo.

- **Rete virtuale (VNet) e subnet.** Ogni VM in Azure viene distribuita in una rete virtuale (VNet), che viene a sua volta suddivisa in subnet.

- **Indirizzo IP pubblico.** Un indirizzo IP pubblico è necessario per comunicare con la macchina virtuale, ad esempio tramite Desktop remoto.

- **Interfaccia di rete (NIC)**. La scheda di interfaccia di rete consente alla VM di comunicare con la rete virtuale.

- **Gruppo di sicurezza di rete (NSG)**. Il [gruppo di sicurezza di rete][nsg] viene usato per consentire/negare il traffico di rete verso la subnet. Un NSG può essere associato a una singola scheda di interfaccia di rete o a una subnet. Se lo si associa a una subnet, le regole dell’NSG si applicano a tutte le VM nella subnet.
 
- **Diagnostica.** La registrazione diagnostica è essenziale per la gestione e la risoluzione dei problemi della macchina virtuale.

## Consigli

### Indicazioni per le VM

- È consigliabile usare le serie GS e DS, a meno che non si disponga di un carico di lavoro specializzato, ad esempio di high-performance computing. Per informazioni dettagliate, vedere l'articolo sulle [dimensioni delle macchine virtuali][virtual-machine-sizes]. Quando si sposta un carico di lavoro esistente in Azure, per iniziare scegliere le dimensioni di VM più simili a quelle dei server locali. Dopodiché, misurare le prestazioni del carico di lavoro effettivo in relazione agli aspetti di CPU, memoria e IOPS del disco e regolare le dimensioni secondo necessità. Inoltre, se sono necessarie più schede di interfaccia di rete, tenerne presente il limite per ogni dimensione.

- Quando si effettua il provisioning della VM e di altre risorse, è necessario specificare una località. È in genere consigliabile scegliere la località più vicina agli utenti interni o ai clienti. È tuttavia possibile che le dimensioni di VM non siano tutte disponibili in tutte le località. Per informazioni dettagliate, vedere l’articolo sui [servizi in base alle aree][services-by-region]. Per elencare le dimensioni delle VM disponibili in una determinata posizione, eseguire il seguente comando di Azure:

    ```
    azure vm sizes --location <location>
    ```

- Per informazioni sulla scelta di un'immagine di VM pubblicata, vedere l'articolo su come [individuare e selezionare immagini di macchine virtuali di Azure][select-vm-image].

### Indicazioni per il disco e l'archiviazione

- Per ottimizzare le prestazioni I/O del disco, si consiglia di utilizzare [Archiviazione Premium][premium-storage], che archivia i dati in unità SSD (Solid State Drive). I costi dipendono dalle dimensioni del disco sottoposto a provisioning. Anche IOPS e velocità effettiva, ad esempio la velocità di trasferimento dati, dipendono dalle dimensioni del disco. Quando si effettua il provisioning di un disco è quindi consigliabile tenere in considerazione tutti e tre i fattori, ovvero capacità, IOPS e velocità effettiva.

- Aggiungere uno o più dischi dati. Quando si crea un nuovo VHD, il disco non è formattato. Accedere alla VM per formattare il disco.

- Se sono presenti molti dischi dati, occorre prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere l'articolo sui [limiti relativi ai dischi delle macchine virtuali][vm-disk-limits].

- Per prestazioni ottimali, creare un account di archiviazione separato per i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

- Quando possibile, installare applicazioni su un disco dati, anziché sul disco del sistema operativo. Tuttavia, per alcune applicazioni legacy potrebbe essere necessario installare componenti nell'unità C:. In tal caso, è possibile [ridimensionare il disco del sistema operativo][resize-os-disk] tramite PowerShell.

### Indicazioni per la rete

- L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

    - Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subirà modifiche, ad esempio se è necessario creare un record A in DNS o se è necessario che l'indirizzo IP sia consentito.

    - È inoltre possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME][cname-record] in DNS che punta al nome FQDN. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn].

- Tutti i gruppi di sicurezza di rete contengono un set di [regole predefinite][nsg-default-rules], inclusa una regola che blocca tutto il traffico Internet in ingresso. Le regole predefinite non possono essere eliminate, ma altre regole possono eseguirne l'override. Per consentire il traffico Internet, creare regole per indirizzare il traffico in ingresso a determinate porte, ad esempio la porta 80 per il traffico HTTP.

- Per abilitare Desktop remoto, aggiungere una regola all'NSG per consentire il traffico in arrivo sulla porta TCP 3389.

## Considerazioni sulla scalabilità

- Per aumentare o ridurre le prestazioni di una VM, è possibile [modificare le dimensioni della VM][vm-resize].

- Per scalare orizzontalmente, inserire due o più VM in un set di disponibilità nell'ambito di un servizio di bilanciamento del carico. Per ulteriori dettagli, vedere l'articolo sull'[esecuzione di più VM Windows in Azure][multi-vm].

## Considerazioni sulla disponibilità

- Come detto in precedenza, non esiste alcun contratto di servizio per una singola VM. Per ottenere il contratto di servizio, è necessario distribuire più VM in un set di disponibilità.

- È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della VM è stato provocato da attività di manutenzione pianificata.

- Il backup dei VHD viene eseguito dall'[Archiviazione di Azure][azure-storage] e ne viene eseguita la replica per finalità di durabilità e disponibilità.

- Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## Considerazioni sulla gestibilità

- **Gruppi di risorse.** Posizionare in uno stesso [gruppo di risorse][resource-manager-overview] le risorse strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse in gruppo, distribuendo i costi per ogni gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test. Assegnare alle risorse nomi significativi. In tal modo, è più semplice individuare una specifica risorsa e comprenderne il ruolo. Vedere l'articolo sulle [convenzioni di denominazione consigliate per le risorse di Azure][naming conventions].

- **Diagnostica delle VM.** Abilitare il monitoraggio e la diagnostica, tra cui le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring]. Usare l'estensione [Raccolta di log di Azure][log-collector] per raccogliere i log della piattaforma Azure e caricarli nell'Archiviazione di Azure.

    Per abilitare la diagnostica, eseguire il comando seguente dell'interfaccia della riga di comando:

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **Arresto di una VM.** Azure distingue tra gli stati "Arrestato" e "Deallocato". Vengono applicati addebiti se lo stato della VM è "Arrestato". Non viene applicato alcun addebito se la VM è stata deallocata.

    Usare il comando seguente dell'interfaccia della riga di comando per deallocare una VM:

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Anche il pulsante **Arresta** nel portale di Azure consente di deallocare la VM. Se, tuttavia, l'arresto viene effettuato tramite il sistema operativo ad accesso eseguito, la VM viene arrestata ma _non_ deallocata, quindi gli addebiti continueranno a essere effettuati.

- **Eliminazione di una VM.** Se si elimina una VM, i VHD non vengono eliminati. È quindi possibile eliminare in modo sicuro la macchina virtuale senza perdere dati. Verranno tuttavia applicati comunque addebiti per l'archiviazione. Per eliminare il VHD, eliminare il file dall'[archiviazione BLOB][blob-storage].

  Per impedire l'eliminazione accidentale, utilizzare un [blocco di risorsa][resource-lock] per bloccare l'intero gruppo di risorse o le singole risorse, ad esempio la VM.

## Considerazioni relative alla sicurezza

- Usare il [Centro sicurezza di Azure][security-center] per ottenere una visualizzazione centrale dello stato di sicurezza delle risorse di Azure. Il Centro sicurezza monitora potenziali problemi di sicurezza, ad esempio aggiornamenti del sistema e antimalware, e fornisce un'immagine completa dello stato della sicurezza della distribuzione.

    - Il Centro sicurezza è configurato per ogni sottoscrizione di Azure. Abilitare la raccolta dei dati di sicurezza come descritto nell'articolo sull'[utilizzo del Centro sicurezza PC].
    - Dopo aver abilitato la raccolta dei dati, il Centro sicurezza PC analizza automaticamente tutte le macchine virtuali create nell'ambito della sottoscrizione.

- **Gestione delle patch.** Se abilitato, Centro sicurezza PC controlla se mancano aggiornamenti critici e della sicurezza. Utilizzare le [impostazioni di Criteri di gruppo][group-policy] nella VM per abilitare gli aggiornamenti automatici del sistema.

- **Antimalware.** Se abilitato, Centro sicurezza PC controlla se è installato il software antimalware. È inoltre possibile utilizzare Centro sicurezza PC per installare il software antimalware all'interno del portale di Azure.

- Utilizzare il [controllo di accesso in base al ruolo][rbac] (RBAC) per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione ai membri del proprio team DevOps. Ad esempio, il ruolo di lettura permette di visualizzare le risorse di Azure, ma non di crearle, gestirle o eliminarle. Alcuni ruoli sono specifici di determinati tipi di risorse di Azure. Ad esempio, il ruolo di Collaboratore Macchina virtuale consente di riavviare o deallocare una VM, reimpostare la password di amministratore, creare una nuova VM e così via. Altri [ruoli RBAC predefiniti][rbac-roles] che potrebbero essere utili per questa architettura di riferimento includono [DevTest Lab User][rbac-devtest] e [Network Contributor][rbac-network]. Oltre a poter assegnare un utente a più ruoli, è possibile creare ruoli personalizzati per autorizzazioni ancora più dettagliate.

    > [AZURE.NOTE] Il controllo degli accessi in base al ruolo non limita le azioni eseguibili da un utente registrato in una VM. Le autorizzazioni sono determinate dal tipo di account sul sistema operativo guest.

- Per reimpostare la password dell'amministratore locale, eseguire il comando `vm reset-access` dell'interfaccia della riga di comando di Azure.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Per verificare le azioni di provisioning e altri eventi della VM, usare i [log di controllo][audit-logs].

- Se si necessitano di crittografare i dischi del sistema operativo e i dischi dati, usare [Crittografia dischi di Azure ][disk-encryption].

## Componenti della soluzione

Lo script batch di Windows seguente esegue i comandi dell'[interfaccia della riga di comando di Azure][azure-cli] per distribuire una singola istanza della VM e le risorse di rete e di archiviazione correlate, come illustrato nel diagramma precedente.

Lo script utilizza le convenzioni di denominazione descritte nell'articolo sulle [convenzioni di denominazione consigliate per le risorse di Azure][naming conventions].

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```


## Passaggi successivi

Per poter applicare il [contratto di servizio per le macchine virtuali][vm-sla], è necessario distribuire due o più istanze in un set di disponibilità. Per ulteriori informazioni, vedere l'articolo sull'[esecuzione di più VM Windows in Azure][multi-vm].

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/it-IT/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/it-IT/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/it-IT/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/it-IT/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/it-IT/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/it-IT/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/it-IT/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/it-IT/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/it-IT/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[utilizzo del Centro sicurezza PC]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/it-IT/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architettura generale di una VM di Azure"

<!---HONumber=AcomDC_0720_2016-->