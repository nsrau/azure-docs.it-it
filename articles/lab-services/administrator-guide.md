---
title: Azure Lab Services - Guida dell'amministratore | Microsoft Docs
description: Questa guida consente agli amministratori che creano e gestiscono gli account Lab usando Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462468"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Guida dell'amministratore
Gli amministratori IT che gestiscono le risorse cloud di un'università sono in genere responsabili della configurazione dell'account Lab per la propria scuola. Dopo aver configurato un account Lab, gli amministratori o gli educatori creano i Lab contenuti nell'account. Questo articolo fornisce una panoramica generale delle risorse di Azure che sono interessati e le linee guida per crearle.

![Diagramma di una visualizzazione di alto livello delle risorse di Azure in un account Lab.](./media/administrator-guide/high-level-view.png)

- I Lab sono ospitati all'interno di una sottoscrizione di Azure di proprietà di Azure Lab Services.
- Gli account Lab, una raccolta di immagini condivise e le versioni di immagine sono ospitati all'interno della sottoscrizione.
- L'account lab e la raccolta di immagini condivise possono essere nello stesso gruppo di risorse. In questo diagramma sono in gruppi di risorse diversi.

Per altre informazioni sull'architettura, vedere [nozioni fondamentali sull'architettura dei Lab](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Subscription
L'Università può avere una o più sottoscrizioni di Azure. Le sottoscrizioni vengono usate per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure usati al suo interno, inclusi gli account Lab.

La relazione tra un account lab e la relativa sottoscrizione è importante perché:

- La fatturazione viene comunicata tramite la sottoscrizione che contiene l'account lab.
- È possibile concedere agli utenti l'accesso al tenant Azure Active Directory (Azure AD) della sottoscrizione per Azure Lab Services. È possibile aggiungere un utente come proprietario o collaboratore di un account Lab oppure come autore del Lab o proprietario del Lab.

I Lab e le relative macchine virtuali (VM) vengono gestiti e ospitati all'interno di una sottoscrizione di proprietà di Azure Lab Services.

## <a name="resource-group"></a>Resource group
Una sottoscrizione contiene uno o più gruppi di risorse. I gruppi di risorse vengono usati per creare raggruppamenti logici di risorse di Azure che vengono usate insieme all'interno della stessa soluzione.  

Quando si crea un account lab, è necessario configurare il gruppo di risorse che lo contiene. 

Quando si crea una [raccolta di immagini condivise](#shared-image-gallery), è necessario anche un gruppo di risorse. È possibile inserire l'account Lab e la raccolta immagini condivise nello stesso gruppo di risorse o in due gruppi di risorse distinti. È possibile adottare questo secondo approccio se si prevede di condividere la raccolta immagini tra varie soluzioni.

Quando si crea un account lab, è possibile allo stesso tempo creare e collegare automaticamente una raccolta di immagini condivise.  Con questa opzione l'account lab e la raccolta di immagini condivise vengono creati in gruppi di risorse separati. Questo comportamento verrà visualizzato quando si seguono i passaggi descritti nella [raccolta di immagini condivise al momento dell'esercitazione per la creazione dell'account Lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) . L'immagine all'inizio di questo articolo usa questa configurazione. 

Si consiglia di investire tempo prima di pianificare la struttura dei gruppi di risorse, perché *non* è possibile modificare un account Lab o un gruppo di risorse della raccolta immagini condivise dopo che è stato creato. Se è necessario modificare il gruppo di risorse per queste risorse, è necessario eliminare e ricreare l'account Lab o la raccolta di immagini condivise.

## <a name="lab-account"></a>Account lab

Un account Lab funge da contenitore per uno o più Lab. Quando si inizia a usare Azure Lab Services, è più comune avere un unico account Lab. Quando si aumenta l'utilizzo del Lab, è possibile scegliere di creare più account Lab in un secondo momento.

Nell'elenco seguente sono illustrati gli scenari in cui è possibile che più di un account Lab risulti vantaggioso:

- **Gestire i requisiti dei criteri diversi nei Lab**

    Quando si configura un account Lab, si impostano i criteri che si applicano a *tutti* i Lab nell'account Lab, ad esempio:
    - Rete virtuale di Azure con risorse condivise a cui il Lab può accedere. È ad esempio possibile disporre di un set di laboratori che necessitano dell'accesso a un set di dati condiviso all'interno di una rete virtuale.
    - Immagini di macchine virtuali che i Lab possono usare per creare macchine virtuali. Ad esempio, si potrebbe avere un set di laboratori che necessitano dell'accesso alla Data Science VM per l'immagine di Azure Marketplace [per Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) .

    Se ogni Lab presenta requisiti di criteri univoci, potrebbe essere utile creare account Lab distinti per la gestione di ogni Lab separatamente.

- **Assegnare un budget separato a ogni account Lab**
  
    Anziché segnalare tutti i costi del Lab tramite un singolo account Lab, potrebbe essere necessario un budget più chiaramente ripartito. Ad esempio, è possibile creare account Lab distinti per il reparto Math dell'Università, il reparto informatica e così via per distribuire il budget tra i reparti.  È quindi possibile visualizzare il costo per ogni singolo account Lab usando [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md).

- **Isolare i laboratori pilota da laboratori attivi o di produzione**
  
    Si potrebbero avere casi in cui si vuole eseguire il pilota delle modifiche dei criteri per un account Lab senza influire potenzialmente sui laboratori attivi o di produzione. In questo tipo di scenario la creazione di un account lab separato a scopo di test consente di isolare le modifiche. 

## <a name="lab"></a>Lab

Un Lab contiene macchine virtuali assegnate a un singolo studente.  In generale si può:

- Avere un Lab per ogni classe.
- Creare un nuovo set di Lab per ogni semestre, trimestre o altro sistema accademico utilizzato. Per le classi che devono usare la stessa immagine, è necessario usare una [raccolta di immagini condivise](#shared-image-gallery). In questo modo, è possibile riutilizzare le immagini tra laboratori e periodi accademici.

Quando si sta determinando come strutturare i Lab, tenere presente quanto segue:

- **Tutte le macchine virtuali all'interno di un Lab vengono distribuite con la stessa immagine pubblicata**

    Di conseguenza, se si dispone di una classe che richiede la pubblicazione contemporaneamente di immagini Lab diverse, è necessario creare un Lab separato per ogni immagine.
  
- **La quota di utilizzo viene impostata a livello di Lab e si applica a tutti gli utenti all'interno del Lab**

    Per impostare quote diverse per gli utenti, è necessario creare laboratori distinti. Tuttavia, dopo aver impostato la quota, è possibile aggiungere altre ore a utenti specifici.
  
- **La pianificazione di avvio o arresto viene impostata a livello di lab e si applica a tutte le VM all'interno del lab**

    Analogamente all'impostazione della quota, se è necessario impostare pianificazioni diverse per gli utenti, è necessario creare un Lab separato per ogni pianificazione.

Per impostazione predefinita, ogni Lab dispone di una propria rete virtuale.  Se è abilitato il peering di rete virtuale, ogni Lab avrà una propria subnet con peering con la rete virtuale specificata.

## <a name="shared-image-gallery"></a>Raccolta di immagini condivise

Una raccolta di immagini condivise è associata a un account lab e funge da repository centrale in cui archiviare le immagini. Un'immagine viene salvata nella raccolta quando un insegnante sceglie di esportarla dalla macchina virtuale modello di un Lab. Ogni volta che un insegnante apporta modifiche alla macchina virtuale del modello e le esporta, vengono salvate le nuove versioni dell'immagine e vengono mantenute le versioni precedenti.

Gli insegnanti possono pubblicare una versione dell'immagine dalla raccolta di immagini condivise quando creano un nuovo Lab. Sebbene nella raccolta vengano archiviate più versioni di un'immagine, i docenti possono selezionare solo la versione più recente durante la creazione del Lab.

Il servizio raccolta immagini condivise è una risorsa facoltativa che potrebbe non essere necessaria immediatamente se si inizia con pochi laboratori. Tuttavia, la raccolta di immagini condivise offre molti vantaggi che risultano utili quando si esegue la scalabilità fino a Lab aggiuntivi:

- **È possibile salvare e gestire le versioni di un'immagine di macchina virtuale modello**

    È utile creare un'immagine personalizzata o apportare modifiche (software, configurazione e così via) a un'immagine dalla raccolta pubblica di Azure Marketplace.  Ad esempio, è comune per gli educatori richiedere l'installazione di software o strumenti diversi. Anziché richiedere agli studenti di installare manualmente questi prerequisiti, è possibile esportare in una raccolta di immagini condivise diverse versioni dell'immagine di macchina virtuale del modello. È quindi possibile usare queste versioni di immagine quando si creano nuovi Lab.

- **È possibile condividere e riutilizzare le immagini di VM del modello nei Lab**

    È possibile salvare e riutilizzare un'immagine in modo che non sia necessario configurarla da zero ogni volta che si crea un nuovo Lab. Se, ad esempio, più classi devono usare la stessa immagine, è possibile crearla una sola volta e esportarla nella raccolta di immagini condivise in modo da poterla condividere tra i Lab.

- **Verifica della disponibilità delle immagini tramite la replica automatica**

    Quando si salva un'immagine da un Lab alla raccolta di immagini condivise, questa viene replicata automaticamente in altre [aree all'interno della stessa area geografica](https://azure.microsoft.com/global-infrastructure/regions/). Se si verifica un'interruzione per un'area, la pubblicazione dell'immagine nel Lab non è interessata, perché è possibile usare una replica di immagini di un'altra area.  La pubblicazione di VM da più repliche contribuisce anche a migliorare le prestazioni.

Per raggruppare logicamente le immagini condivise, è possibile eseguire una delle operazioni seguenti:

- Creare più raccolte di immagini condivise. Ogni account Lab può connettersi a una sola raccolta di immagini condivise, quindi questa opzione richiede anche la creazione di più account Lab.
- Usare una singola raccolta di immagini condivise condivisa da più account Lab. In questo caso, ogni account Lab può abilitare solo le immagini applicabili ai Lab in tale account.

## <a name="naming"></a>Denominazione

Quando si inizia a usare Azure Lab Services, è consigliabile definire convenzioni di denominazione per gruppi di risorse, account Lab, Lab e la raccolta di immagini condivise. Sebbene le convenzioni di denominazione stabilite siano univoche per le esigenze dell'organizzazione, nella tabella seguente vengono fornite le linee guida generali:

| Tipo di risorsa | Ruolo | Schema consigliato | Esempi |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contiene uno o più account lab e una o più raccolte di immagini condivise | \<organization short name\>-\<environment\>-RG<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse.</li><li>**Environment** identifica l'ambiente per la risorsa, ad esempio *Pilot* o *Production*.</li><li>**RG** sta per il gruppo di *risorse* del tipo di risorsa.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Account lab | Contiene uno o più lab. | \<organization short name\>-\<environment\>-la<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse.</li><li>**Environment** identifica l'ambiente per la risorsa, ad esempio *Pilot* o *Production*.</li><li>**La** sta per il tipo di risorsa *account Lab*.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Lab | Contiene una o più VM. |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nome classe** identifica il nome della classe supportata dal Lab.</li><li>**Intervallo di tempo** identifica l'intervallo di tempo in cui il corso in classe viene offerto.</li>L' **identificatore dell'insegnante** identifica l'insegnante che possiede il Lab.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Raccolta di immagini condivise | Contiene una o più versioni di immagini di VM | \<organization short name\>gallery | contosouniversitylabsgallery |

Per altre informazioni sulla denominazione di altre risorse di Azure, vedere [convenzioni di denominazione per le risorse di Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Aree e località

Quando si configurano le risorse di Azure Lab Services, è necessario fornire un'area o una località del Data Center in cui verranno ospitate le risorse. Le sezioni successive descrivono in che modo un'area o una posizione potrebbe influire su ogni risorsa che si occupa della configurazione di un Lab.

### <a name="resource-group"></a>Resource group

L'area specifica il Data Center in cui vengono archiviate le informazioni su un gruppo di risorse. Le risorse di Azure contenute nel gruppo di risorse possono trovarsi in un'area diversa da quella del relativo elemento padre.

### <a name="lab-account"></a>Account lab

Il percorso di un account Lab indica l'area in cui è presente una risorsa.  

### <a name="lab"></a>Lab

La posizione in cui è presente un Lab varia a seconda dei fattori seguenti:

  - **L'account Lab è con peering con una rete virtuale**
  
    È possibile [peering di un account Lab con una rete virtuale](./how-to-connect-peer-virtual-network.md) quando si trovano nella stessa area.  Quando si esegue il peering di un account Lab con una rete virtuale, i Lab vengono creati automaticamente nella stessa area dell'account Lab e della rete virtuale.

    > [!NOTE]
    > Quando si esegue il peering di un account Lab con una rete virtuale, l'impostazione **Consenti all'autore del Lab di selezionare il percorso Lab** è disabilitata. Per altre informazioni, vedere [Consentire all'autore del lab di selezionare la posizione per il lab](./allow-lab-creator-pick-lab-location.md).
    
  - **Non è stato possibile eseguire il peering di una rete virtuale *e* gli autori del Lab non possono scegliere la posizione del Lab**
  
    Quando *Nessuna* rete virtuale viene sottoposta a peering con l'account Lab e gli [autori Lab *non sono autorizzati* a scegliere la posizione del Lab, i](./allow-lab-creator-pick-lab-location.md)Lab vengono creati automaticamente in un'area con capacità VM disponibile.  In particolare, Azure Lab Services cerca disponibilità in [aree nella stessa area geografica dell'account lab](https://azure.microsoft.com/global-infrastructure/regions).

  - **Non viene effettuata la peering di una rete virtuale *e* gli autori del Lab possono scegliere la posizione del Lab**
       
    Quando *Nessuna* rete virtuale viene sottoposta a peering e i [creatori di Lab *sono autorizzati* a scegliere la posizione del Lab](./allow-lab-creator-pick-lab-location.md), le località che possono essere selezionate dall'autore del Lab dipendono dalla capacità disponibile.

> [!NOTE]
> Per garantire che un'area disponga di una capacità sufficiente per le macchine virtuali, è importante prima di tutto richiedere la capacità tramite l'account Lab quando si crea il Lab.

Una regola generale consiste nell'impostare l'area di una risorsa su uno più vicino ai propri utenti. Per i laboratori, questo significa creare il Lab più vicino agli studenti. Per i corsi online i cui studenti si trovano in tutto il mondo, usare il giudizio migliore per creare un Lab situato centralmente. In alternativa, è possibile suddividere una classe in più Lab in base alle aree degli studenti.

## <a name="vm-sizing"></a>Dimensioni di VM

Quando gli amministratori o i creatori di Lab creano un Lab, possono scegliere tra diverse dimensioni di VM, a seconda delle esigenze della propria classe. Tenere presente che la disponibilità delle dimensioni di calcolo dipende dall'area in cui si trova l'account Lab.

| Dimensione | Specifiche | Serie | Uso suggerito |
| ---- | ----- | ------ | ------------- |
| Piccolo| <ul><li>2 &nbsp; Core</li><li>3,5 gigabyte (GB) di RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Ideale per la riga di comando, apertura del Web browser, server Web con traffico ridotto, database di piccole e medie dimensioni. |
| Media | <ul><li>4 &nbsp; Core</li><li>7 &nbsp; GB di &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Ideale per database relazionali, Caching in memoria e analisi. |
| Media (virtualizzazione annidata) | <ul><li>4 &nbsp; Core</li><li>16 &nbsp; GB di &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Ideale per database relazionali, Caching in memoria e analisi.
| Grande | <ul><li>8 &nbsp; Core</li><li>16 &nbsp; GB di &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Ideale per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale, database di grandi dimensioni, cache di memoria di grandi dimensioni.  Queste dimensioni supportano anche la virtualizzazione annidata. |
| Grande (virtualizzazione annidata) | <ul><li>8 &nbsp; Core</li><li>32 &nbsp; GB di &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Ideale per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale, database di grandi dimensioni, cache di memoria di grandi dimensioni. |
| GPU piccola (visualizzazione) | <ul><li>6 &nbsp; Core</li><li>56 &nbsp; GB di &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Ideale per visualizzazione remota, streaming, giochi e codifica usando Framework come OpenGL e DirectX. |
| GPU small (calcolo) | <ul><li>6 &nbsp; Core</li><li>56 &nbsp; GB di &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Ideale per applicazioni a elevato utilizzo di computer, ad esempio intelligenza artificiale e apprendimento avanzato. |
| GPU media (visualizzazione) | <ul><li>12 &nbsp; Core</li><li>112 &nbsp; GB di &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Ideale per visualizzazione remota, streaming, giochi e codifica usando Framework come OpenGL e DirectX. |

## <a name="manage-identity"></a>Gestire le identità

Usando il [controllo degli accessi in base al ruolo di Azure (RBAC)](../role-based-access-control/overview.md) per l'accesso agli account Lab e ai laboratori, è possibile assegnare i ruoli seguenti:

- **Proprietario** dell'account Lab

    Un amministratore che crea un account Lab viene assegnato automaticamente il ruolo di proprietario dell'account Lab. Il ruolo proprietario può:
     - Modificare le impostazioni dell'account Lab.
     - Concedere ad altri amministratori l'accesso all'account Lab come proprietario o collaboratore.
     - Concedi ai docenti l'accesso ai Lab come autore, proprietario o collaboratore.
     - Creare e gestire tutti i Lab nell'account Lab.

- **Collaboratore** account Lab

    Un amministratore a cui è assegnato il ruolo Collaboratore può:
    - Modificare le impostazioni dell'account Lab.
    - Creare e gestire tutti i Lab nell'account Lab.

    Tuttavia, il collaboratore *non può* concedere ad altri utenti l'accesso agli account Lab o ai laboratori.

- **Lab Creator** (Creatore di lab)

    Per creare Lab in un account Lab, un educatore deve essere un membro del ruolo Lab Creator.  Un insegnante che crea un Lab viene aggiunto automaticamente come proprietario del Lab. Per ulteriori informazioni, vedere [aggiungere un utente al ruolo di autore del Lab](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Proprietario** del Lab o **collaboratore**
  
    Un docente in un ruolo di proprietario del Lab o collaboratore può visualizzare e modificare le impostazioni di un Lab. La persona deve anche essere un membro del ruolo lettore account Lab.

    Una differenza fondamentale tra il proprietario del Lab e i ruoli di collaboratore è che solo un proprietario può concedere ad altri utenti l'accesso per gestire un Lab. Un collaboratore *non può* concedere ad altri utenti l'accesso per gestire un Lab.

- **Raccolta di immagini condivise**

    Quando si connette una raccolta di immagini condivise a un account Lab, i proprietari e i collaboratori dell'account Lab, i proprietari e i creatori di Lab, i proprietari del Lab e i collaboratori Lab ricevono automaticamente l'accesso per visualizzare e salvare le immagini nella raccolta.

Quando si assegnano i ruoli, è utile seguire questi suggerimenti:

   - In genere, solo gli amministratori devono essere membri di un ruolo di proprietario o collaboratore dell'account Lab. L'account Lab potrebbe avere più di un proprietario o un collaboratore.
   - Per offrire ai docenti la possibilità di creare nuovi Lab e gestire i Lab creati, è necessario assegnare loro solo il ruolo di autore del Lab.
   - Per consentire ai docenti di gestire laboratori specifici, ma *non* la possibilità di creare nuovi laboratori, assegnare loro il ruolo di proprietario o collaboratore per ogni Lab che gestirà. Ad esempio, è possibile consentire a un professore e a un assistente didattico di creare un co-proprietario di un Lab. Per altre informazioni, vedere [aggiungere proprietari a un Lab](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Prezzi

### <a name="azure-lab-services"></a>Azure Lab Services

Per informazioni sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Raccolta di immagini condivise

È anche necessario prendere in considerazione i prezzi per il servizio raccolta immagini condivise se si prevede di usare le raccolte immagini condivise per l'archiviazione e la gestione delle versioni delle immagini. 

La creazione di una raccolta di immagini condivise e la relativa associazione all'account lab sono gratuite. Non sono previsti costi fino a quando non si salva una versione dell'immagine nella raccolta. I prezzi per l'uso di una raccolta di immagini condivise sono in genere piuttosto trascurabili, ma è importante comprendere il modo in cui vengono calcolati, perché non sono inclusi nei prezzi per Azure Lab Services.  

#### <a name="storage-charges"></a>Addebiti per l'archiviazione

Per archiviare le versioni delle immagini, una raccolta di immagini condivise usa dischi gestiti da unità disco rigido standard (HDD). Le dimensioni del disco gestito da HDD usato dipendono dalle dimensioni della versione dell'immagine da archiviare. Per informazioni sui prezzi, vedere [prezzi di Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Addebiti per la replica e il traffico di rete in uscita

Quando si salva una versione dell'immagine usando una macchina virtuale del modello Lab, Azure Lab Services prima la archivia in un'area di origine e quindi replica automaticamente la versione dell'immagine di origine in una o più aree di destinazione. 

È importante notare che Azure Lab Services replica automaticamente la versione dell'immagine di origine in tutte le [aree di destinazione all'interno dell'area geografica in](https://azure.microsoft.com/global-infrastructure/regions/) cui si trova il Lab. Se, ad esempio, il Lab si trova nella geografia degli Stati Uniti, viene replicata una versione dell'immagine in ognuna delle otto aree presenti negli Stati Uniti.

Un addebito per traffico di rete in uscita viene applicato quando una versione dell'immagine viene replicata dall'area di origine in aree di destinazione aggiuntive. L'importo addebitato è basato sulle dimensioni della versione dell'immagine quando i dati dell'immagine vengono inizialmente trasferiti in uscita dall'area di origine.  Per informazioni dettagliate sui prezzi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/bandwidth/)per la larghezza di banda.

Gli addebiti in uscita potrebbero essere revocati per i clienti di [soluzioni Education](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) . Per ulteriori informazioni, contattare l'account Manager. 

Per ulteriori informazioni, vedere "quali sono i programmi per il trasferimento dei dati per i clienti accademici e come si qualificano?" nella sezione Domande frequenti della pagina [programmi per istituti di istruzione](https://azure.microsoft.com/pricing/details/bandwidth/) .

#### <a name="pricing-example"></a>Esempio di prezzi

Viene ora esaminato un esempio del costo del salvataggio di un'immagine di macchina virtuale modello in una raccolta di immagini condivise. Si suppongano gli scenari seguenti:

- Si dispone di un'immagine di VM personalizzata.
- Si vogliono salvare due versioni dell'immagine.
- Il Lab si trova negli Stati Uniti, che è costituito da un totale di otto aree.
- Le dimensioni di ogni versione dell'immagine sono di 32 GB, quindi il prezzo del disco gestito HDD è di $1,54 al mese.

Il costo totale mensile è stimato come:

* *Numero di immagini numero di &times; versioni &times; numero di repliche &times; disco gestito prezzo = costo totale al mese*

In questo esempio il costo è:

* 1 immagine personalizzata (32 GB) &times; 2 versioni &times; 8 aree degli stati Uniti &times; $1,54 = $24,64 al mese

> [!NOTE]
> Il calcolo precedente è solo a scopo esemplificativo. Vengono illustrati i costi di archiviazione associati all'utilizzo della raccolta di immagini condivise e *non* sono inclusi i costi in uscita. Per i prezzi effettivi per l'archiviazione, vedere [Managed disks prezzi](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Gestione dei costi

È importante per gli amministratori dell'account Lab gestire i costi eliminando periodicamente le versioni di immagini non necessarie dalla raccolta. 

Non eliminare la replica in aree specifiche come modo per ridurre i costi, anche se questa opzione è presente nella raccolta immagini condivise. Le modifiche alla replica possono avere effetti negativi sulla capacità di Azure Lab Services di pubblicare VM da immagini salvate in una raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione e la gestione dei Lab, vedere:

- [Guida all'installazione dell'account Lab](account-setup-guide.md)  
- [Guida all'installazione di Lab](setup-guide.md)  
- [Gestione costi per i lab](cost-management-guide.md)  
- [Usare Azure Lab Services nei team](lab-services-within-teams-overview.md)
