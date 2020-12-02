---
title: Risolvere i problemi relativi alle immagini condivise in Azure
description: Informazioni su come risolvere i problemi relativi alle raccolte di immagini condivise.
author: olayemio
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 74e1dc12be9bfec57d76357a335b46a55912f6df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500275"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Risolvere i problemi delle raccolte di immagini condivise in Azure

Se si verificano problemi durante l'esecuzione di operazioni su raccolte di immagini condivise, definizioni di immagini e versioni di immagini, eseguire di nuovo il comando non riuscito in modalità di debug. Per attivare la modalità di debug, passare l' `--debug` opzione con l'interfaccia della riga di comando di Azure e l' `-Debug` opzione con PowerShell. Dopo aver individuato l'errore, seguire questo articolo per risolvere il problema.


## <a name="creating-or-modifying-a-gallery"></a>Creazione o modifica di una raccolta ##

*Il nome della raccolta non è valido. I caratteri consentiti sono caratteri alfanumerici in inglese, con caratteri di sottolineatura e punti consentiti al centro, fino a 80 caratteri totali. Tutti gli altri caratteri speciali, inclusi i trattini, non sono consentiti.*  
**Motivo**: il nome della raccolta non soddisfa i requisiti di denominazione.  
**Soluzione alternativa**: scegliere un nome che soddisfi le condizioni seguenti: 
- Ha un limite di 80 caratteri
- Contiene solo lettere inglesi, numeri, caratteri di sottolineatura e punti
- Inizia e termina con lettere o numeri inglesi

*Il nome di entità' galleryname ' non è valido in base alla relativa regola di convalida: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Motivo**: il nome della raccolta non soddisfa i requisiti di denominazione.  
**Soluzione alternativa**: scegliere un nome per la raccolta che soddisfi le condizioni seguenti: 
- Ha un limite di 80 caratteri
- Contiene solo lettere inglesi, numeri, caratteri di sottolineatura e punti
- Inizia e termina con lettere o numeri inglesi

*Il nome di risorsa specificato <galleryname \> contiene caratteri finali non validi: <carattere \> . Il nome non può terminare con caratteri: <carattere\>*  
**Motivo**: il nome della raccolta termina con un punto o un carattere di sottolineatura.  
**Soluzione alternativa**: scegliere un nome per la raccolta che soddisfi le condizioni seguenti: 
- Ha un limite di 80 caratteri
- Contiene solo lettere inglesi, numeri, caratteri di sottolineatura e punti
- Inizia e termina con lettere o numeri inglesi

*Il percorso specificato <area \> non è disponibile per il tipo di risorsa ' Microsoft. Compute/Galleries '. L'elenco delle aree disponibili per il tipo di risorsa è...*  
**Motivo**: l'area specificata per la raccolta non è corretta o richiede una richiesta di accesso.  
**Soluzione temporanea**: verificare che il nome dell'area sia stato digitato correttamente. È possibile eseguire questo comando per visualizzare le aree a cui si ha accesso. Se l'area non è presente nell'elenco, inviare [una richiesta di accesso](/troubleshoot/azure/general/region-access-request-process).

*Non è possibile eliminare la risorsa prima che vengano eliminate le risorse annidate.*  
**Motivo**: si è tentato di eliminare una raccolta che contiene almeno una definizione di immagine esistente. Una raccolta deve essere vuota prima di poter essere eliminata.  
**Soluzione temporanea**: eliminare tutte le definizioni di immagine nella raccolta e quindi procedere con l'eliminazione della raccolta. Se la definizione dell'immagine contiene versioni di immagini, è necessario eliminare le versioni delle immagini prima di eliminare le definizioni di immagine.

*La risorsa <galleryname \> esiste già nel percorso <area \_ 1 \> nel gruppo di risorse <resourceGroup \> . Non è possibile creare una risorsa con lo stesso nome nel percorso <area \_ 2 \> . Selezionare un nuovo nome di risorsa.*  
**Motivo**: è presente una raccolta esistente nel gruppo di risorse con lo stesso nome e si è tentato di creare un'altra raccolta con lo stesso nome ma in un'area diversa.  
**Soluzione alternativa**: usare una raccolta diversa o usare un gruppo di risorse diverso.

## <a name="creating-or-modifying-image-definitions"></a>Creazione o modifica di definizioni di immagine ##

*La modifica della proprietà' galleryImage. Properties. <Property \> ' non è consentita.*  
**Motivo**: si è tentato di modificare il tipo di sistema operativo, lo stato del sistema operativo, la generazione, l'offerta, l'editore o lo SKU di Hyper-V. La modifica di queste proprietà non è consentita.  
**Soluzione alternativa**: creare invece una nuova definizione di immagine.

