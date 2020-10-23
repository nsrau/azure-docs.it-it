---
title: Azure Lab Services - Guida dell'amministratore | Microsoft Docs
description: Questa guida è rivolta agli amministratori che creano e gestiscono account lab con Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: a39ee2cc57c8fc1497c3798759bd40d1ed2976e3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425310"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Guida dell'amministratore
Gli amministratori IT che gestiscono le risorse cloud di un'università sono in genere responsabili della configurazione dell'account lab dell'istituto. Una volta configurato un account lab, gli amministratori o i docenti creano i lab per la classe contenuti nell'account lab. Questo articolo fornisce una panoramica generale delle risorse di Azure interessate e le istruzioni per crearle.

![Quadro generale delle risorse di Azure in un account lab](./media/administrator-guide/high-level-view.png)

- I lab per la classe sono ospitati all'interno di una sottoscrizione di Azure di proprietà di Azure Lab Services.
- Gli account lab, la raccolta di immagini condivise e le versioni delle immagini sono ospitati all'interno della sottoscrizione.
- L'account lab e la raccolta di immagini condivise possono essere nello stesso gruppo di risorse. In questo diagramma sono in gruppi di risorse diversi. 

## <a name="subscription"></a>Subscription
L'Università ha una o più sottoscrizioni di Azure. Una sottoscrizione viene usata per gestire la fatturazione e la sicurezza per tutte le risorse e i servizi di Azure usati al suo interno, inclusi gli account lab.

La relazione tra un account lab e la relativa sottoscrizione è importante perché:

- La fatturazione viene comunicata tramite la sottoscrizione che contiene l'account lab.
- È possibile concedere agli utenti del tenant Azure Active Directory della sottoscrizione l'accesso ad Azure Lab Services. È possibile aggiungere un utente come proprietario o collaboratore dell'account lab, autore del lab per la classe o proprietario del lab per la classe.

I lab per la classe e le relative macchine virtuali (VM) sono gestiti e ospitati all'interno di una sottoscrizione di proprietà di Azure Lab Services.

## <a name="resource-group"></a>Resource group
Una sottoscrizione contiene uno o più gruppi di risorse. I gruppi di risorse vengono usati per creare raggruppamenti logici di risorse di Azure che vengono usate insieme all'interno della stessa soluzione.  

Quando si crea un account lab, è necessario configurare il gruppo di risorse che lo contiene. 

