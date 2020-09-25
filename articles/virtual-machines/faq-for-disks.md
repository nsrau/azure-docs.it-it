---
title: Domande frequenti sui dischi
description: Domande frequenti su dischi e dischi Premium (gestiti e non gestiti) di VM IaaS Linux di Azure
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c2589b47c1619dd528ab843dcf4befd0da227d16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331489"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)

Questo articolo risponde alle domande frequenti su Managed Disks e i dischi SSD Premium di Azure.

## <a name="managed-disks"></a>Managed Disks

**Che cos'è Azure Managed Disks?**

Managed Disks è una funzionalità che semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure, gestendo l'account di archiviazione per conto dell'utente. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](managed-disks-overview.md).

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

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito con una sottoscrizione diversa?**

Sì.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito in un'area geografica diversa?**

No.

**Ci sono limitazioni di scalabilità per i clienti che usano dischi gestiti?**

Managed Disks elimina i limiti legati agli account di archiviazione. Tuttavia, il limite massimo è di 50.000 dischi gestiti per area e per tipo di disco per una sottoscrizione.

**Le macchine virtuali in un set di disponibilità possono essere composte da una combinazione di dischi gestiti e non gestiti?**

No. No, i dischi nelle macchine virtuali in un set di disponibilità devono essere o tutti gestiti o tutti non gestiti. Quando si crea un set di disponibilità, è possibile scegliere il tipo di dischi da usare.

**Managed Disks è l'opzione predefinita nel portale di Azure?**

Sì.

**È possibile creare un disco vuoto gestito?**

Sì. È possibile creare un disco vuoto. Un disco gestito può essere creato indipendentemente da una macchina virtuale, ad esempio non collegandolo a una macchina virtuale.

**Qual è il numero di domini di errore supportati per un set di disponibilità con Managed Disks?**

Il numero di domini di errore supportato per i set di disponibilità con Managed Disks è di 2 o 3, a seconda dell'area in cui si trovano.

**Come viene configurato l'account di archiviazione Standard per l'impostazione della diagnostica?**

Si imposta un account di archiviazione privato per la diagnostica della macchina virtuale.

**Che tipo di supporto per il controllo degli accessi in base al ruolo è disponibile per Managed Disks?**

Managed Disks supporta tre ruoli predefiniti principali:

* Proprietario: È in grado di gestire tutti gli elementi, compresi gli accessi
* Collaboratore: Può gestire tutto ad eccezione degli accessi.
* Lettore: È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

È possibile generare un URI di firma di accesso condiviso (SAS) in sola lettura per il disco gestito e usarlo per copiare i contenuti in un account di archiviazione privato o in un archivio locale. È possibile usare l'URI SAS con il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o [AzCopy](../storage/common/storage-use-azcopy-v10.md)

**È possibile creare una copia del proprio disco gestito?**

I clienti possono eseguire uno snapshot dei relativi dischi gestiti e usarlo per creare un altro disco gestito.

**I dischi non gestiti sono ancora supportati?**

Sì, sono supportati sia i dischi non gestiti che quelli gestiti. È consigliabile usare i dischi gestiti per i nuovi carichi di lavoro ed eseguire la migrazione dei carichi di lavoro correnti a dischi gestiti.

**È possibile colocare dischi gestiti e non gestiti nella stessa VM?**

No.

**Se si crea un disco da 128 GB e si aumentano le dimensioni a 130 gibibyte (GiB), verranno addebitati i costi relativi al livello di dimensioni successivo (256 GiB)?**

Sì.

**È possibile creare dischi gestiti per l'archiviazione con ridondanza locale, l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza della zona?**

Attualmente Azure Managed Disks supporta solo dischi gestiti per l'archiviazione con ridondanza locale.

**È possibile ridurre/ridimensionare i dischi gestiti?**

No. Questa funzionalità non è attualmente supportata.

**È possibile interrompe un lease sul disco?**

