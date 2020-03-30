---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 53bbee6dd75e045c2a7e95c88a0138c9859d12db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80374030"
---
Questo articolo risponde alle domande frequenti su Managed Disks e i dischi SSD Premium di Azure.

## <a name="managed-disks"></a>Managed Disks

**Che cos'è Azure Managed Disks?**

Managed Disks è una funzionalità che semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure, gestendo l'account di archiviazione per conto dell'utente. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se si crea un disco gestito Standard da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco gestito Standard creato da un disco rigido virtuale da 80 GB viene considerato come il disco Standard immediatamente successivo in termini di dimensioni, ovvero un disco S10. Il costo addebitato corrisponde al prezzo del disco S10. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Sono previsti costi di transazione per i dischi gestiti Standard?**

Sì. Sì, ogni transazione prevede un addebito. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Per un disco gestito Standard, si riceverà un addebito per le dimensioni effettive dei dati su disco o per la capacità con provisioning del disco?**

L'addebito viene effettuato in base alla capacità con provisioning del disco. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

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

* Proprietario: può gestire tutto, compresi gli accessi
* Collaboratore: può gestire tutto ad eccezione degli accessi
* Lettore: può visualizzare tutto, ma non apportare modifiche

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

È possibile generare un URI di firma di accesso condiviso (SAS) in sola lettura per il disco gestito e usarlo per copiare i contenuti in un account di archiviazione privato o in un archivio locale. È possibile usare l'URI della rete di accesso condiviso usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o [AzCopyYou](../articles/storage/common/storage-use-azcopy.md) can use the SAS URI using the Azure portal, Azure PowerShell, the Azure CLI, or AzCopy

**È possibile creare una copia del proprio disco gestito?**

I clienti possono eseguire uno snapshot dei relativi dischi gestiti e usarlo per creare un altro disco gestito.

**I dischi non gestiti sono ancora supportati?**

Sì, sono supportati sia i dischi non gestiti che quelli gestiti. È consigliabile usare i dischi gestiti per i nuovi carichi di lavoro ed eseguire la migrazione dei carichi di lavoro correnti a dischi gestiti.

**È possibile condividere il percorso dei dischi gestiti e non gestiti nella stessa macchina virtuale?**

No.

