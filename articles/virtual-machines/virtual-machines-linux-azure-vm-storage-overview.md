---
title: VM Linux di Azure e Archiviazione di Azure | Microsoft Docs
description: Descrive l&quot;archiviazione Standard e Premium di Azure, Managed Disks e i dischi non gestiti con le macchine virtuali Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 1ada403a502972ee0d8cd96af2d62d923d43f6cf
ms.lasthandoff: 03/18/2017


---
# <a name="azure-and-linux-vm-storage"></a>Archiviazione delle macchine virtuali Linux e Azure
Archiviazione di Azure è la soluzione di archiviazione cloud per le applicazioni moderne basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti.  Oltre a consentire agli sviluppatori di compilare applicazioni di grande portata per supportare nuovi scenari, Archiviazione di Azure offre anche la base di archiviazione per le macchine virtuali di Azure.

## <a name="managed-disks"></a>Managed Disks

Sono ora disponibili macchine virtuali di Azure con [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), che consentono di creare macchine virtuali senza dover creare o gestire da sé gli [account di archiviazione di Azure](../storage/storage-introduction.md). È sufficiente specificare se si desidera l'Archiviazione Premium o Standard e le dimensioni del disco. Azure penserà a creare i dischi della macchina virtuale. Le macchine virtuali con Managed Disks hanno diverse funzionalità importanti, tra cui:

- Supporto per la scalabilità automatica. Azure crea i dischi e gestisce l'archiviazione sottostante per supportare fino a 10.000 dischi per sottoscrizione.
- Maggiore affidabilità con i set di disponibilità. Azure assicura che i dischi di macchine virtuali siano isolati automaticamente tra loro all'interno dei set di disponibilità.
- Maggiore controllo degli accessi. Managed Disks espone varie operazioni controllate dal [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md).

