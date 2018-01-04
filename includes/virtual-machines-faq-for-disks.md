# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)

Questo articolo risponde alle domande frequenti su Managed Disks e Archiviazione Premium di Azure.

## <a name="managed-disks"></a>Managed Disks

**Che cos'è Azure Managed Disks?**

Managed Disks è una funzionalità che semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure, gestendo l'account di archiviazione per conto dell'utente. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se si crea un disco gestito Standard da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco gestito Standard creato da un disco rigido virtuale da 80 GB viene considerato come il disco Standard immediatamente successivo in termini di dimensioni, ovvero un disco S10. Il costo addebitato corrisponde al prezzo del disco S10. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Sono previsti costi di transazione per i dischi gestiti Standard?**

Sì. Sì, ogni transazione prevede un addebito. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Per un disco gestito Standard, si riceverà un addebito per le dimensioni effettive dei dati su disco o per la capacità con provisioning del disco?**

L'addebito viene effettuato in base alla capacità con provisioning del disco. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**In che modo il prezzo della versione Premium dei dischi gestiti è diverso da quello dei dischi non gestiti?**

Il prezzo della versione Premium dei dischi gestiti è uguale a quello della versione Premium dei dischi non gestiti.

**È possibile modificare il tipo di account di archiviazione (Standard o Premium) dei dischi gestiti?**

Sì. È possibile modificare il tipo di account di archiviazione dei dischi gestiti tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

Sì. Sì, è possibile esportare i dischi gestiti tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito con una sottoscrizione diversa?**

di serie

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito in un'area geografica diversa?**

di serie

**Ci sono limitazioni di scalabilità per i clienti che usano dischi gestiti?**

Managed Disks elimina i limiti legati agli account di archiviazione. Tuttavia, il numero di dischi gestiti per ogni sottoscrizione è limitato a 2.000 per impostazione predefinita. È possibile aumentare questo numero rivolgendosi al supporto.

**È possibile fare uno snapshot incrementale di un disco gestito?**

di serie La funzionalità snapshot corrente crea una copia completa di un disco gestito. Tuttavia è previsto un supporto futuro per gli snapshot incrementali.

**Le macchine virtuali in un set di disponibilità possono essere composte da una combinazione di dischi gestiti e non gestiti?**

di serie No, i dischi nelle macchine virtuali in un set di disponibilità devono essere o tutti gestiti o tutti non gestiti. Quando si crea un set di disponibilità, è possibile scegliere il tipo di dischi da usare.

**Managed Disks è l'opzione predefinita nel portale di Azure?**

Sì. 

**È possibile creare un disco vuoto gestito?**

Sì. È possibile creare un disco vuoto. Un disco gestito può essere creato indipendentemente da una macchina virtuale, ad esempio non collegandolo a una macchina virtuale.

**Qual è il numero di domini di errore supportati per un set di disponibilità con Managed Disks?**

Il numero di domini di errore supportato per i set di disponibilità con Managed Disks è di 2 o 3, a seconda dell'area in cui si trovano.

**Come viene configurato l'account di archiviazione Standard per l'impostazione della diagnostica?**

Si imposta un account di archiviazione privato per la diagnostica della macchina virtuale. In futuro, si intende estendere la diagnostica anche a Managed Disks.

**Che tipo di supporto per il controllo degli accessi in base al ruolo è disponibile per Managed Disks?**

Managed Disks supporta tre ruoli predefiniti principali:

* Proprietario: può gestire tutto, compresi gli accessi
* Collaboratore: può gestire tutto ad eccezione degli accessi
* Lettore: può visualizzare tutto, ma non apportare modifiche

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

È possibile ottenere un URI di firma di accesso condiviso di sola lettura per il disco gestito e usarla per copiare i contenuti in un account di archiviazione privato o in un archivio locale.

**È possibile creare una copia del proprio disco gestito?**

I clienti possono eseguire uno snapshot dei relativi dischi gestiti e usarlo per creare un altro disco gestito.

**I dischi non gestiti sono ancora supportati?**

Sì. Sì, sono supportati sia i dischi gestiti che quelli non gestiti. È consigliabile usare i dischi gestiti per i nuovi carichi di lavoro ed eseguire la migrazione dei carichi di lavoro correnti a dischi gestiti.


**Se si crea un disco da 128 GB e si aumentano le dimensioni a 130 GB, verranno addebitati i costi relativi al livello di dimensioni successivo (512 GB)?**

Sì.

**È possibile creare dischi gestiti per l'archiviazione con ridondanza locale, l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza della zona?**