Un gruppo di risorse è necessario anche quando si crea una [raccolta di immagini condivise](#shared-image-gallery). È possibile scegliere di inserire l'account lab e la raccolta di immagini condivise in due gruppi di risorse distinti, che è la scelta tipica se si prevede di condividere la raccolta immagini tra diverse soluzioni. Oppure si può scegliere di inserirli nello stesso gruppo di risorse.

Quando si crea un account lab, è possibile allo stesso tempo creare e collegare automaticamente una raccolta di immagini condivise.  Con questa opzione l'account lab e la raccolta di immagini condivise vengono creati in gruppi di risorse separati. Una dimostrazione di questo comportamento è illustrata nei passaggi descritti in questa esercitazione: [Configurare la raccolta di immagini condivise al momento della creazione dell'account lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Anche l'immagine all'inizio di questo articolo usa questa configurazione. 

È consigliabile investire inizialmente del tempo per pianificare la struttura dei gruppi di risorse, dato che *non* è possibile modificare il gruppo di risorse o la raccolta di immagini condivise di un account lab dopo la creazione. Se occorre cambiare il gruppo di risorse per queste risorse, sarà necessario eliminare e ricreare l'account lab e/o la raccolta di immagini condivise.

## <a name="lab-account"></a>Account lab

Un account lab funge da contenitore per uno o più lab per la classe. Quando si inizia a usare Azure Lab Services, normalmente si ha un solo account lab. Man mano che l'utilizzo dei lab aumenta, è possibile scegliere di creare altri account.

Di seguito sono illustrate alcune situazioni in cui avere più account lab può essere vantaggioso:

- **Gestire requisiti dei criteri diversi nei vari lab per le classi**

    Quando si configura un account lab, si impostano criteri che si applicano a *tutti* i lab per la classe in tale account, ad esempio:
    - La rete virtuale di Azure con risorse condivise a cui il lab per la classe può accedere. Ad esempio, un set di lab per la classe potrebbe aver bisogno di accedere a un set di dati condiviso all'interno di una rete virtuale.
    - Le immagini di macchina virtuale (VM) che i lab per la classe possono usare per creare VM. Ad esempio, un set di lab per la classe potrebbe aver bisogno di accedere all'immagine [Data Science VM per Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) sul Marketplace.

    Se si dispone di lab per la classe con requisiti di criteri univoci, diversi da un lab all'altro, potrebbe essere utile creare account lab distinti per gestire separatamente questi lab.

- **Separare il budget per account lab**
  
    Invece di segnalare tutti i costi dei lab per la classe tramite un singolo account lab, potrebbe essere necessario un budget che distingua le varie voci in modo più chiaro. Ad esempio, è possibile creare account lab per il dipartimento di Matematica dell'Università, per il dipartimento di Informatica e così via, in modo da separare il budget tra i dipartimenti.  È quindi possibile visualizzare il costo per ogni singolo account lab usando [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isolare i lab pilota dai lab attivi/di produzione**
  
    In alcuni casi può essere opportuno testare le modifiche dei criteri per un account lab senza influire potenzialmente sui lab attivi/di produzione. In questo tipo di scenario la creazione di un account lab separato a scopo di test consente di isolare le modifiche. 

## <a name="classroom-lab"></a>Lab per le classi

Un lab per la classe contiene una serie di macchine virtuali (VM), ognuna assegnata a un singolo studente.  In generale si può:

- Creare un lab per ogni classe.
- Creare un nuovo set di lab per la classe ogni semestre (o per ogni intervallo di tempo offerto alla classe). In genere, per le classi con le stesse esigenze a livello di immagini, è consigliabile usare una [raccolta di immagini condivise](#shared-image-gallery) per riutilizzare le immagini nei vari lab e nel corso dei semestri.

Per determinare il modo in cui strutturare i lab per la classe, tenere presente quanto segue:

- **Tutte le macchine virtuali all'interno di un lab per la classe vengono distribuite con la stessa immagine pubblicata**

    Di conseguenza, se una classe richiede la pubblicazione di immagini di lab diverse contemporaneamente, è necessario creare lab per la classe distinti per ogni immagine.
  
- **La quota di utilizzo viene impostata a livello di lab e si applica a tutti gli utenti del lab**

    Per impostare quote diverse per gli utenti, è necessario creare lab per la classe distinti. È comunque possibile aggiungere altre ore a un utente specifico dopo aver impostato la quota.
  
- **La pianificazione di avvio o arresto viene impostata a livello di lab e si applica a tutte le VM all'interno del lab**

    Analogamente al punto precedente, se occorre impostare pianificazioni diverse per gli utenti, è necessario creare lab per la classe distinti.

Per impostazione predefinita, ogni lab per la classe ha una rete virtuale dedicata.  Se è abilitato il peering reti virtuali, ogni lab per la classe avrà la propria subnet con peering alla rete virtuale specificata.

## <a name="shared-image-gallery"></a>Raccolta di immagini condivise

Una raccolta di immagini condivise è associata a un account lab e funge da repository centrale in cui archiviare le immagini. Un'immagine viene salvata nella raccolta quando un docente decide di eseguire un'esportazione da una macchina virtuale modello del lab per la classe. Ogni volta che un docente apporta modifiche alla VM modello ed esegue l'esportazione, le nuove versioni dell'immagine vengono salvate mantenendo le versioni precedenti.

Gli insegnanti possono pubblicare una versione dell'immagine dalla raccolta di immagini condivise quando creano un nuovo lab per la classe. Sebbene nella raccolta siano archiviate più versioni di un'immagine, i docenti possono selezionare solo la versione più recente durante la creazione del lab.

La raccolta di immagini condivise è una risorsa facoltativa che potrebbe non essere necessaria immediatamente, quando si hanno solo pochi lab per la classe. L'uso di questa raccolta offre però numerosi vantaggi che saranno utili man mano che si creano altri lab:

- **Consente di salvare e gestire le versioni di un'immagine di VM modello**

    È utile creare un'immagine personalizzata o apportare modifiche (software, configurazione e così via) a un'immagine della raccolta pubblica del Marketplace.  Ad esempio, accade di frequente che i docenti richiedano l'installazione di software e strumenti diversi. Invece di affidare agli studenti il compito di installare manualmente questi prerequisiti, è possibile esportare versioni diverse dell'immagine della VM modello in una raccolta di immagini condivise. Queste versioni dell'immagine possono quindi essere usate per creare nuovi lab per la classe.
- **Consente la condivisione e il riutilizzo di immagini di VM modello nei vari lab per la classe**

    È possibile salvare e riutilizzare un'immagine in modo da non doverla configurare da zero ogni volta che si crea un nuovo lab per la classe. Se ad esempio vengono offerte più classi che necessitano della stessa immagine, basterà creare questa immagine una volta ed esportarla nella raccolta di immagini condivise in modo da poterla condividere tra i vari lab per la classe.
- **Garantisce la disponibilità dell'immagine tramite la replica**

    Quando si salva un'immagine nella raccolta di immagini condivise da un lab per la classe, l'immagine viene replicata automaticamente in altre [aree all'interno della stessa area geografica](https://azure.microsoft.com/global-infrastructure/regions/). Se si verifica un'interruzione in un'area, la pubblicazione dell'immagine nel lab per la classe non ne viene interessata perché è possibile usare una replica dell'immagine di un'altra area.  La pubblicazione di VM da più repliche contribuisce anche a migliorare le prestazioni.

Per raggruppare logicamente le immagini condivise sono disponibili due opzioni:

- Creare più raccolte di immagini condivise. Ogni account lab può connettersi solo a una raccolta di immagini condivise, quindi per usare questa opzione occorre anche creare più account lab.
- Usare una sola raccolta di immagini condivise condivisa tra più account lab. In questo caso ogni account lab può abilitare solo le immagini applicabili ai lab per la classe che contiene.

## <a name="naming"></a>Denominazione

Quando si inizia a usare Azure Lab Services, è consigliabile stabilire delle convenzioni di denominazione per i gruppi di risorse, gli account lab, i lab per la classe e la raccolta di immagini condivise. Fermo restando che le convenzioni di denominazione stabilite sono specifiche per le esigenze della propria organizzazione, la tabella seguente offre alcune indicazioni generali.

| Tipo di risorsa | Ruolo | Schema consigliato | Esempi |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contiene uno o più account lab e una o più raccolte di immagini condivise | \<organization short name\>-\<environment\>-RG<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**Ambiente** identifica l'ambiente della risorsa, ad esempio Pilot o Production</li><li>**Rg** indica il tipo di risorsa: gruppo di risorse.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Account lab | Contiene uno o più lab. | \<organization short name\>-\<environment\>-la<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**Ambiente** identifica l'ambiente della risorsa, ad esempio Pilot o Production</li><li>**Rg** indica il tipo di risorsa: account lab.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Lab per le classi | Contiene una o più VM. |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nome classe** identifica il nome del corso in classe supportato dal lab.</li><li>**Intervallo di tempo** identifica l'intervallo di tempo in cui il corso in classe viene offerto.</li>**Identificatore docente** identifica il docente proprietario del lab.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Raccolta di immagini condivise | Contiene una o più versioni di immagini di VM | \<organization short name\>Raccolta | contosouniversitylabsgallery |

Per altre informazioni sulla denominazione di altre risorse di Azure, vedere [Convenzioni di denominazione per le risorse di Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Aree e località

Quando si configurano le risorse di Azure Lab Services, è necessario specificare l'area o la località del data center che ospiterà la risorsa. Di seguito viene descritto in dettaglio l'impatto dell'area su ognuna delle risorse coinvolte nella configurazione di un lab.

### <a name="resource-group"></a>Resource group

L'area specifica il data center in cui vengono archiviate le informazioni sul gruppo di risorse. Le risorse di Azure contenute nel gruppo di risorse possono trovarsi in aree diverse rispetto al relativo elemento padre.

### <a name="lab-account"></a>Account lab

La località di un account lab indica l'area in cui si trova la risorsa.  

### <a name="classroom-lab"></a>Lab per le classi

La località in cui è presente un lab per la classe varia in base ai fattori seguenti:

  - **L'account lab è associato tramite peering a una rete virtuale**
  
    Un account lab può essere [associato tramite peering a una rete virtuale](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando si trovano nella stessa area.  Quando un account lab è associato tramite peering a una rete virtuale, i lab per la classe vengono creati automaticamente nella stessa area dell'account lab e della rete virtuale.

    > [!NOTE]
    > Quando un account lab è associato tramite peering a una rete virtuale, l'impostazione **Consenti all'autore del lab di selezionare la località del lab** è disabilitata. Per altre informazioni su questa impostazione, vedere [Consentire all'autore del lab di selezionare la località del lab in Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - * * Non è consentito eseguire il peering di VNet **_e_*_ Lab Creators non è autorizzato a selezionare il Lab location_ *
  
    Quando all'account lab **non** è associata una rete virtuale tramite peering *e* [agli autori di lab **non** è consentito selezionare la località del lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), i lab per la classe vengono creati automaticamente in un'area con capacità di VM disponibile.  In particolare, Azure Lab Services cerca disponibilità in [aree nella stessa area geografica dell'account lab](https://azure.microsoft.com/global-infrastructure/regions).

  - * * Nessun VNet è con peering **_e_*_ gli autori Lab possono selezionare il Lab location_ *
       
    Quando **non** esiste alcun peering con una rete virtuale e [agli autori di lab è consentito selezionare la località del lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), le località che possono essere selezionate dall'autore del lab dipendono dalla capacità disponibile.

> [!NOTE]
> Per assicurarsi di disporre di capacità di VM sufficiente per un'area, è importante richiedere prima la capacità tramite l'account lab o quando si crea il lab.

Come regola generale, è consigliabile impostare l'area di una risorsa su quella più vicina agli utenti della risorsa. Per i lab per la classe, questo significa creare il lab nell'area più vicina agli studenti. Per i corsi online in cui gli studenti sono sparsi in tutto il mondo, occorre individuare un'area il più possibile centrale a tutti gli studenti in cui creare il lab per la classe. Si può anche suddividere una classe in più lab per la classe in base all'area di residenza degli studenti.

### <a name="shared-image-gallery"></a>Raccolta di immagini condivise

L'area indica l'area di origine in cui viene archiviata la prima versione dell'immagine prima di essere replicata automaticamente nelle aree di destinazione.

## <a name="vm-sizing"></a>Dimensioni di VM

Quando gli amministratori o gli autori di lab creano un lab per la classe, possono scegliere tra le seguenti dimensioni di VM in base alle esigenze della propria classe. Tenere presente che le dimensioni di calcolo disponibili dipendono dall'area in cui si trova l'account lab:

| Dimensione | Specifiche | Serie | Uso suggerito |
| ---- | ----- | ------ | ------------- |
| Piccolo| <ul><li>2 core</li><li>3,5 GB di RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Queste dimensioni sono più indicate per la riga di comando, l'apertura del Web browser, server Web con traffico ridotto, database di piccole e medie dimensioni. |
| Media | <ul><li>4 core</li><li>7 GB di RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi. |
| Medium (virtualizzazione annidata) | <ul><li>4 core</li><li>16 GB di RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi.
| large | <ul><li>8 core</li><li>16 GB di RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate.  Queste dimensioni supportano anche la virtualizzazione annidata. |
| Grande (virtualizzazione annidata) | <ul><li>8 core</li><li>32 GB di RAM</li></ul>  | [Standard_D8s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate. |
| GPU small (visualizzazione) | <ul><li>6 core</li><li>56 GB di RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Queste dimensioni sono più indicate per la visualizzazione remota, lo streaming, i giochi e la codifica con framework quali OpenGL e DirectX. |
| GPU small (calcolo) | <ul><li>6 core</li><li>56 GB di RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Queste dimensioni sono più indicate per applicazioni a elevato utilizzo di calcolo quali intelligenza artificiale e Deep Learning. |
| GPU medium (visualizzazione) | <ul><li>12 core</li><li>112 GB di RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Queste dimensioni sono più indicate per la visualizzazione remota, lo streaming, i giochi e la codifica con framework quali OpenGL e DirectX. |

## <a name="manage-identity"></a>Gestire le identità

Usando il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/overview), è possibile assegnare i ruoli seguenti per concedere l'accesso agli account Lab e ai laboratori in aula:

- **Proprietario dell'account lab**

    L'amministratore che crea l'account lab viene aggiunto automaticamente al ruolo **Proprietario** dell'account lab.  Un amministratore a cui è assegnato il ruolo **Proprietario** può:
     - Modificare le impostazioni dell'account lab.
     - Concedere ad altri amministratori l'accesso all'account lab come proprietari o collaboratori.
     - Concedere ai docenti l'accesso ai lab per la classe come autori, proprietari o collaboratori.
     - Creare e gestire tutti i lab per la classe all'interno dell'account lab.

- **Collaboratore per l'account lab**

    Un amministratore a cui è assegnato il ruolo **Collaboratore** può:
    - Modificare le impostazioni dell'account lab.
    - Creare e gestire tutti i lab per la classe all'interno dell'account lab.

    *Non* può però concedere ad altri utenti l'accesso agli account lab o ai lab per la classe.

- **Autore del lab per la classe**

    Per creare lab per la classe in un account lab, un docente deve essere membro del ruolo **Autore di lab**.  Quando un docente crea un lab per la classe, viene aggiunto automaticamente come proprietario del lab.  Per altre informazioni, vedere l'esercitazione che spiega come [aggiungere un utente al ruolo **Autore di lab**](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Proprietario/collaboratore del lab per la classe**
  
    Un docente può visualizzare e modificare le impostazioni di un lab per la classe quando è membro del ruolo **Proprietario** o **Collaboratore** del lab. Deve anche essere membro del ruolo **Lettore** dell'account lab.

    Una differenza fondamentale tra il ruolo **Proprietario** e il ruolo **Collaboratore** di un lab è che un collaboratore *non può* concedere ad altri utenti l'accesso per gestire il lab. Solo i proprietari possono concedere questo tipo di accesso.

    Inoltre, un docente *non può* creare nuovi lab per la classe se non è anche membro del ruolo **Autore di lab**.

- **Raccolta di immagini condivise**

    Quando si associa una raccolta di immagini condivise a un account lab, ai proprietari e collaboratori dell'account lab e agli autori, proprietari e collaboratori del lab viene concesso automaticamente l'accesso per visualizzare e salvare le immagini nella raccolta.

Ecco alcuni suggerimenti utili per l'assegnazione dei ruoli:
   - In genere, solo gli amministratori devono essere membri del ruolo **Proprietario** o **Collaboratore** di un account lab. Possono essere assegnati più proprietari e collaboratori.
   - Per consentire a un docente di creare nuovi lab per la classe e gestire i lab che crea, è sufficiente assegnargli il ruolo **Autore di lab**.
   - Per consentire a un docente di gestire lab per la classe specifici, ma *non* di creare nuovi lab, è necessario assegnargli il ruolo **Proprietario** o **Collaboratore** per ogni lab per la classe che gestirà.  Ad esempio, si può assegnare sia a un professore che a un assistente docente il ruolo di co-proprietario di un lab per la classe.  Per altre informazioni, vedere [Come aggiungere altri proprietari a un lab esistente in Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Prezzi

### <a name="azure-lab-services"></a>Azure Lab Services

I prezzi di Azure Lab Services sono descritti nell'articolo [Prezzi di Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Occorre prendere in considerazione anche i prezzi per la raccolta di immagini condivise se si prevede di usarla per l'archiviazione e la gestione delle versioni delle immagini. 

### <a name="shared-image-gallery"></a>Raccolta di immagini condivise

La creazione di una raccolta di immagini condivise e la relativa associazione all'account lab sono gratuite. Non viene addebitato alcun costo finché non si salva una versione dell'immagine nella raccolta. In genere i prezzi per l'uso di una raccolta di immagini condivise sono piuttosto trascurabili, ma è importante comprendere il modo in cui vengono calcolati in quanto non sono inclusi nel prezzo di Azure Lab Services.  

#### <a name="storage-charges"></a>Addebiti per l'archiviazione

Per archiviare le versioni delle immagini, una raccolta di immagini condivise usa dischi gestiti HDD standard. Le dimensioni del disco gestito HDD usato dipendono dalle dimensioni della versione dell'immagine da archiviare. Per informazioni sui prezzi, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Addebiti per la replica e il traffico di rete in uscita

Quando si salva una versione dell'immagine usando una macchina virtuale modello di un lab per la classe, Azure Lab Services prima la archivia in un'area di origine e quindi replica automaticamente la versione dell'immagine di origine in una o più aree di destinazione. È importante sottolineare che Azure Lab Services replica automaticamente la versione dell'immagine di origine in tutte le [aree di destinazione all'interno dell'area geografica](https://azure.microsoft.com/global-infrastructure/regions/) in cui si trova il lab per la classe. Ad esempio, se il lab per la classe si trova nell'area geografica Stati Uniti, viene replicata una versione dell'immagine in ognuna delle otto aree presenti negli Stati Uniti.

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

Per istruzioni dettagliate su come creare un account lab e un lab, vedere [Esercitazione: Configurare un account lab con Azure Lab Services](tutorial-setup-lab-account.md).
