---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6cd0c72f94c020f9243a1a95faa799f1f798f36c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199150"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)

Questo articolo risponde alle domande frequenti su Managed Disks e i dischi SSD Premium di Azure.

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

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito con una sottoscrizione diversa?**

Sì.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito in un'area geografica diversa?**

 No.

**Ci sono limitazioni di scalabilità per i clienti che usano dischi gestiti?**

Managed Disks elimina i limiti legati agli account di archiviazione. Tuttavia, il limite massimo è di 50.000 dischi gestiti per area e per tipo di disco per una sottoscrizione.

**È possibile fare uno snapshot incrementale di un disco gestito?**

 No. La funzionalità snapshot corrente crea una copia completa di un disco gestito.

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

È possibile generare un URI di firma di accesso condiviso (SAS) in sola lettura per il disco gestito e usarlo per copiare i contenuti in un account di archiviazione privato o in un archivio locale. È possibile usare l'URI SAS con il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**È possibile creare una copia del proprio disco gestito?**

I clienti possono eseguire uno snapshot dei relativi dischi gestiti e usarlo per creare un altro disco gestito.

**I dischi non gestiti sono ancora supportati?**

Sì, sono supportati sia i dischi non gestiti che quelli gestiti. È consigliabile usare i dischi gestiti per i nuovi carichi di lavoro ed eseguire la migrazione dei carichi di lavoro correnti a dischi gestiti.

**È possibile condividere il percorso dei dischi gestiti e non gestiti nella stessa macchina virtuale?**

 No.

**Se crea un disco da 128 GB e si aumentano le dimensioni a 130 gibibyte (GiB), verrà addebitato per le dimensioni del disco successiva (256 GiB)?**

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

Il partizionamento GPT può essere usato solo nei dischi dati e non nei dischi del sistema operativo. I dischi del sistema operativo devono usare lo stile di partizione MBR.

## <a name="uploading-to-a-managed-disk"></a>Caricare un disco gestito

**È possibile caricare i dati in un disco gestito esistente?**

No, il caricamento può essere utilizzato solo durante la creazione di un nuovo disco vuoto con il **ReadyToUpload** dello stato.

**È possibile collegare un disco a una macchina virtuale mentre è in uno stato di caricamento?**

 No.

**Posso usare uno snapshot di un disco gestito in uno stato di caricamento?**

 No.

## <a name="standard-ssd-disks"></a>Dischi SSD Standard

**Cosa sono i dischi SSD Standard di Azure?**
I dischi SSD standard sono dischi standard supportati da unità SSD, ottimizzati come spazio di archiviazione conveniente per carichi di lavoro che richiedono prestazioni coerenti ai livelli di operazioni di I/O al secondo inferiori.

<a id="standard-ssds-azure-regions"></a>**Quali sono le aree attualmente supportate per i dischi SSD Standard?**
Tutte le aree di Azure supportano ora i dischi SSD Standard.

**Backup di Azure è disponibile quando si usano unità SSD Standard?**
Sì, Backup di Azure è ora disponibile.

**Come si creano i dischi SSD Standard?**
È possibile creare dischi SSD Standard usando i modelli di Azure Resource Manager, SDK, PowerShell o CLI. Di seguito sono elencati i parametri necessari nel modello di Resource Manager per creare dischi SSD Standard:

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
I dischi SSD standard offrono una migliore latenza, coerenza, disponibilità e affidabilità rispetto ai dischi HDD. Per questo motivo, i carichi di lavoro delle applicazioni vengono eseguiti in modo molto più efficiente su unità SSD Standard. Si noti che i dischi SSD Premium rappresentano la soluzione consigliata per la maggior parte dei carichi di lavoro di produzione con I/O intensivo.

**Si possono usare dischi SSD Standard come dischi non gestiti?**
No, i dischi SSD Standard sono disponibili solo come dischi gestiti.

