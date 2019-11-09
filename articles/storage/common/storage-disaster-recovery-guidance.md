---
title: Ripristino di emergenza e failover dell'account di archiviazione (anteprima) - Archiviazione di Azure
description: Archiviazione di Azure supporta il failover dell'account (anteprima) per gli account di archiviazione con ridondanza geografica. Con il failover dell'account, è possibile avviare il processo di failover per l'account di archiviazione se l'endpoint primario non è più disponibile.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b265ff8831275a9f4b84f7dac28b82ae75630f8b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889779"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Ripristino di emergenza e failover dell'account di archiviazione (anteprima) in Archiviazione di Azure

Microsoft si impegna per fare in modo che i servizi di Azure siano sempre disponibili. Possono tuttavia verificarsi interruzioni dei servizi non pianificate. Se l'applicazione richiede resilienza, Microsoft consiglia di usare l'archiviazione con ridondanza geografica, in modo che i dati vengano replicati in una seconda area. I clienti dovrebbero anche predisporre un piano di ripristino di emergenza per gestire un'interruzione dei servizi a livello di area. Un parte importante del piano di ripristino di emergenza è la preparazione del failover nell'endpoint secondario qualora l'endpoint primario non sia più disponibile. 

Archiviazione di Azure supporta il failover dell'account (anteprima) per gli account di archiviazione con ridondanza geografica. Con il failover dell'account, è possibile avviare il processo di failover per l'account di archiviazione se l'endpoint primario non è più disponibile. Il failover aggiorna l'endpoint secondario, che in questo modo diventa l'endpoint primario dell'account di archiviazione. Una volta completato il failover, i clienti possono iniziare a scrivere nel nuovo endpoint primario.

