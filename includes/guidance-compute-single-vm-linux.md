Questo articolo illustra un insieme di procedure consolidate per l'esecuzione di una VM Linux in Azure, con particolare attenzione agli aspetti di scalabilità, disponibilità, gestibilità e sicurezza. Azure supporta l'esecuzione di molte delle distribuzioni Linux più diffuse, tra cui CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Per altre informazioni, vedere [Azure e Linux][azure-linux].

> [AZURE.NOTE] Azure offre due diversi modelli di distribuzione, ovvero [Azure Resource Manager][resource-manager-overview] e la distribuzione classica. Questo articolo usa Azure Resource Manager, consigliato da Microsoft per le nuove distribuzioni.

Non è consigliabile utilizzare una singola VM per carichi di lavoro di produzione, poiché non esiste alcun contratto di servizio che disciplini il tempo di attività delle singole VM in Azure. Per ottenere il contratto di servizio, è necessario distribuire più VM in un set di disponibilità. Per ulteriori informazioni, vedere l'articolo sull'[esecuzione di più VM in Azure][multi-vm].

## Diagramma dell'architettura

Il provisioning di una VM in Azure coinvolge un altri componenti mobili oltre alla VM in sé. Sono presenti elementi di calcolo, rete e archiviazione.

![[0]][0]

- **Gruppo di risorse.** Un [gruppo di risorse][resource-manager-overview] è un contenitore in cui risiedono le risorse correlate. Creare un gruppo di risorse in cui includere le risorse per questa VM.

- **VM**. È possibile effettuare il provisioning di una VM da un elenco di immagini pubblicate oppure da un file VHD caricato nell'archivio BLOB di Azure.

- **Disco del sistema operativo.** Il disco del sistema operativo è un VHD archiviato nell'[Archiviazione di Azure][azure-storage]. Viene quindi salvato in modo permanente anche se il computer host si arresta. Il disco del sistema operativo è `/dev/sda1`

- **Disco temporaneo.** La VM viene creata con un disco temporaneo. Questo disco viene archiviato in un'unità fisica nel computer host. _Non_ viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della VM. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio. Il disco temporaneo è `/dev/sdb1` e montato in `/mnt/resource` o `/mnt`.

- **Dischi dati.** Un [disco dati][data-disk] è un VHD persistente usato per i dati dell'applicazione. I dischi dati vengono archiviati nell'Archiviazione di Azure, come il disco del sistema operativo.

- **Rete virtuale (VNet) e subnet.** Ogni VM in Azure viene distribuita in una rete virtuale (VNet), che viene a sua volta suddivisa in subnet.

- **Indirizzo IP pubblico.** Un indirizzo IP pubblico è necessario per comunicare con la VM, ad esempio tramite SSH.

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

- Aggiungere uno o più dischi dati. Quando si crea un nuovo VHD, il disco non è formattato. Accedere alla VM per formattare il disco. I dischi di dati verranno visualizzati come `/dev/sdc`, `/dev/sdd` e così via. È possibile eseguire `lsblk` per elencare i dispositivi a blocchi, ad esempio i dischi. Per utilizzare un disco dati, creare una nuova partizione e un nuovo file system, quindi montare il disco. ad esempio:

    ```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
    ```

- Se sono presenti molti dischi dati, occorre prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere l'articolo sui [limiti relativi ai dischi delle macchine virtuali][vm-disk-limits].

- Quando si aggiunge un disco dati, al disco viene assegnato un ID numero di unità logica (LUN). Facoltativamente, è possibile specificare l'ID LUN, ad esempio se si intende sostituire un disco e si desidera mantenere lo stesso ID LUN o se si dispone di un'applicazione che cerca un ID LUN specifico. Tuttavia, tenere presente che gli ID LUN devono essere univoci per ogni disco.

