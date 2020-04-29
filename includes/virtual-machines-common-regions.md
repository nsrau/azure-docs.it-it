---
title: includere il file
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5e3f25727204343de107bacb9fc99d6cfb77d76f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76020992"
---
È importante comprendere dove e come funzionano le macchine virtuali (VM) in Azure, così come le possibilità di ottimizzare le prestazioni, la disponibilità e la ridondanza. Questo articolo offre una panoramica delle funzionalità di disponibilità e ridondanza di Azure.


## <a name="what-are-azure-regions"></a>Che cosa sono le aree di Azure?
Azure è compatibile con svariati datacenter in tutto il mondo. Questi datacenter sono raggruppati in aree geografiche, per lasciare l'utente libero di scegliere dove creare le applicazioni. 

Le risorse di Azure vengono create in determinate aree geografiche, come "Stati Uniti occidentali", "Europa settentrionale" o "Asia sud-orientale". Per conoscerle, è possibile consultare l' [elenco delle aree e le relative posizioni](https://azure.microsoft.com/regions/). Ogni area ospita più data center per assicurare la ridondanza e la disponibilità. Questo approccio garantisce flessibilità quando si progettano le applicazioni e permette di creare macchine virtuali più vicine agli utenti che rispondano a requisiti legali, di conformità o fiscali.

## <a name="special-azure-regions"></a>Aree speciali di Azure
Azure ha alcune aree speciali che è possibile usare durante la compilazione di applicazioni per finalità legali o di conformità. Le aree speciali includono:

* **US Gov Virginia** e **US Gov Iowa**
  * Un'istanza logica e fisica di Azure isolata dalla rete per i partner e gli enti pubblici statunitensi, gestita da persone selezionate negli Stati Uniti. Include certificazioni di conformità aggiuntive, come [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Ulteriori informazioni su [Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).
* **Cina orientale** e **Cina settentrionale**
  * Queste aree sono disponibili nel contesto di una partnership esclusiva tra Microsoft e 21Vianet, in virtù della quale i datacenter non sono gestiti direttamente da Microsoft. Scopri di più su [Azure Cina 21ViaNet](https://www.windowsazure.cn/).
* **Germania centrale** e **Germania nord-orientale**
  * Queste aree sono disponibili tramite un modello di trustee dei dati. In base a tale modello i dati dei clienti rimangono in Germania sotto il controllo di T-Systems, una società Deutsche Telekom che funge da trustee dei dati tedesco.

## <a name="region-pairs"></a>Coppie di aree
Ogni area di Azure è associata a un'altra area con la stessa collocazione geografica (ad esempio Stati Uniti, Europa o Asia). Questo approccio consente la replica delle risorse, come lo spazio di archiviazione delle macchine virtuali, in una stessa area geografica in modo da ridurre la probabilità che calamità naturali, agitazioni, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. Tra gli ulteriori vantaggi delle coppie di aree:

* Nel caso di un'interruzione di Azure di vaste proporzioni, viene assegnata la priorità a un'area di ogni coppia, in modo da accelerare il ripristino per le applicazioni. 
* Gli aggiornamenti pianificati di Azure vengono implementati nelle coppie di aree uno alla volta, per ridurre al minimo il tempo di inattività e il rischio di interruzione delle applicazioni.
* Le coppie di dati continuano a trovarsi all'interno della stessa area geografica, ad eccezione del Brasile meridionale, per finalità fiscali e adempimenti legali.

Esempi di coppie di aree includono:

| Primaria | Secondari |
|:--- |:--- |
| Stati Uniti occidentali |Stati Uniti orientali |
| Europa settentrionale |Europa occidentale |
| Asia sud-orientale |Asia orientale |

Consultare [qui l'elenco completo delle coppie di aree](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilità delle funzionalità
Alcuni servizi o funzionalità delle VM sono disponibili solo in determinate aree geografiche, ad esempio alcuni tipi di archiviazione o dimensioni delle VM. Per alcuni servizi globali di Azure non è necessario selezionare un'area geografica specifica, come nel caso di [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), [Gestione traffico](../articles/traffic-manager/traffic-manager-overview.md) o [DNS Azure](../articles/dns/dns-overview.md). Per facilitare la progettazione dell'ambiente applicativo, è possibile controllare la [disponibilità dei servizi di Azure in ogni area geografica](https://azure.microsoft.com/regions/#services). È anche possibile [eseguire una query a livello di codice su restrizioni e dimensioni di VM in ogni area](../articles/azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Disponibilità dell'archiviazione
Conoscere le aree geografiche di Azure diventa importante quando si considerano le opzioni disponibili per la replica dell'archiviazione. A seconda del tipo di archiviazione, sono disponibili opzioni di replica diverse.

**Managed Disks di Azure**
* Archiviazione con ridondanza locale (LRS)
  * I dati vengono replicati tre volte all'interno dell'area in cui è stato creato l'account di archiviazione.

**Dischi basati su account di archiviazione**
* Archiviazione con ridondanza locale (LRS)
  * I dati vengono replicati tre volte all'interno dell'area in cui è stato creato l'account di archiviazione.
* Archiviazione con ridondanza della zona (ZRS)
  * I dati vengono replicati tre volte in due o tre strutture distribuite in un'area sola o in due aree.
* Archiviazione con ridondanza geografica (GRS)
  * I dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria.
* Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
  * I dati vengono replicati in un'area secondaria, come con la ridondanza geografica, ma risultano anche accessibili in sola lettura nell'area secondaria.

La tabella seguente fornisce una rapida panoramica delle differenze tra i tipi di replica di archiviazione:

| Strategia di replica | Archiviazione con ridondanza locale | ZRS | Archiviazione con ridondanza geografica | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| I dati vengono replicati in più strutture |No |Sì |Sì |Sì |
| I dati possono essere letti dalla località secondaria e da quella primaria. |No |No |No |Sì |
| Numero di copie di dati mantenute in nodi distinti |3 |3 |6 |6 |

Per ulteriori informazioni, consultare [qui le opzioni di replica di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md). Per altre informazioni su Managed disks, vedere [Panoramica di Azure Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Costi di archiviazione
I prezzi variano a seconda del tipo di archiviazione e della disponibilità selezionata.

**Managed Disks di Azure**
* Managed Disks Premium si basa su unità SSD, Managed Disks Standard invece su normali dischi a rotazione. Per Managed Disks Premium e Standard l'addebito avviene in base alla capacità di cui è stato effettuato il provisioning per il disco.

**Dischi non gestiti**
* L'archiviazione Premium è supportata da unità SSD. Il costo dipende dalla capacità del disco.
* L'archiviazione standard è supportata dai normali dischi a rotazione e addebitata in base alla capacità in uso e alla disponibilità di archiviazione desiderata.
  * Per l'archiviazione RA-GRS, il trasferimento dati con replica geografica prevede l'ulteriore addebito del costo della larghezza di banda per la replica dei dati in un'altra area di Azure.

Per informazioni sulle opzioni di disponibilità e sui prezzi dei vari tipi di archiviazione, vedere i [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

