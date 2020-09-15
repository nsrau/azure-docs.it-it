---
title: includere file
description: includere file
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 662afb902c97e164cc24bc664b854db118904210
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89494308"
---
Raccolta immagini condivise è un servizio che consente di compilare la struttura e l'organizzazione per le immagini. Raccolte di immagini condivise fornisce:

- Replica globale delle immagini.
- Controllo delle versioni e raggruppamento delle immagini per la semplificazione della gestione.
- Immagini a disponibilità elevata con account di archiviazione con ridondanza della zona (ZRS) in aree che supportano le zone di disponibilità. Le ZRS offrono una migliore resilienza nei confronti degli errori di zona.
- Supporto archiviazione Premium (Premium_LRS).
- Condivisione tra sottoscrizioni e persino tra tenant Active Directory, con il Controllo degli accessi in base al ruolo.
- Ridimensionamento delle distribuzioni con repliche delle immagini in ogni area.

Usando una raccolta di immagini condivise è possibile condividere le immagini con utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione. Le immagini condivise possono essere replicate in più aree, per un ridimensionamento più rapido delle distribuzioni.

Un'immagine è una copia di una macchina virtuale completa (inclusi eventuali dischi dati allegati) o solo del disco del sistema operativo, a seconda del modo in cui viene creata. Quando si crea una macchina virtuale dall'immagine, la copia dei dischi rigidi virtuali dell'immagine viene usata per creare i dischi per la nuova macchina virtuale. L'immagine rimane nella risorsa di archiviazione e può essere usata ripetutamente per creare nuove macchine virtuali.

Se si dispone di un numero elevato di immagini da mantenere e si desidera renderle disponibili in tutta l'azienda, è possibile usare una raccolta immagini condivise come repository. 

La funzionalità Raccolta di immagini condivise presenta più tipi di risorse:

| Risorsa | Descrizione|
|----------|------------|
| **Origine immagine** | Si tratta di una risorsa che è possibile usare per creare una **versione di immagine** in una raccolta di immagini. Un'origine dell'immagine può essere una macchina virtuale di Azure esistente [generalizzata o specializzata](#generalized-and-specialized-images), un'immagine gestita, uno snapshot, un disco rigido virtuale o una versione di immagine in un'altra raccolta immagini. |
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le definizioni di immagini vengono create all'interno di una raccolta e forniscono informazioni sull'immagine e sui requisiti per il relativo uso interno. Questa include il fatto che l'immagine è per Windows o Linux, le note sulla versione e i requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>

![Immagine che mostra in che modo è possibile avere più versioni di un'immagine nella propria raccolta](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definizioni dell'immagine

Le definizioni dell'immagine rappresentano un raggruppamento logico per le versioni di un'immagine. La definizione dell'immagine detiene informazioni sui motivi per cui è stata creata l'immagine, sul sistema operativo per cui è stata creata e altre informazioni sull'uso. Una definizione dell'immagine è simile a un piano per tutti i dettagli che riguardano la creazione di un'immagine specifica. Non si distribuisce una macchina virtuale da una definizione dell'immagine, ma da versioni dell'immagine create dalla definizione.

Sono disponibili tre parametri per ogni definizione dell'immagine usati in combinazione: **Editore**, **Offerta** e **SKU**. Questi parametri vengono usati per trovare una specifica definizione dell'immagine. È possibile avere versioni delle immagini che condividono uno o due, ma non tutti e tre i valori.  Di seguito, un esempio di tre definizioni di immagini con i relativi valori:

|Definizione delle immagini|Editore|Offerta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanza|Back-end|
|myImage2|Contoso|Finanza|Front-end|
|myImage3|Test|Finanza|Front-end|

Questi tre presentano set univoci di valori. Il formato è simile a quello in cui è attualmente possibile specificare l'editore, l'offerta e lo SKU per [immagini di Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md), in Azure PowerShell, per ottenere la versione più recente di un'immagine del Marketplace. Ogni definizione dell'immagine deve avere un set univoco di questi valori.

Di seguito sono elencati altri parametri che è possibile impostare nella definizione dell'immagine, in modo da tenere traccia più facilmente delle risorse:

* Stato sistema operativo: è possibile impostare lo stato sistema operativo su [generalizzato o specializzato](#generalized-and-specialized-images).
* Sistema operativo: può essere Windows o Linux.
* Descrizione: usare la descrizione per fornire informazioni più dettagliate sul motivo per cui la definizione dell'immagine esiste. Ad esempio, si potrebbe avere una definizione dell'immagine per il server front-end in cui l'applicazione è preinstallata.
* Eula: può essere usato per puntare a un contratto di licenza con l'utente finale specifico per la definizione dell'immagine.
* Informativa sulla privacy e note sulla versione: archivia le note sulla versione e le informative sulla privacy in Archiviazione di Azure e fornisce un URI per accedervi come parte della definizione dell'immagine.
* Data di fine vita: consente di allegare una data di fine vita alla definizione dell'immagine in modo da eliminare automaticamente le definizioni delle immagini obsolete.
* Tag: quando si crea la definizione dell'immagine è possibile aggiungere tag. Per altre informazioni sui tag, vedere [Usare i tag per organizzare le risorse](../articles/azure-resource-manager/management/tag-resources.md)
* Elementi consigliati per la CPU virtuale e memoria minime e massime: se l'immagine presenta CPU virtuale ed elementi consigliati per la memoria, è possibile allegare tali informazioni alla definizione dell'immagine.
* Tipi di dischi non consentiti: è possibile fornire informazioni sulle esigenze di archiviazione per la macchina virtuale. Se, ad esempio, l'immagine non è adatta per dischi HDD standard, è necessario aggiungerli all'elenco di quelli non consentiti.
* Generazione di Hyper-V: è possibile specificare se l'immagine è stata creata da un disco rigido virtuale Hyper-V di prima o di seconda generazione.
* Informazioni sui piani di acquisto per immagini del Marketplace - `-PurchasePlanPublisher `, `-PurchasePlanName` e `-PurchasePlanProduct`. Per un approfondimento delle informazioni sui piani di acquisto, vedere [Trovare immagini in Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage) e [Fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](../articles/virtual-machines/marketplace-images.md).

## <a name="generalized-and-specialized-images"></a>Immagini specializzate e generalizzate

Raccolta immagini condivise supporta due stati del sistema operativo. Solitamente le immagini richiedono che la macchina virtuale usata per creare l'immagine sia stata generalizzata prima di acquisire l'immagine. La generalizzazione è un processo che rimuove le informazioni specifiche del computer e dell'utente dalla macchina virtuale. Windows usa lo strumento Sysprep. Per Linux è possibile usare il comando [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` o i parametri `-deprovision+user`.

Le macchine virtuali specializzate non sono state sottoposte a un processo per rimuovere account e informazioni specifici del computer. Oltre a ciò, alle macchine virtuali create da immagini specializzate non è associato un `osProfile`. Ciò significa che le immagini specializzate presentano alcune limitazioni, oltre ai vantaggi.

- Le macchine virtuali e i set di scalabilità creati da immagini specializzate possono essere operativi più velocemente. Poiché vengono creati da un'origine già avviata per la prima volta, le macchine virtuali create da queste immagini si avviano più velocemente.
- Gli account che possono essere usati per accedere alla macchina virtuale possono inoltre essere usati in qualsiasi macchina virtuale creata con l'immagine specializzata creata da tale macchina virtuale.
- Le macchine virtuali avranno il **Nome computer** della macchina virtuale da cui è stata ricavata l'immagine. È necessario modificare il nome computer per evitare conflitti.
- La proprietà `osProfile` indica il modo in cui vengono passate informazioni riservate alla macchina virtuale con `secrets`. Questo può causare problemi con KeyVault, WinRM e altre funzionalità che usano `secrets` nella proprietà `osProfile`. In alcuni casi, è possibile usare le identità del servizio gestito (MSI) per aggirare queste limitazioni.

## <a name="regional-support"></a>Supporto a livello di area

Tutte le aree pubbliche possono essere aree di destinazione, tuttavia per la replica in Australia centrale e Australia centrale 2 è necessario che la sottoscrizione sia aggiunta all'elenco elementi consentiti. per richiedere che una sottoscrizione venga aggiunta all'elenco elementi consentiti, visitare: https://azure.microsoft.com/global-infrastructure/australia/contact/

## <a name="limits"></a>Limiti 

Sono previsti limiti, per sottoscrizione, per la distribuzione delle risorse tramite Raccolte immagini condivise:
- 100 raccolte di immagini condivise, per ogni sottoscrizione, per area
- 1\.000 definizioni di immagini, per ogni sottoscrizione, per area
- 10.000 versioni di immagini, per ogni sottoscrizione, per area
- 10 repliche di versioni di immagini, per ogni sottoscrizione, per area
- Qualsiasi disco collegato all'immagine deve essere di dimensioni minori o uguali a 1 TB

Per altre informazioni, vedere [Controllare l'utilizzo delle risorse rispetto ai limiti](https://docs.microsoft.com/azure/networking/check-usage-against-limits) per esempi su come controllare l'utilizzo corrente.
 
## <a name="scaling"></a>Scalabilità
Raccolta di immagini condivise consente di specificare il numero di repliche delle immagini che si desidera vengano conservate da Azure. Questa possibilità è particolarmente utile in scenari di distribuzione di più macchine virtuali, poiché le distribuzioni di macchine virtuali possono essere estese a diverse repliche, riducendo le possibilità che il processo di creazione di istanze venga limitato a causa dell'overload di una singola replica.

Con Raccolta immagini condivise è ora possibile distribuire fino a 1.000 istanze di macchina virtuale in un set di scalabilità di macchine virtuali (fino a 600 con le immagini gestite). Le repliche di immagini offrono migliori prestazioni, affidabilità e coerenza per la distribuzione.  È possibile impostare un numero di repliche diverso in ogni area di destinazione, in base alle esigenze di ridimensionamento per l'area. Poiché ogni replica rappresenta una copia completa dell'immagine, questo consente di ridimensionare le distribuzioni in modo lineare con ogni replica aggiuntiva. Pur comprendendo che non ci siano due immagini o aree identiche, di seguito sono riportate le linee guida generali su come usare le repliche in 1 area:

- Per le distribuzioni di set di scalabilità di macchine non virtuali: per ogni 20 macchine virtuali create simultaneamente è consigliabile usare una replica. Se, ad esempio, si creano 120 macchine virtuali contemporaneamente usando la stessa immagine in un'area, si consiglia di mantenere almeno 6 repliche dell'immagine. 
- Per le distribuzioni del set di scalabilità di macchine virtuali: per ogni distribuzione del set di scalabilità con un massimo di 600 istanze è consigliabile mantenere almeno una replica. Se, ad esempio, si creano 5 set di scalabilità contemporaneamente, ognuno con 600 istanze di macchine virtuali e usando la stessa immagine in un'area, si consiglia di mantenere almeno 5 repliche dell'immagine. 

Si consiglia sempre di eseguire il provisioning eccessivo del numero di repliche, a causa di fattori quali dimensioni dell'immagine, contenuto e tipo di sistema operativo.

![Immagine che mostra come ridimensionare le immagini](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Rendere le immagini a disponibilità elevata

[Archiviazione di Azure con ridondanza della zona (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) garantisce resilienza in caso di errore della zona di disponibilità nell'area. Con la disponibilità generale di Raccolta immagini condivise è possibile scegliere di archiviare le immagini in account ZRS in aree con zone di disponibilità. 

È inoltre possibile scegliere il tipo di account per ogni area di destinazione. Il tipo di account di archiviazione predefinito è 	Archiviazione con ridondanza locale Standard, tuttavia è anche possibile scegliere Archiviazione con ridondanza della zona Standard per le aree con zone di disponibilità. Controllare [qui](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) la disponibilità a livello di area dell'archiviazione con ridondanza della zona.

![Immagine che mostra l'archiviazione con ridondanza della zona](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replica
Raccolta di immagini condivise consente inoltre di replicare automaticamente le immagini in altre aree di Azure. Ogni versione di immagini condivise può essere replicata in diverse aree a seconda delle esigenze della propria organizzazione. Ad esempio, è possibile replicare sempre l'immagine più recente in più aree, mentre tutte le versioni precedenti sono disponibili solo in un'area. Questo permette di risparmiare sui costi di archiviazione per le versioni di immagini condivise. 

Le aree in cui la versione di immagini condivise viene replicata possono essere aggiornate dopo l'ora di creazione. Il tempo necessario per la replica in aree diverse dipende dalla quantità di dati da copiare e dal numero di aree in cui viene replicata la versione. In alcuni casi, questa operazione può richiedere alcune ore. Mentre le replica è in corso, è possibile visualizzare lo stato della replica per area. Al termine della replica di un'immagine in un'area, è quindi possibile distribuire una macchina virtuale o un set di scalabilità usando la versione dell'immagine disponibile nell'area.

![Immagine che mostra come replicare le immagini](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Accesso

Poiché la Raccolta immagini condivise, la definizione dell'immagine e la versione dell'immagine condivisa sono tutte risorse, possono essere condivise usando i controlli degli accessi in base al ruolo nativi di Azure. Usando il RBAC è possibile condividere tali risorse con altri utenti, entità servizio e gruppi. È inoltre possibile condividere l'accesso a utenti esterni al tenant in cui sono stati creati. Una volta che un utente ha accesso alla versione dell'immagine condivisa, può distribuire una macchina virtuale o un set di scalabilità di macchine virtuali.  Di seguito è riportata la matrice di condivisione che consente all'utente di riconoscere a cosa ha accesso:

| Condivisi con l'utente     | Raccolta di immagini condivise | Definizione delle immagini | Versione dell'immagine |
|----------------------|----------------------|--------------|----------------------|
| Raccolta di immagini condivise | Sì                  | Sì          | Sì                  |
| Definizione delle immagini     | No                   | Sì          | Sì                  |

Per un'esperienza ottimale è consigliabile condividere a livello di raccolta. Non è consigliabile condividere le versioni dell'immagine singole. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo](../articles/role-based-access-control/role-assignments-portal.md).

Le immagini possono inoltre essere condivise su larga scala anche tra tenant usando una registrazione di app multi-tenant. Per altre informazioni sulla condivisione di immagini tra tenant, vedere [Condividere immagini di macchine virtuali della raccolta tra tenant di Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fatturazione
Non sono previsti addebiti aggiuntivi per l'uso del servizio Raccolta di immagini condivise. Vengono addebitate le risorse seguenti:
- Costi di archiviazione per le versioni di immagini condivise. Il costo dipende dal numero di repliche della versione dell'immagine e dal numero di aree in cui la versione viene replicata. Se, ad esempio, si hanno 2 immagini ed entrambe vengono replicate in 3 aree, verranno addebitati 6 dischi gestiti in base alla relativa dimensione. Per altre informazioni, vedere [Prezzi del servizio Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Addebiti in uscita di rete per la replica della prima versione dell'immagine dall'area di origine alle aree replicate. Repliche successive vengono gestite tra le aree, così da non incorrere in nessun addebito aggiuntivo. 

## <a name="updating-resources"></a>Aggiornamento delle risorse

Non appena create le risorse della raccolta immagini, è possibile effettuare modifiche. Sono limitate a:
 
Raccolta di immagini condivise:
- Descrizione

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- Descrizione
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Escludi da versione più recente
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

* [Come elencare tutte le risorse di Raccolta immagini condivise tra le sottoscrizioni?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [È possibile spostare l'immagine esistente nella raccolta di immagini condivise?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [È possibile creare una versione di immagine da un disco specializzato?](#can-i-create-an-image-version-from-a-specialized-disk)
* [È possibile spostare la risorsa Raccolta immagini condivise in una sottoscrizione diversa in seguito alla sua creazione?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [È possibile replicare le versioni delle immagini in diversi cloud quali Azure China (21Vianet), Azure Germania o Cloud di Azure per enti pubblici?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [È possibile replicare le versioni delle immagini tra sottoscrizioni diverse?](#can-i-replicate-my-image-versions-across-subscriptions)
* [È possibile condividere versioni delle immagini nei tenant di Azure Active Directory?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo occorre per replicare le versioni delle immagini tra le aree di destinazione?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual è la differenza tra area di origine e area di destinazione?](#what-is-the-difference-between-source-region-and-target-region)
* [Come si specifica l'area di origine durante la creazione della versione dell'immagine?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Come specificare il numero di repliche della versione dell'immagine da creare in ogni area?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [È possibile creare la raccolta di immagini condivise in una posizione diversa da quella per la definizione di immagine e la versione dell'immagine?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quali sono gli addebiti per l'uso della Raccolta immagini condivise?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Quale versione API è necessario usare per creare Raccolta immagini condivise, Definizione dell'immagine e Versione immagine?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Quale versione dell'API è consigliabile usare per creare una macchina virtuale condivisa o un set di scalabilità di macchine virtuali dalla versione dell'immagine?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [È possibile aggiornare il set di scalabilità di macchine virtuali creato con un'immagine gestita per usare immagini di Raccolta immagini condivise?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Come elencare tutte le risorse di Raccolta di immagini condivise tra le sottoscrizioni?

Per elencare tutte le risorse di Raccolta immagini condivise tra le sottoscrizioni a cui si ha accesso nel portale di Azure, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Scorrere verso il basso e selezionare **Tutte le risorse**.
1. Selezionare tutte le sottoscrizioni in cui si desidera elencare tutte le risorse.
1. Identificare le risorse di tipo **Raccolta immagini condivise**.
  
Per elencare tutte le risorse della raccolta di immagini condivise tra le sottoscrizioni a cui si ha accesso, usare il seguente comando nell'interfaccia della riga di comando di Azure:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Per altre informazioni, vedere **Gestire le risorse della raccolta** con l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/update-image-resources-cli.md) o [PowerShell](../articles/virtual-machines/update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>È possibile spostare l'immagine esistente nella raccolta di immagini condivise?
 
Sì. Esistono tre scenari in base ai tipi di immagini che si possono avere.

 Scenario 1: se si dispone di un'immagine gestita, è possibile creare una definizione di immagine e una versione dell'immagine a partire da essa. Per altre informazioni, vedere **Eseguire la migrazione da un'immagine gestita a una versione dell'immagine** con l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/image-version-managed-image-cli.md) o [PowerShell](../articles/virtual-machines/image-version-managed-image-powershell.md).

 Scenario 2: se si dispone di un'immagine non gestita, è possibile creare un'immagine gestita da quest'ultima e quindi creare una definizione di immagine e la versione dell'immagine da essa. 

 Scenario 3: se si dispone di un disco rigido virtuale nel file system locale, è necessario caricare il disco rigido virtuale su un'immagine gestita; quindi è possibile creare una definizione di immagine e una versione dell'immagine da quest'ultima.

- Se il disco rigido virtuale è di una macchina virtuale Windows, vedere [Caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se il disco rigido virtuale è per una macchina virtuale Linux, vedere [Caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>È possibile creare una versione di immagine da un disco specializzato?

Sì, è possibile creare una macchina virtuale da un'immagine specializzata usando l'[interfaccia della riga di comando](../articles/virtual-machines/vm-specialized-image-version-cli.md), [PowerShell](../articles/virtual-machines/vm-specialized-image-version-powershell.md) o l'API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>È possibile spostare la risorsa Raccolta immagini condivise in una sottoscrizione diversa in seguito alla sua creazione?

No, non è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa. È possibile replicare le versioni delle immagini nella raccolta in altre aree o copiare un'immagine da un'altra raccolta usando l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/image-version-another-gallery-cli.md) o [PowerShell](../articles/virtual-machines/image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>È possibile replicare le versioni delle immagini in diversi cloud quali Azure China (21Vianet), Azure Germania o Cloud di Azure per enti pubblici?

No, non è possibile replicare le versioni delle immagini in diversi cloud.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>È possibile replicare le versioni delle immagini tra sottoscrizioni diverse?

No, è possibile replicare le versioni delle immagini nelle aree in una sottoscrizione e usarle in altre sottoscrizioni tramite RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>È possibile condividere versioni delle immagini nei tenant di Azure Active Directory? 

Sì, è possibile usare il controllo degli accessi in base al ruolo per condividere utenti singoli tra tenant. Tuttavia, per condividere su larga scala, vedere l'argomento relativo alla condivisione delle immagini della raccolta tra tenant di Azure con [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) o [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

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

Per specificare il conteggio di repliche a livello di area, trasmettere la posizione e il numero di repliche che si vuole creare in quell'area: "Stati Uniti centro-meridionali=2". 

Se il conteggio delle repliche a livello di area non è specificato per ogni posizione, il numero predefinito di repliche sarà il conteggio comune delle repliche comuni che è stato specificato. 

Per specificare il numero comune di repliche nella riga di comando, usare l'argomento **conteggio di repliche** nel comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>È possibile creare la raccolta di immagini condivise in una posizione diversa da quella per la definizione di immagine e la versione dell'immagine?

Sì, è possibile. Tuttavia, come procedura consigliata, è opportuno mantenere gruppo di risorse, raccolta di immagini condivise, definizione dell'immagine e versione dell'immagine nella stessa posizione.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quali sono gli addebiti per l'uso della Raccolta di immagini condivise?

Non sono previsti addebiti per l'uso del servizio Raccolta di immagini condivise, ad eccezione delle spese per l'archiviazione delle versioni di immagini e dei costi di rete di uscita per la replica di versioni di immagini dall'area di origine alle aree di destinazione.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Quale versione API è necessario usare per creare Raccolta immagini condivise, Definizione dell'immagine e Versione immagine?

Per lavorare con le raccolte di immagini condivise, le definizioni di immagini e le versioni dell'immagine, si consiglia di usare la versione API 2018-06-01. Per l'archiviazione con ridondanza della zona è richiesta la versione 01/03/2019 o successive.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Quale versione dell'API è consigliabile usare per creare una macchina virtuale condivisa o un set di scalabilità di macchine virtuali dalla versione dell'immagine?

Per le distribuzioni di macchine virtuali e set di scalabilità di macchine virtuali eseguite usando una versione di immagine, si consiglia di usare la versione API 2018-04-01 o una successiva.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>È possibile aggiornare il set di scalabilità di macchine virtuali creato con un'immagine gestita per usare immagini di Raccolta immagini condivise?

Sì, è possibile aggiornare il riferimento all'immagine del set di scalabilità da un'immagine gestita a un'immagine della raccolta immagini condivise, purché il tipo di sistema operativo, la generazione Hyper-V e il layout del disco dati corrispondano tra le immagini. 