- Se lo si desidera, è possibile modificare l'utilità di pianificazione I/O per ottimizzare le prestazioni nelle unità SSD (utilizzato da Archiviazione Premium). Normalmente si consiglia di utilizzare l'utilità di pianificazione NOOP per le unità SSD, ma è necessario utilizzare uno strumento come [iostat] per monitorare le prestazioni di I/O del disco relative al un carico di lavoro specifico.

- Per prestazioni ottimali, creare un account di archiviazione separato per i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

### Indicazioni per la rete

- L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

    - Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subirà modifiche, ad esempio se è necessario creare un record A in DNS o se è necessario che l'indirizzo IP sia consentito.

    - È inoltre possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME][cname-record] in DNS che punta al nome FQDN. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn].

- Tutti i gruppi di sicurezza di rete contengono un set di [regole predefinite][nsg-default-rules], inclusa una regola che blocca tutto il traffico Internet in ingresso. Le regole predefinite non possono essere eliminate, ma altre regole possono eseguirne l'override. Per consentire il traffico Internet, creare regole per indirizzare il traffico in ingresso a determinate porte, ad esempio la porta 80 per il traffico HTTP.

- Per abilitare SSH, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in arrivo sulla porta TCP 22.

## Considerazioni sulla scalabilità

- Per aumentare o ridurre le prestazioni di una VM, è possibile [modificare le dimensioni della VM][vm-resize].

- Per scalare orizzontalmente, inserire due o più VM in un set di disponibilità nell'ambito di un servizio di bilanciamento del carico. Per ulteriori dettagli, vedere l'articolo sull'[esecuzione di più VM in Azure][multi-vm].

## Considerazioni sulla disponibilità

- Come detto in precedenza, non esiste alcun contratto di servizio per una singola VM. Per ottenere il contratto di servizio, è necessario distribuire più VM in un set di disponibilità.

- È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della VM è stato provocato da attività di manutenzione pianificata.

- Il backup dei VHD viene eseguito dall'[Archiviazione di Azure][azure-storage] e ne viene eseguita la replica per finalità di durabilità e disponibilità.

- Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## Considerazioni sulla gestibilità

- **Gruppi di risorse.** Posizionare in uno stesso [gruppo di risorse][resource-manager-overview] le risorse strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse in gruppo, distribuendo i costi per ogni gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test. Assegnare alle risorse nomi significativi. In tal modo, è più semplice individuare una specifica risorsa e comprenderne il ruolo. Vedere l'articolo sulle [convenzioni di denominazione consigliate per le risorse di Azure][naming conventions].

- **ssh**. Prima di creare una VM Linux, generare una coppia di chiavi RSA pubblica/privata a 2.048 bit. Quando si crea la VM, utilizzare il file di chiave pubblica. Per altre informazioni, vedere [Come usare SSH con Linux e Mac in Azure][ssh-linux].

- **Diagnostica delle VM.** Abilitare il monitoraggio e la diagnostica, tra cui le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring].

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

- Automatizzare gli aggiornamenti del sistema operativo utilizzando l'estensione [OSPatching] della VM. Installare l'estensione al momento di eseguire il provisioning della VM. È possibile specificare la frequenza di installazione delle patch e stabilire se eseguire il riavvio dopo l'applicazione di patch.

- Utilizzare il [controllo degli accessi in base al ruolo][rbac] (RBAC) per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione ai membri del proprio team DevOps. Ad esempio, il ruolo di lettura permette di visualizzare le risorse di Azure, ma non di crearle, gestirle o eliminarle. Alcuni ruoli sono specifici di determinati tipi di risorse di Azure. Ad esempio, il ruolo di Collaboratore Macchina virtuale consente di riavviare o deallocare una VM, reimpostare la password di amministratore, creare una nuova VM e così via. Altri [ruoli RBAC predefiniti][rbac-roles] che potrebbero essere utili per questa architettura di riferimento includono [DevTest Lab User][rbac-devtest] e [Network Contributor][rbac-network]. Oltre a poter assegnare un utente a più ruoli, è possibile creare ruoli personalizzati per autorizzazioni ancora più dettagliate.

    > [AZURE.NOTE] Il controllo degli accessi in base al ruolo non limita le azioni eseguibili da un utente registrato in una VM. Le autorizzazioni sono determinate dal tipo di account sul sistema operativo guest.