**Se si crea un disco da 128 GB e quindi si aumenta la dimensione a 130 gibibyte (GiB), verrà addebitato il costo della dimensione successiva del disco (256 GiB)?**

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
* [Elenco di modelli che usano Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Quando si crea un disco da un BLOB, è presente una relazione continua con il BLOB di origine?**

No, quando viene creato, il nuovo disco è una copia completamente autonoma del BLOB in quel determinato momento e non esiste alcuna connessione tra i due. Dopo aver creato il disco, è possibile eliminare il BLOB di origine senza modificare in alcun modo il disco appena creato.

**È possibile rinominare un disco gestito o non gestito dopo che è stato creato?**

Non è possibile rinominare i dischi gestiti. È tuttavia possibile rinominare un disco non gestito purché non sia collegato a una macchina virtuale o a un disco rigido virtuale.

**È possibile usare il partizionamento GPT in un disco di Azure?**

Le immagini di generazione 1 possono utilizzare il partizionamento DI GPT solo su dischi dati, non su dischi del sistema operativo. I dischi del sistema operativo devono usare lo stile di partizione MBR.

[Le immagini](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) di generazione 2 possono utilizzare il partizionamento GPT sul disco del sistema operativo e sui dischi dati.

**Quali tipi di disco supportano gli snapshot?**

Premium SSD, SSD standard e istantanee di supporto HDD standard. Per questi tre tipi di disco, gli snapshot sono supportati per tutte le dimensioni dei dischi (inclusi i dischi di dimensioni fino a 32 TiB). I dischi ultra non supportano le istantanee.

**Che cosa sono le prenotazioni del disco di Azure?**
La prenotazione del disco è la possibilità di acquistare un anno di spazio di archiviazione su disco in anticipo, riducendo il costo totale. Per informazioni dettagliate sulle prenotazioni disco di Azure, vedere l'articolo sull'argomento: [Informazioni su come viene applicato lo sconto sulla prenotazione al](../articles/cost-management-billing/reservations/understand-disk-reservations.md)disco di Azure.For details regarding Azure disk reservations, see our article on the subject: Understand how your reservation discount is applied to Azure Disk .

**Quali opzioni offre la prenotazione del disco di Azure?**    
La prenotazione su disco di Azure offre la possibilità di acquistare SSD Premium negli SKU specificati da P30 (1 TiB) fino a P80 (32 TiB) per un periodo di un anno. Non vi è alcuna limitazione sulla quantità minima di dischi necessari per acquistare una prenotazione del disco. Inoltre, puoi scegliere di pagare con un unico pagamento anticipato o pagamenti mensili. Non è previsto alcun costo transazionale aggiuntivo per i dischi gestiti SSD Premium.There is no additional transactional cost applied for Premium SSD Managed Disks.    

Le prenotazioni vengono effettuate sotto forma di dischi, non di capacità. In altre parole, quando si prenota un disco P80 (32 TiB), si ottiene un singolo disco P80, non è possibile dividere tale prenotazione specifica in due dischi P70 più piccoli (16 TiB). Naturalmente, è possibile prenotare il numero o il numero di dischi che si desidera, inclusi due dischi P70 (16 TiB) separati.

**Come viene applicata la prenotazione del disco di Azure?**    
La prenotazione dei dischi segue un modello simile alle istanze di macchine virtuali (VM) riservate. La differenza è che una prenotazione su disco non può essere applicata a SKU diversi, mentre un'istanza di macchina virtuale può. Per altre informazioni sulle istanze di macchine virtuali, vedere [Salvare i costi con le istanze di macchine virtuali riservate di Azure.See Costs with Azure Reserved VM Instances](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) for more information on VM instances.     

**È possibile usare l'archiviazione dei dati acquistata tramite la prenotazione dei dischi di Azure in più aree?**    
La prenotazione dei dischi di Azure viene acquistata per un'area specifica e sKU (ad esempio P30 in Stati Uniti orientali 2) e pertanto non può essere usata all'esterno di questi costrutti. È sempre possibile acquistare una prenotazione di dischi di Azure aggiuntiva per le esigenze di archiviazione su disco in altre aree o SKU.    

**Cosa succede quando scade la prenotazione dei dischi di Azure?**    
Riceverai notifiche via email 30 giorni prima della scadenza e di nuovo alla data di scadenza. Alla scadenza della prenotazione, i dischi distribuiti continueranno a essere eseguiti e verranno fatturati con le ultime tariffe con pagamento in base al [numero di azioni.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-shared-disks"></a>Dischi condivisi di AzureAzure shared disks

**La funzionalità di dischi condivisi è supportata per i dischi non gestiti o i BLOB di pagine?**

No, è supportato solo per i dischi gestiti SSD premium.

**Quali aree supportano i dischi condivisi?**

Attualmente solo Stati Uniti centro-occidentali.

**I dischi condivisi possono essere utilizzati come disco del sistema operativo?**

No, i dischi condivisi sono supportati solo per i dischi dati.

**Quali dimensioni dei dischi supportano i dischi condivisi?**

Solo gli SSD premium che sono P15 o superiore supportano i dischi condivisi.

**Se ho un SSD premium esistente, posso abilitare i dischi condivisi su di esso?**

Tutti i dischi gestiti creati con la versione API 2019-07-01 o successiva possono abilitare i dischi condivisi. A tale scopo, è necessario smontare il disco da tutte le macchine virtuali a cui è collegato. Modificare quindi `maxShares` la proprietà sul disco.

**Se non si desidera più utilizzare un disco in modalità condivisa, come è possibile disattivarlo?**

Smontare il disco da tutte le macchine virtuali a cui è collegato. Quindi modificare la proprietà maxShare sul disco su 1.

**È possibile ridimensionare un disco condiviso?**

Sì.

**È possibile abilitare l'acceleratore di scrittura su un disco in cui sono abilitati anche i dischi condivisi?**

No.

**È possibile abilitare la memorizzazione nella cache dell'host per un disco con disco condiviso abilitato?**

L'unica opzione di memorizzazione nella cache host supportata è 'None'.

## <a name="ultra-disks"></a>Dischi Ultra

**Su cosa è necessario impostare la velocità effettiva del disco ultra?**
Se non si è certi dell'impostazione della velocità effettiva del disco, è consigliabile iniziare assumendo una dimensione di I/O di 16 KiB e regolare le prestazioni durante il monitoraggio dell'applicazione. La formula è: Velocità effettiva in MBps - numero di Operazioni pari a IOPS - 16 / 1000.

**Ho configurato il mio disco su 40000 IOPS ma vedo solo 12800 IOPS, perché non vedo le prestazioni del disco?**
Oltre alla limitazione del disco, è presente una limitazione di I/O che viene imposta a livello di macchina virtuale. Assicurarsi che le dimensioni della macchina virtuale in uso siano in grado di supportare i livelli configurati nei dischi. Per informazioni dettagliate sui limiti di I/O imposti dalla macchina virtuale, vedere [Dimensioni per macchine virtuali Windows in Azure.](../articles/virtual-machines/windows/sizes.md)

**È possibile utilizzare i livelli di memorizzazione nella cache con un disco ultra?**
No, i dischi ultra non supportano i diversi metodi di memorizzazione nella cache supportati in altri tipi di disco. Impostare la memorizzazione nella cache del disco su Nessuno.Set the disk caching to None.

**È possibile collegare un disco ultra alla macchina virtuale esistente?**
È possibile che la macchina virtuale si appaia in una coppia di aree e zona di disponibilità che supporta i dischi Ultra. Per informazioni [dettagliate, vedere Introduzione ai dischi ultra.](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md)

**È possibile usare un disco ultra come disco del sistema operativo per la macchina virtuale?**
No, i dischi ultra sono supportati solo come dischi dati e sono supportati solo come dischi nativi 4K.

**Posso convertire un disco esistente in un disco ultra?**
No, ma è possibile eseguire la migrazione dei dati da un disco esistente a un disco ultra. Per eseguire la migrazione di un disco esistente a un disco ultra, collegare entrambi i dischi alla stessa macchina virtuale e copiare i dati del disco da un disco all'altro o sfruttare una soluzione di terze parti per la migrazione dei dati.

**È possibile creare istantanee per dischi ultra?**
No, le istantanee non sono ancora disponibili.

**Backup di Azure è disponibile per i dischi ultra?**
No, il supporto di Backup di Azure non è ancora disponibile.

**È possibile collegare un disco ultra a una macchina virtuale in esecuzione in un set di disponibilità?**
No, questo non è ancora supportato.

**È possibile abilitare Azure Site Recovery per le macchine virtuali usando dischi ultra?**
No, Azure Site Recovery non è ancora supportato per i dischi ultra.

## <a name="uploading-to-a-managed-disk"></a>Caricamento su un disco gestito

**È possibile caricare dati su un disco gestito esistente?**

No, il caricamento può essere utilizzato solo durante la creazione di un nuovo disco vuoto con lo stato **ReadyToUpload.**

**Come si carica su un disco gestito?**

Creare un disco gestito con la proprietà [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) di [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) impostata su "Upload", quindi è possibile caricarvi i dati.

**È possibile collegare un disco a una macchina virtuale mentre è in stato di caricamento?**

No.

**È possibile creare un'istantanea di un disco manofilato in uno stato di caricamento?**

No.

## <a name="standard-ssd-disks"></a>Dischi SSD Standard

**Cosa sono i dischi SSD Standard di Azure?**
I dischi SSD standard sono dischi standard supportati da unità SSD, ottimizzati come spazio di archiviazione conveniente per carichi di lavoro che richiedono prestazioni coerenti ai livelli di operazioni di I/O al secondo inferiori.

<a id="standard-ssds-azure-regions"></a>**Quali sono le aree attualmente supportate per i dischi SSD Standard?**
Tutte le aree di Azure supportano ora i dischi SSD Standard.

**Backup di Azure è disponibile quando si usano unità SSD Standard?**
Sì, Backup di Azure è ora disponibile.

**Come si creano i dischi SSD Standard?**
È possibile creare dischi SSD standard usando i modelli di Azure Resource Manager, SDK, PowerShell o CLI. Di seguito sono elencati i parametri necessari nel modello di Resource Manager per creare dischi SSD Standard:

* *apiVersion* per Microsoft.Computer deve essere impostato su `2018-04-01` (o versioni successive)
* Specificare *managedDisk.storageAccountType* come `StandardSSD_LRS`

L'esempio seguente mostra la sezione *properties.storageProfile.osDisk* per una macchina virtuale che usa dischi SSD Standard:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Per un esempio di modello completo di come creare un disco SSD standard con un modello, vedere [Create a VM from a Windows Image with Standard SSD Data Disks](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Creare una macchina virtuale da un'immagine Windows con dischi dati SSD standard).

**È possibile convertire i dischi esistenti in unità SSD Standard?**
Sì, Per le linee guida generali per la conversione di Azure Managed Disks fare riferimento a [Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Usare anche il valore seguente per aggiornare il tipo di disco in SSD Standard.
-AccountType StandardSSD_LRS

**Quali sono i vantaggi offerti dall'uso dei dischi SSD Standard rispetto ai dischi HDD?**
I dischi SSD standard offrono latenza, coerenza, disponibilità e affidabilità migliori rispetto ai dischi HDD. Per questo motivo, i carichi di lavoro delle applicazioni vengono eseguiti in modo molto più efficiente su unità SSD Standard. Si noti che i dischi SSD Premium rappresentano la soluzione consigliata per la maggior parte dei carichi di lavoro di produzione con I/O intensivo.

**Si possono usare dischi SSD Standard come dischi non gestiti?**
No, i dischi SSD Standard sono disponibili solo come dischi gestiti.

**I dischi SSD Standard supportano "contratti di servizio per macchine virtuali a istanza singola"?**
No, i dischi SSD Standard non supportano contratti di servizio per macchine virtuali a istanza singola. Per i contratti di servizio per macchine virtuali a istanza singola, usare dischi SSD Premium.

## <a name="migrate-to-managed-disks"></a>Eseguire la migrazione a Managed Disks

**C'è qualche impatto sulla migrazione sulle prestazioni dei dischi gestiti?**

La migrazione comporta lo spostamento del disco da una posizione di archiviazione a un'altra. Questa operazione viene orchestrata tramite copia in background dei dati, che può richiedere diverse ore per il completamento, in genere inferiore a 24 risorse a seconda della quantità di dati nei dischi. Durante questo periodo, l'applicazione può riscontrare una latenza di lettura maggiore del solito poiché alcune operazioni di lettura possono essere reindirizzate alla posizione originale e possono quindi richiedere più tempo. Non è previsto alcun impatto sulla latenza di scrittura durante questo periodo.  

**Quali modifiche sono necessarie in una configurazione del servizio Backup di Azure preesistente prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**I backup di macchine virtuali creati tramite il servizio Backup di Azure prima della migrazione continueranno a funzionare correttamente?**

Sì, i backup continueranno a funzionare senza problemi.

**Quali modifiche sono necessarie in una configurazione preesistente di Crittografia dischi di Azure prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**La migrazione automatica di un set di scalabilità di macchine virtuali esistente da dischi non gestiti a dischi gestiti è supportata?**

No. È possibile creare un nuovo set di scalabilità di macchine virtuali con Managed Disks usando l'immagine dal vecchio set di scalabilità con dischi non gestiti.

**È possibile creare un disco gestito da uno snapshot di BLOB di pagine eseguito prima della migrazione a Managed Disks?**

No. È possibile esportare uno snapshot di BLOB di pagine come BLOB di pagine e quindi creare un disco gestito dal BLOB di pagine esportato.

**È possibile eseguire il failover su macchine virtuali locali protette da Azure Site Recovery in una macchina virtuale con Managed Disks?**

Sì, è possibile scegliere di eseguire il failover su una macchina virtuale con Managed Disks.

**La migrazione su macchine virtuali di Azure protette da Azure Site Recovery tramite la replica da Azure ad Azure ha qualche ripercussione?**

No. Azure Site Recovery Azure to Azure Protection per macchine virtuali con dischi gestiti è disponibile.

**È possibile eseguire la migrazione di macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e crittografia del servizio di archiviazione

**La crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un nuovo disco gestito?**

Sì.

**Il volume di avvio è crittografato per impostazione predefinita su un disco gestito?**

Sì. Per impostazione predefinita, tutti i dischi gestiti sono crittografati, incluso il disco del sistema operativo.

**chi gestisce le chiavi di crittografia?**

Le chiavi di crittografia sono gestite da Microsoft.

**È possibile disabilitare la crittografia del servizio di archiviazione per i dischi gestiti?**

No.

**La crittografia del servizio di archiviazione è disponibile solo in aree specifiche?**

No. È disponibile in tutte le aree in cui è disponibile Managed Disks. Managed Disks è disponibile in tutte le aree pubbliche e in Germania. È disponibile anche in Cina, ma solo per le chiavi gestite da Microsoft e non per quelle gestite dal cliente.

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

No. Se però si esporta un disco rigido virtuale da un disco gestito o uno snapshot crittografato a un account di archiviazione crittografato, verrà crittografato. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dischi Premium, gestiti e non gestiti

**Se una macchina virtuale usa una serie di dimensioni che supporta i dischi SSD Premium, ad esempio DSv2, è possibile collegare dischi dati sia Premium che Standard?** 

Sì.

**È possibile collegare dischi dati sia Premium che Standard a una serie di dimensioni che non supporta dischi SSD Premium, come le serie D, Dv2, G o F?**

No. È possibile collegare solo dischi dati Standard alle macchine virtuali che non usano una serie di dimensioni con supporto di dischi SSD Premium.

**Se si crea un disco dati Premium da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco dati Premium creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco P10. Il costo addebitato corrisponde al prezzo del disco P10.

**Sono previsti costi per transazione per usare i dischi SSD Premium?**

È previsto un costo fisso per ogni dimensione di disco con provisioning di un certo numero di IOPS e di una certa velocità effettiva. Gli altri costi sono la larghezza di banda in uscita e la capacità di snapshot, se applicabile. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Quali sono i limiti per IOPS e velocità effettiva che è possibile ottenere dalla cache del disco?**

I limiti combinati per la cache e l'unità SSD locale per la serie DS sono 4.000 operazioni di I/O al secondo per core e 33 MiB al secondo per core. La serie GS offre 5,000 operazioni di I/O al secondo per core e 50 MiB al secondo per core.

**Sono supportate le unità SSD locali per le macchine virtuali di Managed Disks?**

L'unità SSD locale è un archivio temporaneo che è incluso in una macchina virtuale di Managed Disks. Questa archiviazione temporanea non comporta costi aggiuntivi. Si consiglia di non usare questa unità SSD locale per archiviare dati applicazione, perché non vengono salvati in modo permanente nell'archiviazione BLOB di Azure.

**L'uso di TRIM su dischi Premium ha ripercussioni?**

L'uso di TRIM su dischi Azure Premium o Standard non ha alcun impatto negativo.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Dimensioni dei nuovi dischi, gestiti e non gestiti

**Quali aree supportano la funzionalità di bursting per le dimensioni del disco SSD premium applicabili?**

La funzionalità di bursting è attualmente supportata in Azure West Central US.The bursting capability is currently supported in Azure West Central US.

**In quali aree sono supportate le dimensioni 4/8/16 di dischi gestiti GiB (P1/P2/P3, E1/E2/E3)?**

Queste nuove dimensioni del disco sono attualmente supportate in Azure West Central US.These new disk sizes are currently supported in Azure West Central US.

**Le dimensioni dei dischi P1/P2/P3 sono supportate per i dischi non gestiti o i BLOB di pagine?**

No, è supportato solo su dischi gestiti SSD premium. 

**Le dimensioni dei dischi E1/E2/E3 sono supportate per i dischi non gestiti o i BLOB di pagine?**

No, i dischi gestiti SSD standard di qualsiasi dimensione non possono essere usati con dischi non gestiti o BLOB di pagine.

**Qual è la dimensioni massima supportata per i dischi gestiti e il sistema operativo?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco di sistema operativo è a 2 TiB. Azure supporta fino a 32 TiB per i dischi dati gestiti.

**Quali sono le dimensioni massime del disco non gestito supportate per il sistema operativo e i dischi dati?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco non gestito del sistema operativo è di 2 TiB. Azure supporta fino a 4 TiB per i dischi non gestiti di dati.

**Quali sono le dimensioni massime supportate per un BLOB di pagine?**

Le dimensioni massime supportate da Azure per un BLOB di pagine sono di 8 TiB (8.191 GiB). Le dimensioni massime per un BLOB di pagine collegato a una macchina virtuale come dischi dati o dischi del sistema operativo sono di 4 TiB (4.095 GiB).

**È necessario usare una nuova versione degli strumenti di Azure per creare, collegare, ridimensionare e caricare dischi più grandi di 1 TiB?**

Non è necessario aggiornare gli strumenti di Azure esistenti per creare, collegare o ridimensionare i dischi di dimensioni superiori a 1 TiB. Per caricare il file VHD dall'ambiente locale direttamente in Azure come BLOB di pagine o disco non gestito, è necessario usare i set di strumenti più recenti, elencati di seguito: Sono supportati solo i caricamenti del disco rigido virtuale fino a 8 TiB.

|Strumenti di Azure      | Versioni supportate                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numero di versione 4.1.0: versione di giugno 2017 o successiva|
|Interfaccia della riga di comando di Azure v1     | Numero di versione 0.10.13: versione di maggio 2017 o successiva|
|Interfaccia della riga di comando di Azure versione 2     | Numero di versione 2.0.12: versione di giugno 2017 o successiva|
|AzCopy              | Numero di versione 6.1.0: versione di giugno 2017 o successiva|

**Le dimensioni del disco P4 e P6 sono supportate per i dischi gestiti o i BLOB di pagine?**

Le dimensioni dei dischi P4 (32 GiB) e P6 (64 GiB) non sono supportate come livelli di disco predefinito per i dischi non gestiti e i BLOB di pagine. È necessario [impostare il livello di Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) in modo esplicito su P4 e P6 per eseguire il mapping su questi livelli. Se si distribuisce un disco non gestito o un BLOB di pagine con dimensioni del disco o lunghezza del contenuto inferiori a 32 GiB o tra 32 GiB a 64 GiB senza aver impostato il livello di blob, P10 continuerà con 500 IOPS e 100 MiB/secondo e persisterà il piano tariffario mappato.

**Come viene fatturato un disco gestito Premium di dimensioni inferiori a 64 GiB creato prima dell'abilitazione dei dischi più piccoli (intorno al 15 giugno 2017)?**

I dischi Premium esistenti di dimensioni inferiori a 64 GiB continuano a essere fatturati in base al piano tariffario P10.

**Secondo quali modalità è possibile modificare il piano tariffario dei dischi Premium di dimensioni inferiori a 64 GiB da P10 a P4 o P6?**

È possibile creare uno snapshot dei dischi di piccole dimensioni e quindi creare un disco per passare automaticamente al piano tariffario a P4 o P6 in base alla dimensione del disco di cui viene effettuato il provisioning.

**È possibile ridimensionare i dischi gestiti esistenti da dimensioni inferiori a 4 tebibyte (TiB) a nuove dimensioni del disco appena introdotte fino a 32 TiB?**

Sì.

**Quali sono le dimensioni di disco più grandi supportate dal servizio Backup di Azure e Azure Site Recovery?**

La dimensione massima del disco supportata da Backup di Azure è 32 TiB (4 TiB per dischi crittografati). La dimensione massima del disco supportata da Azure Site Recovery è 8 TiB. Il supporto per i dischi più grandi fino a 32 TiB non è ancora disponibile in Azure Site Recovery.Support for the larger disks up to 32 TiB is not yet available in Azure Site Recovery.

**Quali sono le dimensioni consigliate per le macchine virtuali per dischi di dimensioni maggiori (>4 TiB) per i dischi SSD standard e HDD standard per ottenere operazioni di I/O al secondo e larghezza di banda su disco ottimizzate?**

Per ottenere la velocità effettiva del disco di SSD standard e DISCO rigido standard di grandi dimensioni (>4 TiB) oltre 500 IOPS e 60 MiB/s, è consigliabile distribuire una nuova macchina virtuale da una delle seguenti dimensioni di VM per ottimizzare le prestazioni: serie B, serie DSv2, serie Dsv3, serie ESv3, serie Fs, serie Fsv2, serie M, serie GS, serie NCv2, serie NCv3 o VM serie Ls. Il collegamento di dischi di grandi dimensioni a macchine virtuali o macchine virtuali esistenti che non utilizzano le dimensioni consigliate sopra riportate potrebbe avere prestazioni inferiori.

**Come è possibile aggiornare i dischi (>4 TiB) distribuiti durante l'anteprima delle dimensioni dei dischi maggiori per ottenere le operazioni di I/O al secondo & larghezza di banda più elevata in GA?**

È possibile arrestare e avviare la macchina virtuale a cui è collegato il disco o scollegare e ricollegare il disco. Gli obiettivi di prestazioni di dimensioni disco più grandi sono stati aumentati sia per gli SSD premium che per gli SSD standard di GA.

**In quali aree sono supportate le dimensioni dei dischi gestiti di 8 TiB, 16 TiB e 32 TiB?**

Gli 8 SKU del disco TiB, 16 TiB e 32 TiB sono supportati in tutte le aree di Azure globale, Microsoft Azure per enti pubblici e Azure China 21Vianet.

**È supportata l'abilitazione della memorizzazione nella cache dell'host su tutte le dimensioni del disco?**

Supportiamo la memorizzazione nella cache host di ReadOnly e lettura/scrittura su dimensioni del disco inferiori a 4 TiB. Per le dimensioni del disco superiori a 4 TiB, non è supportata l'impostazione dell'opzione di memorizzazione nella cache diversa da Nessuno.For disk sizes more than 4 TiB, we don't support setting caching option other than None. È consigliabile sfruttare la memorizzazione nella cache per una dimensione disco inferiore in merito alla quale sarà possibile osservare l'incremento delle prestazioni con i dati memorizzati nella cache per la macchina virtuale.

## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto. È possibile pubblicare una domanda nei commenti alla fine di questo articolo. Per interagire con il team di Archiviazione di Azure e altri membri della community in merito a questo articolo, usare il [forum MSDN di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Per richiedere funzionalità, inviare richieste e idee al [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).
