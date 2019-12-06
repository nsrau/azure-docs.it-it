---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 067ac0f7f000f749f61d302db4c5c6b856e698a2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875538"
---
Raccolta immagini condivise è un servizio che consente di creare struttura e organizzazione in base alle immagini gestite. Le raccolte di immagini condivise forniscono:

- Replica globale gestita delle immagini.
- Controllo delle versioni e raggruppamento di immagini per una gestione più semplice.
- Immagini a disponibilità elevata con account di archiviazione con ridondanza della zona (ZRS) in aree che supportano zone di disponibilità. L'archiviazione con ridondanza della zona offre una resilienza migliore in caso di errori specifici di una zona.
- Condivisione tra sottoscrizioni e anche tra Active Directory tenant (AD) usando il controllo degli accessi in base al ruolo.
- Ridimensionare le distribuzioni con le repliche di immagini in ogni area.

Usando una raccolta di immagini condivise è possibile condividere le immagini con utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione. Le immagini condivise possono essere replicate in più aree, per un ridimensionamento più rapido delle distribuzioni.

Un'immagine gestita è una copia di una macchina virtuale completa (inclusi eventuali dischi dati allegati) o solo del disco del sistema operativo, a seconda del modo in cui si crea l'immagine. Quando si crea una macchina virtuale dall'immagine, la copia dei dischi rigidi virtuali dell'immagine viene usata per creare i dischi per la nuova macchina virtuale. L'immagine gestita rimane nella risorsa di archiviazione e può essere usata ripetutamente per creare nuove macchine virtuali.

Se si dispone di un numero elevato di immagini gestite che è necessario gestire e si desidera renderle disponibili nell'azienda, è possibile usare una raccolta di immagini condivise come repository che semplifica la condivisione delle immagini. 

La funzionalità Raccolta di immagini condivise presenta più tipi di risorse:

| Gruppi | Description|
|----------|------------|
| **Immagine gestita** | Un'immagine di base che può essere usata da sola o usata per creare una **versione dell'immagine** in una raccolta di immagini. Le immagini gestite vengono create da VM [generalizzate](#generalized-and-specialized-images) . Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Snapshot** | Copia di un disco rigido virtuale che può essere utilizzato per creare una **versione dell'immagine**. Gli snapshot possono essere ricavati da una VM [specializzata](#generalized-and-specialized-images) (uno che non è stato generalizzato), quindi usati singolarmente o con snapshot di dischi dati, per creare una versione di immagine specializzata.
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini vengono definite all'interno di una raccolta e contengono informazioni sull'immagine e sui requisiti per l'uso all'interno dell'organizzazione. È possibile includere informazioni come se l'immagine sia generalizzata o specializzata, il sistema operativo, i requisiti di memoria minimi e massimi e le note sulla versione. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>

![Immagine che mostra in che modo è possibile avere più versioni di un'immagine nella propria raccolta](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definizioni di immagini

Le definizioni di immagine sono un raggruppamento logico per le versioni di un'immagine. La definizione dell'immagine include informazioni sui motivi per cui è stata creata l'immagine, sul sistema operativo per cui è stata creata e sulle informazioni sull'utilizzo dell'immagine. La definizione di un'immagine è simile a un piano per tutti i dettagli relativi alla creazione di un'immagine specifica. Non si distribuisce una macchina virtuale da una definizione di immagine, ma dalla versione dell'immagine creata dalla definizione.

Esistono tre parametri per ogni definizione di immagine usati in combinazione- **autore**, **offerta** e **SKU**. Questi vengono usati per trovare una definizione di immagine specifica. È possibile avere versioni delle immagini che condividono uno o due, ma non tutti e tre i valori.  Di seguito, un esempio di tre definizioni di immagini con i relativi valori:

|Definizione delle immagini|Editore|Offerta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finanza|Back-end|
|myImage2|Contoso|Finanza|Front-end|
|myImage3|Test|Finanza|Front-end|

Questi tre presentano set univoci di valori. Il formato è simile a quello in cui è attualmente possibile specificare il server di pubblicazione, l'offerta e lo SKU per le [Immagini di Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) in Azure PowerShell per ottenere la versione più recente di un'immagine del Marketplace. Ogni definizione di immagine deve avere un set univoco di questi valori.

Di seguito sono riportati altri parametri che è possibile impostare nella definizione dell'immagine, in modo da poter tenere traccia più facilmente delle risorse:

* Stato del sistema operativo: è possibile impostare lo stato del sistema operativo su [generalizzato o specializzato](#generalized-and-specialized-images).
* Sistema operativo: può essere Windows o Linux.
* Descrizione: usare Description per fornire informazioni più dettagliate sul motivo per cui la definizione dell'immagine esiste. Ad esempio, si potrebbe avere una definizione di immagine per il server front-end in cui è preinstallata l'applicazione.
* EULA-può essere usato per puntare a un contratto di licenza con l'utente finale specifico per la definizione dell'immagine.
* Informativa sulla privacy e note sulla versione: archivia le note sulla versione e le informative sulla privacy in archiviazione di Azure e fornisce un URI per accedervi come parte della definizione dell'immagine.
* Data di fine della vita: consente di aggiungere una data di fine vita alla definizione dell'immagine per poter usare l'automazione per eliminare le definizioni di immagini obsolete.
* Tag: è possibile aggiungere tag quando si crea la definizione dell'immagine. Per altre informazioni sui tag, vedere [uso dei tag per organizzare le risorse](../articles/azure-resource-manager/resource-group-using-tags.md)
* Raccomandazioni vCPU e Memory minime e massime: se l'immagine presenta vCPU e consigli per la memoria, è possibile alleghi tali informazioni alla definizione dell'immagine.
* Tipi di dischi non consentiti: è possibile fornire informazioni sulle esigenze di archiviazione per la macchina virtuale. Se, ad esempio, l'immagine non è adatta per i dischi HDD standard, è necessario aggiungerli all'elenco non consentiti.

## <a name="generalized-and-specialized-images"></a>Immagini generalizzate e specializzate

La raccolta di immagini condivise supporta due Stati del sistema operativo. In genere le immagini richiedono che la macchina virtuale usata per creare l'immagine sia stata generalizzata prima di acquisire l'immagine. Generalizzare è un processo che rimuove le informazioni specifiche del computer e dell'utente dalla macchina virtuale. Per Windows, viene utilizzato anche Sysprep. Per Linux, è possibile usare [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` o `-deprovision+user` parametri.

Le macchine virtuali specializzate non hanno attraversato un processo per rimuovere informazioni e account specifici del computer. Inoltre, alle macchine virtuali create da immagini specializzate non è associato un `osProfile`. Ciò significa che le immagini specializzate avranno alcune limitazioni.

- Gli account che potrebbero essere usati per accedere alla macchina virtuale possono essere usati anche in qualsiasi macchina virtuale creata usando l'immagine specializzata creata da tale macchina virtuale.
- Le macchine virtuali avranno il **nome computer** della macchina virtuale da cui è stata ricavata l'immagine. È necessario modificare il nome del computer per evitare conflitti.
- Il `osProfile` indica il modo in cui vengono passate informazioni riservate alla macchina virtuale usando `secrets`. Questo può causare problemi usando l'insieme di credenziali delle credenziali, WinRM e altre funzionalità che usano `secrets` nel `osProfile`. In alcuni casi, è possibile usare identità del servizio gestito (MSI) per aggirare queste limitazioni.

> [!IMPORTANT]
> Le immagini specializzate sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni di anteprima note** Le macchine virtuali possono essere create solo da immagini specializzate usando il portale o l'API. Non è disponibile l'interfaccia della riga di comando o il supporto PowerShell per l'anteprima.


## <a name="regional-support"></a>Supporto a livello di area

Le aree di origine sono elencate nella tabella seguente. Tutte le aree pubbliche possono essere aree di destinazione, ma per eseguire la replica in Australia centrale e Australia centrale 2 è necessario che la sottoscrizione sia consentita. Per richiedere l'inserimento nella whitelist, passare a: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Aree di origine        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australia centrale     | Cina orientale        | India meridionale        | Europa occidentale        |
| Australia centrale 2   | Cina orientale 2      | Asia sud-orientale     | Regno Unito meridionale           |
| Australia orientale        | Cina settentrionale       | Giappone orientale         | Regno Unito occidentale            |
| Australia sud-orientale   | Cina settentrionale 2     | Giappone occidentale         | US DoD (area centrale)     |
| Brasile meridionale          | Asia orientale         | Corea centrale      | US DoD (area orientale)        |
| Canada centrale        | Stati Uniti Orientali           | Corea meridionale        | US Gov Arizona     |
| Canada orientale           | Stati Uniti orientali 2         | Stati Uniti centro-settentrionali   | US Gov Texas       |
| India centrale         | Stati Uniti orientali 2 EUAP    | Europa settentrionale       | US Gov Virginia    |
| Stati Uniti centrali            | Francia centrale    | Stati Uniti centro-meridionali   | India occidentale         |
| Stati Uniti centrali EUAP       | Francia meridionale      | Stati Uniti centro-occidentali    | Stati Uniti occidentali            |
|                       |                   |                    | Stati Uniti occidentali 2          |



## <a name="limits"></a>limiti 

Per la distribuzione delle risorse tramite le raccolte di immagini condivise sono previsti limiti, per sottoscrizione:
- 100 raccolte di immagini condivise, per sottoscrizione, per area
- 1\.000 definizioni di immagine, per sottoscrizione, per area
- 10.000 versioni dell'immagine, per sottoscrizione, per area
- I dischi collegati all'immagine devono essere di dimensioni minori o uguali a 1 TB

Per altre informazioni, vedere [controllare l'utilizzo delle risorse rispetto ai limiti](https://docs.microsoft.com/azure/networking/check-usage-against-limits) per esempi su come controllare l'utilizzo corrente.
 
## <a name="scaling"></a>Scalabilità
Raccolta di immagini condivise consente di specificare il numero di repliche delle immagini che si desidera vengano conservate da Azure. Questa possibilità è particolarmente utile in scenari di distribuzione di più macchine virtuali, poiché le distribuzioni di macchine virtuali possono essere estese a diverse repliche, riducendo le possibilità che il processo di creazione di istanze venga limitato a causa dell'overload di una singola replica.

Con la raccolta di immagini condivise, è ora possibile distribuire fino a una macchina virtuale di 1.000 istanze in un set di scalabilità di macchine virtuali (fino a 600 con le immagini gestite). Le repliche di immagini offrono prestazioni, affidabilità e coerenza migliori per la distribuzione.  È possibile impostare un numero di repliche diverso in ogni area di destinazione, in base alle esigenze di scalabilità per l'area. Poiché ogni replica è una copia completa dell'immagine, questo consente di ridimensionare le distribuzioni in modo lineare con ogni replica aggiuntiva. Sebbene non siano state riconoscite due immagini o aree, di seguito sono riportate le linee guida generali su come usare le repliche in un'area:

- Per le distribuzioni di set di scalabilità di macchine virtuali (VMSS) non virtuali, per ogni 20 VM create simultaneamente, è consigliabile usare una replica. Se, ad esempio, si creano VM 120 contemporaneamente usando la stessa immagine in un'area, si consiglia di tenere almeno sei repliche dell'immagine. 
- Per le distribuzioni del set di scalabilità di macchine virtuali (VMSS): per ogni distribuzione del set di scalabilità con un massimo di 600 istanze, è consigliabile contenere almeno una replica. Se, ad esempio, si creano 5 set di scalabilità simultaneamente, ognuno con 600 istanze di VM che usano la stessa immagine in un'unica area, si consiglia di tenere almeno 5 repliche dell'immagine. 

Si consiglia sempre di eseguire l'overprovisioning del numero di repliche a causa di fattori quali le dimensioni dell'immagine, il contenuto e il tipo di sistema operativo.

![Immagine che mostra come ridimensionare le immagini](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Rendi le tue immagini a disponibilità elevata

[Archiviazione con ridondanza della zona di Azure (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) offre resilienza in caso di errore della zona di disponibilità nell'area. Con la disponibilità generale della raccolta immagini condivise, è possibile scegliere di archiviare le immagini in account ZRS in aree con zone di disponibilità. 

È anche possibile scegliere il tipo di account per ciascuna delle aree di destinazione. Il tipo di account di archiviazione predefinito è Standard_LRS, ma è possibile scegliere Standard_ZRS per le aree con zone di disponibilità. Controllare la disponibilità a livello di area di ZRS [qui](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Grafico che mostra ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replica
Raccolta di immagini condivise consente inoltre di replicare automaticamente le immagini in altre aree di Azure. Ogni versione di immagini condivise può essere replicata in diverse aree a seconda delle esigenze della propria organizzazione. Ad esempio, è possibile replicare sempre l'immagine più recente in più aree, mentre tutte le versioni precedenti sono disponibili solo in un'area. Questo permette di risparmiare sui costi di archiviazione per le versioni di immagini condivise. 

Le aree in cui la versione di immagini condivise viene replicata possono essere aggiornate dopo l'ora di creazione. Il tempo necessario per la replica in aree diverse dipende dalla quantità di dati da copiare e dal numero di aree in cui viene replicata la versione. In alcuni casi, questa operazione può richiedere alcune ore. Mentre le replica è in corso, è possibile visualizzare lo stato della replica per area. Una volta completata la replica dell'immagine in un'area, è possibile distribuire una macchina virtuale o un set di scalabilità usando tale versione dell'immagine nell'area.

![Immagine che mostra come replicare le immagini](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Accesso

Poiché la raccolta di immagini condivise, la definizione di immagine e la versione dell'immagine sono tutte risorse, possono essere condivise usando i controlli RBAC nativi di Azure predefiniti. Utilizzando il controllo degli accessi in base al ruolo è possibile condividere queste risorse con altri utenti, entità servizio e gruppi. È anche possibile condividere l'accesso a utenti esterni al tenant in cui sono stati creati. Una volta che un utente ha accesso alla versione dell'immagine condivisa, può distribuire una VM o un set di scalabilità di macchine virtuali.  Di seguito è riportata la matrice di condivisione che consente all'utente di riconoscere a cosa ha accesso:

| Condivisi con l'utente     | Raccolta immagini condivisa | Definizione delle immagini | Versione dell'immagine |
|----------------------|----------------------|--------------|----------------------|
| Raccolta immagini condivisa | SÌ                  | SÌ          | SÌ                  |
| Definizione delle immagini     | No                   | SÌ          | SÌ                  |

Per un'esperienza ottimale, è consigliabile condividere a livello di raccolta. Non è consigliabile condividere le singole versioni dell'immagine. Per altre informazioni su RBAC, vedere [gestire l'accesso alle risorse di Azure con RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Le immagini possono anche essere condivise, su larga scala, anche tra i tenant usando una registrazione di app multi-tenant. Per altre informazioni sulla condivisione di immagini tra i tenant, vedere [condividere le immagini di VM tra i tenant di Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fatturazione
Non sono previsti addebiti aggiuntivi per l'uso del servizio Raccolta di immagini condivise. Vengono addebitate le risorse seguenti:
- Costi di archiviazione per le versioni di immagini condivise. Il costo dipende dal numero di repliche della versione dell'immagine e dal numero di aree in cui la versione viene replicata. Ad esempio, se si dispone di 2 immagini ed entrambe vengono replicate in 3 aree, si verrà modificati per 6 dischi gestiti in base alla relativa dimensione. Per ulteriori informazioni, vedere [Managed disks prezzi](https://azure.microsoft.com/pricing/details/managed-disks/).
- Addebiti per l'uscita di rete per la replica della prima versione immagine dall'area di origine alle aree replicate. Le repliche successive vengono gestite all'interno dell'area, quindi non sono previsti addebiti aggiuntivi. 

## <a name="updating-resources"></a>Aggiornamento delle risorse

Una volta creato, è possibile apportare alcune modifiche alle risorse della raccolta immagini. Sono limitati a:
 
Raccolta di immagini condivise:
- Description

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- Description
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Escludi dall'ultima versione
- Data di scadenza

## <a name="sdk-support"></a>Supporto SDK

Gli SDK seguenti supportano la creazione di raccolte di immagini condivise:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelli

È possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Domande frequenti 

* [Come è possibile elencare tutte le risorse della raccolta immagini condivise tra le sottoscrizioni?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [È possibile spostare l'immagine esistente nella raccolta di immagini condivise?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [È possibile creare una versione di immagine da un disco specializzato?](#can-i-create-an-image-version-from-a-specialized-disk)
* [È possibile spostare la risorsa raccolta immagini condivise in una sottoscrizione diversa dopo che è stata creata?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [È possibile replicare le versioni delle immagini tra cloud, ad esempio Azure Cina 21Vianet o Azure Germania o Azure per enti pubblici?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [È possibile replicare le versioni delle immagini tra le sottoscrizioni?](#can-i-replicate-my-image-versions-across-subscriptions)
* [È possibile condividere le versioni delle immagini tra Azure AD tenant?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo è necessario per replicare le versioni delle immagini tra le aree di destinazione?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual è la differenza tra l'area di origine e quella di destinazione?](#what-is-the-difference-between-source-region-and-target-region)
* [Ricerca per categorie specificare l'area di origine durante la creazione della versione dell'immagine?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Ricerca per categorie specificare il numero di repliche della versione di immagine da creare in ogni area?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [È possibile creare la raccolta di immagini condivise in un percorso diverso rispetto a quello per la definizione dell'immagine e la versione dell'immagine?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quali sono gli addebiti per l'uso della raccolta di immagini condivise?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Quale versione dell'API è consigliabile usare per creare la raccolta di immagini condivise e la versione dell'immagine e della definizione dell'immagine?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Quale versione dell'API è consigliabile usare per creare una VM condivisa o un set di scalabilità di macchine virtuali per la versione dell'immagine?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Come elencare tutte le risorse di Raccolta di immagini condivise tra le sottoscrizioni?

Per elencare tutte le risorse della raccolta immagini condivise tra le sottoscrizioni a cui si ha accesso nel portale di Azure, attenersi alla procedura seguente:

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare a **Tutte le risorse**.
1. Selezionare tutte le sottoscrizioni in cui si desidera elencare tutte le risorse.
1. Identificare le risorse di tipo **Raccolta privata**.
 
   Per visualizzare le definizioni di immagini e le versioni di immagini, è necessario selezionare anche **Mostra tipi nascosti**.
 
   Per elencare tutte le risorse della raccolta di immagini condivise tra le sottoscrizioni a cui si ha accesso, usare il seguente comando nell'interfaccia della riga di comando di Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>È possibile spostare l'immagine esistente nella raccolta di immagini condivise?
 
Sì. Esistono tre scenari in base ai tipi di immagini che si possono avere.

 Scenario 1: se si dispone di un'immagine gestita, è possibile creare una definizione di immagine e una versione dell'immagine a partire da essa.

 Scenario 2: se si dispone di un'immagine non gestita, è possibile creare un'immagine gestita e quindi creare una definizione di immagine e una versione dell'immagine. 

 Scenario 3: se si dispone di un disco rigido virtuale nel file system locale, è necessario caricare il disco rigido virtuale in un'immagine gestita, quindi è possibile creare una definizione di immagine e una versione dell'immagine.

- Se il disco rigido virtuale è di una macchina virtuale Windows, vedere [caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se il disco rigido virtuale è per una macchina virtuale Linux, vedere [Caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>È possibile creare una versione di immagine da un disco specializzato?

Sì, il supporto per dischi specializzati come immagini è in anteprima. È possibile creare una macchina virtuale solo da un'immagine specializzata usando il portale ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) o [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) e l'API. Non è disponibile alcun supporto di PowerShell per l'anteprima.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>È possibile spostare la risorsa raccolta immagini condivise in una sottoscrizione diversa dopo che è stata creata?

No, non è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa. Tuttavia, è possibile replicare le versioni delle immagini della raccolta in altre aree secondo necessità.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>È possibile replicare le versioni delle immagini tra cloud, ad esempio Azure Cina 21Vianet o Azure Germania o Azure per enti pubblici?

No, non è possibile replicare le versioni delle immagini in diversi cloud.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>È possibile replicare le versioni delle immagini tra sottoscrizioni diverse?

No, è possibile replicare le versioni delle immagini nelle aree in una sottoscrizione e usarle in altre sottoscrizioni tramite RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>È possibile condividere versioni delle immagini nei tenant di Azure Active Directory? 

Sì, è possibile usare il controllo degli accessi in base al ruolo per condividere le singole persone nei tenant. Tuttavia, per condividere la scalabilità, vedere l'argomento "condividere immagini della raccolta tra i tenant di Azure" tramite [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) o l' [interfaccia](../articles/virtual-machines/linux/share-images-across-tenants.md)della riga di comando.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo occorre per replicare le versioni delle immagini tra le aree di destinazione?

Il tempo di replica della versione dell'immagine dipende interamente dalle dimensioni dell'immagine e dal numero di aree in cui viene replicata. Tuttavia, come procedura consigliata, si consiglia di mantenere l'immagine piccola e le aree di origine e di destinazione vicine per ottenere risultati ottimali. È possibile controllare lo stato della replica usando il flag ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual è la differenza tra area di origine e area di destinazione?

L'area di origine è l'area in cui viene creata la versione dell'immagine e le aree di destinazione sono le aree in cui viene archiviata una copia della versione dell'immagine. Per ogni versione dell'immagine, è possibile avere solo un'area di origine. Inoltre, assicurarsi di trasmettere la posizione dell'area di origine come una delle aree di destinazione quando si crea una versione di immagine.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Come si specifica l'area di origine durante la creazione della versione dell'immagine?

Durante la creazione di una versione dell'immagine, è possibile usare il tag **posizione** all'interno dell'interfaccia della riga di comando e il tag **Posizione** in PowerShell per specificare l'area di origine. Assicurarsi che l'immagine gestita in uso come immagine di base per creare la versione dell'immagine sia nella stessa posizione in cui si intende creare la versione dell'immagine. Inoltre, assicurarsi di trasmettere la posizione dell'area di origine come una delle aree di destinazione quando si crea una versione di immagine.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Come specificare il numero di repliche della versione dell'immagine da creare in ogni area?

Esistono due modi in cui è possibile specificare il numero delle repliche di versione dell'immagine da creare in ogni area:
 
1. Il conteggio delle repliche a livello di area che specifica il numero di repliche che si desidera creare per ogni area. 
2. Il conteggio comune delle repliche, che è il conteggio predefinito per area nel caso in cui non sia specificato il conteggio di repliche a livello di area. 

Per specificare il numero di repliche internazionali, passare il percorso insieme al numero di repliche che si vuole creare in tale area: "Stati Uniti centro-meridionali = 2". 

Se il conteggio delle repliche a livello di area non è specificato per ogni posizione, il numero predefinito di repliche sarà il conteggio comune delle repliche comuni che è stato specificato. 

Per specificare il numero comune di repliche nella riga di comando, usare l'argomento **conteggio di repliche** nel comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>È possibile creare la raccolta di immagini condivise in un percorso diverso rispetto a quello per la definizione dell'immagine e la versione dell'immagine?

Sì, è possibile. Tuttavia, come procedura consigliata, si consiglia di proteggere il gruppo di risorse, la raccolta di immagini condivise, la definizione dell'immagine e la versione dell'immagine nella stessa posizione.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quali sono gli addebiti per l'uso della Raccolta di immagini condivise?

Non sono previsti addebiti per l'uso del servizio Raccolta di immagini condivise, ad eccezione delle spese per l'archiviazione delle versioni di immagini e dei costi di rete di uscita per la replica di versioni di immagini dall'area di origine alle aree di destinazione.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Quale versione dell'API è consigliabile usare per creare la raccolta di immagini condivise e la versione dell'immagine e della definizione dell'immagine?

Per lavorare con le raccolte di immagini condivise, le definizioni di immagini e le versioni dell'immagine, si consiglia di usare la versione API 2018-06-01. Per l'archiviazione con ridondanza della zona (ZRS) è richiesta la versione 2019-03-01 o successiva.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Quale versione dell'API è consigliabile usare per creare una VM condivisa o un set di scalabilità di macchine virtuali per la versione dell'immagine?

Per le distribuzioni di macchine virtuali e set di scalabilità di macchine virtuali eseguite usando una versione di immagine, si consiglia di usare la versione API 2018-04-01 o una successiva.
