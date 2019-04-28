---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 8c7da8d04b456642b158dda77d9c745891aa18e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620396"
---
Raccolta di immagini condivise è un servizio che consente di compilare la struttura e l'organizzazione per le immagini di macchine virtuali gestite e personalizzate. Usando una raccolta di immagini condivise è possibile condividere le immagini con utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione. Le immagini condivise possono essere replicate in più aree, per un ridimensionamento più rapido delle distribuzioni.

Un'immagine gestita è una copia di una macchina virtuale completa (inclusi eventuali dischi dati allegati) o solo del disco del sistema operativo, a seconda del modo in cui si crea l'immagine. Quando si crea una macchina virtuale dall'immagine, la copia dei dischi rigidi virtuali dell'immagine viene usata per creare i dischi per la nuova macchina virtuale. L'immagine gestita rimane nella risorsa di archiviazione e può essere usata ripetutamente per creare nuove macchine virtuali.

Se si dispone di un numero elevato di immagini gestite da mantenere e si desidera renderle disponibili in tutta l'azienda, è possibile usare una raccolta di immagini condivise come repository che semplifica l'aggiornamento e la condivisione delle immagini. Gli addebiti per l'uso di una raccolta di immagini condivise sono semplicemente i costi per la risorsa di archiviazione usata dalle immagini, oltre a eventuali costi di uscita dalla rete per la replica di immagini dall'area di origine alle aree pubblicate.

La funzionalità Raccolta di immagini condivise presenta più tipi di risorse:

| Risorsa | DESCRIZIONE|
|----------|------------|
| **Immagine gestita** | Si tratta di un'immagine di base che può essere usata da sola o per creare una **versione dell'immagine** in una raccolta di immagini. Le immagini gestite vengono create da macchine virtuali generalizzate. Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini vengono definite all'interno di una raccolta e forniscono informazioni sull'immagine e sui requisiti per il suo uso interno. Questa include il fatto che l'immagine è per Windows o Linux, le note sulla versione e i requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>


