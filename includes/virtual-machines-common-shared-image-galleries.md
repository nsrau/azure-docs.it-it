---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 529a8b6136a5d9c69b044df2614644bdbd4fd4f4
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012164"
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

| Risorsa | Descrizione|
|----------|------------|
| **Immagine gestita** | Un'immagine di base che può essere usata da sola o usata per creare una **versione dell'immagine** in una raccolta di immagini. Le immagini gestite vengono create da macchine virtuali generalizzate. Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini vengono definite all'interno di una raccolta e contengono informazioni sull'immagine e sui requisiti per l'uso all'interno dell'organizzazione. È possibile includere informazioni come se l'immagine sia Windows o Linux, i requisiti di memoria minimi e massimi e le note sulla versione. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>


![Immagine che mostra in che modo è possibile avere più versioni di un'immagine nella propria raccolta](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definizioni immagini

Le definizioni di immagine sono un raggruppamento logico per le versioni di un'immagine. La definizione dell'immagine include informazioni sui motivi per cui è stata creata l'immagine, sul sistema operativo per cui è stata creata e sulle informazioni sull'utilizzo dell'immagine. La definizione di un'immagine è simile a un piano per tutti i dettagli relativi alla creazione di un'immagine specifica. Non si distribuisce una macchina virtuale da una definizione di immagine, ma dalla versione dell'immagine creata dalla definizione.


Esistono tre parametri per ogni definizione di immagine usati in combinazione- **autore**, **offerta** e **SKU**. Questi vengono usati per trovare una definizione di immagine specifica. È possibile avere versioni delle immagini che condividono uno o due, ma non tutti e tre i valori.  Di seguito, un esempio di tre definizioni di immagini con i relativi valori:

|Definizione delle immagini|Pubblicato da|Offerta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanza|Back-end|
|myImage2|Contoso|Finanza|Front-end|
|myImage3|Test|Finanza|Front-end|

Questi tre presentano set univoci di valori. Il formato è simile a quello in cui è attualmente possibile specificare il server di pubblicazione, l'offerta e lo SKU per le [Immagini di Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) in Azure PowerShell per ottenere la versione più recente di un'immagine del Marketplace. Ogni definizione di immagine deve avere un set univoco di questi valori.

Di seguito sono riportati altri parametri che è possibile impostare nella definizione dell'immagine, in modo da poter tenere traccia più facilmente delle risorse:

* Stato del sistema operativo: è possibile impostare lo stato del sistema operativo su generalizzato o specializzato, ma è attualmente supportato solo generalizzato. Le immagini devono essere create da macchine virtuali generalizzate mediante Sysprep per Windows o `waagent -deprovision` per Linux.
* Sistema operativo: può essere Windows o Linux.
* Descrizione: usare Description per fornire informazioni più dettagliate sul motivo per cui la definizione dell'immagine esiste. Ad esempio, si potrebbe avere una definizione di immagine per il server front-end in cui è preinstallata l'applicazione.
* EULA-può essere usato per puntare a un contratto di licenza con l'utente finale specifico per la definizione dell'immagine.
* Informativa sulla privacy e note sulla versione: archivia le note sulla versione e le informative sulla privacy in archiviazione di Azure e fornisce un URI per accedervi come parte della definizione dell'immagine.
* Data fine del ciclo di vita: consente di aggiungere una data di fine vita alla definizione dell'immagine per poter usare l'automazione per eliminare le definizioni di immagini obsolete.
* Tag: è possibile aggiungere tag quando si crea la definizione dell'immagine. Per altre informazioni sui tag, vedere [uso dei tag per organizzare le risorse](../articles/azure-resource-manager/resource-group-using-tags.md)
* Raccomandazioni vCPU e Memory minime e massime: se l'immagine presenta vCPU e consigli per la memoria, è possibile alleghi tali informazioni alla definizione dell'immagine.
* Tipi di dischi non consentiti: è possibile fornire informazioni sulle esigenze di archiviazione per la macchina virtuale. Se, ad esempio, l'immagine non è adatta per i dischi HDD standard, è necessario aggiungerli all'elenco non consentiti.


## <a name="regional-support"></a>Supporto a livello di area

Le aree di origine sono elencate nella tabella seguente. Tutte le aree pubbliche possono essere aree di destinazione, ma per eseguire la replica in Australia centrale e Australia centrale 2 è necessario che la sottoscrizione sia consentita. Per richiedere l'inserimento nella whitelist, passare a: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Aree di origine |
|---------------------|-----------------|------------------|-----------------|
| Australia centrale   | Stati Uniti centrali EUAP | Corea del Sud centrale    | Stati Uniti centro-occidentali |
| Australia centrale 2 | Asia orientale       | Corea del Sud meridionale      | Europa occidentale     |
| Australia orientale      | East US         | Stati Uniti centro-settentrionali | India occidentale      |
| Australia sud-orientale | Stati Uniti orientali 2       | Europa settentrionale     | Stati Uniti occidentali         |
| Brasile meridionale        | Stati Uniti orientali 2 EUAP  | Stati Uniti centro-meridionali | Stati Uniti occidentali 2       |
| Canada centrale      | Francia centrale  | India meridionale      |                 |
| Canada orientale         | Francia meridionale    | Asia sud-orientale   |                 |
| India centrale       | Giappone orientale      | Regno Unito meridionale         |                 |
| Stati Uniti centrali          | Giappone occidentale      | Regno Unito occidentale          |                 |



## <a name="limits"></a>Limiti 

Per la distribuzione delle risorse tramite le raccolte di immagini condivise sono previsti limiti, per sottoscrizione:
- 100 raccolte di immagini condivise, per sottoscrizione, per area
- 1\.000 definizioni di immagine, per sottoscrizione, per area
- 10.000 versioni dell'immagine, per sottoscrizione, per area

Per altre informazioni, vedere [controllare l'utilizzo delle risorse rispetto ai limiti](https://docs.microsoft.com/azure/networking/check-usage-against-limits) per esempi su come controllare l'utilizzo corrente.
 

## <a name="scaling"></a>Ridimensionamento
Raccolta di immagini condivise consente di specificare il numero di repliche delle immagini che si desidera vengano conservate da Azure. Questa possibilità è particolarmente utile in scenari di distribuzione di più macchine virtuali, poiché le distribuzioni di macchine virtuali possono essere estese a diverse repliche, riducendo le possibilità che il processo di creazione di istanze venga limitato a causa dell'overload di una singola replica.


Con la raccolta di immagini condivise, è ora possibile distribuire fino a una macchina virtuale di 1.000 istanze in un set di scalabilità di macchine virtuali (fino a 600 con le immagini gestite). Le repliche di immagini offrono prestazioni, affidabilità e coerenza migliori per la distribuzione.  È possibile impostare un numero di repliche diverso in ogni area di destinazione, in base alle esigenze di scalabilità per l'area. Poiché ogni replica è una copia completa dell'immagine, questo consente di ridimensionare le distribuzioni in modo lineare con ogni replica aggiuntiva. Sebbene non siano state riconoscite due immagini o aree, di seguito sono riportate le linee guida generali su come usare le repliche in un'area:

- Ogni 20 macchine virtuali create simultaneamente, si consiglia di conservarne una. Se, ad esempio, si creano VM 120 contemporaneamente usando la stessa immagine in un'area, si consiglia di tenere almeno sei repliche dell'immagine. 
- Per ogni distribuzione di set di scalabilità con un massimo di 600 istanze, è consigliabile conservarne almeno una. Se, ad esempio, si creano 5 set di scalabilità simultaneamente, ognuno con 600 istanze di VM che usano la stessa immagine in un'unica area, si consiglia di tenere almeno 5 repliche dell'immagine. 

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

| Condivisi con l'utente     | Raccolta immagini condivisa | Definizione delle immagini | Versione grafica |
|----------------------|----------------------|--------------|----------------------|
| Raccolta immagini condivisa | Sì                  | Sì          | Yes                  |
| Definizione delle immagini     | No                   | Yes          | Sì                  |

Per un'esperienza ottimale, è consigliabile condividere a livello di raccolta. Non è consigliabile condividere le singole versioni dell'immagine. Per altre informazioni su RBAC, vedere [gestire l'accesso alle risorse di Azure con RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Le immagini possono anche essere condivise, su larga scala, anche tra i tenant usando una registrazione di app multi-tenant. Per altre informazioni sulla condivisione di immagini tra i tenant, vedere [condividere le immagini di VM tra i tenant di Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fatturazione
Non sono previsti addebiti aggiuntivi per l'uso del servizio Raccolta di immagini condivise. Vengono addebitate le risorse seguenti:
- Costi di archiviazione per le versioni di immagini condivise. Il costo dipende dal numero di repliche della versione dell'immagine e dal numero di aree in cui la versione viene replicata. Ad esempio, se si dispone di 2 immagini ed entrambe vengono replicate in 3 aree, si verrà modificati per 6 dischi gestiti in base alla relativa dimensione. Per ulteriori informazioni, vedere [Managed disks prezzi](https://azure.microsoft.com/pricing/details/managed-disks/).
- Addebiti per l'uscita di rete per la replica della prima versione immagine dall'area di origine alle aree replicate. Le repliche successive vengono gestite all'interno dell'area, quindi non sono previsti addebiti aggiuntivi. 

## <a name="updating-resources"></a>Aggiornamento delle risorse

Una volta creato, è possibile apportare alcune modifiche alle risorse della raccolta immagini. Sono limitati a:
 
Raccolta di immagini condivise:
- DESCRIZIONE

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- DESCRIZIONE
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
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelli

È possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Domande frequenti 

**D.** Come elencare tutte le risorse di Raccolta di immagini condivise tra le sottoscrizioni? 
 
 R. Per elencare tutte le risorse di Raccolta di immagini condivise tra le sottoscrizioni a cui si ha accesso nel portale di Azure, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare a **Tutte le risorse**.
1. Selezionare tutte le sottoscrizioni in cui si desidera elencare tutte le risorse.
1. Identificare le risorse di tipo **Raccolta privata**.
 
   Per visualizzare le definizioni di immagini e le versioni di immagini, è necessario selezionare anche **Mostra tipi nascosti**.
 
   Per elencare tutte le risorse della raccolta di immagini condivise tra le sottoscrizioni a cui si ha accesso, usare il seguente comando nell'interfaccia della riga di comando di Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**D.** È possibile spostare l'immagine esistente nella raccolta di immagini condivise?
 
 R. Sì. Esistono tre scenari in base ai tipi di immagini che si possono avere.

 Scenario 1: se si dispone di un'immagine gestita, è possibile creare una definizione di immagine e una versione dell'immagine a partire da essa.

 Scenario 2: se si dispone di un'immagine generalizzata non gestita, è possibile creare un'immagine gestita da quest'ultima e quindi creare una definizione di immagine e la versione dell'immagine da essa. 

 Scenario 3: se si dispone di un disco rigido virtuale nel file system locale, è necessario caricare il disco rigido virtuale e creare un'immagine gestita; quindi è possibile creare una definizione di immagine e una versione dell'immagine da quest'ultima.
- Se il disco rigido virtuale è di una macchina virtuale Windows, vedere [Caricare un disco rigido virtuale generalizzato](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se il disco rigido virtuale è per una macchina virtuale Linux, vedere [Caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**D.** È possibile creare una versione di immagine da un disco specializzato?

 R. No, attualmente non sono supportati dischi specializzati come immagini. Se si dispone di un disco specializzato, è necessario [creare una macchina virtuale dal disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) collegando il disco specializzato a una nuova macchina virtuale. Una volta che si dispone di una macchina virtuale in esecuzione, è necessario seguire le istruzioni per creare un'immagine gestita dalla [macchina virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) oppure dalla [macchina virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Una volta che si dispone di un'immagine gestita generalizzata, è possibile avviare il processo per creare una descrizione di immagine condivisa e una versione dell'immagine.

 
**D.** Una volta creata, è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa?

 R. No, non è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa. Tuttavia, è possibile replicare le versioni delle immagini della raccolta in altre aree secondo necessità.

**D.** È possibile replicare le versioni delle immagini in diversi cloud quali Azure 21Vianet, Azure Germania e Cloud di Azure per enti pubblici? 

 R. No, non è possibile replicare le versioni delle immagini in diversi cloud.

**D.** È possibile replicare le versioni delle immagini tra sottoscrizioni diverse? 

 R. No, è possibile replicare le versioni delle immagini nelle aree in una sottoscrizione e usarle in altre sottoscrizioni tramite RBAC.

**D.** È possibile condividere versioni delle immagini nei tenant di Azure Active Directory? 

 R. Sì, è possibile usare il controllo degli accessi in base al ruolo per condividere le singole persone nei tenant. Tuttavia, per condividere la scalabilità, vedere l'argomento "condividere immagini della raccolta tra i tenant di Azure" tramite [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) o l' [interfaccia](../articles/virtual-machines/linux/share-images-across-tenants.md)della riga di comando.


**D.** Quanto tempo occorre per replicare le versioni delle immagini tra le aree di destinazione?

 R. Il tempo di replica della versione dell'immagine dipende interamente dalle dimensioni dell'immagine e dal numero di aree in cui viene replicata. Tuttavia, come procedura consigliata, si consiglia di mantenere l'immagine piccola e le aree di origine e di destinazione vicine per ottenere risultati ottimali. È possibile controllare lo stato della replica usando il flag ReplicationStatus.


**D.** Qual è la differenza tra area di origine e area di destinazione?

 R. L'area di origine è l'area in cui viene creata la versione dell'immagine e le aree di destinazione sono le aree in cui viene archiviata una copia della versione dell'immagine. Per ogni versione dell'immagine, è possibile avere solo un'area di origine. Inoltre, assicurarsi di trasmettere la posizione dell'area di origine come una delle aree di destinazione quando si crea una versione di immagine.  


**D.** Come si specifica l'area di origine durante la creazione della versione dell'immagine?

 R. Durante la creazione di una versione dell'immagine, è possibile usare il tag **posizione** all'interno dell'interfaccia della riga di comando e il tag **Posizione** in PowerShell per specificare l'area di origine. Assicurarsi che l'immagine gestita in uso come immagine di base per creare la versione dell'immagine sia nella stessa posizione in cui si intende creare la versione dell'immagine. Inoltre, assicurarsi di trasmettere la posizione dell'area di origine come una delle aree di destinazione quando si crea una versione di immagine.  


**D.** Come specificare il numero di repliche della versione dell'immagine da creare in ogni area?

 R. Esistono due modi in cui è possibile specificare il numero delle repliche di versione dell'immagine da creare in ogni area:
 
1. Il conteggio delle repliche a livello di area che specifica il numero di repliche che si desidera creare per ogni area. 
2. Il conteggio comune delle repliche, che è il conteggio predefinito per area nel caso in cui non sia specificato il conteggio di repliche a livello di area. 

Per specificare il numero di repliche internazionali, passare il percorso insieme al numero di repliche che si vuole creare in tale area: "Stati Uniti centro-meridionali=2". 

Se il conteggio delle repliche a livello di area non è specificato per ogni posizione, il numero predefinito di repliche sarà il conteggio comune delle repliche comuni che è stato specificato. 

Per specificare il numero comune di repliche nella riga di comando, usare l'argomento **conteggio di repliche** nel comando `az sig image-version create`.


**D.** È possibile creare la raccolta di immagini condivise in una posizione diversa da quella in cui si desidera creare la definizione di immagine e la versione dell'immagine?

 R. Sì, è possibile. Tuttavia, come procedura consigliata, si consiglia di proteggere il gruppo di risorse, la raccolta di immagini condivise, la definizione dell'immagine e la versione dell'immagine nella stessa posizione.


**D.** Quali sono gli addebiti per l'uso della Raccolta di immagini condivise?

 R. Non sono previsti addebiti per l'uso del servizio Raccolta di immagini condivise, ad eccezione delle spese per l'archiviazione delle versioni di immagini e dei costi di rete di uscita per la replica di versioni di immagini dall'area di origine alle aree di destinazione.

**D.** Quale versione API è necessario usare per creare Raccolta di immagini condivise, Definizione immagine, Versione immagine e VM/VMSS della Versione immagine?

 R. Per le distribuzioni di macchine virtuali e set di scalabilità di macchine virtuali eseguite usando una versione di immagine, si consiglia di usare la versione API 2018-04-01 o una successiva. Per lavorare con le raccolte di immagini condivise, le definizioni di immagini e le versioni dell'immagine, si consiglia di usare la versione API 2018-06-01. Per l'archiviazione con ridondanza della zona (ZRS) è richiesta la versione 2019-03-01 o successiva.