No. Questa funzionalità non è supportata attualmente in quanto è presente un lease per impedire l'eliminazione accidentale quando il disco è in uso.

**È possibile modificare la proprietà del nome del computer quando si usa un disco del sistema operativo specializzato (non preparato con Utilità preparazione sistema o generalizzato) per il provisioning di una VM?**

No. Non è possibile aggiornare la proprietà del nome del computer. La nuova VM eredita la proprietà dalla VM padre usata per creare il disco del sistema operativo. 

**Dove si possono trovare modelli di Azure Resource Manager di esempio per creare macchine virtuali con dischi gestiti?**
* [Elenco di modelli che usano Managed Disks](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Quando si crea un disco da un BLOB, è presente una relazione continua con il BLOB di origine?**

No, quando viene creato, il nuovo disco è una copia completamente autonoma del BLOB in quel determinato momento e non esiste alcuna connessione tra i due. Dopo aver creato il disco, è possibile eliminare il BLOB di origine senza modificare in alcun modo il disco appena creato.

**È possibile rinominare un disco gestito o non gestito dopo che è stato creato?**

Non è possibile rinominare i dischi gestiti. È tuttavia possibile rinominare un disco non gestito purché non sia collegato a una macchina virtuale o a un disco rigido virtuale.

**È possibile usare il partizionamento GPT in un disco di Azure?**

Le immagini di prima generazione possono usare il partizionamento GPT solo sui dischi dati, non sui dischi del sistema operativo. I dischi del sistema operativo devono usare lo stile di partizione MBR.

Le [immagini di seconda generazione](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) possono usare il partizionamento GPT sul disco del sistema operativo e sui dischi dati.

**Quali tipi di dischi supportano gli snapshot?**

Gli snapshot sono supportati da SSD Premium, SDD Standard e HDD Standard. Per questi tre tipi di dischi, gli snapshot sono supportati per tutte le dimensioni di dischi (inclusi i dischi con dimensioni fino a 32 TiB). I dischi Ultra non supportano gli snapshot.

**Che cosa sono le prenotazioni dischi di Azure?**
La prenotazione dischi è la possibilità di acquistare un anno di spazio di archiviazione su disco in anticipo, riducendo i costi totali. Per informazioni dettagliate sulle prenotazioni dischi di Azure, vedere l'articolo sull'argomento: [Informazioni su come viene applicato lo sconto della prenotazione all'archiviazione su disco di Azure](../cost-management-billing/reservations/understand-disk-reservations.md).

**Quali sono le opzioni offerte dalla prenotazione dischi di Azure?**     
Prenotazione dischi di Azure offre la possibilità di acquistare unità SSD Premium negli SKU specificati da P30 (1 TiB) fino a P80 (32 TiB) per un periodo di un anno. Per acquistare una prenotazione dischi, non esiste alcuna limitazione sulla quantità minima di dischi necessari. Inoltre, è possibile scegliere di pagare con un unico pagamento anticipato o con pagamenti mensili. Non vengono applicati costi di transazione aggiuntivi per dischi gestiti SSD Premium.    

Le prenotazioni vengono effettuate sotto forma di dischi, non di capacità. In altre parole, quando si prenota un disco P80 (32 TiB), si ottiene un singolo disco P80, non è quindi possibile dividere la prenotazione specifica in due dischi P70 (16 TiB) più piccoli. Naturalmente, è possibile prenotare il numero di dischi desiderato, inclusi due dischi P70 (16 TiB) distinti.

**Come viene applicata la prenotazione dischi di Azure?**     
La prenotazione dischi segue un modello simile alle istanze di macchina virtuale (VM) riservate. La differenza consiste nel fatto che non è possibile applicare una prenotazione dischi a SKU diversi, mentre con un'istanza di macchina virtuale è possibile. Per altre informazioni sulle istanze di macchina virtuale, vedere [Risparmiare sui costi con le istanze di macchina virtuale riservate di Azure](./linux/prepay-reserved-vm-instances.md).     

**È possibile usare la propria risorsa di archiviazione dati acquistata tramite la prenotazione dischi di Azure in più aree?**     
La prenotazione dischi di Azure viene acquistata per un'area e uno SKU specifici, ad esempio P30 nell'area Stati Uniti orientali 2, e pertanto non può essere usata all'esterno di questi costrutti. È sempre possibile acquistare una prenotazione dischi di Azure aggiuntiva per le esigenze di archiviazione su disco in altre aree o SKU.    

**Che cosa accade alla scadenza della prenotazione dischi di Azure?**     
Si riceveranno notifiche tramite posta elettronica 30 giorni prima della scadenza e nuovamente alla data di scadenza. Alla scadenza della prenotazione, i dischi distribuiti continueranno a funzionare e verranno fatturati con le [tariffe con pagamento in base al consumo](https://azure.microsoft.com/pricing/details/managed-disks/) più recenti.

**I dischi SSD Standard supportano "contratti di servizio per macchine virtuali a istanza singola"?**

Sì, tutti i tipi di dischi supportano il contratto di servizio per VM a istanza singola.

### <a name="azure-shared-disks"></a>Dischi condivisi di Azure

**La funzionalità dischi condivisi è supportata per i dischi non gestiti o i BLOB di pagine?**

No, è supportato solo per dischi rigidi e dischi rigidi SSD Premium.

**Quali aree supportano i dischi condivisi?**

Per informazioni internazionali, vedere l' [articolo concettuale](./linux/disks-shared.md).

**I dischi condivisi possono essere usati come disco del sistema operativo?**

No, i dischi condivisi sono supportati solo per i dischi dati.

**Quali dimensioni dei dischi supportano i dischi condivisi?**

Per informazioni sulle dimensioni supportate, vedere l' [articolo concettuale](./linux/disks-shared.md).

**Se si dispone di un disco esistente, è possibile abilitare I dischi condivisi?**

Tutti i dischi gestiti creati con API versione 2019-07-01 o successiva possono abilitare i dischi condivisi. A tale scopo, è necessario smontare il disco da tutte le macchine virtuali a cui è collegato. Successivamente, modificare la proprietà **maxShares** sul disco.

**Se non si vuole più usare un disco in modalità condivisa, come si disabilita?**

Smontare il disco da tutte le macchine virtuali a cui è collegato. Modificare quindi la proprietà maxShare del disco impostandola su 1.

**È possibile ridimensionare un disco condiviso?**

Sì.

**È possibile abilitare l'acceleratore di scrittura in un disco in cui sono abilitati anche dischi condivisi?**

No.

**È possibile abilitare la memorizzazione nella cache dell'host per un disco in cui è abilitato il disco condiviso?**

L'unica opzione supportata per la memorizzazione nella cache dell'host è **None**.

## <a name="ultra-disks"></a>Dischi Ultra

**Qual è l'impostazione della velocità effettiva del disco Ultra?**
Se non si è sicuri del valore da impostare per la velocità effettiva del disco, è consigliabile iniziare supponendo una dimensione di I/O di 16 KiB e regolare le prestazioni partendo da tale valore mentre si monitora l'applicazione. La formula è: Velocità effettiva in MBps = N. di operazioni di I/O al secondo * 16 / 1.000.

**Il disco è stato configurato per 40.000 operazioni di I/O al secondo, ma vengono visualizzate solo 12.800 operazioni di I/O al secondo. Perché non vengono visualizzate le prestazioni del disco?**
Oltre alla limitazione del disco, viene applicata una limitazione delle operazione di I/O a livello di macchina virtuale. Verificare che le dimensioni della macchina virtuale in uso siano in grado di supportare i livelli configurati sui dischi. Per informazioni dettagliate sui limiti di i/o imposti dalla VM, vedere [dimensioni delle macchine virtuali in Azure](sizes.md).

**È possibile usare i livelli di memorizzazione nella cache con un disco Ultra?**
No, i dischi Ultra non supportano i diversi metodi di memorizzazione nella cache supportati in altri tipi di dischi. Impostare la memorizzazione nella cache del disco su **nessuno**.

**È possibile collegare un disco Ultra alla macchina virtuale esistente?**
Solo se la macchina virtuale si trova in un'area e una zona di disponibilità che supportano i dischi Ultra. Per informazioni dettagliate, vedere [Introduzione ai dischi Ultra](disks-enable-ultra-ssd.md).

**È possibile usare un disco Ultra come disco del sistema operativo per una macchina virtuale?**
No, i dischi Ultra sono supportati solo come dischi dati e sono supportati solo come dischi nativi 4K.

**È possibile convertire un disco esistente in un disco Ultra?**
No, ma è possibile eseguire la migrazione dei dati da un disco esistente a un disco Ultra. Per eseguire la migrazione di un disco esistente a un disco Ultra, collegare entrambi i dischi alla stessa macchina virtuale e copiare i dati del disco da un disco all'altro o usare una soluzione di terze parti per la migrazione dei dati.

**È possibile creare snapshot per i dischi Ultra?**
No, gli snapshot non sono ancora disponibili.

**Backup di Azure è disponibile per i dischi Ultra?**
No, il supporto di Backup di Azure non è ancora disponibile.

**È possibile collegare un disco Ultra a una macchina virtuale in esecuzione in un set di disponibilità?**
No, questa opzione non è ancora supportata.

**È possibile abilitare Azure Site Recovery per le macchine virtuali con dischi Ultra?**
No, Azure Site Recovery non è ancora supportato per i dischi Ultra.

## <a name="uploading-to-a-managed-disk"></a>Caricamento in un disco gestito

**È possibile caricare dati in un disco gestito esistente?**

No, il caricamento può essere usato solo durante la creazione di un nuovo disco vuoto con lo stato **ReadyToUpload**.

**Come è possibile caricare dati in un disco gestito?**

Creare un disco gestito con la proprietà [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) di [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) impostata su "Upload", quindi è possibile caricarvi dati.

**È possibile collegare un disco a una macchina virtuale mentre è in stato di caricamento?**

No.

**È possibile creare uno snapshot di un disco gestito in stato di caricamento?**

No.

## <a name="standard-ssd-disks"></a>Dischi SSD Standard

**Cosa sono i dischi SSD Standard di Azure?**
I dischi SSD standard sono dischi standard supportati da unità SSD, ottimizzati come spazio di archiviazione conveniente per carichi di lavoro che richiedono prestazioni coerenti ai livelli di operazioni di I/O al secondo inferiori.

<a id="standard-ssds-azure-regions"></a>**Quali sono le aree attualmente supportate per i dischi SSD Standard?**
Tutte le aree di Azure supportano ora i dischi SSD Standard.

**Backup di Azure è disponibile quando si usano unità SSD Standard?**
Sì, Backup di Azure è ora disponibile.

**Quali sono i vantaggi offerti dall'uso dei dischi SSD Standard rispetto ai dischi HDD?**
I dischi SSD Standard offrono latenza, coerenza, disponibilità e affidabilità migliori rispetto ai dischi HDD. Per questo motivo, i carichi di lavoro delle applicazioni vengono eseguiti in modo molto più efficiente su unità SSD Standard. Si noti che i dischi SSD Premium rappresentano la soluzione consigliata per la maggior parte dei carichi di lavoro di produzione con I/O intensivo.

**Si possono usare dischi SSD Standard come dischi non gestiti?**
No, i dischi SSD Standard sono disponibili solo come dischi gestiti.

## <a name="migrate-to-managed-disks"></a>Eseguire la migrazione a Managed Disks

**La migrazione può avere un impatto sulle prestazioni di Managed Disks?**

La migrazione comporta lo spostamento del disco da una posizione di archiviazione a un'altra. Questa operazione viene gestita tramite la copia dei dati in background che può richiedere diverse ore, in genere meno di 24, a seconda della quantità di dati nei dischi. Durante questo periodo, l'applicazione può riscontrare una latenza di lettura maggiore del solito poiché alcune operazioni di lettura possono essere reindirizzate alla posizione originale e possono quindi richiedere più tempo. Non è previsto alcun impatto sulla latenza di scrittura durante questo periodo.  

**Quali modifiche sono necessarie in una configurazione del servizio Backup di Azure preesistente prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**I backup di macchine virtuali creati tramite il servizio Backup di Azure prima della migrazione continueranno a funzionare correttamente?**

Sì, i backup continueranno a funzionare senza problemi.

**Quali modifiche sono necessarie in una configurazione preesistente di Crittografia dischi di Azure prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**È supportata la migrazione automatica di un set di scalabilità di macchine virtuali esistente da dischi non gestiti a Managed Disks?**

No. È possibile creare un nuovo set di scalabilità di macchine virtuali con Managed Disks usando l'immagine dal vecchio set di scalabilità con dischi non gestiti.

**È possibile creare un disco gestito da uno snapshot di BLOB di pagine eseguito prima della migrazione a Managed Disks?**

No. È possibile esportare uno snapshot di BLOB di pagine come BLOB di pagine e quindi creare un disco gestito dal BLOB di pagine esportato.

**È possibile eseguire il failover su macchine virtuali locali protette da Azure Site Recovery in una macchina virtuale con Managed Disks?**

Sì, è possibile scegliere di eseguire il failover su una macchina virtuale con Managed Disks.

**La migrazione su macchine virtuali di Azure protette da Azure Site Recovery tramite la replica da Azure ad Azure ha qualche ripercussione?**

No. È disponibile la protezione tramite Azure Site Recovery da Azure ad Azure per le macchine virtuali con Managed Disks.

**È possibile eseguire la migrazione di macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e crittografia del servizio di archiviazione

**La crittografia lato server è abilitata per impostazione predefinita quando si crea un nuovo disco gestito?**

Sì. I dischi gestiti vengono crittografati con la crittografia lato server con chiavi gestite dalla piattaforma. 

**Il volume di avvio è crittografato per impostazione predefinita in un disco gestito?**

Sì. Per impostazione predefinita, tutti i dischi gestiti sono crittografati, incluso il disco del sistema operativo.

**Chi gestisce le chiavi di crittografia?**

Le chiavi gestite dalla piattaforma vengono gestite da Microsoft. È anche possibile usare e gestire le proprie chiavi archiviate in Azure Key Vault. 

**È possibile disabilitare la crittografia lato server per i dischi gestiti?**

No.

**La crittografia lato server è disponibile solo in aree specifiche?**

No. La crittografia lato server con chiavi gestite sia dalla piattaforma che dal cliente è disponibile in tutte le aree in cui è disponibile Managed Disks. 

**Azure Site Recovery supporta la crittografia lato server con la chiave gestita dal cliente per gli scenari di ripristino di emergenza da sito locale ad Azure e da Azure ad Azure?**

Sì. 

**È possibile eseguire il backup dei dischi gestiti crittografati con la crittografia lato server con la chiave gestita dal cliente usando il servizio Backup di Azure?**

Sì.

**Le immagini e gli snapshot gestiti vengono crittografati?**

Sì. Tutte le immagini e gli snapshot gestiti vengono crittografati automaticamente. 

**È possibile convertire macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

**Un disco rigido virtuale esportato da un disco gestito o uno snapshot verrà crittografato?**

No. Se però si esporta un disco rigido virtuale da un disco gestito o uno snapshot crittografato a un account di archiviazione crittografato, verrà crittografato. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dischi Premium: gestiti e non gestiti

**Se una macchina virtuale usa una serie di dimensioni che supporta i dischi SSD Premium, ad esempio DSv2, è possibile collegare dischi dati sia Premium che Standard?** 

Sì.

**È possibile collegare dischi dati sia Premium che Standard a una serie di dimensioni che non supporta dischi SSD Premium, come le serie D, Dv2, G o F?**

No. È possibile collegare solo dischi dati Standard alle macchine virtuali che non usano una serie di dimensioni con supporto di dischi SSD Premium.

**Se si crea un disco dati Premium da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco dati Premium creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco P10. Il costo addebitato corrisponde al prezzo del disco P10.

**Sono previsti costi per transazione per usare i dischi SSD Premium?**

È previsto un costo fisso per ogni dimensione di disco con provisioning di un certo numero di IOPS e di una certa velocità effettiva. Gli altri costi sono la larghezza di banda in uscita e la capacità di snapshot, se applicabile. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Quali sono i limiti per IOPS e velocità effettiva che è possibile ottenere dalla cache del disco?**

I limiti combinati per la cache e l'unità SSD locale per la serie DS sono 4.000 operazioni di I/O al secondo per core e 33 MiB al secondo per core. La serie GS offre 5,000 operazioni di I/O al secondo per core e 50 MiB al secondo per core.

**Sono supportate le unità SSD locali per le macchine virtuali di Managed Disks?**

L'unità SSD locale è un archivio temporaneo che è incluso in una macchina virtuale di Managed Disks. Questa archiviazione temporanea non comporta costi aggiuntivi. Si consiglia di non usare questa unità SSD locale per archiviare dati applicazione, perché non vengono salvati in modo permanente nell'archiviazione BLOB di Azure.

**L'uso di TRIM su dischi Premium ha ripercussioni?**

L'uso di TRIM su dischi Azure Premium o Standard non ha alcun impatto negativo.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nuove dimensioni dei dischi: gestiti e non gestiti

**Quali aree supportano la capacità di bursting per le dimensioni del disco SSD Premium applicabili?**

La capacità di bursting è attualmente supportata in tutte le aree del cloud pubblico di Azure con il supporto per i cloud sovrani presto disponibile. 

**In quali aree sono supportate le dimensioni del disco gestito 4/8/16 GiB (P1/P2/P3, E1/E2/E3)?**

Queste nuove dimensioni del disco sono attualmente supportate in tutte le aree del cloud pubblico di Azure con il supporto per i cloud sovrani presto disponibile. 

**Le dimensioni del disco P1/P2/P3 sono supportate per i dischi non gestiti o i BLOB di pagine?**

No, sono supportate solo per i dischi gestiti SSD Premium. 

**Le dimensioni del disco E1/E2/E3 sono supportate per i dischi non gestiti o i BLOB di pagine?**

No, non è possibile usare i dischi gestiti SSD standard di qualsiasi dimensione con i dischi non gestiti o i BLOB di pagine.

**Qual è la dimensioni massima supportata per i dischi gestiti e il sistema operativo?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco del sistema operativo è 4 TiB. Azure supporta fino a 32 TiB per i dischi dati gestiti.

**Quali sono le dimensioni massime del disco non gestito supportate per il sistema operativo e i dischi dati?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco non gestito del sistema operativo è 4 TiB. Azure supporta fino a 4 TiB per i dischi non gestiti di dati.

**Quali sono le dimensioni massime supportate per un BLOB di pagine?**

Le dimensioni massime supportate da Azure per un BLOB di pagine sono di 8 TiB (8.191 GiB). Le dimensioni massime per un BLOB di pagine collegato a una macchina virtuale come dischi dati o dischi del sistema operativo sono di 4 TiB (4.095 GiB).

**È necessario usare una nuova versione degli strumenti di Azure per creare, collegare, ridimensionare e caricare dischi più grandi di 1 TiB?**

Non è necessario aggiornare gli strumenti di Azure esistenti per creare, collegare o ridimensionare i dischi di dimensioni superiori a 1 TiB. Per caricare il file VHD dall'ambiente locale direttamente in Azure come BLOB di pagine o disco non gestito, è necessario usare i set di strumenti più recenti, elencati di seguito: Sono supportati solo i caricamenti del disco rigido virtuale fino a 8 TiB.

|Strumenti di Azure      | Versioni supportate                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numero di versione 4.1.0: versione di giugno 2017 o successiva|
|Interfaccia della riga di comando di Azure v1     | Numero di versione 0.10.13: versione di maggio 2017 o successiva|
|Interfaccia della riga di comando di Azure versione 2     | Numero di versione 2.0.12: versione di luglio 2017 o successiva|
|AzCopy              | Numero di versione 6.1.0: versione di giugno 2017 o successiva|

**Le dimensioni del disco P4 e P6 sono supportate per i dischi gestiti o i BLOB di pagine?**

Le dimensioni dei dischi P4 (32 GiB) e P6 (64 GiB) non sono supportate come livelli di disco predefinito per i dischi non gestiti e i BLOB di pagine. È necessario [impostare il livello di Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) in modo esplicito su P4 e P6 per eseguire il mapping su questi livelli. Se si distribuisce un disco non gestito o un BLOB di pagine con dimensioni del disco o lunghezza del contenuto inferiori a 32 GiB o tra 32 GiB a 64 GiB senza aver impostato il livello di blob, P10 continuerà con 500 IOPS e 100 MiB/secondo e persisterà il piano tariffario mappato.

**Come viene fatturato un disco gestito Premium di dimensioni inferiori a 64 GiB creato prima dell'abilitazione dei dischi più piccoli (intorno al 15 giugno 2017)?**

I dischi Premium esistenti di dimensioni inferiori a 64 GiB continuano a essere fatturati in base al piano tariffario P10.

**Secondo quali modalità è possibile modificare il piano tariffario dei dischi Premium di dimensioni inferiori a 64 GiB da P10 a P4 o P6?**

È possibile creare uno snapshot dei dischi di piccole dimensioni e quindi creare un disco per passare automaticamente al piano tariffario a P4 o P6 in base alla dimensione del disco di cui viene effettuato il provisioning.

**È possibile ridimensionare i dischi gestiti esistenti da dimensioni inferiori a 4 tebibyte (TiB) a nuove dimensioni dei dischi fino a un massimo di 32 TiB?**

Sì.

**Quali sono le dimensioni massime dei dischi supportate dal servizio Backup di Azure e Azure Site Recovery?**

Le dimensioni massime del disco supportate da Backup di Azure sono di 32 TiB (4 TiB per i dischi crittografati). Le dimensioni massime del disco supportate da Azure Site Recovery sono di 8 TiB. Il supporto di dischi di dimensioni maggiori fino a 32 TiB non è ancora disponibile in Azure Site Recovery.

**Quali sono le dimensioni delle macchine virtuali consigliate per dischi di grandi dimensioni (> 4TiB) come SDD Standard e HDD Standard che consentono di ottimizzare le operazioni di I/O al secondo relative al disco e la larghezza di banda?**

Per raggiungere la velocità effettiva dei dischi di grandi dimensioni SSD Standard e HDD Standard (> 4 TiB) che superano le 500 operazioni di I/O al secondo e i 60 MiB/s, è consigliabile distribuire una nuova macchina virtuale da una delle dimensioni di macchina virtuale seguenti per ottimizzare le prestazioni: serie B, serie DSv2, serie Dsv3, serie ESv3, serie Fs, serie Fsv2, serie M, serie GS, serie NCv2, serie NCv3 o serie Ls. Il collegamento di dischi di grandi dimensioni a macchine virtuali esistenti o a macchine virtuali che non usano le dimensioni consigliate indicate può comportare prestazioni inferiori.

**Come è possibile aggiornare i dischi (> 4 TiB) che sono stati distribuiti durante l'anteprima di dimensioni del disco maggiori per ottenere i valori di operazioni di I/O al secondo e larghezza di banda più elevati al momento della disponibilità a livello generale?**

È possibile arrestare e avviare la macchina virtuale a cui è collegato il disco o scollegare e ricollegare il disco. Gli obiettivi di prestazioni di dimensioni del disco maggiori sono stati aumentati sia per le unità SSD Premium che per le unità SSD standard al momento della disponibilità a livello generale.

**In quali aree sono supportate le dimensioni del disco gestito di 8 TiB, 16 TiB e 32 TiB?**

Gli SKU dei dischi da 8 TiB, 16 TiB e 32 TiB sono supportati in tutte le aree in Azure globale, Microsoft Azure per enti pubblici e Azure China (21Vianet).

**L'abilitazione della memorizzazione nella cache dell'host è supportata in tutte le dimensioni del disco?**

La memorizzazione nella cache dell'host (**ReadOnly** e **lettura/scrittura**) è supportata su dimensioni del disco inferiori a 4 tib. Ciò significa che tutti i dischi di cui è stato eseguito il provisioning fino a 4095 GiB possono sfruttare i vantaggi della memorizzazione nella cache dell'host. La memorizzazione nella cache dell'host non è supportata per le dimensioni dei dischi maggiori o uguali a 4096 GiB. Ad esempio, un disco P50 Premium fornito a 4095 GiB può sfruttare i vantaggi della memorizzazione nella cache dell'host e un disco P50 di cui è stato effettuato il provisioning a 4096 GiB non può sfruttare la memorizzazione nella cache dell'host. È consigliabile sfruttare la memorizzazione nella cache per una dimensione disco inferiore in merito alla quale sarà possibile osservare l'incremento delle prestazioni con i dati memorizzati nella cache per la macchina virtuale.

## <a name="private-links-for-securely-exporting-and-importing-managed-disks"></a>Collegamenti privati per l'esportazione e l'importazione sicure Managed Disks

**Quali sono i vantaggi dell'utilizzo di collegamenti privati per l'esportazione e l'importazione di Managed Disks?**

È possibile sfruttare i collegamenti privati per limitare l'esportazione e l'importazione in Managed Disks solo dalla rete virtuale di Azure. 

**Cosa è possibile verificare che un disco possa essere esportato o importato solo tramite collegamenti privati?**

È necessario impostare la `DiskAccessId` proprietà su un'istanza di un oggetto di accesso al disco e impostare anche la proprietà NetworkAccessPolicy su `AllowPrivate` .

**È possibile collegare più reti virtuali allo stesso oggetto di accesso al disco?**

No. Attualmente, è possibile collegare un oggetto di accesso al disco a una sola rete virtuale.

**È possibile collegare una rete virtuale a un oggetto di accesso al disco in un'altra sottoscrizione?**

No. Attualmente, è possibile collegare un oggetto di accesso al disco a una rete virtuale nella stessa sottoscrizione.

**È possibile collegare una rete virtuale a un oggetto di accesso al disco in un'altra sottoscrizione?**

No. Attualmente, è possibile collegare un oggetto di accesso al disco a una rete virtuale nella stessa sottoscrizione.

**Quante esportazioni o importazioni che utilizzano lo stesso oggetto di accesso al disco possono verificarsi nello stesso momento?**

5

**È possibile usare un URI di firma di accesso condiviso di un disco/snapshot per scaricare il VHD sottostante di una macchina virtuale nella stessa subnet della subnet dell'endpoint privato associato al disco?**

Sì.

**È possibile usare un URI di firma di accesso condiviso di un disco/snapshot per scaricare il VHD sottostante di una macchina virtuale che non si trovi nella stessa subnet della subnet dell'endpoint privato non associato al disco?**

No.

## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto. È possibile pubblicare una domanda nei commenti alla fine di questo articolo. Per coinvolgere il team di archiviazione di Azure e altri membri della community in merito a questo articolo, usare la [pagina Domande e risposte di Microsoft&per archiviazione di Azure](https://docs.microsoft.com/answers/products/azure?product=storage).

Per richiedere funzionalità, inviare richieste e idee al [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).
