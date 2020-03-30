---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117228"
---
Shared Image Gallery è un servizio che consente di creare la struttura e l'organizzazione intorno alle immagini gestite. Le raccolte di immagini condivise forniscono:

- Replica globale gestita delle immagini.
- Controllo delle versioni e raggruppamento delle immagini per semplificarne la gestione.
- Immagini a disponibilità elevata con account di archiviazione con ridondanza di zona (RR) nelle aree che supportano le zone di disponibilità. L'RS offre una migliore resilienza contro i guasti delle aree.
- Condivisione tra sottoscrizioni e anche tra tenant di Active Directory (AD), tramite RBAC.
- Scalabilità delle distribuzioni con repliche di immagini in ogni area.

Usando una raccolta di immagini condivise è possibile condividere le immagini con utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione. Le immagini condivise possono essere replicate in più aree, per un ridimensionamento più rapido delle distribuzioni.

Un'immagine gestita è una copia di una macchina virtuale completa (inclusi eventuali dischi dati allegati) o solo del disco del sistema operativo, a seconda del modo in cui si crea l'immagine. Quando si crea una macchina virtuale dall'immagine, la copia dei dischi rigidi virtuali dell'immagine viene usata per creare i dischi per la nuova macchina virtuale. L'immagine gestita rimane nella risorsa di archiviazione e può essere usata ripetutamente per creare nuove macchine virtuali.

Se si dispone di un numero elevato di immagini gestite che è necessario gestire e si desidera renderle disponibili in tutta l'azienda, è possibile utilizzare una Galleria di immagini condivise come repository che semplifica la condivisione delle immagini. 

La funzionalità Raccolta di immagini condivise presenta più tipi di risorse:

| Risorsa | Descrizione|
|----------|------------|
| **Immagine gestita** | Immagine di base che può essere utilizzata da sola o utilizzata per creare una **versione dell'immagine** in una raccolta immagini. Le immagini gestite vengono create da macchine virtuali [generalizzate.](#generalized-and-specialized-images) Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Snapshot** | Copia di un disco rigido virtuale che può essere utilizzata per creare una **versione dell'immagine.** Gli snapshot possono essere ricavati da una macchina virtuale [specializzata](#generalized-and-specialized-images) (non generalizzata) quindi usata da sola o con snapshot di dischi dati, per creare una versione di immagine specializzata.
| **Raccolta immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini sono definite all'interno di una galleria e contengono informazioni sull'immagine e sui requisiti per il suo utilizzo all'interno dell'organizzazione. È possibile includere informazioni come se l'immagine è generalizzata o specializzata, il sistema operativo, i requisiti minimi e massimi di memoria e le note sulla versione. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>

![Immagine che mostra in che modo è possibile avere più versioni di un'immagine nella propria raccolta](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definizioni delle immagini

Le definizioni di immagine sono un raggruppamento logico per le versioni di un'immagine. La definizione dell'immagine contiene informazioni sul motivo per cui l'immagine è stata creata, su quale sistema operativo è destinata e informazioni sull'utilizzo dell'immagine. Una definizione di immagine è come un piano per tutti i dettagli relativi alla creazione di un'immagine specifica. Non si distribuisce una macchina virtuale da una definizione di immagine, ma dalla versione dell'immagine creata dalla definizione.

Esistono tre parametri per ogni definizione di immagine utilizzati in combinazione: **Publisher**, **Offer** e **SKU**. Questi vengono utilizzati per trovare una definizione di immagine specifica. È possibile avere versioni delle immagini che condividono uno o due, ma non tutti e tre i valori.  Di seguito, un esempio di tre definizioni di immagini con i relativi valori:

|Definizione delle immagini|Editore|Offerta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanza|Back-end|
|myImage2|Contoso|Finanza|Front-end|
|myImage3|Test|Finanza|Front-end|

Questi tre presentano set univoci di valori. Il formato è simile a come è attualmente possibile specificare le immagini di publisher, offerta e SKU per Azure Marketplace in Azure PowerShell per ottenere la versione più recente di un'immagine Marketplace.The format is similar to how you can currently specify publisher, offer, and SKU for [Azure Marketplace images](../articles/virtual-machines/windows/cli-ps-findimage.md) in Azure PowerShell to get the latest version of a Marketplace image. Ogni definizione di immagine deve avere un set univoco di questi valori.

Di seguito sono riportati altri parametri che possono essere impostati nella definizione dell'immagine in modo da poter tenere traccia più facilmente delle risorse:The following are other parameters that can be set on your image definition so that you can more easily track your resources:

* Stato del sistema operativo: è possibile impostare lo stato del sistema operativo su [generalizzato o specializzato.](#generalized-and-specialized-images)
* Sistema operativo - può essere Windows o Linux.
* Descrizione - utilizzare la descrizione per fornire informazioni più dettagliate sul motivo per cui esiste la definizione dell'immagine. Ad esempio, si potrebbe avere una definizione di immagine per il server front-end con l'applicazione preinstallata.
* Eula - può essere utilizzato per puntare a un contratto di licenza con l'utente finale specifico per la definizione dell'immagine.
* Informativa sulla privacy e note sulla versione: archiviare le note sulla versione e le informative sulla privacy nell'archiviazione di Azure e fornire un URI per l'accesso come parte della definizione dell'immagine.
* Data di fine vita: allega una data di fine del ciclo di vita alla definizione dell'immagine per poter utilizzare l'automazione per eliminare le vecchie definizioni di immagine.
* Tag: è possibile aggiungere tag quando si crea la definizione dell'immagine. Per altre informazioni sui tag, vedere [Utilizzo dei tag per organizzare le risorseFor](../articles/azure-resource-manager/management/tag-resources.md) more information about tags, see Using tags to organize your resources
* Raccomandazioni minime e massime di vCPU e memoria: se l'immagine include suggerimenti per vCPU e memoria, è possibile allegare tali informazioni alla definizione dell'immagine.
* Tipi di disco non consentiti: è possibile fornire informazioni sulle esigenze di archiviazione per la macchina virtuale. Ad esempio, se l'immagine non è adatta per i dischi HDD standard, aggiungerli all'elenco di autorizzazioni.

## <a name="generalized-and-specialized-images"></a>Immagini generalizzate e specializzate

Esistono due stati del sistema operativo supportati da Shared Image Gallery. In genere le immagini richiedono che la macchina virtuale usata per creare l'immagine sia stata generalizzata prima di prendere l'immagine. Generalizzazione è un processo che rimuove le informazioni specifiche del computer e dell'utente dalla macchina virtuale. Per Windows viene utilizzato anche Sysprep. Per Linux, è possibile `-deprovision+user` utilizzare [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` o parametri.

Le macchine virtuali specializzate non sono state sottoposte a un processo per rimuovere account e informazioni specifiche del computer. Inoltre, le macchine virtuali create da `osProfile` immagini specializzate non dispongono di un associato. Ciò significa che le immagini specializzate avranno alcune limitazioni.

- Gli account che possono essere usati per accedere alla macchina virtuale possono essere usati anche in qualsiasi macchina virtuale creata usando l'immagine specializzata creata da tale macchina virtuale.
- Le macchine virtuali avranno il **nome computer** della macchina virtuale da cui è stata scattata l'immagine. È necessario modificare il nome del computer per evitare conflitti.
- Il `osProfile` è il modo in cui alcune `secrets`informazioni riservate vengono passate alla macchina virtuale, utilizzando . Ciò può causare problemi utilizzando KeyVault, `secrets` Gestione `osProfile`remota Windows e altre funzionalità utilizzate nel file . In alcuni casi, è possibile utilizzare le identità del servizio gestito (MSI) per aggirare queste limitazioni.

> [!IMPORTANT]
> Le immagini specializzate sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni note dell'anteprima** Le macchine virtuali possono essere create solo da immagini specializzate usando il portale o l'API. Il non è un supporto CLI o PowerShell per l'anteprima.


## <a name="regional-support"></a>Supporto a livello di area

Le aree di origine sono elencate nella tabella seguente. Tutte le aree pubbliche possono essere aree di destinazione, ma per eseguire la replica in Australia Central e Australia Central 2 è necessario avere la sottoscrizione inserita nella whitelist. Per richiedere l'inserimento nella whitelist, passare a: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regioni di origine        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australia centrale     | Cina orientale        | India meridionale        | Europa occidentale        |
| Australia centrale 2   | Cina orientale 2      | Asia sud-orientale     | Regno Unito meridionale           |
| Australia orientale        | Cina settentrionale       | Giappone orientale         | Regno Unito occidentale            |
| Australia sud-orientale   | Cina settentrionale 2     | Giappone occidentale         | US DoD (area centrale)     |
| Brasile meridionale          | Asia orientale         | Corea centrale      | US DoD (area orientale)        |
| Canada centrale        | Stati Uniti orientali           | Corea meridionale        | US Gov Arizona     |
| Canada orientale           | Stati Uniti orientali 2         | Stati Uniti centro-settentrionali   | US Gov Texas       |
| India centrale         | Stati Uniti orientali 2 EUAP    | Europa settentrionale       | US Gov Virginia    |
| Stati Uniti centrali            | Francia centrale    | Stati Uniti centro-meridionali   | India occidentale         |
| Stati Uniti centrali EUAP       | Francia meridionale      | Stati Uniti centro-occidentali    | Stati Uniti occidentali            |
|                       |                   |                    | Stati Uniti occidentali 2          |



## <a name="limits"></a>Limiti 

Esistono limiti, per ogni sottoscrizione, per la distribuzione delle risorse tramite le raccolte di immagini condivise:There are limits, per subscription, for deploying resources using Shared Image Galleries:
- 100 gallerie di immagini condivise, per sottoscrizione, per area geografica
- 1.000 definizioni di immagini, per sottoscrizione, per area
- 10.000 versioni di immagini, per sottoscrizione, per area
- Qualsiasi disco collegato all'immagine deve essere minore o uguale a 1 TB di dimensione

Per altre informazioni, vedere [Controllare l'utilizzo delle risorse rispetto ai limiti](https://docs.microsoft.com/azure/networking/check-usage-against-limits) per esempi su come controllare l'utilizzo corrente.
 
## <a name="scaling"></a>Scalabilità
Raccolta di immagini condivise consente di specificare il numero di repliche delle immagini che si desidera vengano conservate da Azure. Questa possibilità è particolarmente utile in scenari di distribuzione di più macchine virtuali, poiché le distribuzioni di macchine virtuali possono essere estese a diverse repliche, riducendo le possibilità che il processo di creazione di istanze venga limitato a causa dell'overload di una singola replica.

Con Shared Image Gallery, è ora possibile distribuire fino a 1.000 istanze di macchine virtuali in un set di scalabilità di macchine virtuali (a partire da 600 con immagini gestite). Le repliche delle immagini consentono di migliorare le prestazioni, l'affidabilità e la coerenza della distribuzione. È possibile impostare un numero di repliche diverso in ogni area di destinazione, in base alle esigenze di scalabilità per l'area. Poiché ogni replica è una copia completa dell'immagine, ciò consente di scalare le distribuzioni in modo lineare con ogni replica aggiuntiva. Anche se sappiamo che non esistono due immagini o aree uguali, ecco le linee guida generali su come usare le repliche in un'area:

- Per le distribuzioni di VMSS (Virtual Machine Scale Set): per ogni 20 macchine virtuali create contemporaneamente, è consigliabile mantenere una replica. Ad esempio, se si creano 120 macchine virtuali contemporaneamente usando la stessa immagine in un'area, è consigliabile mantenere almeno 6 repliche dell'immagine. 
- Per le distribuzioni di Virtual Machine Scale Set (VMSS): per ogni distribuzione del set di scalabilità con un massimo di 600 istanze, è consigliabile mantenere almeno una replica. Ad esempio, se si creano 5 set di scalabilità contemporaneamente, ognuno con 600 istanze di macchine virtuali che usano la stessa immagine in una singola area, è consigliabile mantenere almeno 5 repliche dell'immagine. 

Ti consigliamo sempre di eseguire il provisioning eccessivo del numero di repliche a causa di fattori come le dimensioni dell'immagine, il contenuto e il tipo di sistema operativo.

![Immagine che mostra come ridimensionare le immagini](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Rendi le tue immagini a disponibilità elevata

[Archiviazione ridondante](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) della zona di Azure offre resilienza in caso di errore della zona di disponibilità nell'area. Con la disponibilità generale di Shared Image Gallery, è possibile scegliere di archiviare le immagini negli account .RS nelle aree con zone di disponibilità. 

Puoi anche scegliere il tipo di account per ciascuna delle aree di destinazione. Il tipo di account di archiviazione predefinito è Standard_LRS, ma è possibile scegliere Standard_ZRS per le aree con zone di disponibilità. Controllare la disponibilità regionale di . [here](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)

![Immagine che mostra gli effetti ortografici](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replica
Raccolta di immagini condivise consente inoltre di replicare automaticamente le immagini in altre aree di Azure. Ogni versione di immagini condivise può essere replicata in diverse aree a seconda delle esigenze della propria organizzazione. Ad esempio, è possibile replicare sempre l'immagine più recente in più aree, mentre tutte le versioni precedenti sono disponibili solo in un'area. Questo permette di risparmiare sui costi di archiviazione per le versioni di immagini condivise. 

Le aree in cui la versione di immagini condivise viene replicata possono essere aggiornate dopo l'ora di creazione. Il tempo necessario per la replica in aree diverse dipende dalla quantità di dati da copiare e dal numero di aree in cui viene replicata la versione. In alcuni casi, questa operazione può richiedere alcune ore. Mentre le replica è in corso, è possibile visualizzare lo stato della replica per area. Al termine della replica dell'immagine in un'area, è possibile distribuire una macchina virtuale o un set di scalabilità usando tale versione dell'immagine nell'area.

![Immagine che mostra come replicare le immagini](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Accesso

Poiché la raccolta di immagini condivise, la definizione dell'immagine e la versione immagine sono tutte risorse, possono essere condivise usando i controlli RBAC di Azure nativi incorporati. Usando il controllo degli accessi in base al ruolo è possibile condividere queste risorse con altri utenti, entità servizio e gruppi. È anche possibile condividere l'accesso a persone esterne al tenant in cui sono state create. Una volta che un utente ha accesso alla versione Shared Image, può distribuire una macchina virtuale o un set di scalabilità di macchine virtuali.  Di seguito è riportata la matrice di condivisione che consente all'utente di riconoscere a cosa ha accesso:

| Condivisi con l'utente     | Raccolta di immagini condivise | Definizione delle immagini | Versione dell'immagine |
|----------------------|----------------------|--------------|----------------------|
| Raccolta di immagini condivise | Sì                  | Sì          | Sì                  |
| Definizione delle immagini     | No                   | Sì          | Sì                  |

Ti consigliamo di condividere a livello della Galleria per un'esperienza ottimale. Non è consigliabile condividere singole versioni di immagini. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere Gestire l'accesso alle risorse di Azure usando [il controllo degli accessi in base al ruolo.](../articles/role-based-access-control/role-assignments-portal.md)

Le immagini possono anche essere condivise, su larga scala, anche tra tenant usando una registrazione di app multi-tenant. Per altre informazioni sulla condivisione di immagini tra tenant, vedere Condividere immagini di macchine virtuali della raccolta tra tenant di Azure.For more information about sharing images across tenants, see [Share gallery VM images across Azure tenants.](../articles/virtual-machines/linux/share-images-across-tenants.md)

## <a name="billing"></a>Fatturazione
Non sono previsti addebiti aggiuntivi per l'uso del servizio Raccolta di immagini condivise. Vengono addebitate le risorse seguenti:
- Costi di archiviazione per le versioni di immagini condivise. Il costo dipende dal numero di repliche della versione dell'immagine e dal numero di aree in cui viene replicata la versione. Ad esempio, se si dispone di 2 immagini ed entrambe vengono replicate in 3 aree, verranno addebitati 6 dischi gestiti in base alle dimensioni. Per ulteriori informazioni, vedere [Determinazione dei prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/).
- Costi in uscita di rete per la replica della prima versione dell'immagine dall'area di origine alle aree replicate. Le repliche successive vengono gestite all'interno dell'area, pertanto non sono previsti costi aggiuntivi. 

## <a name="updating-resources"></a>Aggiornamento delle risorse

Una volta creato, è possibile apportare alcune modifiche alle risorse della raccolta immagini. Questi sono limitati a:
 
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
- Escludi dal più recente
- Data di scadenza

## <a name="sdk-support"></a>Supporto SDK

Gli SDK seguenti supportano la creazione di raccolte di immagini condivise:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [invio](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelli

È possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Domande frequenti 

* [Come elencare tutte le risorse di Raccolta di immagini condivise tra le sottoscrizioni?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [È possibile spostare l'immagine esistente nella raccolta di immagini condivise?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [È possibile creare una versione di immagine da un disco specializzato?](#can-i-create-an-image-version-from-a-specialized-disk)
* [È possibile spostare la risorsa Raccolta immagini condivise in una sottoscrizione diversa dopo che è stata creata?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [È possibile replicare le versioni delle immagini in cloud come Azure China 21Vianet o Azure Germania o Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [È possibile replicare le versioni delle immagini tra sottoscrizioni diverse?](#can-i-replicate-my-image-versions-across-subscriptions)
* [È possibile condividere versioni delle immagini nei tenant di Azure Active Directory?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo occorre per replicare le versioni delle immagini tra le aree di destinazione?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual è la differenza tra area di origine e area di destinazione?](#what-is-the-difference-between-source-region-and-target-region)
* [Come si specifica l'area di origine durante la creazione della versione dell'immagine?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Come specificare il numero di repliche della versione dell'immagine da creare in ogni area?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [È possibile creare la raccolta immagini condivisa in una posizione diversa da quella per la definizione dell'immagine e la versione dell'immagine?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quali sono gli addebiti per l'uso della Raccolta di immagini condivise?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Quale versione dell'API devo usare per creare Shared Image Gallery e Image Definition and Image Version?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Quale versione dell'API devo usare per creare una macchina virtuale condivisa o un set di scalabilità di macchine virtuali dalla versione dell'immagine?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Come elencare tutte le risorse di Raccolta di immagini condivise tra le sottoscrizioni?

Per elencare tutte le risorse della raccolta immagini condivise tra le sottoscrizioni a cui si ha accesso nel portale di Azure, eseguire la procedura seguente:To list all the Shared Image Gallery resources across subscriptions that you have access to on the Azure portal, follow the steps below:

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare a **Tutte le risorse**.
1. Selezionare tutte le sottoscrizioni in cui si desidera elencare tutte le risorse.
1. Identificare le risorse di tipo **Raccolta privata**.
 
   Per visualizzare le definizioni di immagini e le versioni di immagini, è necessario selezionare anche **Mostra tipi nascosti**.
 
   Per elencare tutte le risorse della raccolta di immagini condivise tra le sottoscrizioni a cui si ha accesso, usare il seguente comando nell'interfaccia della riga di comando di Azure:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>È possibile spostare l'immagine esistente nella raccolta di immagini condivise?
 
Sì. Esistono tre scenari in base ai tipi di immagini che si possono avere.

 Scenario 1: se si dispone di un'immagine gestita nella stessa sottoscrizione del SIG, è possibile creare una definizione dell'immagine e una versione dell'immagine da essa.

 Scenario 2: se si dispone di un'immagine non gestita nella stessa sottoscrizione del SIG, è possibile creare un'immagine gestita da essa e quindi creare una definizione dell'immagine e una versione dell'immagine da essa. 

 Scenario 3: se si dispone di un disco rigido virtuale nel file system locale, è necessario caricare il disco rigido virtuale in un'immagine gestita, è possibile creare una definizione dell'immagine e una versione dell'immagine da esso.

- Se il disco rigido virtuale è di una macchina virtuale Windows, vedere [Caricare un disco rigido virtuale.](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)
- Se il disco rigido virtuale è per una macchina virtuale Linux, vedere [Caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>È possibile creare una versione di immagine da un disco specializzato?

Sì, il supporto per dischi specializzati mentre le immagini sono in anteprima. È possibile creare una macchina virtuale solo da un'immagine specializzata usando il portale ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) o [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) e l'API. Non è disponibile alcun supporto di PowerShell per l'anteprima.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>È possibile spostare la risorsa Raccolta immagini condivise in una sottoscrizione diversa dopo che è stata creata?

No, non è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa. Tuttavia, è possibile replicare le versioni delle immagini della raccolta in altre aree secondo necessità.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>È possibile replicare le versioni delle immagini in cloud come Azure China 21Vianet o Azure Germania o Azure Government Cloud?

No, non è possibile replicare le versioni delle immagini in diversi cloud.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>È possibile replicare le versioni delle immagini tra sottoscrizioni diverse?

No, è possibile replicare le versioni delle immagini nelle aree in una sottoscrizione e usarle in altre sottoscrizioni tramite RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>È possibile condividere versioni delle immagini nei tenant di Azure Active Directory? 

Sì, è possibile usare il controllo degli accessi in base al ruolo per condividere con utenti tra utenti tra tenant. Tuttavia, per condividere su larga scala, vedere "Condividere le immagini della raccolta tra tenant di Azure" usando [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) o [l'interfaccia della riga](../articles/virtual-machines/linux/share-images-across-tenants.md)di comando.

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

Per specificare il numero di repliche regionali, passare il percorso insieme al numero di repliche che si desidera creare in tale area: "Stati Uniti centro-meridionali 2". 

Se il conteggio delle repliche a livello di area non è specificato per ogni posizione, il numero predefinito di repliche sarà il conteggio comune delle repliche comuni che è stato specificato. 

Per specificare il numero comune di repliche nella riga di comando, usare l'argomento **conteggio di repliche** nel comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>È possibile creare la raccolta immagini condivisa in una posizione diversa da quella per la definizione dell'immagine e la versione dell'immagine?

Sì, è possibile. Tuttavia, come procedura consigliata, ti invitiamo a mantenere il gruppo di risorse, la raccolta di immagini condivise, la definizione dell'immagine e la versione dell'immagine nello stesso percorso.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quali sono gli addebiti per l'uso della Raccolta di immagini condivise?

Non sono previsti addebiti per l'uso del servizio Raccolta di immagini condivise, ad eccezione delle spese per l'archiviazione delle versioni di immagini e dei costi di rete di uscita per la replica di versioni di immagini dall'area di origine alle aree di destinazione.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Quale versione dell'API devo usare per creare Shared Image Gallery e Image Definition and Image Version?

Per lavorare con le raccolte di immagini condivise, le definizioni di immagini e le versioni dell'immagine, si consiglia di usare la versione API 2018-06-01. L'archiviazione ridondante dell'area richiede la versione 2019-03-01 o successiva.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Quale versione dell'API devo usare per creare una macchina virtuale condivisa o un set di scalabilità di macchine virtuali dalla versione dell'immagine?

Per le distribuzioni di macchine virtuali e set di scalabilità di macchine virtuali eseguite usando una versione di immagine, si consiglia di usare la versione API 2018-04-01 o una successiva.