*La risorsa <galleryname/imageDefinitionName \> esiste già nel percorso <area \_ 1 \> nel gruppo di risorse <resourceGroup \> . Non è possibile creare una risorsa con lo stesso nome nel percorso <area \_ 2 \> . Selezionare un nuovo nome di risorsa.*  
**Motivo**: è presente una definizione di immagine esistente nella stessa raccolta e nello stesso gruppo di risorse con lo stesso nome. Si è tentato di creare un'altra definizione di immagine con lo stesso nome e nella stessa raccolta ma in un'area diversa.  
**Soluzione alternativa**: usare un nome diverso per la definizione dell'immagine oppure inserire la definizione dell'immagine in una raccolta o in un gruppo di risorse diverso.

*Il nome di risorsa specificato <galleryname \> /<imageDefinitionName \> contiene caratteri finali non validi: <carattere \> . Il nome non può terminare con caratteri: <carattere\>*  
**Motivo**: il nome del <imageDefinitionName \> termina con un punto o un carattere di sottolineatura.  
**Soluzione temporanea**: scegliere un nome per la definizione dell'immagine che soddisfi le condizioni seguenti: 
- Ha un limite di 80 caratteri
- Contiene solo lettere inglesi, numeri, caratteri di sottolineatura, trattini e punti
- Inizia e termina con lettere o numeri inglesi.

*Il nome dell'entità <imageDefinitionName \> non è valido in base alla relativa regola di convalida: ^ [^ \_ \\ w] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Motivo**: il nome del <imageDefinitionName \> termina con un punto o un carattere di sottolineatura.  
**Soluzione temporanea**: scegliere un nome per la definizione dell'immagine che soddisfi le condizioni seguenti: 
- Ha un limite di 80 caratteri
- Contiene solo lettere inglesi, numeri, caratteri di sottolineatura, trattini e punti
- Inizia e termina con lettere o numeri inglesi

*Il nome dell'asset galleryImage. Properties. Identifier. <\> la proprietà non è valida. Non può essere vuoto. I caratteri consentiti sono lettere maiuscole o minuscole, cifre, trattino (-), punto (.), carattere di sottolineatura ( \_ ). I nomi non possono terminare con un punto (.). La lunghezza del nome non può superare <caratteri numerici \> .*  
**Motivo**: il valore del server di pubblicazione, dell'offerta o dello SKU non soddisfa i requisiti di denominazione.  
**Soluzione alternativa**: scegliere un valore che soddisfi le condizioni seguenti: 
- Ha un limite di 128 caratteri per l'autore o il limite di 64 caratteri per l'offerta e lo SKU
- Contiene solo lettere inglesi, numeri, trattini, caratteri di sottolineatura e punti
- Non termina con un punto

*Non è possibile eseguire l'operazione richiesta sulla risorsa nidificata. La risorsa padre <raccoltaname \> non è stata trovata.*  
**Motivo**: non è presente alcuna raccolta con il nome <galleryname \> nella sottoscrizione e nel gruppo di risorse correnti.  
**Soluzione temporanea**: verificare che i nomi della raccolta, della sottoscrizione e del gruppo di risorse siano corretti. In caso contrario, creare una nuova raccolta denominata <galleryname \> .

*Il percorso specificato <area \> non è disponibile per il tipo di risorsa ' Microsoft. Compute/Galleries '. L'elenco delle aree disponibili per il tipo di risorsa è...*  
**Motivo**: il nome dell'area <non \> è corretto o richiede una richiesta di accesso.  
**Soluzione temporanea**: verificare che il nome dell'area sia stato digitato correttamente. È possibile eseguire questo comando per visualizzare le aree a cui si ha accesso. Se l'area non è presente nell'elenco, inviare [una richiesta di accesso](/troubleshoot/azure/general/region-access-request-process).

*Non è possibile serializzare il valore: <valore \> come tipo:' ISO-8601'., ISO8601Error: l'indicatore dell'ora iso 8601 non è presente. Impossibile analizzare la stringa DateTime <valore\>*  
**Motivo**: il valore specificato per la proprietà non è formattato correttamente come data.  
**Soluzione temporanea**: specificare una data nel formato aaaa-mm-gg, aaaa-mm-dd'T'HH: mm: sszzz o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-valido.