- Per verificare le azioni di provisioning e altri eventi della VM, usare i [log di controllo][audit-logs].

- Se si necessitano di crittografare i dischi del sistema operativo e i dischi dati, usare [Crittografia dischi di Azure ][disk-encryption].

## Componenti della soluzione

<!-- TO BE UPDATED WHEN THE NEW TEMPLATES ARE AVAILABLE -->

Lo script bash seguente esegue i comandi dell'[interfaccia della riga di comando di Azure][azure-cli] per distribuire una singola istanza della VM e le risorse di rete e di archiviazione correlate, come illustrato nel diagramma precedente.

Lo script utilizza le convenzioni di denominazione descritte nell'articolo sulle [convenzioni di denominazione consigliate per le risorse di Azure][naming conventions].

Per eseguire lo script:

1. Generare una chiave di autenticazione RSA a 2.048 bit.

        ssh-keygen -t rsa -b 2048

2. Passare l'ID sottoscrizione di Azure e il nome del file di chiave pubblica allo script come parametri.

        ./azurecli-single-vm-sample.sh <subscription ID> ~/.ssh/id_rsa.pub

3. Al completamento dello script, accedere alla VM utilizzando SSH. Utilizzare la chiave privata per eseguire l'autenticazione.

        ssh testuser@<app>-vm1.<location>.cloudapp.azure.com -i ~/.ssh/id_rsa

    dove `<app>` è il valore della variabile dello script `APP_NAME`, mentre `<location>` è il valore della variabile `LOCATION`.

