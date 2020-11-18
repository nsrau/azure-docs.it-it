---
title: Azure Lab Services - Guida dell'amministratore | Microsoft Docs
description: Questa guida è rivolta agli amministratori che creano e gestiscono account lab con Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: b1fadc58926b00c75ab888dad86e45b181059a38
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659846"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Guida dell'amministratore
Gli amministratori IT che gestiscono le risorse cloud di un'università sono in genere responsabili della configurazione dell'account lab dell'istituto. Una volta configurato un account Lab, gli amministratori o gli educatori creano i Lab contenuti nell'account Lab. Questo articolo fornisce una panoramica generale delle risorse di Azure interessate e le istruzioni per crearle.

![Quadro generale delle risorse di Azure in un account lab](./media/administrator-guide/high-level-view.png)

- I Lab sono ospitati all'interno di una sottoscrizione di Azure di proprietà di Azure Lab Services.
- Gli account lab, la raccolta di immagini condivise e le versioni delle immagini sono ospitati all'interno della sottoscrizione.
- L'account lab e la raccolta di immagini condivise possono essere nello stesso gruppo di risorse. In questo diagramma sono in gruppi di risorse diversi.

Per altri dettagli sull'architettura, vedere l'articolo relativo alle [nozioni di base sull'architettura Labs](./classroom-labs-fundamentals.md) .

## <a name="subscription"></a>Subscription
L'Università ha una o più sottoscrizioni di Azure. Una sottoscrizione viene usata per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure usati al suo interno, inclusi gli account lab.

La relazione tra un account lab e la relativa sottoscrizione è importante perché:

- La fatturazione viene comunicata tramite la sottoscrizione che contiene l'account lab.
- È possibile concedere agli utenti del tenant Azure Active Directory della sottoscrizione l'accesso ad Azure Lab Services. È possibile aggiungere un utente come account Lab owner\contributor, Lab Creator o Owner Lab.

I Lab e le relative macchine virtuali (VM) vengono gestiti e ospitati all'interno di una sottoscrizione di proprietà di Azure Lab Services.

## <a name="resource-group"></a>Resource group
Una sottoscrizione contiene uno o più gruppi di risorse. I gruppi di risorse vengono usati per creare raggruppamenti logici di risorse di Azure che vengono usate insieme all'interno della stessa soluzione.  

Quando si crea un account lab, è necessario configurare il gruppo di risorse che lo contiene. 

Un gruppo di risorse è necessario anche quando si crea una [raccolta di immagini condivise](#shared-image-gallery). È possibile scegliere di inserire l'account lab e la raccolta di immagini condivise in due gruppi di risorse distinti, che è la scelta tipica se si prevede di condividere la raccolta immagini tra diverse soluzioni. Oppure si può scegliere di inserirli nello stesso gruppo di risorse.

Quando si crea un account lab, è possibile allo stesso tempo creare e collegare automaticamente una raccolta di immagini condivise.  Con questa opzione l'account lab e la raccolta di immagini condivise vengono creati in gruppi di risorse separati. Una dimostrazione di questo comportamento è illustrata nei passaggi descritti in questa esercitazione: [Configurare la raccolta di immagini condivise al momento della creazione dell'account lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Anche l'immagine all'inizio di questo articolo usa questa configurazione. 

È consigliabile investire inizialmente del tempo per pianificare la struttura dei gruppi di risorse, dato che *non* è possibile modificare il gruppo di risorse o la raccolta di immagini condivise di un account lab dopo la creazione. Se occorre cambiare il gruppo di risorse per queste risorse, sarà necessario eliminare e ricreare l'account lab e/o la raccolta di immagini condivise.

## <a name="lab-account"></a>Account lab

Un account Lab funge da contenitore per uno o più Lab. Quando si inizia a usare Azure Lab Services, normalmente si ha un solo account lab. Man mano che l'utilizzo dei lab aumenta, è possibile scegliere di creare altri account.

Di seguito sono illustrate alcune situazioni in cui avere più account lab può essere vantaggioso:

- **Gestire i requisiti dei criteri diversi nei Lab**

    Quando si configura un account Lab, si impostano i criteri che si applicano a *tutti* i Lab nell'account Lab, ad esempio:
    - Rete virtuale di Azure con risorse condivise a cui il Lab può accedere. Ad esempio, è possibile disporre di un set di Lab che necessitano dell'accesso a un set di dati condiviso all'interno di una rete virtuale.
    - Immagini della macchina virtuale (VM) che possono essere usate dai Lab per creare macchine virtuali. Ad esempio, è possibile disporre di un set di laboratori che necessitano dell'accesso al [Data Science VM per](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) l'immagine del Marketplace Linux.

    Se si dispone di Lab con requisiti di criteri univoci, potrebbe essere utile creare account Lab distinti per gestire separatamente questi Lab.

- **Separare il budget per account lab**
  
    Anziché segnalare tutti i costi del Lab tramite un singolo account Lab, potrebbe essere necessario un budget separato più chiaramente. Ad esempio, è possibile creare account lab per il dipartimento di Matematica dell'Università, per il dipartimento di Informatica e così via, in modo da separare il budget tra i dipartimenti.  È quindi possibile visualizzare il costo per ogni singolo account lab usando [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md).

- **Isolare i lab pilota dai lab attivi/di produzione**
  
    In alcuni casi può essere opportuno testare le modifiche dei criteri per un account lab senza influire potenzialmente sui lab attivi/di produzione. In questo tipo di scenario la creazione di un account lab separato a scopo di test consente di isolare le modifiche. 

## <a name="lab"></a>Lab

Un Lab contiene macchine virtuali (VM) assegnate a un singolo studente.  In generale si può:

- Avere un Lab per ogni classe.
- Creare un nuovo set di Lab ogni semestre (o per ogni intervallo di tempo offerto dalla classe). In genere, per le classi con le stesse esigenze a livello di immagini, è consigliabile usare una [raccolta di immagini condivise](#shared-image-gallery) per riutilizzare le immagini nei vari lab e nel corso dei semestri.

Quando si determina la struttura dei Lab, tenere presenti i punti seguenti:

- **Tutte le macchine virtuali all'interno di un Lab vengono distribuite con la stessa immagine pubblicata**

    Di conseguenza, se si dispone di una classe che richiede la pubblicazione contemporaneamente di immagini Lab diverse, è necessario creare laboratori distinti per ognuno di essi.
  
- **La quota di utilizzo viene impostata a livello di lab e si applica a tutti gli utenti del lab**

    Per impostare quote diverse per gli utenti, è necessario creare laboratori distinti. È comunque possibile aggiungere altre ore a un utente specifico dopo aver impostato la quota.
  
- **La pianificazione di avvio o arresto viene impostata a livello di lab e si applica a tutte le VM all'interno del lab**

    Analogamente al punto precedente, se è necessario impostare pianificazioni diverse per gli utenti, è necessario creare laboratori distinti.

Per impostazione predefinita, ogni Lab avrà una propria rete virtuale.  Se è abilitato il peering di VNET, ogni Lab avrà una propria subnet con peering alla rete virtuale specificata.

## <a name="shared-image-gallery"></a>Raccolta di immagini condivise

Una raccolta di immagini condivise è associata a un account lab e funge da repository centrale in cui archiviare le immagini. Un'immagine viene salvata nella raccolta quando un insegnante sceglie di eseguire l'esportazione dalla macchina virtuale (VM) modello di un Lab. Ogni volta che un docente apporta modifiche alla VM modello ed esegue l'esportazione, le nuove versioni dell'immagine vengono salvate mantenendo le versioni precedenti.

Gli insegnanti possono pubblicare una versione dell'immagine dalla raccolta di immagini condivise quando creano un nuovo Lab. Sebbene nella raccolta siano archiviate più versioni di un'immagine, i docenti possono selezionare solo la versione più recente durante la creazione del lab.

Raccolta immagini condivise è una risorsa facoltativa che potrebbe non essere necessaria immediatamente quando si inizia con pochi laboratori. Tuttavia, l'uso della raccolta di immagini condivise presenta molti vantaggi che risultano utili per la scalabilità in modo da avere più Lab:

- **Consente di salvare e gestire le versioni di un'immagine di VM modello**

    È utile creare un'immagine personalizzata o apportare modifiche (software, configurazione e così via) a un'immagine della raccolta pubblica del Marketplace.  Ad esempio, accade di frequente che i docenti richiedano l'installazione di software e strumenti diversi. Invece di affidare agli studenti il compito di installare manualmente questi prerequisiti, è possibile esportare versioni diverse dell'immagine della VM modello in una raccolta di immagini condivise. Queste versioni delle immagini possono quindi essere usate durante la creazione di nuovi Lab.
- **Abilita sharing\reuse di immagini di VM modello nei Lab**

    È possibile salvare e riutilizzare un'immagine in modo da non dover configurare l'immagine da zero ogni volta che si crea un nuovo Lab. Se, ad esempio, vengono offerte più classi che necessitano della stessa immagine, questa immagine deve essere creata una sola volta ed esportata nella raccolta di immagini condivise in modo da poterla condividere tra i Lab.
- **Garantisce la disponibilità dell'immagine tramite la replica**

    Quando si salva nella raccolta di immagini condivise da un Lab, l'immagine viene replicata automaticamente in altre [aree all'interno della stessa area geografica](https://azure.microsoft.com/global-infrastructure/regions/). Nel caso in cui si verifichi un'interruzione per un'area, la pubblicazione dell'immagine nel Lab non ha effetto perché è possibile usare una replica di immagini di un'altra area.  La pubblicazione di VM da più repliche contribuisce anche a migliorare le prestazioni.

Per raggruppare logicamente le immagini condivise sono disponibili due opzioni:

- Creare più raccolte di immagini condivise. Ogni account lab può connettersi solo a una raccolta di immagini condivise, quindi per usare questa opzione occorre anche creare più account lab.
- Usare una sola raccolta di immagini condivise condivisa tra più account lab. In questo caso, ogni account Lab può abilitare solo le immagini applicabili ai Lab che contiene.

## <a name="naming"></a>Denominazione

Quando si inizia a usare Azure Lab Services, è consigliabile definire convenzioni di denominazione per gruppi di risorse, account Lab, Lab e la raccolta di immagini condivise. Fermo restando che le convenzioni di denominazione stabilite sono specifiche per le esigenze della propria organizzazione, la tabella seguente offre alcune indicazioni generali.

| Tipo di risorsa | Ruolo | Schema consigliato | Esempi |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contiene uno o più account lab e una o più raccolte di immagini condivise | \<organization short name\>-\<environment\>-RG<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**Ambiente** identifica l'ambiente della risorsa, ad esempio Pilot o Production</li><li>**Rg** indica il tipo di risorsa: gruppo di risorse.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Account lab | Contiene uno o più lab. | \<organization short name\>-\<environment\>-la<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**Ambiente** identifica l'ambiente della risorsa, ad esempio Pilot o Production</li><li>**Rg** indica il tipo di risorsa: account lab.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Lab | Contiene una o più VM. |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nome classe** identifica il nome del corso in classe supportato dal lab.</li><li>**Intervallo di tempo** identifica l'intervallo di tempo in cui il corso in classe viene offerto.</li>**Identificatore docente** identifica il docente proprietario del lab.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Raccolta di immagini condivise | Contiene una o più versioni di immagini di VM | \<organization short name\>gallery | contosouniversitylabsgallery |

Per altre informazioni sulla denominazione di altre risorse di Azure, vedere [Convenzioni di denominazione per le risorse di Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Aree e località

Quando si configurano le risorse di Azure Lab Services, è necessario specificare l'area o la località del data center che ospiterà la risorsa. Di seguito viene descritto in dettaglio l'impatto dell'area su ognuna delle risorse coinvolte nella configurazione di un lab.

### <a name="resource-group"></a>Resource group

L'area specifica il data center in cui vengono archiviate le informazioni sul gruppo di risorse. Le risorse di Azure contenute nel gruppo di risorse possono trovarsi in aree diverse rispetto al relativo elemento padre.

### <a name="lab-account"></a>Account lab

La località di un account lab indica l'area in cui si trova la risorsa.  

### <a name="lab"></a>Lab

Il percorso in cui è presente un Lab varia in base ai fattori seguenti:

  - **L'account lab è associato tramite peering a una rete virtuale**
  
    Un account lab può essere [associato tramite peering a una rete virtuale](./how-to-connect-peer-virtual-network.md) quando si trovano nella stessa area.  Quando si esegue il peering di un account Lab con una VNet, i Lab vengono creati automaticamente nella stessa area dell'account Lab e di VNet.

    > [!NOTE]
    > Quando un account lab è associato tramite peering a una rete virtuale, l'impostazione **Consenti all'autore del lab di selezionare la località del lab** è disabilitata. Per altre informazioni su questa impostazione, vedere [Consentire all'autore del lab di selezionare la località del lab in Azure Lab Services](./allow-lab-creator-pick-lab-location.md).
    
  - * * Non è consentito eseguire il peering di VNet **_e_* _ Lab Creators non è autorizzato a selezionare il Lab location_ *
  
    Quando non è presente **alcun** VNet con peering con l'account Lab *e* gli [autori del Lab **non** sono autorizzati a scegliere la posizione del Lab, i](./allow-lab-creator-pick-lab-location.md)Lab vengono creati automaticamente in un'area con capacità VM disponibile.  In particolare, Azure Lab Services cerca disponibilità in [aree nella stessa area geografica dell'account lab](https://azure.microsoft.com/global-infrastructure/regions).

  - * * Nessun VNet è con peering **_e_* _ gli autori Lab possono selezionare il Lab location_ *
       
    Quando **non** esiste alcun peering con una rete virtuale e [agli autori di lab è consentito selezionare la località del lab](./allow-lab-creator-pick-lab-location.md), le località che possono essere selezionate dall'autore del lab dipendono dalla capacità disponibile.

> [!NOTE]
> Per assicurarsi di disporre di capacità di VM sufficiente per un'area, è importante richiedere prima la capacità tramite l'account lab o quando si crea il lab.

Come regola generale, è consigliabile impostare l'area di una risorsa su quella più vicina agli utenti della risorsa. Per i laboratori, questo significa creare il Lab più vicino agli studenti. Per i corsi online in cui gli studenti si trovano in tutto il mondo, è necessario usare il giudizio migliore per creare un Lab situato centralmente. In alternativa, suddividere una classe in più Lab in base all'area degli studenti.

### <a name="shared-image-gallery"></a>Raccolta di immagini condivise

L'area indica l'area di origine in cui viene archiviata la prima versione dell'immagine prima di essere replicata automaticamente nelle aree di destinazione.

## <a name="vm-sizing"></a>Dimensioni di VM

Quando gli amministratori o i creatori di Lab creano un Lab, possono scegliere tra le seguenti dimensioni della macchina virtuale in base alle esigenze della propria classe. Tenere presente che le dimensioni di calcolo disponibili dipendono dall'area in cui si trova l'account lab:

| Dimensione | Specifiche | Serie | Uso suggerito |
| ---- | ----- | ------ | ------------- |
| Piccolo| <ul><li>2 core</li><li>3,5 GB di RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Queste dimensioni sono più indicate per la riga di comando, l'apertura del Web browser, server Web con traffico ridotto, database di piccole e medie dimensioni. |
| Media | <ul><li>4 core</li><li>7 GB di RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi. |
| Medium (virtualizzazione annidata) | <ul><li>4 core</li><li>16 GB di RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi.
| large | <ul><li>8 core</li><li>16 GB di RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate.  Queste dimensioni supportano anche la virtualizzazione annidata. |
| Grande (virtualizzazione annidata) | <ul><li>8 core</li><li>32 GB di RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate. |
| GPU small (visualizzazione) | <ul><li>6 core</li><li>56 GB di RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Queste dimensioni sono più indicate per la visualizzazione remota, lo streaming, i giochi e la codifica con framework quali OpenGL e DirectX. |
| GPU small (calcolo) | <ul><li>6 core</li><li>56 GB di RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Queste dimensioni sono più indicate per applicazioni a elevato utilizzo di calcolo quali intelligenza artificiale e Deep Learning. |
| GPU medium (visualizzazione) | <ul><li>12 core</li><li>112 GB di RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Queste dimensioni sono più indicate per la visualizzazione remota, lo streaming, i giochi e la codifica con framework quali OpenGL e DirectX. |

## <a name="manage-identity"></a>Gestire le identità

Usando il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md), è possibile assegnare i ruoli seguenti per concedere l'accesso agli account Lab e ai Lab:

- **Proprietario dell'account lab**

    L'amministratore che crea l'account lab viene aggiunto automaticamente al ruolo **Proprietario** dell'account lab.  Un amministratore a cui è assegnato il ruolo **Proprietario** può:
     - Modificare le impostazioni dell'account lab.
     - Concedere ad altri amministratori l'accesso all'account lab come proprietari o collaboratori.
     - Concedi ai docenti l'accesso ai Lab come autori, proprietari o collaboratori.
     - Creare e gestire tutti i Lab all'interno dell'account Lab.

- **Collaboratore per l'account lab**

    Un amministratore a cui è assegnato il ruolo **Collaboratore** può:
    - Modificare le impostazioni dell'account lab.
    - Creare e gestire tutti i Lab nell'account Lab.

    Tuttavia, *non possono* concedere ad altri utenti l'accesso agli account Lab o ai Lab.

- **Autore del Lab**

    Per creare Lab in un account Lab, un educatore deve essere un membro del ruolo **Lab Creator** .  Quando un insegnante crea un Lab, viene aggiunto automaticamente come proprietario del Lab.  Per altre informazioni, vedere l'esercitazione che spiega come [aggiungere un utente al ruolo **Autore di lab**](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Owner\contributor Lab**
  
    Un insegnante può visualizzare e modificare le impostazioni di un Lab quando sono membri del ruolo **proprietario** o **collaboratore** di un Lab. devono inoltre essere membri del ruolo **lettore** dell'account Lab.

    Una differenza fondamentale tra il ruolo **Proprietario** e il ruolo **Collaboratore** di un lab è che un collaboratore *non può* concedere ad altri utenti l'accesso per gestire il lab. Solo i proprietari possono concedere questo tipo di accesso.

    Inoltre, un insegnante *non può* creare nuovi Lab a meno che non siano anche membri del ruolo di **autore del Lab** .

- **Raccolta di immagini condivise**

    Quando si associa una raccolta di immagini condivise a un account lab, ai proprietari e collaboratori dell'account lab e agli autori, proprietari e collaboratori del lab viene concesso automaticamente l'accesso per visualizzare e salvare le immagini nella raccolta.

Ecco alcuni suggerimenti utili per l'assegnazione dei ruoli:
   - In genere, solo gli amministratori devono essere membri del ruolo **Proprietario** o **Collaboratore** di un account lab. Possono essere assegnati più proprietari e collaboratori.
   - Per consentire a un insegnante di creare nuovi Lab e gestire i Lab creati; è sufficiente assegnare l'accesso al ruolo **Lab Creator** .
   - Per consentire a un insegnante di gestire laboratori specifici, ma *non* la possibilità di creare nuovi laboratori; è necessario assegnare l'accesso al ruolo **proprietario** o **collaboratore** per ogni Lab che verrà gestito.  Ad esempio, è possibile consentire a un professore e a un assistente didattico di creare un co-proprietario di un Lab.  Vedere la guida su come [aggiungere un utente come proprietario di un Lab](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Prezzi

### <a name="azure-lab-services"></a>Azure Lab Services

I prezzi di Azure Lab Services sono descritti nell'articolo [Prezzi di Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Occorre prendere in considerazione anche i prezzi per la raccolta di immagini condivise se si prevede di usarla per l'archiviazione e la gestione delle versioni delle immagini. 

### <a name="shared-image-gallery"></a>Raccolta di immagini condivise

La creazione di una raccolta di immagini condivise e la relativa associazione all'account lab sono gratuite. Non viene addebitato alcun costo finché non si salva una versione dell'immagine nella raccolta. In genere i prezzi per l'uso di una raccolta di immagini condivise sono piuttosto trascurabili, ma è importante comprendere il modo in cui vengono calcolati in quanto non sono inclusi nel prezzo di Azure Lab Services.  

#### <a name="storage-charges"></a>Addebiti per l'archiviazione

Per archiviare le versioni delle immagini, una raccolta di immagini condivise usa dischi gestiti HDD standard. Le dimensioni del disco gestito HDD usato dipendono dalle dimensioni della versione dell'immagine da archiviare. Per informazioni sui prezzi, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Addebiti per la replica e il traffico di rete in uscita

Quando si salva una versione dell'immagine usando una macchina virtuale (VM) modello di un Lab, Azure Lab Services prima la archivia in un'area di origine e quindi replica automaticamente la versione dell'immagine di origine in una o più aree di destinazione. È importante notare che Azure Lab Services replica automaticamente la versione dell'immagine di origine in tutte le aree di destinazione [all'interno dell'area geografica in](https://azure.microsoft.com/global-infrastructure/regions/) cui si trova il Lab. Se, ad esempio, il Lab si trova nella geografia degli Stati Uniti, viene replicata una versione dell'immagine in ognuna delle otto aree presenti negli Stati Uniti

Un addebito per traffico di rete in uscita viene applicato quando una versione dell'immagine viene replicata dall'area di origine in aree di destinazione aggiuntive. L'importo addebitato è basato sulle dimensioni della versione dell'immagine quando i dati dell'immagine vengono inizialmente trasferiti in uscita dall'area di origine.  Per informazioni dettagliate sui prezzi, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

I clienti di [soluzioni per gli istituti di istruzione](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) possono essere esentati dal pagamento delle tariffe per il traffico in uscita. Per altre informazioni, rivolgersi al proprio account manager.  Per altre informazioni, vedere la sezione sulle **domande frequenti** nel documento collegato, in particolare la domanda "Quali programmi di trasferimento dei dati sono disponibili per i clienti accademici e come si ottiene l'idoneità?".

#### <a name="pricing-example"></a>Esempio di prezzi

Per ricapitolare le informazioni sui prezzi riportate sopra, si userà un esempio in cui l'immagine della macchina virtuale modello viene salvata nella raccolta di immagini condivise. Si suppongano gli scenari seguenti:

- Si dispone di un'immagine di VM personalizzata.
- Si vogliono salvare due versioni dell'immagine.
- Il lab si trova negli Stati Uniti, che comprendono un totale di otto aree.
- Le dimensioni di ogni versione dell'immagine sono di 32 GB, quindi il prezzo del disco gestito HDD è di $1,54 al mese.

Il costo totale è stimato come:

Numero di immagini × numero di versioni × numero di repliche × prezzo del disco gestito

In questo esempio il costo è:

1 immagine personalizzata (32 GB) x 2 versioni x 8 aree degli Stati Uniti x $ 1,54 = $ 24,64 al mese

#### <a name="cost-management"></a>Gestione dei costi

È importante che l'amministratore dell'account lab gestisca i costi eliminando periodicamente le versioni di immagini non necessarie dalla raccolta. 

Non è consigliabile eliminare la replica in aree specifiche come soluzione per ridurre i costi (questa opzione è disponibile nella raccolta di immagini condivise). Le modifiche alla replica possono infatti avere effetti negativi sulla capacità di Azure Lab Services di pubblicare VM da immagini salvate in una raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

Passaggi successivi comuni per la configurazione di un ambiente lab.

- [Guida all'installazione dell'account Lab](account-setup-guide.md)
- [Guida all'installazione di Lab](setup-guide.md)
- [Gestione costi per i lab](cost-management-guide.md)
- [Utilizzo di Azure Lab Services all'interno di Team](lab-services-within-teams-overview.md)