*Non è stato possibile convertire la stringa in DateTimeOffset: <valore \> . Percorso ' Properties. <Property \> '*  
**Motivo**: il valore specificato per la proprietà non è formattato correttamente come data.  
**Soluzione temporanea**: specificare una data nel formato aaaa-mm-gg, aaaa-mm-dd'T'HH: mm: sszzz o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-valido.

*EndOfLifeDate deve essere impostato su una data futura.*  
**Motivo**: la proprietà della data di fine vita non è formattata correttamente come data successiva alla data odierna.  
**Soluzione temporanea**: specificare una data nel formato aaaa-mm-gg, aaaa-mm-dd'T'HH: mm: sszzz o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-valido.

*argomento--<proprietà \> : valore int non valido: valore <\>*  
**Motivo**: <proprietà \> accetta solo valori integer e il valore di <\> non è un numero intero.  
**Soluzione alternativa**: scegliere un valore integer.

*Il valore minimo di <proprietà \> non deve essere maggiore del valore massimo della proprietà <\> .*  
**Motivo**: il valore minimo specificato per <proprietà \> è superiore al valore massimo fornito per la proprietà <\> .  
**Soluzione alternativa**: modificare i valori in modo che il valore minimo sia minore o uguale al valore massimo.

*Immagine della raccolta: <imageDefinitionName \> identificata da (Editore: <Publisher \> , offerta: <offerta \> , SKU: <SKU \> ) esiste già. Scegliere un'altra combinazione di editore, offerta, SKU.*  
**Motivo**: si è tentato di creare una nuova definizione di immagine con lo stesso server di pubblicazione, l'offerta e la tripletta dello SKU come definizione di immagine esistente nella stessa raccolta.  
**Soluzione temporanea**: all'interno di una raccolta, tutte le definizioni di immagine devono avere una combinazione univoca di server di pubblicazione, offerta e SKU. Scegliere una combinazione univoca oppure scegliere una nuova raccolta e creare nuovamente la definizione dell'immagine.

*Non è possibile eliminare la risorsa prima che vengano eliminate le risorse annidate.*  
**Motivo**: si è tentato di eliminare una definizione di immagine che contiene le versioni dell'immagine. Prima di poter eliminare la definizione di un'immagine, è necessario che sia vuota.  
**Soluzione temporanea**: eliminare tutte le versioni dell'immagine all'interno della definizione dell'immagine e quindi procedere con l'eliminazione della definizione dell'immagine.

*Impossibile associare il parametro <proprietà \> . Non è possibile convertire il valore <valore \> nel tipo <PropertyType \> . Impossibile associare il nome dell'identificatore <valore \> a un nome di enumeratore valido. Specificare uno dei seguenti nomi di enumeratore e riprovare: <choice1 \> , <choice2 \> ,...*  
**Motivo**: la proprietà ha un elenco limitato di valori possibili e <valore \> non è uno di essi.  
**Soluzione alternativa**: scegliere uno dei possibili valori di <Choice \> .

*Impossibile associare il parametro <proprietà \> . Non è possibile convertire il valore <valore \> nel tipo &quot; System. DateTime&quot;*  
**Motivo**: il valore specificato per la proprietà non è formattato correttamente come data.  
**Soluzione temporanea**: specificare una data nel formato aaaa-mm-gg, aaaa-mm-dd'T'HH: mm: sszzz o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-valido.

*Impossibile associare il parametro <proprietà \> . Non è possibile convertire il valore <valore \> nel tipo &quot; System. Int32&quot;*  
**Motivo**: <proprietà \> accetta solo valori integer e il valore di <\> non è un numero intero.  
**Soluzione alternativa**: scegliere un valore integer.