Attualmente Azure Managed Disks supporta solo dischi gestiti per l'archiviazione con ridondanza locale.

**È possibile ridurre/ridimensionare i dischi gestiti?**

di serie Questa funzionalità non è attualmente supportata. 

**È possibile modificare la proprietà del nome del computer quando si usa un disco del sistema operativo specializzato (non preparato con Utilità preparazione sistema o generalizzato) per il provisioning di una VM?**

di serie Non è possibile aggiornare la proprietà del nome del computer. La nuova VM eredita la proprietà dalla VM padre usata per creare il disco del sistema operativo. 

**Dove si possono trovare modelli di Azure Resource Manager di esempio per creare macchine virtuali con dischi gestiti?**
* [Elenco di modelli che usano Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="migrate-to-managed-disks"></a>Eseguire la migrazione a Managed Disks 

**Quali modifiche sono necessarie in una configurazione del servizio Backup di Azure preesistente prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche. 

**I backup di macchine virtuali creati tramite il servizio Backup di Azure prima della migrazione continueranno a funzionare correttamente?**

Sì, i backup continueranno a funzionare senza problemi.

**Quali modifiche sono necessarie in una configurazione preesistente di Crittografia dischi di Azure prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche. 

**È supportata la migrazione automatica di un set di scalabilità di macchine virtuali di Microsoft Azure (VMSS) da dischi non gestiti a Managed Disks?**

di serie È possibile creare un nuovo set di scalabilità di macchine virtuali di Microsoft Azure con Managed Disks usando l'immagine dal vecchio set di scalabilità con dischi non gestiti. 

**È possibile creare un disco gestito da uno snapshot di BLOB di pagine eseguito prima della migrazione a Managed Disks?**

di serie È possibile esportare uno snapshot di BLOB di pagine come BLOB di pagine e quindi creare un disco gestito dal BLOB di pagine esportato. 

**È possibile eseguire il failover su macchine virtuali locali protette da Azure Site Recovery in una macchina virtuale con Managed Disks?**

Sì, è possibile scegliere di eseguire il failover su una macchina virtuale con Managed Disks.

**La migrazione su macchine virtuali di Azure protette da Azure Site Recovery (ASR) tramite la replica da Azure ad Azure ha qualche ripercussione?**

Sì. La protezione tramite Azure Site Recovery (ASR) da Azure ad Azure non è supportata per le macchine virtuali con Managed Disks. Saranno devono essere supportati da Q1 CY2018 fine. 

**È possibile eseguire la migrazione di macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e crittografia del servizio di archiviazione 

**La crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un nuovo disco gestito?**

Sì.

**Chi gestisce le chiavi di crittografia?**

Le chiavi di crittografia sono gestite da Microsoft.

**È possibile disabilitare la crittografia del servizio di archiviazione per i dischi gestiti?**

di serie

**La crittografia del servizio di archiviazione è disponibile solo in aree specifiche?**

di serie È disponibile in tutte le aree in cui è disponibile Managed Disks. Managed Disks è disponibile in tutte le aree pubbliche e in Germania.

**Come si può determinare se un disco gestito è crittografato?**

Si può scoprire quando è stato creato il disco gestito tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Se è stato creato dopo il 9 giugno 2017, il disco è crittografato. 

**Come è possibile crittografare i dischi esistenti creati prima del 10 giugno 2017?**

A partire dal 10 giugno 2017, i nuovi dati scritti nei dischi gestiti esistenti vengono crittografati automaticamente. È anche prevista l'implementazione della crittografia dei dati esistenti, che verrà eseguita in modo asincrono in background. Se è necessario crittografare i dati esistenti adesso, creare una copia del disco. I nuovi dischi verranno crittografati.

* [Copiare i dischi gestiti tramite l'interfaccia della riga di comando di Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiare i dischi gestiti tramite PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Le immagini e gli snapshot gestiti vengono crittografati?**

Sì. Tutte le immagini e gli snapshot gestiti creati dopo il 9 giugno 2017 vengono crittografati automaticamente. 

**È possibile convertire macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

**Un disco rigido virtuale esportato da un disco gestito o uno snapshot verrà crittografato?**

di serie Se però si esporta un disco rigido virtuale da un disco gestito o uno snapshot crittografato a un account di archiviazione crittografato, verrà crittografato. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dischi Premium, gestiti e non gestiti

**Se una macchina virtuale usa una serie di dimensioni che supporta Archiviazione Premium, ad esempio DSv2, è possibile collegare dischi dati sia Premium che Standard?** 

Sì.

**È possibile collegare dischi sia Premium che Standard a una serie di dimensioni che non supporta Archiviazione Premium, come le serie D, Dv2, G o F?**

di serie È possibile collegare solo dischi dati Standard alle macchine virtuali che non usano una serie di dimensioni con supporto di Archiviazione Premium.

**Se si crea un disco dati Premium da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco dati Premium creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco P10. Il costo addebitato corrisponde al prezzo del disco P10.

**Sono previsti costi per transazione per usare Archiviazione Premium?**

È previsto un costo fisso per ogni dimensione di disco con provisioning di un certo numero di IOPS e di una certa velocità effettiva. Gli altri costi sono la larghezza di banda in uscita e la capacità di snapshot, se applicabile. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Quali sono i limiti per IOPS e velocità effettiva che è possibile ottenere dalla cache del disco?**

I limiti combinati per la cache e l'unità SSD locale per la serie DS sono 4.000 IOPS per core e 33 MB al secondo per core. La serie GS offre 5,000 IOPS per core e 50 MB al secondo per core.

**Sono supportate le unità SSD locali per le macchine virtuali di Managed Disks?**

L'unità SSD locale è un archivio temporaneo che è incluso in una macchina virtuale di Managed Disks. Questa archiviazione temporanea non comporta costi aggiuntivi. Si consiglia di non usare questa unità SSD locale per archiviare dati applicazione, perché non vengono salvati in modo permanente nell'archiviazione BLOB di Azure.

**L'uso di TRIM su dischi Premium ha ripercussioni?**

L'uso di TRIM su dischi Azure Premium o Standard non ha alcun impatto negativo.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Dimensioni dei nuovi dischi, gestiti e non gestiti

**Quali sono le dimensioni massime supportate per i dischi dati e del sistema operativo?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta dischi di dimensioni fino a 2 TB. Azure supporta dischi del sistema operativo di dimensioni massime pari a 2 TB. Azure supporta fino a 4 TB per i dischi dati. 

**Quali sono le dimensioni massime supportate per un BLOB di pagine?**

Le dimensioni massime supportate da Azure per un BLOB di pagine sono di 8 TB (8.191 GB). Non sono supportati BLOB di pagine di dimensioni superiori a 4 TB (4.095 GB) collegati a una VM come dischi dati o del sistema operativo.

**È necessario usare una nuova versione degli strumenti di Azure per creare, collegare, ridimensionare e caricare dischi più grandi di 1 TB?**

Non è necessario aggiornare gli strumenti di Azure esistenti per creare, collegare o ridimensionare i dischi di dimensioni superiori a 1 TB. Per caricare il file VHD dall'ambiente locale direttamente in Azure come BLOB di pagine o disco non gestito, è necessario usare i set di strumenti più recenti:

|Strumenti di Azure      | Versioni supportate                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numero di versione 4.1.0: versione di giugno 2017 o successiva|
|Interfaccia della riga di comando di Azure v1     | Numero di versione 0.10.13: versione di maggio 2017 o successiva|
|AzCopy           | Numero di versione 6.1.0: versione di giugno 2017 o successiva|

Il supporto per l'interfaccia della riga di comando di Azure v2 e Azure Storage Explorer sarà disponibile a breve. 

**Le dimensioni del disco P4 e P6 sono supportate per i dischi gestiti o i BLOB di pagine?**

di serie Le dimensioni del disco P4 (32 GB) e P6 (64 GB) sono supportate solo per i dischi gestiti. Il supporto per dischi non gestiti e BLOB di pagine sarà disponibile a breve.

**Come viene fatturato un disco gestito Premium di dimensioni inferiori a 64 GB creato prima dell'abilitazione dei dischi più piccoli (intorno al 15 giugno 2017)?**

I dischi Premium esistenti di dimensioni inferiori a 64 GB continuano a essere fatturati in base al piano tariffario P10. 

**Come è possibile modificare il piano tariffario dei dischi Premium di dimensioni inferiori a 64 GB da P10 a P4 o P6?**

È possibile creare uno snapshot dei dischi di piccole dimensioni e quindi creare un disco per passare automaticamente al piano tariffario a P4 o P6 in base alla dimensione del disco di cui viene effettuato il provisioning. 


## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto. È possibile pubblicare una domanda nei commenti alla fine di questo articolo. Per interagire con il team di Archiviazione di Azure e altri membri della community in merito a questo articolo, usare il [forum MSDN di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Per richiedere funzionalità, inviare richieste e idee al [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).