```bat
#!/bin/bash

############################################################################
#script for generating infrastructure for single VM running linux          #
# of user choice. It creates azure resource group, storage account for VM  #
# vnet, subnets for VM, and NSG rule                                       #
# tags for main variables used                                             #
# ScriptCommandParameters                                                  #
# ScriptVars                                                               #
############################################################################

############################################################################
# User defined functions for single VM script                              #
# errhandle : handles errors via trap if any exception happens             #
# in the cli execution or if the user interrupts with CTRL+C               #
# allowing for fast interruption                                           #
############################################################################

# error handling or interruption via ctrl-c.
# line number and error code of executed command is passed to errhandle function

trap 'errhandle $LINENO $?' SIGINT ERR

errhandle()
{
  echo "Error or Interruption at line ${1} exit code ${2} "
  exit ${2}
}

###############################################################################
############################## End of user defined functions ##################
###############################################################################

# 2 paramaters are expected
# public key file needs to be generated using ssh-keygen

if [ $# -ne 2  ]
then
	echo  "Usage:  ${0}  subscription-id public-ssh-key-file"
	exit
fi

if [ ! -f $2  ]
then
	echo "Public Key file ${2} does not exist. please generate it"
	echo "ssh-keygen -t rsa -b 2048"
	exit
fi

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
# ScriptCommandParameters
SUBSCRIPTION=$1
PUBLICKEYFILE=$2

# ScriptVars
LOCATION=eastus2
APP_NAME=app1
ENVIRONMENT=dev
USERNAME=testuser
VM_NAME="${APP_NAME}-vm1"
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
IP_NAME="${APP_NAME}-pip"
NIC_NAME="${VM_NAME}-1nic"
NSG_NAME="${APP_NAME}-nsg"
SUBNET_NAME="${APP_NAME}-subnet"
VNET_NAME="${APP_NAME}-vnet"
VHD_STORAGE="${VM_NAME//-}st1"
DIAGNOSTICS_STORAGE="${VM_NAME//-}diag"

# Use the following command to get the list of URNs for RHEL, UBUNTU, and OPENSUSE:
# RHEL
# azure vm image list $LOCATION  redhat RHEL 7.2
# UBUNTU
# azure vm image list $LOCATION canonical ubuntuserver 14.04.3-LTS
# SUSE
# azure vm image $LOCATION  suse opensuse 13.2

LINUX_BASE_IMAGE=redhat:rhel:7.2:7.2.20160302

# For a list of VM sizes see: 
#   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
# To see the VM sizes available in a region:
# 	azure vm sizes --location <location>
VM_SIZE=Standard_DS1

# Set up the postfix variables attached to most CLI commands

POSTFIX="--resource-group ${RESOURCE_GROUP} --subscription ${SUBSCRIPTION}"

azure config mode arm

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#Create resources

#Create the enclosing resource group
azure group create --name $RESOURCE_GROUP --location $LOCATION --subscription $SUBSCRIPTION

# Create the VNet
azure network vnet create --address-prefixes 172.17.0.0/16 --name $VNET_NAME \
--location $LOCATION $POSTFIX

#Create the network security group
azure network nsg create --name $NSG_NAME --location $LOCATION $POSTFIX

#Create the subnet
azure network vnet subnet create --vnet-name $VNET_NAME --address-prefix  "172.17.0.0/24" \
--name $SUBNET_NAME --network-security-group-name $NSG_NAME $POSTFIX

#Create the public IP address (dynamic)
azure network public-ip create --name $IP_NAME --domain-name-label $VM_NAME \
--location $LOCATION $POSTFIX

#Create the NIC
azure network nic create --public-ip-name $IP_NAME --subnet-name $SUBNET_NAME \
--subnet-vnet-name $VNET_NAME --name $NIC_NAME --location $LOCATION $POSTFIX

#Create the storage account for the OS VHD
azure storage account create --type PLRS --location $LOCATION $POSTFIX $VHD_STORAGE

#Create the storage account for diagnostics logs
azure storage account create --type LRS --location $LOCATION $POSTFIX $DIAGNOSTICS_STORAGE

#Create the VM
azure vm create --name $VM_NAME --os-type Linux --image-urn  $LINUX_BASE_IMAGE \
--vm-size $VM_SIZE --vnet-subnet-name $SUBNET_NAME --vnet-name $VNET_NAME \
--nic-name $NIC_NAME --storage-account-name $VHD_STORAGE \
--os-disk-vhd "${VM_NAME}-osdisk.vhd" --admin-username $USERNAME \
--ssh-publickey-file $PUBLICKEYFILE --boot-diagnostics-storage-uri \
"https://${DIAGNOSTICS_STORAGE}.blob.core.windows.net/" --location $LOCATION $POSTFIX

#Attach a data disk
azure vm disk attach-new --vm-name $VM_NAME --size-in-gb 128 --vhd-name data1.vhd \
--storage-account-name $VHD_STORAGE $POSTFIX

#Allow SSH
azure network nsg rule create --nsg-name $NSG_NAME --direction Inbound --protocol Tcp \
--destination-port-range 22  --source-port-range "*"  --priority 100 --access Allow \
SSHAllow $POSTFIX

#Install patching extension
PATCH_CONFIG='{"rebootAfterPatch":"RebootIfNeed","startTime":"3:00","dayOfWeek":"Sunday","category":"ImportantAndRecommended"}'
azure vm extension set --name OSPatchingForLinux --publisher-name Microsoft.OSTCExtensions \
--public-config $PATCH_CONFIG --vm-name $VM_NAME --version 2.0 $POSTFIX
```

## Passaggi successivi

Per poter applicare il [contratto di servizio per le macchine virtuali][vm-sla], è necessario distribuire due o più istanze in un set di disponibilità. Per ulteriori informazioni, vedere l'articolo sull'[esecuzione di più VM in Azure][multi-vm].

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/it-IT/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/it-IT/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/it-IT/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/it-IT/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/it-IT/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/it-IT/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/it-IT/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architettura generale di una VM di Azure"

<!---HONumber=AcomDC_0720_2016-->