![Immagine che mostra in che modo è possibile avere più versioni di un'immagine nella propria raccolta](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Supporto a livello di area

Il supporto a livello di area per le raccolte di immagini condivise è disponibile in anteprima limitata, ma si espanderà nel corso del tempo. Per l'anteprima limitata, di seguito sono riportati gli elenchi che specificano dove è possibile creare le raccolte e in quale aree è possibile replicare un'immagine della raccolta: 

| Crea raccolta in  | Replica versione in |
|--------------------|----------------------|
| Stati Uniti centro-occidentali    |Tutte le aree pubbliche &#42;|
| Stati Uniti orientali 2          ||
| Stati Uniti centro-meridionali   ||
| Asia sud-orientale     ||
| Europa occidentale        ||
| Stati Uniti occidentali            ||
| Stati Uniti orientali            ||
| Canada centrale     ||
|                    ||



&#42; Per la replica in Australia centrale e Australia centrale 2 è necessario che la sottoscrizione sia inserita nell'elenco elementi consentiti. Per richiedere l'inserimento nella whitelist, passare a: https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>Ridimensionamento
Raccolta di immagini condivise consente di specificare il numero di repliche delle immagini che si desidera vengano conservate da Azure. Questa possibilità è particolarmente utile in scenari di distribuzione di più macchine virtuali, poiché le distribuzioni di macchine virtuali possono essere estese a diverse repliche, riducendo le possibilità che il processo di creazione di istanze venga limitato a causa dell'overload di una singola replica.

![Immagine che mostra come ridimensionare le immagini](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replica
Raccolta di immagini condivise consente inoltre di replicare automaticamente le immagini in altre aree di Azure. Ogni versione di immagini condivise può essere replicata in diverse aree a seconda delle esigenze della propria organizzazione. Ad esempio, è possibile replicare sempre l'immagine più recente in più aree, mentre tutte le versioni precedenti sono disponibili solo in un'area. Questo permette di risparmiare sui costi di archiviazione per le versioni di immagini condivise. 

Le aree in cui la versione di immagini condivise viene replicata possono essere aggiornate dopo l'ora di creazione. Il tempo necessario per la replica in aree diverse dipende dalla quantità di dati da copiare e dal numero di aree in cui viene replicata la versione. In alcuni casi, questa operazione può richiedere alcune ore. Mentre le replica è in corso, è possibile visualizzare lo stato della replica per area. Al termine della replica di un'immagine in un'area, è quindi possibile distribuire una macchina virtuale o un set di scalabilità di macchine virtuali usando la versione dell'immagine disponibile nell'area.

![Immagine che mostra come replicare le immagini](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Accesso
Poiché la raccolta di immagini condivise, l'immagine condivisa e la versione dell'immagine condivisa sono tutte risorse, possono essere condivise usando i controlli degli accessi in base al ruolo nativi di Azure. Usando il RBAC è possibile condividere tali risorse con altri utenti, entità servizio e gruppi nella propria organizzazione. L'ambito di condivisione di queste risorse è all'interno dello stesso tenant di Azure Active Directory. Una volta che un utente ha accesso alla versione di immagini condivise, può distribuire una macchina virtuale o un set di scalabilità di macchine virtuali in una qualsiasi delle sottoscrizioni a cui ha accesso all'interno dello stesso tenant di Azure AD della versione di immagini condivise.  Di seguito è riportata la matrice di condivisione che consente all'utente di riconoscere a cosa ha accesso:

| Condivisi con l'utente     | Raccolta di immagini condivise | Immagine condivisa | Versione di immagini condivise |
|----------------------|----------------------|--------------|----------------------|
| Raccolta di immagini condivise | Sì                  | Sì          | Sì                  |
| Immagine condivisa         | No                    | Sì          | Sì                  |
| Versione di immagini condivise | No                    | No            | Sì                  |



## <a name="billing"></a>Fatturazione
Non sono previsti addebiti aggiuntivi per l'uso del servizio Raccolta di immagini condivise. Vengono addebitate le risorse seguenti:
- Costi di archiviazione per le versioni di immagini condivise. Dipende dal numero di repliche della versione e dal numero di aree in cui la versione viene replicata.
- Addebiti in uscita di rete per la replica dall'area di origine della versione alle aree replicate.

## <a name="sdk-support"></a>Supporto SDK

Gli SDK seguenti supportano la creazione di raccolte di immagini condivise:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Modelli

È possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Domande frequenti 

**D.** Come iscriversi all'anteprima pubblica di Raccolta di immagini condivise?
 
 R. Per iscriversi all'anteprima pubblica di Raccolta di immagini condivise, è necessario effettuare la registrazione per la funzionalità eseguendo i comandi seguenti da ognuna delle sottoscrizioni in cui si intende creare una raccolta di immagini condivise, una definizione di immagine o risorse di versione di immagine e anche di quelle in cui si prevede di distribuire le macchine virtuali usando le versioni dell'immagine.

**Interfaccia della riga di comando**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**D.** Come si condividono immagini personali tra sottoscrizioni?
 
 R. È possibile condividere immagini tra sottoscrizioni usando il Controllo di accesso in base al ruolo (RBAC). Qualsiasi utente che disponga di autorizzazioni di lettura per la versione immagine, perfino tra sottoscrizioni, riuscirà a distribuire una macchina virtuale usando la versione immagine.


**D.** È possibile spostare l'immagine esistente nella raccolta di immagini condivise?
 
 R. Sì. Esistono tre scenari in base ai tipi di immagini che si possono avere.

 Scenario 1: se si dispone di un'immagine gestita, è possibile creare una definizione di immagine e una versione dell'immagine a partire da essa.

 Scenario 2: se si dispone di un'immagine generalizzata non gestita, è possibile creare un'immagine gestita da quest'ultima e quindi creare una definizione di immagine e la versione dell'immagine da essa. 

 Scenario 3: se si dispone di un disco rigido virtuale nel file system locale, è necessario caricare il disco rigido virtuale e creare un'immagine gestita; quindi è possibile creare una definizione di immagine e una versione dell'immagine da quest'ultima.
- Se il disco rigido virtuale è di una macchina virtuale Windows, vedere [Caricare un disco rigido virtuale generalizzato](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se il disco rigido virtuale è per una macchina virtuale Linux, vedere [Caricare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**D.** È possibile creare una versione di immagine da un disco specializzato?

 R. No, attualmente non sono supportati dischi specializzati come immagini. Se si dispone di un disco specializzato, è necessario [creare una macchina virtuale dal disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) collegando il disco specializzato a una nuova macchina virtuale. Una volta che si dispone di una macchina virtuale in esecuzione, è necessario seguire le istruzioni per creare un'immagine gestita dalla [macchina virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) oppure dalla [macchina virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Una volta che si dispone di un'immagine gestita generalizzata, è possibile avviare il processo per creare una descrizione di immagine condivisa e una versione dell'immagine.


**D.** È possibile creare una raccolta di immagini condivise, una definizione di immagine e una versione dell'immagine tramite il portale di Azure?

 R. No, attualmente non è supportata la creazione di alcuna risorsa di Raccolta di immagini condivise tramite il portale di Azure. Ad ogni modo, è supportata la creazione delle risorse di Raccolta di immagini condivise tramite l'interfaccia della riga di comando, Modelli e SDK. Anche PowerShell verrà presto rilasciato.

 
**D.** Una volta creata, è possibile aggiornare la definizione di immagine o la versione dell'immagine? Che tipologia di dettagli è possibile modificare?

 R. I dettagli che possono essere aggiornati in ogni risorsa sono indicati di seguito:
 
Raccolta di immagini condivise:
- DESCRIZIONE

Definizione delle immagini:
- VCPU consigliati
- Memoria
- DESCRIZIONE
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Esclusione dalla versione più recente
- Data di scadenza


**D.** Una volta creata, è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa?

 R. No, non è possibile spostare la risorsa Raccolta di immagini condivise in una sottoscrizione diversa. Tuttavia, è possibile replicare le versioni delle immagini della raccolta in altre aree secondo necessità.

**D.** È possibile replicare le versioni delle immagini in diversi cloud quali Azure 21Vianet, Azure Germania e Cloud di Azure per enti pubblici? 

 R. No, non è possibile replicare le versioni delle immagini in diversi cloud.

**D.** È possibile replicare le versioni delle immagini tra sottoscrizioni diverse? 

 R. No, è possibile replicare le versioni delle immagini nelle aree in una sottoscrizione e usarle in altre sottoscrizioni tramite RBAC.

**D.** È possibile condividere versioni delle immagini nei tenant di Azure Active Directory? 

 R. No, la raccolta di immagini condivise attualmente non supporta la condivisione delle versioni delle immagini tra tenant di Azure AD diversi. È possibile tuttavia usare la funzionalità relativa alle offerte private in Azure Marketplace per ottenere questo risultato.


**D.** Quanto tempo occorre per replicare le versioni delle immagini tra le aree di destinazione?

 R. Il tempo di replica della versione dell'immagine dipende interamente dalle dimensioni dell'immagine e dal numero di aree in cui viene replicata. Tuttavia, come procedura consigliata, si consiglia di mantenere l'immagine piccola e le aree di origine e di destinazione vicine per ottenere risultati ottimali. È possibile controllare lo stato della replica usando il flag ReplicationStatus.


**D.** Quante raccolte di immagini condivise è possibile creare in una sottoscrizione?

 R. La quantità predefinita è: 
- 10 raccolte di immagini condivise, per ogni sottoscrizione per area
- 200 definizioni di immagini, per ogni sottoscrizione per area
- 2000 versionidi immagini, per ogni sottoscrizione per area


**D.** Qual è la differenza tra area di origine e area di destinazione?

 R. L'area di origine è l'area in cui viene creata la versione dell'immagine e le aree di destinazione sono le aree in cui viene archiviata una copia della versione dell'immagine. Per ogni versione dell'immagine, è possibile avere solo un'area di origine. Inoltre, assicurarsi di trasmettere la posizione dell'area di origine come una delle aree di destinazione quando si crea una versione di immagine.  


**D.** Come si specifica l'area di origine durante la creazione della versione dell'immagine?

 R. Durante la creazione di una versione dell'immagine, è possibile usare il tag **posizione** all'interno dell'interfaccia della riga di comando e il tag **Posizione** in PowerShell per specificare l'area di origine. Assicurarsi che l'immagine gestita in uso come immagine di base per creare la versione dell'immagine sia nella stessa posizione in cui si intende creare la versione dell'immagine. Inoltre, assicurarsi di trasmettere la posizione dell'area di origine come una delle aree di destinazione quando si crea una versione di immagine.  


**D.** Come specificare il numero di repliche della versione dell'immagine da creare in ogni area?

 R. Esistono due modi in cui è possibile specificare il numero delle repliche di versione dell'immagine da creare in ogni area:
 
1. Il conteggio delle repliche a livello di area che specifica il numero di repliche che si desidera creare per ogni area. 
2. Il conteggio comune delle repliche, che è il conteggio predefinito per area nel caso in cui non sia specificato il conteggio di repliche a livello di area. 

Per specificare il conteggio di repliche a livello di area, trasmettere la posizione e il numero di repliche che si vuole creare in quell'area in questo modo: "Stati Uniti centro-meridionali=2". 

Se il conteggio delle repliche a livello di area non è specificato per ogni posizione, il numero predefinito di repliche sarà il conteggio comune delle repliche comuni che è stato specificato. 

Per specificare il numero comune di repliche nella riga di comando, usare l'argomento **conteggio di repliche** nel comando `az sig image-version create`.


**D.** È possibile creare la raccolta di immagini condivise in una posizione diversa da quella in cui si desidera creare la definizione di immagine e la versione dell'immagine?

 R. Sì, è possibile. Come procedura consigliata, è opportuno mantenere gruppo di risorse, raccolta di immagini condivise, definizione dell'immagine e versione dell'immagine nella stessa posizione.


**D.** Quali sono gli addebiti per l'uso della Raccolta di immagini condivise?

 R. Non sono previsti addebiti per l'uso del servizio Raccolta di immagini condivise, ad eccezione delle spese per l'archiviazione delle versioni di immagini e dei costi di rete di uscita per la replica di versioni di immagini dall'area di origine alle aree di destinazione.

**D.** Quale versione API è necessario usare per creare Raccolta di immagini condivise, Definizione immagine, Versione immagine e VM/VMSS della Versione immagine?

 R. Per le distribuzioni di macchine virtuali e set di scalabilità di macchine virtuali eseguite usando una versione di immagine, si consiglia di usare la versione API 2018-04-01 o una successiva. Per lavorare con le raccolte di immagini condivise, le definizioni di immagini e le versioni dell'immagine, si consiglia di usare la versione API 2018-06-01. 
