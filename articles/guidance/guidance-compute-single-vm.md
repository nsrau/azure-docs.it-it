<properties
   pageTitle="Esecuzione di una macchina virtuale (Windows) | Progetto | Microsoft Azure"
   description="Informazioni su come eseguire una singola VM in Azure, con particolare attenzione a scalabilità, resilienza, gestibilità e sicurezza."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Esecuzione di una singola VM Windows in Azure

Questo articolo illustra un insieme di procedure consolidate per l'esecuzione di una singola VM Windows in Azure, con una particolare attenzione a scalabilità, resilienza, gestibilità e sicurezza.

> [AZURE.WARNING] Non è disponibile alcun contratto di servizio per il tempo di attività per le singole VM in Azure. Usare questa configurazione per lo sviluppo e i test, ma non come distribuzione di produzione.

Azure offre due diversi modelli di distribuzione, ovvero [Azure Resource Manager][resource-manager-overview] e la distribuzione classica. Questo articolo usa Azure Resource Manager, consigliato da Microsoft per le nuove distribuzioni. È possibile usare Azure Resource Manager in molti modi, ad esempio tramite il [portale di Azure][azure-portal], [Azure PowerShell][azure-powershell], i comandi dell'[interfaccia della riga di comando di Azure][azure-cli] oppure i modelli di [Azure Resource Manager][arm-templates]. Questo articolo include un esempio che prevede l'uso dell'interfaccia della riga di comando di Azure.

![IaaS: singola VM](media/guidance-compute-single-vm.png)

Il provisioning di una singola VM comporta un numero maggiore di componenti mobili rispetto alla macchina virtuale core stessa. Sono presenti elementi di calcolo, rete e archiviazione.

- **Gruppo di risorse**. Creare un [gruppo di risorse][resource-manager-overview] in cui includere le risorse per questa macchina virtuale. Un _gruppo di risorse_ è un contenitore che contiene risorse correlate.

- **Macchina virtuale**. È possibile effettuare il provisioning di una VM da un elenco di immagini pubblicate oppure da un file VHD caricato nell'archivio BLOB di Azure.

- **Disco del sistema operativo**. Il disco del sistema operativo è un VHD archiviato nell'[Archiviazione di Azure][azure-storage]. Viene quindi salvato in modo permanente anche se il computer host si arresta.

- **Disco temporaneo**. La macchina virtuale viene creata con un disco temporaneo, ovvero l'unità `D:` in Windows. Questo disco viene archiviato in un'unità fisica nel computer host. _Non_ viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della VM. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio.

- **Dischi dati**. Un [disco dati][data-disk] è un VHD persistente usato per i dati dell'applicazione. I dischi dati vengono archiviati nell'Archiviazione di Azure, come il disco del sistema operativo.

- **Rete virtuale (VNet) e subnet**. Ogni VM in Azure viene distribuita in una rete virtuale (VNet), che viene a sua volta suddivisa in subnet.

- **Indirizzo IP pubblico**. Un indirizzo IP pubblico è necessario per comunicare con la macchina virtuale, ad esempio tramite Desktop remoto.

- **Gruppo di sicurezza di rete**. Il [gruppo di sicurezza di rete][nsg] viene usato per consentire/negare il traffico di rete verso la macchina virtuale. Il gruppo di sicurezza di rete predefinito non consente tutto il traffico Internet in arrivo.

- **Scheda di interfaccia di rete (NIC)**. La scheda di interfaccia di rete consente alla VM di comunicare con la rete virtuale.

- **Diagnostica.** La registrazione diagnostica è essenziale per la gestione e la risoluzione dei problemi della macchina virtuale.

## Indicazioni per le VM

- Quando si sposta un carico di lavoro esistente in Azure, scegliere le [dimensioni di VM][virtual-machine-sizes] più simili a quelle dei server locali. Sono consigliate le serie DS e GS, che possono usare l'Archiviazione Premium per carichi di lavoro con molte operazioni di I/O.

    - Se il carico di lavoro non richiede l'accesso al disco a prestazioni elevate e bassa latenza, è possibile prendere in considerazione le altre dimensioni di VM del livello Standard, ad esempio la serie A o la serie D.