*Il tipo di account di archiviazione ZRS non è supportato in questa area.*  
**Motivo**: è stata scelta l'archiviazione con ridondanza della zona standard (ZRS) in un'area che non è ancora supportata.  
**Soluzione alternativa**: modificare il tipo di account di archiviazione in **Premium \_ con ridondanza locale** o **\_ con ridondanza locale standard**. Per un [elenco aggiornato delle aree](../storage/common/storage-redundancy.md#zone-redundant-storage) con ZRS Preview abilitata, vedere la documentazione.

## <a name="creating-or-updating-image-versions"></a>Creazione o aggiornamento delle versioni delle immagini ##

*Il percorso specificato <area \> non è disponibile per il tipo di risorsa ' Microsoft. Compute/Galleries '. L'elenco delle aree disponibili per il tipo di risorsa è...*  
**Motivo**: il nome dell'area <non \> è corretto o richiede una richiesta di accesso.  
**Soluzione temporanea**: verificare che il nome dell'area sia stato digitato correttamente. È possibile eseguire questo comando per visualizzare le aree a cui si ha accesso. Se l'area non è presente nell'elenco, inviare [una richiesta di accesso](/troubleshoot/azure/general/region-access-request-process).

*Non è possibile eseguire l'operazione richiesta sulla risorsa nidificata. Impossibile trovare la risorsa padre <galleryname/imageDefinitionName \> .*  
**Motivo**: non è presente alcuna raccolta con il nome <galleryname/imageDefinitionName \> nella sottoscrizione e nel gruppo di risorse correnti.  
**Soluzione temporanea**: verificare che i nomi della raccolta, della sottoscrizione e del gruppo di risorse siano corretti. In caso contrario, creare una nuova raccolta con il nome <galleryname \> e/o una definizione di immagine denominata <imageDefinitionName \> nel gruppo di risorse indicato.

*Impossibile associare il parametro <proprietà \> . Non è possibile convertire il valore <valore \> nel tipo &quot; System. DateTime&quot;*  
**Motivo**: il valore specificato per la proprietà non è formattato correttamente come data.  
**Soluzione temporanea**: specificare una data nel formato aaaa-mm-gg, aaaa-mm-dd'T'HH: mm: sszzz o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-valido.

*Impossibile associare il parametro <proprietà \> . Non è possibile convertire il valore <valore \> nel tipo &quot; System. Int32&quot;*  
**Motivo**: <proprietà \> accetta solo valori integer e il valore di <\> non è un numero intero.  
**Soluzione alternativa**: scegliere un valore integer.

*Le aree del profilo di pubblicazione della versione dell'immagine della raccolta <publishingRegions \> devono contenere il percorso della versione dell'immagine <sourceRegion\>*  
**Causa**: il percorso dell'immagine di origine (<sourceRegion \> ) deve essere incluso nell'elenco <publishingRegions \> .  
**Soluzione alternativa**: includere <sourceRegion \> nell'elenco <publishingRegions \> .

*Il valore <valore \> della proprietà <parametro non \> è compreso nell'intervallo. Il valore deve essere compreso tra <minValue \> e <MaxValue \> , inclusi.*  
**Motivo**: <valore non \> è compreso nell'intervallo dei valori possibili per <proprietà \> .  
**Soluzione alternativa**: scegliere un valore compreso nell'intervallo tra <minValue \> e <MaxValue \> , inclusi.

*Impossibile trovare l'origine <resourceID \> . Verificare che l'origine esista e che si trovi nella stessa area della versione dell'immagine della raccolta creata.*  
**Causa**: non esiste un'origine in <ResourceId \> o l'origine in <ResourceId \> non si trova nella stessa area dell'immagine della raccolta creata.  
**Soluzione temporanea**: verificare che il valore <ResourceId \> sia corretto e che l'area di origine della versione dell'immagine della raccolta corrisponda all'area del valore di <ResourceId \> .

*Non è consentito modificare la proprietà' galleryImageVersion. Properties. storageProfile. <devariante \> . Source.ID '.*  
**Causa**: non è possibile modificare l'ID di origine di una versione dell'immagine della raccolta dopo la creazione.  
**Soluzione alternativa**: verificare che l'ID di origine corrisponda all'ID di origine esistente, modificare il numero di versione della versione dell'immagine oppure eliminare la versione dell'immagine corrente e riprovare.

*I numeri LUN duplicati sono stati rilevati nei dischi dati di input. Il numero LUN deve essere univoco per ogni disco dati.*  
**Motivo**: quando si crea una versione dell'immagine usando un elenco di dischi e/o snapshot del disco, due o più dischi o snapshot del disco hanno lo stesso LUN.  
**Soluzione alternativa**: rimuovere o modificare i LUN duplicati.

*Gli ID di origine duplicati si trovano nei dischi di input. L'ID di origine deve essere univoco per ogni disco.*  
**Motivo**: quando si crea una versione dell'immagine usando un elenco di dischi e/o snapshot del disco, due o più dischi o snapshot del disco hanno lo stesso ID risorsa.  
**Soluzione alternativa**: rimuovere o modificare gli ID di origine disco duplicati.

*ID di proprietà <resourceID \> nel percorso ' Properties. storageProfile. <diskImages \> . Source.ID ' non valido. È previsto un ID risorsa completo che inizia con '/subscriptions/{subscriptionId}' o '/providers/{resourceProviderNamespace}/'.*  
**Motivo**: il valore del <ResourceId non \> è formattato correttamente.  
**Soluzione alternativa**: verificare che l'ID risorsa sia corretto.

*ID origine: <resourceID \> deve essere un'immagine gestita, una macchina virtuale o un'altra versione dell'immagine della raccolta*  
**Motivo**: il valore del <ResourceId non \> è formattato correttamente.  
**Soluzione alternativa**: se si usa una macchina virtuale, un'immagine gestita o una versione dell'immagine della raccolta come immagine di origine, verificare che l'ID di risorsa della macchina virtuale, dell'immagine gestita o della versione dell'immagine della raccolta sia corretto.

*ID origine: <resourceID \> deve essere un disco gestito o uno snapshot.*  
**Motivo**: il valore del <ResourceId non \> è formattato correttamente.  
**Soluzione alternativa**: se si usano dischi e/o snapshot del disco come origini per la versione dell'immagine, verificare che gli ID di risorsa dei dischi e/o degli snapshot del disco siano corretti.

*Non è possibile creare la versione dell'immagine della raccolta da: <resourceID \> poiché lo stato del sistema operativo nell'immagine della raccolta padre (<OsState \_ 1 \> ) non è <OsState \_ 2 \> .*  
**Motivo**: lo stato del sistema operativo (generalizzato o specializzato) non corrisponde allo stato del sistema operativo specificato nella definizione dell'immagine.  
**Soluzione alternativa**: scegliere un'origine basata su una macchina virtuale con lo stato del sistema operativo di <OsState \_ 1 \> o creare una nuova definizione di immagine per le macchine virtuali basate su <OsState \_ 2 \> .

*La risorsa con ID ' <resourceID \> ' ha una generazione di hypervisor diversa [' <V # \_ 1 \> '] rispetto alla generazione dell'hypervisor di immagini della raccolta padre [' <V # \_ 2 \> ']*  
**Motivo**: la generazione dell'hypervisor della versione dell'immagine non corrisponde alla generazione dell'hypervisor specificata nella definizione dell'immagine. Il sistema operativo per la definizione dell'immagine è <V # \_ 1 \> e il sistema operativo della versione dell'immagine è <v # \_ 2 \> .  
**Soluzione alternativa**: scegliere un'origine con la stessa generazione di hypervisor della definizione dell'immagine oppure creare/scegliere una nuova definizione di immagine con la stessa generazione di hypervisor della versione dell'immagine.

*La risorsa con ID ' <resourceID \> ' ha un tipo di sistema operativo diverso [' <OsType \_ 1 \> '] rispetto alla generazione del tipo di sistema operativo dell'immagine della raccolta padre [' <OsType \_ 2 \> ']*  
**Motivo**: la generazione dell'hypervisor della versione dell'immagine non corrisponde alla generazione dell'hypervisor specificata nella definizione dell'immagine. Il sistema operativo per la definizione dell'immagine è <OsType \_ 1 \> e il sistema operativo per la versione dell'immagine è <OsType \_ 2 \> .  
**Soluzione alternativa**: scegliere un'origine con lo stesso sistema operativo (Linux/Windows) della definizione dell'immagine oppure creare/scegliere una nuova definizione di immagine con lo stesso sistema operativo di generazione della versione dell'immagine.

*La macchina virtuale di origine <resourceID \> non può contenere un disco del sistema operativo temporaneo.*  
**Causa**: l'origine in <ResourceId \> contiene un disco del sistema operativo temporaneo. La raccolta di immagini condivise attualmente non supporta dischi del sistema operativo temporanei.  
**Soluzione alternativa**: scegliere un'origine diversa in base a una macchina virtuale che non usa un disco del sistema operativo temporaneo.

*La macchina virtuale di origine <resourceID \> non può contenere il disco [' <DiskID \> '] archiviato in un tipo di account UltraSSD.*  
**Motivo**: il disco <DiskID \> è un disco Ultra SSD. La raccolta di immagini condivise attualmente non supporta Ultra SSD dischi.  
**Soluzione temporanea**: usare un'origine che contiene solo SSD Premium, SDD standard e/o HDD standard Managed Disks.

*È necessario creare la macchina virtuale di origine <resourceID \> da Managed Disks.*  
**Motivo**: la macchina virtuale in <ResourceId \> Usa dischi non gestiti.  
**Soluzione alternativa**: usare un'origine basata su una macchina virtuale che contiene solo SSD Premium, SDD standard e/o HDD standard Managed Disks.

*Troppe richieste sull'origine ' <resourceID \> '. Ridurre il numero di richieste sull'origine o attendere del tempo prima di riprovare.*  
**Causa**: l'origine di questa versione dell'immagine è attualmente in fase di limitazione a causa di un numero eccessivo di richieste.  
**Soluzione temporanea**: provare a creare la versione dell'immagine in un secondo momento.

*Il set di crittografia del disco ' <diskEncryptionSetID \> ' deve trovarsi nella stessa sottoscrizione ' <subscriptionID \> ' della risorsa della raccolta.*  
**Motivo**: i set di crittografia del disco possono essere usati solo nella stessa sottoscrizione e nella stessa area in cui sono stati creati.  
**Soluzione temporanea**: creare o usare un set di crittografia nella stessa sottoscrizione e nella stessa area della versione dell'immagine.

*Origine crittografata:' <resourceID \> ' si trova in un ID sottoscrizione diverso da quello della sottoscrizione della versione dell'immagine della raccolta ' <subscriptionID \_ 1 \> ' corrente. Riprovare con un'origine non crittografata o usare la sottoscrizione ' <subcriptionID \_ 2' dell'origine \> per creare la versione dell'immagine della raccolta.*  
**Causa**: la raccolta di immagini condivise non supporta attualmente la creazione di versioni di immagini in un'altra sottoscrizione da un'altra immagine di origine se l'immagine di origine è crittografata.  
**Soluzione alternativa**: usare un'origine non crittografata o creare la versione dell'immagine nella stessa sottoscrizione dell'origine.

*Il set di crittografia del disco <diskEncryptionSetID \> non è stato trovato.*  
**Motivo**: la crittografia del disco potrebbe non essere corretta.  
**Soluzione temporanea**: verificare che l'ID risorsa del set di crittografia del disco sia corretto.

*Il nome della versione dell'immagine non è valido. Il nome della versione dell'immagine deve seguire il numero principale (int). Minore (int). Formato patch (int), ad esempio: 1.0.0, 2018.12.1 e così via.*  
**Motivo**: il formato valido per una versione dell'immagine è tre numeri interi separati da un punto. Il nome della versione dell'immagine non soddisfa il formato valido.  
**Soluzione alternativa**: usare un nome di versione dell'immagine che segue il formato principale (int). Minore (int). Patch (int). Ad esempio: 1.0.0. o 2018.12.1.

*Il valore del parametro galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. dataDiskImages. diskEncryptionSetId non è valido*  
**Motivo**: l'ID di risorsa del set di crittografia del disco utilizzato in un'immagine del disco dati utilizza un formato non valido.  
**Soluzione alternativa**: verificare che l'ID di risorsa del set di crittografia del disco segua il formato/subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

*Il valore del parametro galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId non è valido.*  
**Motivo**: l'ID di risorsa del set di crittografia del disco usato nell'immagine del disco del sistema operativo usa un formato non valido.  
**Soluzione alternativa**: verificare che l'ID di risorsa del set di crittografia del disco segua il formato/subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

*Non è possibile specificare un nuovo LUN di crittografia delle immagini del disco dati [<numero \> ] con una crittografia del disco impostata nell'area [<Region \> ] per la richiesta di aggiornamento della versione dell'immagine della raccolta. Per aggiornare questa versione, rimuovere il nuovo lun. Se è necessario modificare le impostazioni di crittografia dell'immagine del disco dati, è necessario creare una nuova versione dell'immagine della raccolta con le impostazioni corrette.*  
**Motivo**: è stata aggiunta la crittografia al disco dati di una versione di immagine esistente. Non è possibile aggiungere la crittografia a una versione di immagine esistente.  
**Soluzione temporanea**: creare una nuova versione dell'immagine della raccolta o rimuovere le impostazioni di crittografia aggiuntive.

*L'origine della versione dell'artefatto della raccolta può essere specificata solo direttamente in storageProfile o nei singoli dischi dati o del sistema operativo. È possibile fornire solo un tipo di origine (immagine utente, snapshot, disco, macchina virtuale).*  
**Causa**: manca l'ID origine.  
**Soluzione temporanea**: verificare che sia presente l'ID origine dell'origine.

*Impossibile trovare l'origine: <resourceID \> . Verificare che l'origine esista.*  
**Causa**: l'ID risorsa dell'origine potrebbe non essere corretto.  
**Soluzione alternativa**: verificare che l'ID risorsa dell'origine sia corretto.

*È necessario un set di crittografia del disco per il disco ' galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId ' nell'area di destinazione ' <area \_ 1 \> ' perché è usato il set di crittografia del disco ' <diskEncryptionSetId \> ' per il disco corrispondente nell'area ' <Region \_ 2 \> '*  
**Motivo**: la crittografia è stata usata nel disco del sistema operativo in <area \_ 2 \> , ma non nell'area <\_ 1 \> .  
**Soluzione alternativa**: se si usa la crittografia nel disco del sistema operativo, usare la crittografia in tutte le aree.

*È necessario un set di crittografia del disco per il disco ' LUN <Number \> ' nell'area di destinazione ' <area \_ 1 \> ' perché è usato il set di crittografia del disco ' <diskEncryptionSetID \> ' per il disco corrispondente nell'area ' <Region \_ 2 \> '*  
**Motivo**: la crittografia è stata usata nel disco dati al numero di <lun \> nell'area <\_ 2 \> , ma non nell'area <\_ 1 \> .  
**Soluzione alternativa**: se si usa la crittografia in un disco dati, usare la crittografia in tutte le aree.

*\>In Encryption. dataDiskImages è stato specificato un LUN non valido [numero <]. Il lun deve essere uno dei valori seguenti: [' 0, 9'].*  
**Motivo**: il lun specificato per la crittografia non corrisponde ad alcuno dei LUN per i dischi collegati alla macchina virtuale.  
**Soluzione alternativa**: modificare il lun nella crittografia per il lun di un disco dati presente nella macchina virtuale.

*Sono stati specificati lun ' <Number ' duplicati \> nell'area di destinazione ' <Region \> ' Encryption. dataDiskImages.*  
**Motivo**: le impostazioni di crittografia usate in <area hanno \> specificato almeno due volte un lun.  
**Soluzione alternativa**: modificare il lun nell'area <\> per assicurarsi che tutti i LUN siano univoci nell'area <\> .

*OSDiskImage e DataDiskImage non possono puntare allo stesso BLOB <sourceID\>*  
**Motivo**: le origini per il disco del sistema operativo e almeno un disco dati non sono univoche.  
**Soluzione alternativa**: modificare l'origine per il disco del sistema operativo e/o i dischi dati per garantire che il disco del sistema operativo e ogni disco dati siano univoci.

*Le aree duplicate non sono consentite nelle aree di pubblicazione di destinazione.*  
**Motivo**: un'area viene elencata più volte tra le aree di pubblicazione.  
**Soluzione alternativa**: rimuovere l'area duplicata.

*L'aggiunta di nuovi dischi dati o la modifica del LUN di un disco dati in un'immagine esistente non è consentita.*  
**Motivo**: una chiamata di aggiornamento alla versione dell'immagine contiene un nuovo disco dati o un nuovo LUN per un disco.  
**Soluzione alternativa**: usare i LUN e i dischi dati della versione di immagine esistente.

*Il set di crittografia del disco <diskEncryptionSetID \> deve trovarsi nella stessa sottoscrizione <subscriptionID \> della risorsa della raccolta.*  
**Motivo**: la raccolta di immagini condivise non supporta attualmente l'uso di una crittografia del disco impostata in una sottoscrizione diversa.  
**Soluzione temporanea**: creare la versione dell'immagine e la crittografia del disco impostate nella stessa sottoscrizione.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Creazione o aggiornamento di una macchina virtuale o di set di scalabilità da una versione di immagine ##

*Il client dispone dell'autorizzazione per eseguire l'azione ' Microsoft. Compute/Galleries/images/Versions/Read ' nell'ambito <resourceID \> , tuttavia il tenant corrente <tenantId1 \> non è autorizzato ad accedere alla sottoscrizione collegata <subscriptionId2 \> .*  
**Motivo**: la macchina virtuale o il set di scalabilità è stato creato tramite un'immagine sig in un altro tenant. Si è tentato di apportare una modifica alla macchina virtuale o al set di scalabilità, ma non si è autorizzati ad accedere alla sottoscrizione che possiede l'immagine.  
**Soluzione temporanea**: contattare il proprietario della sottoscrizione della versione dell'immagine per concedere l'accesso in lettura alla versione dell'immagine.

*L'immagine della raccolta <resourceID \> non è disponibile nell'area dell'area <\> . Contattare l'immagine Owner per eseguire la replica in questa area oppure modificare l'area richiesta.*  
**Motivo**: la macchina virtuale viene creata in un'area non inclusa nell'elenco delle aree pubblicate per l'immagine della raccolta.  
**Soluzione alternativa**: replicare l'immagine nell'area o creare una macchina virtuale in una delle aree di pubblicazione dell'immagine della raccolta.

*Il parametro ' osProfile ' non è consentito.*  
**Motivo**: sono stati specificati nome utente amministratore, password o chiavi SSH per una macchina virtuale creata da una versione di immagine specializzata.  
**Soluzione alternativa**: non includere il nome utente, la password o le chiavi SSH dell'amministratore se si intende creare una macchina virtuale da tale immagine. In caso contrario, usare una versione di immagine generalizzata e specificare il nome utente, la password o le chiavi SSH dell'amministratore.

*Manca il parametro obbligatorio ' osProfile ' (null).*  
**Motivo**: la VM viene creata da un'immagine generalizzata e manca il nome utente, la password o le chiavi SSH dell'amministratore. Poiché le immagini generalizzate non conservano il nome utente, la password o le chiavi SSH dell'amministratore, questi campi devono essere specificati durante la creazione di una macchina virtuale o di un set di scalabilità.  
**Soluzione alternativa**: specificare il nome utente, la password o le chiavi SSH dell'amministratore oppure usare una versione di immagine specializzata.

*Non è possibile creare la versione dell'immagine della raccolta da: <resourceID \> poiché lo stato del sistema operativo nell'immagine della raccolta padre (' Specialized ') non è' generalizzato '.*  
**Causa**: la versione dell'immagine viene creata da un'origine generalizzata, ma la relativa definizione padre è specializzata.  
**Soluzione temporanea**: creare la versione dell'immagine usando un'origine specializzata o usare una definizione padre generalizzata.

*Non è possibile aggiornare il set di scalabilità di macchine virtuali <vmssName \> perché lo stato corrente del sistema operativo del set di scalabilità di macchine virtuali è generalizzato, che è diverso dall'immagine aggiornata dello stato del sistema operativo della raccolta.*  
**Causa**: l'immagine di origine corrente per il set di scalabilità è un'immagine di origine generalizzata, ma è in fase di aggiornamento con un'immagine di origine specializzata. L'immagine di origine corrente e la nuova immagine di origine per un set di scalabilità devono essere dello stesso stato.  
**Soluzione alternativa**: per aggiornare il set di scalabilità, usare una versione di immagine generalizzata.

*Il set di crittografia del disco <diskEncryptionSetId \> nella raccolta di immagini condivise <VersionId \> appartiene alla sottoscrizione <subscriptionId1 \> e non può essere usato con la risorsa '' nella sottoscrizione <subscriptionId2\>*  
**Motivo**: il set di crittografia del disco usato per crittografare la versione dell'immagine si trova in una sottoscrizione diversa rispetto alla sottoscrizione per ospitare la versione dell'immagine.  
**Soluzione alternativa**: usare la stessa sottoscrizione per la versione dell'immagine e la crittografia del disco impostate.

*La creazione della macchina virtuale o del set di scalabilità di macchine virtuali richiede molto tempo.*  
**Soluzione temporanea**: verificare che il **OSType** della versione dell'immagine che si sta tentando di creare la VM o il set di scalabilità di macchine virtuali abbia lo stesso **OSType** dell'origine usato per creare la versione dell'immagine. 

## <a name="creating-a-disk-from-an-image-version"></a>Creazione di un disco da una versione di immagine ##

*Il valore del parametro imageReference non è valido.*  
**Motivo**: si è tentato di esportare da una versione dell'immagine sig in un disco ma è stata usata una posizione lun che non esiste nell'immagine.    
**Soluzione alternativa**: verificare la versione dell'immagine per visualizzare le posizioni dei LUN in uso.

## <a name="sharing-resources"></a>Condivisione di risorse

La condivisione delle risorse della raccolta immagini, della definizione dell'immagine e della versione di immagine tra le sottoscrizioni viene abilitata tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md) 

## <a name="replication-speed"></a>Velocità di replica

Usare il flag **--expand ReplicationStatus** per verificare se la replica in tutte le aree di destinazione specificate è stata completata. In caso contrario, attendere fino a sei ore per il completamento del processo. In caso di esito negativo, attivare nuovamente il comando per creare e replicare la versione dell'immagine. Se sono presenti numerose aree di destinazione in cui viene replicata la versione dell'immagine, è consigliabile eseguire la replica in fasi.

## <a name="azure-limits-and-quotas"></a>Limiti e quote di Azure 

[Limiti e quote di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) si applicano a tutte le risorse della raccolta di immagini condivisa, della definizione dell'immagine e della versione dell'immagine. Assicurarsi di essere entro i limiti per le sottoscrizioni. 


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [raccolte di immagini condivise](./linux/shared-image-galleries.md).