Il prezzo di Managed Disks è diverso da quello dei dischi non gestiti. Per le relative informazioni, vedere [Prezzi e fatturazione di Managed Disks](../storage/storage-managed-disks-overview.md#pricing-and-billing).

È possibile convertire le macchine virtuali esistenti che usano i dischi non gestiti per usare quelli gestiti tramite [az vm convert](/cli/azure/vm#convert). Per altre informazioni, vedere [come convertire una macchina virtuale Linux da dischi non gestiti ad Azure Managed Disks](virtual-machines-linux-convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Non è possibile convertire un disco non gestito in un disco gestito se il disco non gestito si trova in un account di archiviazione che è stato, anche in passato, crittografato con la [Crittografia del servizio di archiviazione di Azure (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La procedura seguente illustra come convertire dischi non gestiti che sono, o sono stati, in un account di archiviazione crittografato:

- Copiare il disco rigido virtuale con [az storage blob copy start](/cli/azure/storage/blob/copy#start) in un account di archiviazione che non è mai stato abilitato per la crittografia del servizio di archiviazione di Azure.
- Creare una macchina virtuale che usi dischi gestiti e specificare il file del disco rigido virtuale durante la creazione con [az vm create](/cli/azure/vm#create) o
- Collegare il disco rigido virtuale copiato con [az vm disk attach](/cli/azure/vm/disk#attach) a una macchina virtuale in esecuzione con dischi gestiti.


## <a name="azure-storage-standard-and-premium"></a>Archiviazione di Azure: Standard e Premium
È possibile creare macchine virtuali di Azure, con Managed Disks o dischi non gestiti, basate su dischi di archiviazione Standard o Premium. Quando si usa il portale per scegliere la macchina virtuale, è necessario aprire un menu a tendina nella schermata **Informazioni di base** per visualizzare i dischi Standard e Premium. Quando se ne esegue l'attivazione sull'unità SSD, vengono mostrate soltanto le macchine virtuali con l'archiviazione premium abilitata, tutte supportate dalle unità SSD.  Quando se ne esegue l'attivazione sull'unità HDD, vengono visualizzate le macchine virtuali con l'archiviazione standard abilitata supportate dalle unità disco rotante, oltre alle macchine virtuali con l'archiviazione premium supportate dalle unità SSD.

Durante la creazione di una macchina virtuale da `azure-cli` è possibile scegliere tra l'archiviazione standard e quella premium quando si sceglie la dimensione della macchina virtuale tramite il flag dell'interfaccia della riga di comando di `-z` o `--vm-size`.

## <a name="creating-a-vm-with-a-managed-disk"></a>Creazione di una macchina virtuale con un disco gestito

L'esempio seguente richiede l'interfaccia della riga di comando di Azure 2.0, [installabile da qui].

Creare innanzitutto un gruppo di risorse per gestire le risorse:

```azurecli
az group create --location westus --name myResourceGroup
```

Quindi creare la macchina virtuale con il comando `az vm create`, come nell'esempio seguente. È necessario specificare un argomento `--public-ip-address-dns-name` univoco, poiché `manageddisks` sarà probabilmente in uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```

L'esempio precedente crea una macchina virtuale con un disco gestito in un account di archiviazione Standard. Per usare un account di archiviazione Premium, aggiungere l'argomento `--storage-sku Premium_LRS`, come nell'esempio seguente:

```azurecli
az vm create \
--storage-sku Premium_LRS
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


### <a name="create-a-vm-with-an-unmanaged-standard-disk-using-the-azure-cli-10"></a>Creare una macchina virtuale con un disco Standard non gestito tramite l'interfaccia della riga di comando di Azure 1.0

È naturalmente possibile usare anche l'interfaccia della riga di comando di Azure 1.0 per creare macchine virtuali con dischi Standard e Premium. In questo momento non è possibile usare tale interfaccia per creare macchine virtuali con Managed Disks.

L'opzione `-z` sceglie Standard_A1, che rappresenta una macchina virtuale Linux basata sull'archiviazione Standard.

```azurecli
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-using-the-azure-cli-10"></a>Creare una macchina virtuale con archiviazione Premium usando l'interfaccia della riga di comando di Azure 1.0
L'opzione `-z` sceglie Standard_DS1, che rappresenta una macchina virtuale Linux basata sull'archiviazione Premium.

```azurecli
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Archiviazione standard
Archiviazione standard di Azure è il tipo di archiviazione predefinito.  Archiviazione standard è conveniente e garantisce ottime prestazioni.  

## <a name="premium-storage"></a>Archiviazione Premium
Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. I dischi delle macchine virtuali (VM) che usano Archiviazione Premium archiviano i dati in unità SSD (Solid State Drive). È possibile migrare i dischi delle macchine virtuali dell'applicazione ad Archiviazione Premium di Azure per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi.

Funzionalità di Archiviazione Premium:

* Dischi di Archiviazione Premium: Archiviazione Premium di Azure supporta dischi VM che possono essere collegati a VM di Azure della serie DS, DSv2 o GS.
* BLOB di pagine Premium: Archiviazione Premium supporta i BLOB di pagine di Azure, che vengono usati per conservare dischi persistenti per Macchine virtuali di Azure.
* Archiviazione con ridondanza locale Premium: un account di Archiviazione Premium supporta solo l'opzione di replica di archiviazione con ridondanza locale e mantiene tre copie dei dati in una singola area.
* [Archiviazione Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>VM supportate da Archiviazione Premium
Archiviazione Premium supporta Macchine virtuali di Azure (VM) delle serie DS, DSv2, GS e Fs. Con le VM supportate da Archiviazione Premium è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. Non è tuttavia possibile usare i dischi di Archiviazione Premium con le macchine virtuali di serie non compatibili con Archiviazione Premium.

Di seguito sono riportate le distribuzioni di Linux convalidate con Archiviazione Premium.

| Distribuzione | Versione | Kernel supportato |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="file-storage"></a>Archiviazione file
L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard. Con l’archiviazione file di Azure è possibile migrare le applicazioni aziendali basate su server di file in Azure. Le applicazioni in esecuzione in Azure possono montare condivisioni file dalle macchine virtuali di Azure con Linux. Con la versione più recente dell’archiviazione di file, è inoltre possibile montare una condivisione di file da un'applicazione locale che supporta SMB 3.0.  Poiché le condivisioni file sono condivisioni SMB, è possibile accedervi tramite le API del file system standard.

Il servizio di archiviazione file è basato sulla stessa tecnologia dei servizi BLOB, tabelle e archiviazione code e può quindi sfruttare le funzionalità di disponibilità, durabilità, scalabilità e ridondanza geografica integrate nella piattaforma di archiviazione di Azure. Per informazioni sugli obiettivi di prestazioni e sui limiti di Archiviazione file, vedere Obiettivi di scalabilità e prestazioni per Archiviazione di Azure.

* [Come usare Archiviazione file di Azure con Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Archiviazione ad accesso frequente
Il livello di archiviazione ad accesso frequente di Azure è ottimizzato per l'archiviazione di dati a cui si accede di frequente.  L'Archiviazione ad accesso frequente è il tipo di archiviazione predefinito per gli archivi BLOB.

## <a name="cool-storage"></a>Archiviazione ad accesso sporadico
Il livello di archiviazione ad accesso sporadico di Azure è ottimizzato per l'archiviazione di dati a cui si accede di raramente e di lunga durata. Esempi di casi d'uso dell'archiviazione ad accesso sporadico includono backup, contenuti multimediali, dati scientifici, conformità e archiviazione dati. In generale, i dati usati raramente sono i candidati ideali per l'archiviazione ad accesso sporadico.

|  | livello di archiviazione ad accesso frequente | livello di archiviazione ad accesso sporadico |
|:--- |:---:|:---:|
| Disponibilità |99,9% |99% |
| Disponibilità (letture RA-GRS) |99,99% |99,9% |
| Addebiti per utilizzo |Costi di archiviazione più elevati |Costi di archiviazione più bassi |
| Accesso inferiore |Accesso superiore | |
| e costi di transazione |e costi di transazione | |

## <a name="redundancy"></a>Ridondanza
I dati dell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne durabilità e disponibilità elevata, rispettando il contratto di servizio di Archiviazione di Azure anche in caso di errori hardware temporanei.

Quando si crea un account di archiviazione, è necessario selezionare una delle opzioni di replica seguenti:

* Archiviazione con ridondanza locale (LRS)
* Archiviazione con ridondanza della zona (ZRS).
* Archiviazione con ridondanza geografica (GRS)
* Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale
Con l'archiviazione con ridondanza locale (LRS), i dati vengono replicati all'interno dell'area in cui è stato creato l'account di archiviazione. Per massimizzare la durabilità, ogni richiesta effettuata per i dati nell'account di archiviazione viene replicata per tre volte. Queste tre repliche risiedono ognuna in domini di errore e domini di aggiornamento distinti.  Una richiesta viene restituita correttamente dopo che è stata scritta in tutte e tre le repliche.

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona
Con l'archiviazione con ridondanza della zona (ZRS) i dati vengono replicati in due o tre strutture all'interno di una singola area o in due aree, fornendo una durabilità maggiore rispetto all'archiviazione con ridondanza locale. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza della zona, la durabilità dei dati è assicurata anche in caso di problemi in una delle strutture.

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica
Con l'archiviazione con ridondanza geografica (GRS) i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

### <a name="read-access-geo-redundant-storage"></a>Archiviazione con ridondanza geografica e accesso in lettura
L'archiviazione con ridondanza geografica e accesso in lettura(RA-GRS) massimizza la disponibilità dell'account di archiviazione fornendo l'accesso in sola lettura ai dati nella posizione secondaria, oltre alla replica tra due aree assicurata dall'archiviazione con ridondanza geografica. Qualora i dati diventino non disponibili nell'area primaria, l'applicazione potrà leggerli dall'area secondaria.

Per un approfondimento sulla ridondanza dell'Archiviazione di Azure, vedere:

* [Replica di Archiviazione di Azure](../storage/storage-redundancy.md)

## <a name="scalability"></a>Scalabilità
Archiviazione di Azure è estremamente scalabile, per consentire l'archiviazione e l'elaborazione di centinaia di terabyte di dati per supportare gli scenari di Big Data necessari per applicazioni scientifiche, di analisi finanziaria e multimediali. In alternativa, è possibile archiviare piccole quantità di dati necessarie per un piccolo sito Web aziendale. Qualsiasi siano le esigenze, il cliente paga solo per i dati archiviati. In Archiviazione di Azure sono al momento archiviate decine di migliaia di miliardi di oggetti univoci dei clienti e sono gestiti in media milioni di richieste al secondo.

Per gli account di archiviazione standard: un account di archiviazione standard con una frequenza totale massima di richieste di 20.000 IOPS. Il numero totale di IOPS in tutti i dischi della macchina virtuale in un account di archiviazione standard non deve superare questo limite.

Per gli account di archiviazione Premium: un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

## <a name="availability"></a>Disponibilità
Microsoft garantisce che per almeno il 99,99% (99,9% per il livello di accesso Non frequente) del tempo sarà in grado di elaborare le richieste di lettura dei dati degli account di archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), purché i tentativi non riusciti di lettura dei dati nell'area primaria vengano ripetuti nell'area secondaria.

Microsoft garantisce che per almeno il 99,9% (99% per il livello di accesso Non frequente) del tempo sarà in grado di elaborare le richieste di lettura dei dati degli account di archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS) e archiviazione con ridondanza geografica (GRS).

Microsoft garantisce che per almeno il 99,9% (99% per il livello di accesso Non frequente) del tempo sarà in grado di elaborare le richieste di scrittura dei dati negli account di archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

* [Contratto di servizio per l'archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Regioni
Azure è disponibile a livello generale in 30 aree del mondo ed è stata annunciata l'aggiunta di altre 4 aree. L'espansione geografica è una priorità per Azure, perché permette ai clienti di ottenere prestazioni più elevate e supporta i loro requisiti e preferenze in merito alla posizione dei dati.  L'area aggiunta più di recente per il lancio di Azure è la Germania.

Microsoft Cloud Germany offre un'opzione differenziata per i servizi Microsoft Cloud già disponibili in Europa, creando maggiori opportunità di innovazione e crescita economica per i clienti e i partner altamente regolati in Germania, nell'Unione europea (UE) e nell'Associazione europea di libero scambio (AELS).

La gestione dei dati dei clienti archiviati in questi nuovi data center, a Magdeburg e Francoforte, avviene sotto il controllo di un trustee dati, T-Systems International, ossia una società tedesca indipendente e sussidiaria di Deutsche Telekom. I servizi cloud commerciali di Microsoft in questi data center sono conformi alle normative sul trattamento dei dati vigenti in Germania e offrono ai clienti l'ulteriore possibilità di scegliere la modalità e il luogo di elaborazione dei dati.

* [Mappa delle aree di Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Sicurezza
Archiviazione di Azure fornisce un set completo di funzionalità di sicurezza, che consentono agli sviluppatori di creare applicazioni sicure. Lo stesso account di archiviazione può essere protetto tramite il controllo degli accessi in base al ruolo e Azure Active Directory. È possibile proteggere i dati in transito tra un'applicazione e Azure usando la crittografia lato client, HTTPS o SMB 3.0. I dati possono essere impostati per la crittografia automatica quando vengono scritti in Archiviazione di Azure con Crittografia del servizio di archiviazione di Azure (SSE). I dischi di dati e del sistema operativo usati dalle macchine virtuali possono essere impostati per la crittografia con Crittografia dischi di Azure. È possibile concedere l'accesso delegato agli oggetti dati in Archiviazione di Azure usando le firme di accesso condiviso.

### <a name="management-plane-security"></a>Sicurezza del piano di gestione
Il piano di gestione è costituito dalle risorse usate per gestire l'account di archiviazione. Questa sezione illustra il modello di distribuzione Azure Resource Manager e come usare il controllo degli accessi in base al ruolo per controllare l'accesso agli account di archiviazione. Descrive anche la gestione delle chiavi dell'account di archiviazione e come rigenerarle.

### <a name="data-plane-security"></a>Sicurezza del piano dati
Questa sezione illustra come concedere l'accesso agli oggetti dati effettivi nell'account di archiviazione, ad esempio BLOB, file, code e tabelle, usando firme di accesso condiviso e criteri di accesso archiviati. Vengono trattate anche le firme di accesso condiviso sia a livello di servizio che di account. Si vedrà anche come limitare l'accesso a un indirizzo IP specifico, o un intervallo di indirizzi IP, come limitare il protocollo usato per HTTPS e come revocare una firma di accesso condiviso senza attendere che scada.

## <a name="encryption-in-transit"></a>Crittografia in transito
Questa sezione descrive come proteggere i dati durante il trasferimento da e verso Archiviazione di Azure. Si esamineranno l'uso consigliato di HTTPS e la crittografia usata da SMB 3.0 per Condivisioni file di Azure. Si osserverà anche la crittografia lato client, che consente di crittografare i dati prima che siano trasferiti nel servizio di archiviazione in un'applicazione client e di decrittografarli dopo il trasferimento dal servizio di archiviazione.

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi
Si discuterà di Crittografia del servizio di archiviazione (SSE) e come è possibile abilitarla per un account di archiviazione, determinando la crittografia automatica dei BLOB in blocchi, BLOB di pagine e BLOB aggiunti al momento della scrittura nell'Archiviazione di Azure. Verrà illustrato anche come è possibile usare Crittografia dischi di Azure e si esamineranno differenze di base e casi relativi a Crittografia dischi rispetto a SSE e alla crittografia lato client. Si esaminerà brevemente la conformità FIPS per i computer del Governo degli Stati Uniti.

* [Guida alla sicurezza di Archiviazione di Azure](../storage/storage-security-guide.md)

## <a name="temporary-disk"></a>Disco temporaneo
Ogni VM contiene un disco temporaneo. Il disco temporaneo offre archiviazione a breve termine per applicazioni e processi ed è destinato solo all'archiviazione di dati come file di paging o di scambio. I dati presenti nel disco temporaneo potrebbero andare persi durante un [evento di manutenzione](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance) o la [ridistribuzione di una VM](virtual-machines-linux-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante un riavvio standard della VM, i dati nell'unità temporanea vengono mantenuti.

Nelle macchine virtuali Linux il disco è in genere **/dev/sdb** e viene formattato e montato in **/mnt** dall'agente Linux di Azure. Le dimensioni del disco temporaneo variano in base alle dimensioni della macchina virtuale. Per altre informazioni, vedere [Dimensioni per le macchine virtuali Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Per altre informazioni sull'uso del disco temporaneo in Azure, vedere l'articolo relativo alle [unità temporanee nelle macchine virtuali di Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>Risparmi sui costi
* [Costi di archiviazione](https://azure.microsoft.com/pricing/details/storage/)
* [Calcolatore dei costi di archiviazione](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione
* [Limiti del servizio di archiviazione](../azure-subscription-service-limits.md#storage-limits)