- Quando si effettua il provisioning della VM e di altre risorse, è necessario specificare una località. È in genere consigliabile scegliere la località più vicina agli utenti interni o ai clienti. È tuttavia possibile che non tutti gli SKU di VM siano disponibili in tutte le località. Per informazioni dettagliate, vedere [Servizi in base all'area][services-by-region].

- Per informazioni sulla scelta di un'immagine di VM pubblicata, vedere [Esplorazione e selezione di immagini delle macchine virtuali di Azure][select-vm-image].

## Indicazioni per il disco e l'archiviazione

Per ottenere prestazioni ottimali per I/O del disco, è consigliabile l'[Archiviazione Premium][premium-storage]. Si noti tuttavia che l'Archiviazione Premium richiede VM della serie DS o GS.

- I costi per l'Archiviazione Premium sono basati sulle dimensioni del disco sottoposto a provisioning. Anche IOPS e velocità effettiva, ad esempio la velocità di trasferimento dei dati, dipendono dalle dimensioni del disco. Quando si effettua il provisioning di un disco è quindi consigliabile tenere in considerazione tutti e tre i fattori, ovvero capacità, IOPS e velocità effettiva.

- Per l'Archiviazione Standard i costi sono basati sulla quantità di dati scritta su disco. È quindi consigliabile effettuare il provisioning della dimensione massima (1023 GB). Assicurarsi tuttavia di usare la formattazione veloce durante la formattazione dei dischi. Una formattazione completa del disco scrive zero sul disco e ciò si traduce nell'uso effettivo di spazio di archiviazione. Vedere [Prezzi di Archiviazione di Azure][storage-price].

- Se si seleziona l'Archiviazione Standard, è consigliabile usare l'archiviazione con ridondanza geografica, perché risulta durevole anche in caso di interruzione completa dei servizi in un'area o in caso di emergenza in cui l'area primaria non è recuperabile.

- Aggiungere uno o più dischi dati. Quando si crea un nuovo VHD, il disco non è formattato. Accedere alla VM per formattare il disco.

- Se sono presenti molti dischi dati, occorre prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere [Limiti relativi a Macchine virtuali][vm-disk-limits].

- Per prestazioni ottimali, creare un account di archiviazione separato per i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

## Indicazioni per la rete

- Per una singola macchina virtuale è consigliabile creare una VNet con una subnet. Creare anche un gruppo di sicurezza di rete e un indirizzo IP pubblico.

- L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

    - Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subirà modifiche, ad esempio se è necessario creare un record A in DNS o se è necessario che l'indirizzo IP sia consentito.

    - Per impostazione predefinita, l'indirizzo IP non ha un nome di dominio completo. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn].

- Allocare una scheda di interfaccia di rete e associarla all'indirizzo IP, alla subnet e al gruppo di sicurezza di rete.

- Le regole predefinite del gruppo di sicurezza di rete non consentono Desktop remoto. Per abilitare Desktop remoto, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in arrivo sulla porta TCP 3389.

## Scalabilità

È possibile aumentare o ridurre le prestazioni di una VM cambiando le dimensioni della macchina virtuale. Il comando seguente dell'interfaccia della riga di comando di Azure ridimensiona una VM:

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

Il ridimensionamento della macchina virtuale attiverà un riavvio del sistema ed eseguirà di nuovo il mapping del sistema operativo esistente e dei dischi dati dopo il riavvio. Qualsiasi elemento presente nel disco temporaneo andrà perso. L'opzione `--boot-diagnostics-storage-uri` consente alla [diagnostica di avvio][boot-diagnostics] di registrare eventuali errori correlati all'avvio.

È possibile che non sia consentito passare da una famiglia di SKU all'altra, ad esempio dalla serie A alla serie G. Usare il comando seguente dell'interfaccia della riga di comando per ottenere un elenco delle dimensioni disponibili per una VM esistente:

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

Per passare a una dimensione non inclusa nell'elenco, è necessario eliminare l'istanza della macchina virtuale e crearne una nuova. Se si elimina una VM, non verranno eliminati i VHD.

## Availability

- Non è disponibile alcun Contratto di servizio relativo alla disponibilità per le singole VM. Per ottenere un Contratto di servizio relativo alla disponibilità, è necessario distribuire più macchine virtuali in un set di disponibilità.

- È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della macchina virtuale è stato provocato da attività di manutenzione pianificata.

- Non inserire una singola VM in un set di disponibilità. Se si inserisce una macchina virtuale in un set di disponibilità, Azure considererà la VM come parte di un set a istanza multipla e non si riceveranno avvisi o notifiche anticipate in merito a riavvii per la gestione pianificata.

- Il backup dei VHD viene eseguito dall'[Archiviazione di Azure][azure-storage] e ne viene eseguita la replica per finalità di durabilità e disponibilità.

- Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## Gestibilità

- Eseguire il comando seguente dell'interfaccia della riga di comando per abilitare la diagnostica della VM:
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    Questo comando abilita le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la diagnostica di avvio. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring].