Questo articolo illustra i concetti e il processo relativi a un failover dell'account e spiega come preparare l'account di archiviazione per il ripristino con il minimo impatto per i clienti. Per informazioni su come avviare un failover dell'account nel portale di Azure o in PowerShell, vedere [Avviare un failover dell'account (anteprima)](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Scegliere l'opzione di ridondanza appropriata

Per motivi di ridondanza, tutti gli account di archiviazione vengono replicati. La scelta dell'opzione di ridondanza per l'account dipende dal grado di resilienza necessario. Per la protezione da interruzioni a livello di area, scegliere l'archiviazione con ridondanza geografica con o senza l'opzione di accesso in lettura dall'area secondaria:  

Con l'**archiviazione con ridondanza geografica** i dati vengono replicati in modo asincrono in due aree geografiche ad almeno centinaia di chilometri di distanza. Se nell'area primaria si verifica un'interruzione, l'area secondaria funge da origine ridondante per i dati. È possibile avviare un failover per trasformare l'endpoint secondario nell'endpoint primario.

L'**archiviazione con ridondanza geografica e accesso in lettura** aggiunge all'archiviazione con ridondanza geografica il vantaggio dell'accesso in lettura all'endpoint secondario. Se si verifica un'interruzione nell'endpoint primario, le applicazioni configurate per l'archiviazione con ridondanza geografica e accesso in lettura e progettate per la disponibilità elevata possono continuare la lettura dall'endpoint secondario. Microsoft consiglia l'archiviazione con ridondanza geografica e accesso in lettura per la massima resilienza per le applicazioni.

Le altre opzioni di ridondanza di Archiviazione di Azure includono l'archiviazione con ridondanza della zona, che replica i dati nelle zone di disponibilità di una singola area, e l'archiviazione con ridondanza locale, che replica i dati in un singolo data center di una singola area. Se l'account di archiviazione è configurato per l'archiviazione con ridondanza della zona o l'archiviazione con ridondanza locale, è possibile convertire tale account per usare l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura. La configurazione dell'account per l'archiviazione con ridondanza geografica comporta costi aggiuntivi. Per altre informazioni, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).

> [!NOTE]
> Archiviazione con ridondanza geografica (GZRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS) sono attualmente in anteprima, ma non sono ancora disponibili nelle stesse aree del failover dell'account gestito dal cliente. Per questo motivo, i clienti non possono attualmente gestire gli eventi di failover degli account con gli account GZRS e RA-GZRS. Durante l'anteprima, Microsoft gestirà gli eventi di failover che interessano gli account GZRS/RA-GZRS.

> [!WARNING]
> L'archiviazione con ridondanza geografica comporta il rischio di perdita di dati. Poiché i dati vengono replicati nell'area secondaria in modo asincrono, si verifica un ritardo prima che i dati scritti nell'area primaria vengano scritti nell'area secondaria. In caso di interruzione, le operazioni di scrittura nell'endpoint primario che non sono ancora state replicate nell'endpoint secondario andranno perse.

## <a name="design-for-high-availability"></a>Progettare la disponibilità elevata

È importante progettare l'applicazione per la disponibilità elevata fin dall'inizio. Per indicazioni sulla progettazione dell'applicazione e sulla pianificazione del ripristino di emergenza, vedere queste risorse di Azure:

* [Progettazione di applicazioni resilienti per Azure](/azure/architecture/checklist/resiliency-per-service): Panoramica dei concetti chiave per l'architettura di applicazioni a disponibilità elevata in Azure.
* [Elenco di controllo della disponibilità](/azure/architecture/checklist/resiliency-per-service): elenco di controllo per verificare che l'applicazione implementi le procedure di progettazione consigliate per la disponibilità elevata.
* [Progettazione di applicazioni a disponibilità elevata con RA-GRS](storage-designing-ha-apps-with-ragrs.md): linee guida di progettazione per la creazione di applicazioni per sfruttare i vantaggi di RA-GRS.
* [Esercitazione: creare un'applicazione a disponibilità elevata con archiviazione BLOB](../blobs/storage-create-geo-redundant-storage.md): un'esercitazione che illustra come creare un'applicazione a disponibilità elevata che passa automaticamente tra gli endpoint come errori e i recuperi vengono simulati. 

Tenere anche presenti queste procedure consigliate per mantenere la disponibilità elevata per i dati di Archiviazione di Azure:

* **Dischi:** Usare [backup di Azure](https://azure.microsoft.com/services/backup/) per eseguire il backup dei dischi di VM usati dalle macchine virtuali di Azure. Valutare anche l'opportunità di usare [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) per proteggere le macchine virtuali in caso di emergenza a livello di area.
* **BLOB in blocchi:** Attivare l' [eliminazione](../blobs/storage-blob-soft-delete.md) temporanea per proteggersi da eliminazioni a livello di oggetto e sovrascritture oppure copiare BLOB in blocchi in un altro account di archiviazione in un'area diversa usando [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)o la [libreria di spostamento dei dati di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **File:** Usare [AzCopy](storage-use-azcopy.md) o [Azure PowerShell](storage-powershell-guide-full.md) per copiare i file in un altro account di archiviazione in un'area diversa.
* **Tabelle:** usare [AzCopy](storage-use-azcopy.md) per esportare i dati delle tabelle in un altro account di archiviazione in un'area diversa.

## <a name="track-outages"></a>Tenere traccia delle interruzioni

I clienti possono sottoscrivere il [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/) per tenere traccia dell'integrità e dello stato di Archiviazione di Azure e di altri servizi di Azure.

Microsoft consiglia anche di progettare l'applicazione per prepararsi alla possibilità di errori di scrittura. L'applicazione deve esporre gli errori di scrittura in modo che l'utente sia avvisato di una possibile interruzione nell'area primaria.

## <a name="understand-the-account-failover-process"></a>Informazioni sul processo di failover dell'account

Il failover dell'account gestito dal cliente (anteprima) consente di eseguire il failover dell'intero account di archiviazione nell'area secondaria se quella primaria non è più disponibile per qualsiasi motivo. Quando si forza un failover nell'area secondaria, i clienti possono iniziare a scrivere i dati nell'endpoint secondario al termine del failover. Il failover richiede in genere circa un'ora.

### <a name="how-an-account-failover-works"></a>Come funziona il failover di un account

In circostanze normali, un cliente scrive i dati in un account di archiviazione di Azure nell'area primaria e tali dati vengono replicati in modo asincrono nell'area secondaria. L'immagine seguente illustra lo scenario quando l'area primaria è disponibile:

![I clienti scrivono i dati nell'account di archiviazione nell'area primaria](media/storage-disaster-recovery-guidance/primary-available.png)

Se per qualsiasi motivo l'endpoint primario non è disponibile, il cliente non può più scrivere nell'account di archiviazione. L'immagine seguente illustra lo scenario in cui l'endpoint primario non è più disponibile, ma non è ancora stato eseguito il ripristino:

![L'endpoint primario non è disponibile, quindi i clienti non possono scrivere dati](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Il cliente avvia il failover dell'account nell'endpoint secondario. Il processo di failover aggiorna la voce DNS fornita da Archiviazione di Azure in modo che l'endpoint secondario diventi il nuovo endpoint primario per l'account di archiviazione, come illustrato nell'immagine seguente:

![Il cliente avvia il failover dell'account nell'endpoint secondario](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

L'accesso in scrittura viene ripristinato per gli account di archiviazione con ridondanza geografica e di archiviazione con ridondanza geografica e accesso in lettura dopo che la voce DNS è stata aggiornata e le richieste vengono indirizzate al nuovo endpoint primario. Gli endpoint del servizio di archiviazione esistenti per BLOB, tabelle, code e file rimangono invariati dopo il failover.

> [!IMPORTANT]
> Al termine del failover, l'account di archiviazione viene configurato come account con ridondanza locale nel nuovo endpoint primario. Per riprendere la replica nel nuovo endpoint secondario, configurare l'account per usare di nuovo l'archiviazione con ridondanza geografica (archiviazione con ridondanza geografica e accesso in lettura o archiviazione con ridondanza geografica).
>
> Tenere presente che la conversione di un account di archiviazione con ridondanza locale in uno di archiviazione con ridondanza geografica e accesso in lettura di o archiviazione con ridondanza geografica comporta un costo, che si applica all'aggiornamento dell'account di archiviazione nella nuova area primaria per usare l'archiviazione con ridondanza geografica e accesso in lettura o l'archiviazione con ridondanza geografica dopo un failover.  

### <a name="anticipate-data-loss"></a>Prevenire la perdita di dati

> [!CAUTION]
> Un failover dell'account in genere comporta una perdita di dati. È importante comprendere le implicazioni dell'avvio di un failover dell'account.  

Poiché i dati vengono scritti in modo asincrono dall'area primaria all'area secondaria, si verifica sempre un ritardo prima che un'operazione di scrittura nell'area primaria venga replicata nell'area secondaria. Se l'area primaria non è più disponibile, le operazioni di scrittura più recenti potrebbero non essere ancora state replicate nell'area secondaria.

Quando si forza un failover, tutti i dati nell'area primaria vanno persi perché l'area secondaria diventa la nuova area primaria e l'account di archiviazione viene configurato come account con ridondanza locale. Tutti i dati già replicati nell'area secondaria vengono mantenuti quando si verifica il failover, ma i dati scritti nell'area primaria non replicati in quella secondaria vengono persi definitivamente. 

La proprietà **Ora ultima sincronizzazione** indica l'ora in cui è stata eseguita con certezza l'ultima operazione di scrittura dei dati dall'area primaria all'area secondaria. Tutti i dati scritti prima dell'ora dell'ultima sincronizzazione sono disponibili nell'area secondaria, mentre i dati scritti dopo l'ora dell'ultima sincronizzazione potrebbero non essere stati scritti nell'area secondaria e andare persi. Usare questa proprietà in caso di interruzione per stimare la quantità di dati che potrebbero andare persi avviando un failover dell'account. 

Come procedura consigliata, progettare l'applicazione per poter usare l'ora dell'ultima sincronizzazione per valutare la perdita di dati prevista. Se ad esempio si registrano tutte le operazioni di scrittura, è possibile confrontare l'ora delle ultime operazioni di scrittura con quella dell'ultima sincronizzazione per determinare quali operazioni di scrittura non sono state sincronizzate con l'area secondaria.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Prestare attenzione quando si effettua il failback all'area primaria originale

Dopo aver effettuato il failover dall'area primaria a quella secondaria, l'account di archiviazione viene configurato come account con ridondanza locale nella nuova area primaria. È possibile configurare nuovamente l'account per la ridondanza geografica aggiornandolo per usare l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura. Quando l'account viene nuovamente configurato per la ridondanza geografica dopo un failover, la nuova area primaria inizia immediatamente a replicare i dati nella nuova area secondaria, che era quella primaria prima del failover originale. Può tuttavia trascorrere del tempo prima che i dati esistenti nell'area primaria vengano completamente replicati nella nuova area secondaria.

Dopo che l'account di archiviazione è stato riconfigurato per la ridondanza geografica, è possibile avviare un altro failover dalla nuova area primaria alla nuova area secondaria. In questo caso, l'area primaria originale prima del failover diventa nuovamente l'area primaria e viene configurata come area con ridondanza locale. Tutti i dati nell'area primaria dopo il failover (quella secondaria originale) vanno quindi persi. Se la maggior parte dei dati nell'account di archiviazione non è stata replicata nella nuova area secondaria prima del failback, potrebbe verificarsi la perdita di una grande quantità di dati. 

Per evitare la perdita di una grande quantità di dati, controllare il valore della proprietà **Ora ultima sincronizzazione** prima di effettuare il failback. Confrontare l'ora dell'ultima sincronizzazione con le ultime in cui i dati sono stati scritti nella nuova area primaria per valutare la perdita di dati prevista. 

## <a name="initiate-an-account-failover"></a>Avviare il failover di un account

È possibile avviare un failover dell'account dal portale di Azure, da PowerShell, dall'interfaccia della riga di comando di Azure o dall'API del provider di risorse di Archiviazione di Azure. Per altre informazioni su come avviare un failover, vedere [Avviare il failover di un account (anteprima)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

Il failover dell'account è disponibile in anteprima per tutti i clienti che usano GRS o RA-GRS con distribuzioni Azure Resource Manager. Sono supportati i tipi di account di archiviazione per utilizzo generico v1, utilizzo generico v2 e BLOB. Il failover dell'account è attualmente disponibile nelle aree seguenti:

- Asia orientale
- Asia sudorientale
- Australia orientale
- Australia sudorientale
- Stati Uniti centrali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali 2

La versione di anteprima è destinata solo all'uso in ambienti non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

### <a name="register-for-the-preview"></a>Registrarsi per l'anteprima

Per eseguire la registrazione per l'anteprima, usare i comandi seguenti in PowerShell. Assicurarsi di sostituire il segnaposto tra parentesi quadre con il proprio ID sottoscrizione:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Potrebbero essere necessari 1-2 giorni per ricevere l'approvazione per l'anteprima. Per verificare che la registrazione sia stata approvata, usare il comando seguente:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Considerazione aggiuntive 

Leggere le considerazioni aggiuntive esposte in questa sezione per comprendere come le applicazioni e i servizi potrebbero essere interessati quando si forza un failover durante il periodo di anteprima.

#### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Le macchine virtuali di Azure non effettuano il failover durante un failover dell'account. Se l'area primaria non è disponibile e si effettua il failover nell'area secondaria, sarà necessario ricreare le macchine virtuali dopo il failover. 

#### <a name="azure-unmanaged-disks"></a>Dischi non gestiti di Azure

Come procedura consigliata, Microsoft consiglia di convertire i dischi non gestiti in dischi gestiti. Se tuttavia è necessario effettuare il failover di un account che contiene dischi non gestiti collegati a macchine virtuali di Azure, sarà necessario arrestare le macchine virtuali prima di avviare il failover.

I dischi non gestiti vengono archiviati come BLOB di pagine in Archiviazione di Azure. Quando una macchina virtuale è in esecuzione in Azure, eventuali dischi non gestiti collegati alla macchina virtuale vengono concessi in lease. Un failover dell'account non può continuare se è presente un lease su un BLOB. Per eseguire il failover, seguire questa procedura:

1. Prima di iniziare, prendere nota dei nomi dei dischi non gestiti, dei numeri di unità logica e della macchina virtuale a cui sono collegati. In questo modo sarà più facile ricollegare i dischi dopo il failover. 
2. Arrestare la VM.
3. Eliminare la macchina virtuale, ma conservare i file VHD per i dischi non gestiti. Prendere nota dell'ora in cui è stata eliminata la macchina virtuale.
4. Attendere che l'**ora dell'ultima sincronizzazione** venga aggiornata e che sia successiva all'ora in cui è stata eliminata la macchina virtuale. Questo passaggio è importante perché se l'endpoint secondario non è stato completamente aggiornato con i file VHD quando si verifica il failover, la macchina virtuale potrebbe non funzionare correttamente nella nuova area primaria.
5. Avviare il failover dell'account.
6. Attendere che il failover dell'account venga completato e che l'area secondaria diventi la nuova area primaria.
7. Creare una macchina virtuale nella nuova area primaria e ricollegare i dischi rigidi virtuali.
8. Avviare la nuova macchina virtuale.

Tenere presente che i dati archiviati in un disco temporaneo vanno persi quando la macchina virtuale viene arrestata.

### <a name="unsupported-features-or-services"></a>Funzionalità o servizi non supportati
Le funzionalità o i servizi seguenti non sono supportati per il failover dell'account per la versione in anteprima:

- Sincronizzazione file di Azure non supporta il failover dell'account di archiviazione. È consigliabile non effettuare il failover degli account di archiviazione contenenti condivisioni file di Azure che vengono usate come endpoint cloud in Sincronizzazione file di Azure. Il failover causerebbe l'arresto della sincronizzazione e potrebbe causare inoltre una perdita di dati imprevista nel caso di file appena disposti su livelli.  
- Non è possibile effettuare il failover di un account di archiviazione contenente BLOB archiviati. Mantenere i BLOB archiviati in un account di archiviazione separato di cui non si intende effettuare il failover.
- Non è possibile effettuare il failover di un account di archiviazione contenente BLOB in blocchi Premium. Gli account di archiviazione che supportano i BLOB in blocchi Premium non supportano attualmente la ridondanza geografica.
- Al termine del failover, le funzionalità seguenti smetteranno di funzionare se originariamente abilitate: [sottoscrizioni di eventi](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [criteri del ciclo](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)di vita, [analisi archiviazione registrazione](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Copia dei dati come alternativa al failover

Se l'account di archiviazione è configurato per l'archiviazione con ridondanza geografica e accesso in lettura, si ha accesso in lettura ai dati tramite l'endpoint secondario. Se si preferisce non effettuare il failover in caso di interruzione nell'area primaria, è possibile usare strumenti come [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) o la [libreria di spostamento dati di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) per copiare i dati dall'account di archiviazione nell'area secondaria a un altro account di archiviazione in un'area non interessata. È quindi possibile indirizzare le applicazioni a tale account di archiviazione per la disponibilità sia in lettura che in scrittura.

## <a name="microsoft-managed-failover"></a>Failover gestito da Microsoft

In casi estremi, in cui un'area va persa a causa di una grave emergenza, Microsoft potrebbe avviare un failover a livello di area. In tal caso, non è necessaria alcuna azione da parte dell'utente. Si avrà di nuovo accesso in scrittura all'account di archiviazione solo dopo il completamento del failover gestito da Microsoft. Le applicazioni possono eseguire operazioni di lettura dall'area secondaria se l'account di archiviazione è configurato per l'archiviazione con ridondanza geografica e accesso in lettura. 

## <a name="see-also"></a>Vedere anche

* [Avviare il failover di un account (anteprima)](storage-initiate-account-failover.md)
* [Progettazione di applicazioni a disponibilità elevata con RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Esercitazione: creare un'applicazione a disponibilità elevata con archiviazione BLOB](../blobs/storage-create-geo-redundant-storage.md) 