**I dischi SSD Standard supportano "contratti di servizio per macchine virtuali a istanza singola"?**
No, i dischi SSD Standard non supportano contratti di servizio per macchine virtuali a istanza singola. Per i contratti di servizio per macchine virtuali a istanza singola, usare dischi SSD Premium.

## <a name="migrate-to-managed-disks"></a>Eseguire la migrazione a Managed Disks

**La migrazione può avere un impatto sulle prestazioni di Managed Disks?**

La migrazione comporta lo spostamento del disco da una posizione di archiviazione a un'altra. Questa operazione viene gestita tramite copia in background dei dati, che possono richiedere diverse ore, in genere meno di 24 ore a seconda della quantità di dati nei dischi. Durante questo periodo, l'applicazione può riscontrare una latenza di lettura maggiore del solito poiché alcune operazioni di lettura possono essere reindirizzate alla posizione originale e possono quindi richiedere più tempo. Non è previsto alcun impatto sulla latenza di scrittura durante questo periodo.  

**Quali modifiche sono necessarie in una configurazione del servizio Backup di Azure preesistente prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**I backup di macchine virtuali creati tramite il servizio Backup di Azure prima della migrazione continueranno a funzionare correttamente?**

Sì, i backup continueranno a funzionare senza problemi.

**Quali modifiche sono necessarie in una configurazione preesistente di Crittografia dischi di Azure prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**Migrazione automatica di una scalabilità di macchine virtuali impostata da dischi non gestiti a Managed Disks è supportata?**

 No. È possibile creare un nuovo set di scalabilità di macchine virtuali con Managed Disks usando l'immagine dal vecchio set di scalabilità con dischi non gestiti.

**È possibile creare un disco gestito da uno snapshot di BLOB di pagine eseguito prima della migrazione a Managed Disks?**

 No. È possibile esportare uno snapshot di BLOB di pagine come BLOB di pagine e quindi creare un disco gestito dal BLOB di pagine esportato.

**È possibile eseguire il failover su macchine virtuali locali protette da Azure Site Recovery in una macchina virtuale con Managed Disks?**

Sì, è possibile scegliere di eseguire il failover su una macchina virtuale con Managed Disks.

**La migrazione su macchine virtuali di Azure protette da Azure Site Recovery tramite la replica da Azure ad Azure ha qualche ripercussione?**

Sì. Attualmente, Azure ad Azure Site Recovery alla protezione di Azure per macchine virtuali con Managed Disks è disponibile come servizio disponibile a livello generale.

**È possibile eseguire la migrazione di macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e crittografia del servizio di archiviazione

**La crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un nuovo disco gestito?**

Sì.

**Chi gestisce le chiavi di crittografia?**

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

**Qual è la dimensioni massima supportata per i dischi gestiti e il sistema operativo?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco di sistema operativo è a 2 TiB. Azure supporta un massimo di 32 TiB per i dischi dati gestiti in Azure globale, 4 TiB nei cloud sovrani di Azure.

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
|Interfaccia della riga di comando di Azure versione 2     | Numero di versione 2.0.12: versione di luglio 2017 o successiva|
|AzCopy           | Numero di versione 6.1.0: versione di giugno 2017 o successiva|

**Le dimensioni del disco P4 e P6 sono supportate per i dischi gestiti o i BLOB di pagine?**

Le dimensioni dei dischi P4 (32 GiB) e P6 (64 GiB) non sono supportate come livelli di disco predefinito per i dischi non gestiti e i BLOB di pagine. È necessario [impostare il livello di Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) in modo esplicito su P4 e P6 per eseguire il mapping su questi livelli. Se si distribuisce un disco non gestito o un BLOB di pagine con dimensioni del disco o lunghezza del contenuto inferiori a 32 GiB o tra 32 GiB a 64 GiB senza aver impostato il livello di blob, P10 continuerà con 500 IOPS e 100 MiB/secondo e persisterà il piano tariffario mappato.