- Usare l'estensione [Raccolta di log di Azure][log-collector] per raccogliere i log e caricarli nell'Archiviazione di Azure.

- Azure distingue tra gli stati "Arrestato" e "Deallocato". Vengono applicati addebiti se lo stato della VM è "Arrestato". Non viene applicato alcun addebito se la VM è stata deallocata. Vedere le [Domande frequenti sulle macchine virtuali][vm-faq].

    Usare il comando seguente dell'interfaccia della riga di comando per deallocare una VM:
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    Nota: anche il pulsante **Arresta** nel portale di Azure consente di deallocare la VM. Se, tuttavia, l'arresto viene effettuato all'interno di Windows, tramite Desktop remoto, la VM viene arrestata ma _non_ deallocata, quindi verranno ancora applicati addebiti.

- Se si elimina una VM, i VHD non vengono eliminati. È quindi possibile eliminare in modo sicuro la macchina virtuale senza perdere dati. Verranno tuttavia applicati comunque addebiti per l'archiviazione. Per eliminare il VHD, eliminare il file dall'[archivio BLOB][blog-storage].

- Per ridimensionare il disco del sistema operativo, scaricare il file con estensione vhd e usare uno strumento come [Resize-VHD][Resize-VHD] per ridimensionare il VHD. Caricare il VHD nell'archivio BLOB, quindi eliminare l'istanza della VM ed effettuare il provisioning di una nuova istanza che usa il VHD ridimensionato.


## Sicurezza

- Usare il [Centro sicurezza di Azure][security-center] per ottenere una visualizzazione centrale dello stato di sicurezza delle risorse di Azure. Il Centro sicurezza monitora potenziali problemi di sicurezza, ad esempio aggiornamenti del sistema, antimalware e ACL basati su endpoint, e fornisce un'immagine completa dello stato della sicurezza della distribuzione. **Nota:** il Centro sicurezza è ancora disponibile in anteprima al momento della scrittura di questo articolo.

- Usare il [controllo degli accessi in base al ruolo][rbac] per definire i membri del team DevOps che possono gestire le risorse di Azure distribuite, ovvero VM, rete e così via.

- Prendere in considerazione l'installazione delle [estensioni di sicurezza][security-extensions].

- Usare [Crittografia dischi di Azure ][disk-encryption] per crittografare i dischi del sistema operativo e i dischi dati. **Nota:** Crittografia dischi di Azure è ancora disponibile in anteprima al momento della scrittura di questo articolo.

## Risoluzione dei problemi

- Per reimpostare la password dell'amministratore locale, eseguire il comando `vm reset-access` dell'interfaccia della riga di comando di Azure.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- Se la VM passa a uno stato non avviabile, usare la [diagnostica di avvio][boot-diagnostics] per diagnosticare gli errori di avvio.

- Per verificare le azioni di provisioning e altri eventi della VM, vedere i [log di controllo][audit-logs].

## Comandi dell'interfaccia della riga di comando di Azure (esempio)

Lo script batch di Windows seguente esegue i comandi dell'[interfaccia della riga di comando di Azure][azure-cli] per distribuire una singola istanza della macchina virtuale e le risorse di rete e di archiviazione correlate, come illustrato nel diagramma precedente.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

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

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/it-IT/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/it-IT/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/it-IT/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/it-IT/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/it-IT/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/it-IT/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/it-IT/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->