**Come viene fatturato un disco gestito Premium di dimensioni inferiori a 64 GiB creato prima dell'abilitazione dei dischi più piccoli (intorno al 15 giugno 2017)?**

I dischi Premium esistenti di dimensioni inferiori a 64 GiB continuano a essere fatturati in base al piano tariffario P10.

**Secondo quali modalità è possibile modificare il piano tariffario dei dischi Premium di dimensioni inferiori a 64 GiB da P10 a P4 o P6?**

È possibile creare uno snapshot dei dischi di piccole dimensioni e quindi creare un disco per passare automaticamente al piano tariffario a P4 o P6 in base alla dimensione del disco di cui viene effettuato il provisioning.

**È possibile ridimensionare i dischi gestiti esistenti tra dimensioni di meno di 4 tebibytes (TiB) a nuove dimensioni dei dischi appena introdotta un massimo di 32 TiB?**

Sì.

**Quali sono le dimensioni di disco più grande supportate dal servizio Backup di Azure e Azure Site Recovery?**

La dimensione del disco massima supportata dal Backup di Azure e dal servizio Azure Site Recovery è di 4 TiB. Presto verrà aggiunti il supporto per i dischi di maggiori dimensioni fino a 32 TiB.

**Quali sono le VM consigliata di dimensioni per dimensioni del disco maggiori (> 4 TiB) per unità SSD Standard e i dischi HDD Standard per ottenere con ottimizzazione per IOPS e larghezza di banda del disco?**

Per ottenere la velocità effettiva del disco di dimensioni dei dischi elevate Standard SSD e HDD Standard (> 4 TiB) oltre a 500 IOPS e a 60 MiB/s, è consigliabile distribuire una nuova macchina virtuale da una delle dimensioni della macchina virtuale seguenti per ottimizzare le prestazioni: Serie B, DSv2-series, serie Dsv3, macchine virtuali serie ESv3, serie Fs, macchine virtuali serie Fsv2, serie M, della serie GS, serie NCv2, NCv3 serie o le VM serie Ls. Collegamento di dischi di grandi dimensioni per le macchine virtuali o le macchine virtuali che non usano le dimensioni consigliate sopra esistenti potrebbe notare prestazioni inferiori.

**Come è possibile aggiornare i dischi (> 4 TiB) che sono state distribuite durante l'anteprima di dimensioni di disco più grande per poter ottenere i valori di IOPS e larghezza di banda superiore alla versione GA?**

Possibile arrestare e avviare la macchina virtuale che è collegato il disco o, scollegare e ricollegare il disco. Gli obiettivi di prestazioni di maggiori dimensioni dei dischi sono stati aumentati per unità SSD premium e standard SSDs a livello generale.

**Quali aree sono le dimensioni dei dischi gestiti di 8 TiB TiB 16 e 32 TiB supportati in?**

Il TiB 8 TiB 16, 32 TiB disco gli SKU e sono supportati in tutte le aree in Azure globale. Supporto per Microsoft Azure per enti pubblici e Azure Cina 21Vianet non è ancora disponibile.

**Abilitare la memorizzazione nella cache Host in tutte le dimensioni del disco sono supportati?**

Supportiamo Host la memorizzazione nella cache di sola lettura e lettura/scrittura sulle dimensioni di disco inferiore a 4 TiB. Per una dimensione disco superiore a 4 TiB, non è supportata l'impostazione dell'opzione di memorizzazione nella cache diversa da Nessuno. È consigliabile sfruttare la memorizzazione nella cache per una dimensione disco inferiore in merito alla quale sarà possibile osservare l'incremento delle prestazioni con i dati memorizzati nella cache per la macchina virtuale.

## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto. È possibile pubblicare una domanda nei commenti alla fine di questo articolo. Per interagire con il team di Archiviazione di Azure e altri membri della community in merito a questo articolo, usare il [forum MSDN di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Per richiedere funzionalità, inviare richieste e idee al [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).
