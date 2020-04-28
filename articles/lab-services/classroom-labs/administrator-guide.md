---
title: Guida dell'amministratore di Azure Lab Services | Microsoft Docs
description: Questa guida consente agli amministratori che creano e gestiscono account Lab usando Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 307ca08e733417efc9496415a09a0898fe10393e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183467"
---
# <a name="azure-lab-services---administrator-guide"></a>Guida dell'amministratore di Azure Lab Services
Gli amministratori IT che gestiscono le risorse cloud di un'università sono in genere responsabili della configurazione dell'account Lab per la propria scuola. Una volta configurato un account Lab, gli amministratori o gli educatori creano laboratori in aula contenuti nell'account Lab. Questo articolo fornisce una panoramica generale delle risorse di Azure e delle linee guida per la loro creazione.

![Visualizzazione di alto livello delle risorse di Azure in un account Lab](../media/administrator-guide/high-level-view.png)

- I Lab in Aula sono ospitati all'interno di una sottoscrizione di Azure di proprietà di Azure Lab Services.
- Gli account Lab, la raccolta di immagini condivise e le versioni di immagine sono ospitati all'interno della sottoscrizione.
- È possibile avere l'account Lab e la raccolta di immagini condivise nello stesso gruppo di risorse. In questo diagramma si trovano in gruppi di risorse diversi. 

## <a name="subscription"></a>Subscription
L'Università ha una o più sottoscrizioni di Azure. Una sottoscrizione viene usata per gestire la fatturazione e la sicurezza per tutti i resources\services di Azure usati al suo interno, inclusi gli account Lab.

La relazione tra un account Lab e la relativa sottoscrizione è importante perché:

- La fatturazione viene segnalata tramite la sottoscrizione che contiene l'account Lab.
- È possibile concedere agli utenti l'accesso al tenant Azure Active Directory (AD) della sottoscrizione per Azure Lab Services. È possibile aggiungere un utente come account Lab owner\contributor, classe Lab Creator o proprietario del Lab della classe.

I Lab della classe e le macchine virtuali (VM) vengono gestiti e ospitati all'interno di una sottoscrizione di proprietà di Azure Lab Services.

## <a name="resource-group"></a>Resource group
Una sottoscrizione contiene uno o più gruppi di risorse. I gruppi di risorse vengono usati per creare raggruppamenti logici di risorse di Azure che vengono usati insieme all'interno della stessa soluzione.  

Quando si crea un account Lab, è necessario configurare il gruppo di risorse contenente l'account Lab. 

Quando si crea una [raccolta di immagini condivise](#shared-image-gallery), è necessario anche un gruppo di risorse. È possibile scegliere di inserire l'account Lab e la raccolta di immagini condivise in due gruppi di risorse separati, che è tipico se si prevede di condividere la raccolta immagini tra diverse soluzioni. In alternativa, è possibile scegliere di inserirli nello stesso gruppo di risorse.

Quando si crea un account Lab, è possibile creare e alleghi automaticamente una raccolta di immagini condivise.  Questa opzione consente di creare l'account Lab e la raccolta di immagini condivise in gruppi di risorse separati. Questo comportamento verrà visualizzato quando si esegue la procedura descritta in questa esercitazione: [configurare la raccolta di immagini condivise al momento della creazione dell'account Lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). L'immagine nella parte superiore di questo articolo usa anche questa configurazione. 

Si consiglia di investire tempo prima di pianificare la struttura dei gruppi di risorse perché *non* è possibile modificare il gruppo di risorse di un account Lab o di una raccolta di immagini condivise dopo che è stata creata. Se è necessario modificare il gruppo di risorse per queste risorse, è necessario eliminare e ricreare l'account Lab e\o Shared Image Gallery.

## <a name="lab-account"></a>Account Lab

Un account Lab funge da contenitore per uno o più laboratori della classe. Quando si inizia a usare Azure Lab Services, è comune avere solo un singolo account Lab. Con la scalabilità dell'utilizzo del Lab, è possibile scegliere di creare più account Lab.

Nell'elenco seguente sono evidenziati gli scenari in cui è possibile che più di un account Lab risulti vantaggioso:

- **Gestire i requisiti dei criteri diversi nei Lab della classe**

    Quando si configura un account Lab, si impostano i criteri che si applicano a *tutti* i Lab in aula nell'account Lab, ad esempio:
    - Rete virtuale di Azure con risorse condivise a cui il Lab della classe può accedere. Ad esempio, è possibile disporre di un set di Lab in Aula che necessitano dell'accesso a un set di dati condiviso all'interno di una rete virtuale.
    - Immagini della macchina virtuale (VM) che i Lab della classe possono usare per creare macchine virtuali. Ad esempio, è possibile disporre di un set di Lab in Aula che necessitano dell'accesso al [Data Science VM per](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) l'immagine del Marketplace Linux.

    Se si dispone di Lab in aula con requisiti di criteri univoci, potrebbe essere utile creare account Lab distinti per gestire separatamente questi Lab in aula.

- **Budget separato per account Lab**
  
    Anziché segnalare tutti i costi del Lab in Aula tramite un singolo account Lab, potrebbe essere necessario un budget separato più chiaramente. Ad esempio, è possibile creare account Lab per il reparto matematico dell'Università, per il reparto informatica e così via, per separare il budget tra i reparti.  È quindi possibile visualizzare il costo per ogni singolo account Lab usando [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isolare i laboratori pilota da active\production Labs**
  
    È possibile che si verifichino casi in cui si vuole eseguire il pilota delle modifiche dei criteri per un account Lab senza influire potenzialmente sul active\production Labs. In questo tipo di scenario la creazione di un account Lab separato a scopo pilota consente di isolare le modifiche. 

## <a name="classroom-lab"></a>Lab in aula

Un Lab della classe contiene macchine virtuali (VM) assegnate a un singolo studente.  In generale, è possibile prevedere:

- Disporre di un Lab in aula per ogni classe.
- Creare un nuovo set di Lab in aula ogni semestre (o per ogni intervallo di tempo offerto dalla classe). In genere, per le classi con le stesse esigenze di immagine, è consigliabile usare una [raccolta di immagini condivise](#shared-image-gallery) per riutilizzare le immagini tra i laboratori e i semestri.

Quando si determina la struttura dei Lab in aula, tenere presenti i punti seguenti:

- **Tutte le macchine virtuali all'interno di un Lab della classe vengono distribuite con la stessa immagine pubblicata**

    Di conseguenza, se si dispone di una classe che richiede la pubblicazione contemporaneamente di immagini Lab diverse, è necessario creare laboratori di classe separati per ognuno di essi.
  
- **La quota di utilizzo viene impostata a livello di Lab e si applica a tutti gli utenti all'interno del Lab**

    Per impostare quote diverse per gli utenti, è necessario creare laboratori della classe distinti. È tuttavia possibile aggiungere altre ore a un utente specifico dopo aver impostato la quota.
  
- **La pianificazione di avvio o arresto viene impostata a livello di Lab e si applica a tutte le macchine virtuali all'interno del Lab**

    In modo analogo al punto precedente, se è necessario impostare pianificazioni diverse per gli utenti, è necessario creare laboratori di classe distinti.

Per impostazione predefinita, ogni Lab della classe avrà una propria rete virtuale.  Se è abilitato il peering VNET, ogni Lab della classe avrà una propria subnet con peering alla rete virtuale specificata.

## <a name="shared-image-gallery"></a>Raccolta immagini condivise

Una raccolta di immagini condivise è collegata a un account Lab e funge da repository centrale per archiviare le immagini. Un'immagine viene salvata nella raccolta quando un educatore sceglie di eseguire l'esportazione da una macchina virtuale (VM) modello del Lab della classe. Ogni volta che un insegnante apporta modifiche alla macchina virtuale e alle esportazioni del modello, le nuove versioni dell'immagine vengono salvate mantenendo le versioni precedenti.

Gli insegnanti possono pubblicare una versione dell'immagine dalla raccolta di immagini condivise quando creano un nuovo Lab della classe. Sebbene nella raccolta vengano archiviate più versioni di un'immagine, i docenti possono solo selezionare la versione più recente durante la creazione del Lab.

La raccolta di immagini condivise è una risorsa facoltativa che potrebbe non essere necessaria immediatamente quando si inizia con solo pochi laboratori della classe. Tuttavia, l'uso della raccolta di immagini condivise presenta molti vantaggi che risultano utili per la scalabilità in modo da avere più laboratori in aula:

- **Consente di salvare e gestire le versioni di un'immagine di macchina virtuale modello**

    È utile creare un'immagine personalizzata o apportare modifiche (software, configurazione e così via) a un'immagine dalla raccolta di Marketplace pubblica.  Ad esempio, è comune per gli educatori richiedere l'installazione di software\tooling diversi. Anziché richiedere agli studenti di installare manualmente questi prerequisiti, è possibile esportare in una raccolta di immagini condivise diverse versioni dell'immagine di macchina virtuale del modello. Queste versioni delle immagini possono quindi essere usate durante la creazione di nuovi Lab per la classe.
- **Abilita sharing\reuse di immagini di VM modello nei Lab della classe**

    È possibile salvare e riutilizzare un'immagine in modo che non sia necessario configurare l'immagine da zero ogni volta che si crea un nuovo Lab della classe. Se, ad esempio, vengono offerte più classi che necessitano della stessa immagine, questa immagine deve essere creata una sola volta ed esportata nella raccolta immagini condivise in modo da poterla condividere nei Lab della classe.
- **Garantisce la disponibilità delle immagini tramite la replica**

    Quando si salva nella raccolta di immagini condivise da un Lab della classe, l'immagine viene replicata automaticamente in altre [aree all'interno della stessa area geografica](https://azure.microsoft.com/global-infrastructure/regions/). Nel caso in cui si verifichi un'interruzione per un'area, la pubblicazione dell'immagine nel Lab della classe non ha effetto perché è possibile usare una replica di immagini di un'altra area.  La pubblicazione di VM da più repliche può essere utile anche per le prestazioni.

Per raggruppare logicamente le immagini condivise, sono disponibili due opzioni:

- Creare più raccolte di immagini condivise. Ogni account Lab può connettersi solo a una raccolta di immagini condivise, quindi questa opzione richiederà anche la creazione di più account Lab.
- In alternativa, è possibile usare una singola raccolta di immagini condivise condivisa da più account Lab. In questo caso, ogni account Lab può abilitare solo le immagini applicabili ai laboratori della classe che contiene.

## <a name="naming"></a>Denominazione

Quando si inizia a usare Azure Lab Services, è consigliabile definire convenzioni di denominazione per i gruppi di risorse, gli account Lab, i Lab della classe e la raccolta di immagini condivise. Sebbene le convenzioni di denominazione stabilite siano univoche per le esigenze dell'organizzazione, nella tabella seguente vengono descritte le linee guida generali.

| Tipo di risorsa | Ruolo | Schema consigliato | Esempi |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contiene uno o più account Lab e una o più raccolte immagini condivise | \<\>-\<ambiente\>nome breve organizzazione-RG<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**Ambiente** identifica l'ambiente per la risorsa, ad esempio un progetto pilota o di produzione</li><li>**RG** sta per il tipo di risorsa: gruppo di risorse.</li></ul> | contosouniversitylabs-RG<br/>contosouniversitylabs-Pilot-RG<br/>contosouniversitylabs-prod-RG |
| Account Lab | Contiene uno o più Lab | \<\>-\<ambiente\>nome breve organizzazione-la<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**Ambiente** identifica l'ambiente per la risorsa, ad esempio un progetto pilota o di produzione</li><li>**La** sta per il tipo di risorsa: account Lab.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-Pilot-la<br/>sciencedeptlabs-prod-la |
| Lab in aula | Contiene una o più macchine virtuali |\<Identificatore dell'\>-\<educatore temporale\>-\<nome classe\><ul><li>**Nome classe** identifica il nome della classe supportata dal Lab.</li><li>L' **intervallo** di tempo identifica l'intervallo di tempo in cui viene offerta la classe.</li>L' **identificatore dell'istruzione** identifica l'educatore proprietario del Lab.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Raccolta immagini condivise | Contiene una o più versioni di immagini di macchina virtuale | \<raccolta nome\>breve organizzazione | contosouniversitylabsgallery |

Per altre informazioni sulla denominazione di altre risorse di Azure, vedere [convenzioni di denominazione per le risorse di Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Quando si configurano le risorse di Azure Lab Services, è necessario specificare un'area o un percorso del data center che ospiterà la risorsa. Di seguito sono riportate altre informazioni sul modo in cui l'area influisca su ognuna delle risorse riportate in relazione alla configurazione di un Lab.

### <a name="resource-group"></a>Resource group

L'area specifica il data center in cui vengono archiviate le informazioni sul gruppo di risorse. Le risorse di Azure contenute all'interno del gruppo di risorse possono trovarsi in aree diverse rispetto al relativo elemento padre.

### <a name="lab-account"></a>Account Lab

Il percorso di un account Lab indica l'area in cui è presente la risorsa.  

### <a name="classroom-lab"></a>Lab in aula

Il percorso in cui è presente un Lab della classe varia in base ai fattori seguenti:

  - **Il peering di un account Lab viene associato a una rete virtuale (VNet)**
  
    Un account Lab può essere sottoposto a [peering con un VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando si trovano nella stessa area.  Quando si esegue il peering di un account Lab con una VNet, i Lab della classe vengono creati automaticamente nella stessa area dell'account Lab e di VNet.

    > [!NOTE]
    > Quando si esegue il peering di un account Lab con un VNet, l'impostazione per **consentire al creatore del Lab di selezionare la posizione del Lab** è disabilitata. Per informazioni aggiuntive su questa impostazione, vedere l'articolo: [Allow Lab Creators to pick location for the Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nessun VNet viene sottoposto a peering ***e*** i creatori di Lab non possono scegliere la posizione del Lab**
  
    Quando non è presente **alcun** VNet con peering con l'account Lab *e* i [creatori di Lab **non** sono autorizzati a scegliere la posizione del Lab, i](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)Lab della classe vengono creati automaticamente in un'area con capacità VM disponibile.  In particolare, Azure Lab Services cerca la disponibilità in [aree che si trovano all'interno della stessa area geografica dell'account Lab](https://azure.microsoft.com/global-infrastructure/regions).

  - **Non è stato possibile eseguire il peering di VNet ***e*** gli autori del Lab possono scegliere la posizione del Lab**
       
    Quando non è presente **alcun** peering di VNet e gli [autori del Lab possono scegliere la posizione del Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), le posizioni selezionabili dall'autore del Lab sono basate sulla capacità disponibile.

> [!NOTE]
> Per garantire la capacità di una macchina virtuale sufficiente per un'area, è importante richiedere prima la capacità tramite l'account Lab o quando si crea il Lab.

Una regola generale consiste nell'impostare l'area di una risorsa su uno più vicino ai propri utenti. Per i laboratori in aula, questo significa creare il Lab della classe più vicino agli studenti. Per i corsi online in cui gli studenti si trovano in tutto il mondo, è necessario usare il giudizio migliore per creare un Lab in aula situato centralmente. In alternativa, suddividere una classe in più laboratori in aula in base all'area degli studenti.

### <a name="shared-image-gallery"></a>Raccolta immagini condivise

L'area indica l'area di origine in cui viene archiviata la prima versione dell'immagine prima che venga replicata automaticamente nelle aree di destinazione.

## <a name="vm-sizing"></a>Dimensionamento VM

Quando gli amministratori o i creatori di Lab creano un Lab per le aule, possono scegliere tra le seguenti dimensioni della macchina virtuale in base alle esigenze della propria classe. Tenere presente che le dimensioni di calcolo disponibili dipendono dall'area in cui si trova l'account Lab:

| Dimensione | Specifiche | Serie | Uso consigliato |
| ---- | ----- | ------ | ------------- |
| Piccolo| <ul><li>2 core</li><li>3,5 GB DI RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Queste dimensioni sono ideali per la riga di comando, l'apertura del Web browser, i server Web con traffico ridotto, i database di piccole e medie dimensioni. |
| Medio | <ul><li>4 core</li><li>7 GB DI RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Queste dimensioni sono ideali per database relazionali, Caching in memoria e analisi. |
| Media (virtualizzazione annidata) | <ul><li>4 core</li><li>16 GB DI RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Queste dimensioni sono ideali per database relazionali, Caching in memoria e analisi.  Questa dimensione supporta anche la virtualizzazione nidificata. |
| large | <ul><li>8 core</li><li>32 GB DI RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Queste dimensioni sono ideali per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale, database di grandi dimensioni, cache di memoria di grandi dimensioni.  Questa dimensione supporta anche la virtualizzazione nidificata. |
| GPU piccola (visualizzazione) | <ul><li>6 core</li><li>56 GB DI RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Queste dimensioni sono ideali per la visualizzazione remota, lo streaming, i giochi e la codifica tramite Framework come OpenGL e DirectX. |
| GPU piccola (calcolo) | <ul><li>6 core</li><li>56 GB DI RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Queste dimensioni sono ideali per applicazioni a elevato utilizzo di computer, ad esempio l'intelligenza artificiale e l'apprendimento avanzato. |
| GPU media (visualizzazione) | <ul><li>12 core</li><li>112 GB DI RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Queste dimensioni sono ideali per la visualizzazione remota, lo streaming, i giochi e la codifica tramite Framework come OpenGL e DirectX. |

## <a name="manage-identity"></a>Gestisci identità

Usando il [controllo degli accessi in base al ruolo di Azure, è](https://docs.microsoft.com/azure/role-based-access-control/overview)possibile assegnare i ruoli seguenti per concedere l'accesso agli account Lab e ai laboratori in aula:

- **Proprietario dell'account del lab**

    L'amministratore che crea l'account Lab viene aggiunto automaticamente al ruolo **proprietario** dell'account Lab.  Un amministratore a cui è assegnato il ruolo di **proprietario** può:
     - Modificare le impostazioni dell'account Lab.
     - Concedere ad altri amministratori l'accesso all'account Lab come proprietari o collaboratori.
     - Concedi ai docenti l'accesso ai laboratori in aula come autori, proprietari o collaboratori.
     - Creare e gestire tutti i Lab in aula nell'account Lab.

- **Collaboratore account Lab**

    Un amministratore a cui è assegnato il ruolo **collaboratore** può:
    - Modificare le impostazioni dell'account Lab.
    - Crea e Gestisci tutti i Lab in aula nell'account Lab.

    Tuttavia, *non possono* concedere ad altri utenti l'accesso agli account Lab o ai Lab della classe.

- **Creatore Lab della classe**

    Per creare Lab in aula in un account Lab, un educatore deve essere un membro del ruolo **Lab Creator** .  Quando un insegnante crea un Lab in aula, viene aggiunto automaticamente come proprietario del Lab.  Vedere l'esercitazione su come [aggiungere un utente al ruolo di **autore del Lab** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Lab per aule owner\contributor**
  
    Un insegnante può visualizzare e modificare le impostazioni di un Lab della classe quando è membro del ruolo **proprietario** o **collaboratore** del Lab. devono inoltre essere membri del ruolo **lettore** dell'account Lab.

    Una differenza fondamentale tra i ruoli **proprietario** e **collaboratore** di un Lab è che un collaboratore *non può* concedere ad altri utenti l'accesso per gestire i proprietari solo Lab può concedere ad altri utenti l'accesso per gestire il Lab.

    Inoltre, un insegnante *non può* creare nuovi Lab in aula a meno che non siano anche membri del ruolo di **autore del Lab** .

- **Raccolta immagini condivise**

    Quando si connette una raccolta di immagini condivise a un account Lab, all'account Lab owners\contributors e al Lab creators\owners\contributors viene concesso automaticamente l'accesso per visualizzare e salvare le immagini nella raccolta.

Ecco alcuni suggerimenti utili per l'assegnazione dei ruoli:
   - In genere, solo gli amministratori devono essere membri del ruolo **proprietario** o **collaboratore** di un account Lab; è possibile avere più di un owner\contributor.
   - Per consentire a un insegnante di creare nuovi Lab in aula e gestire i Lab creati; è sufficiente assegnare l'accesso al ruolo **Lab Creator** .
   - Per offrire a un insegnante la possibilità di gestire laboratori in aula specifici, ma *non* la possibilità di creare nuovi laboratori; è necessario assegnare l'accesso al ruolo **proprietario** o **collaboratore** per ogni Lab della classe che verrà gestito.  È possibile, ad esempio, consentire a un professore e a un assistente di insegnamento di creare una coordinata di un Lab in classe.  Vedere la guida su come [aggiungere un utente come proprietario a un Lab della classe](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Prezzi

### <a name="azure-lab-services"></a>Azure Lab Services

Il prezzo per Azure Lab Services è descritto nell'articolo seguente: [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

È anche necessario prendere in considerazione i prezzi per la raccolta di immagini condivise se si prevede di usarla per l'archiviazione e la gestione delle versioni delle immagini. 

### <a name="shared-image-gallery"></a>Raccolta immagini condivise

La creazione di una raccolta di immagini condivise e la relativa associazione all'account Lab sono gratuite. I costi non vengono sostenuti fino a quando non si salva una versione dell'immagine nella raccolta. In genere, i prezzi per l'uso di una raccolta di immagini condivise sono piuttosto trascurabili, ma è importante comprendere il modo in cui vengono calcolati perché non sono inclusi nei prezzi per Azure Lab Services.  

#### <a name="storage-charges"></a>Addebiti per l'archiviazione

Per archiviare le versioni delle immagini, una raccolta di immagini condivise usa dischi standard gestiti da HDD. Le dimensioni del disco gestito da HDD usato dipendono dalle dimensioni della versione dell'immagine da archiviare. Per visualizzare i prezzi, vedere l'articolo relativo ai prezzi di [Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Addebiti per la replica e l'uscita di rete

Quando si salva una versione dell'immagine usando una macchina virtuale (VM) modello di un Lab, Azure Lab Services prima la archivia in un'area di origine e quindi replica automaticamente la versione dell'immagine di origine in una o più aree di destinazione. È importante notare che Azure Lab Services replica automaticamente la versione dell'immagine di origine in tutte le aree di destinazione [all'interno dell'area geografica in](https://azure.microsoft.com/global-infrastructure/regions/) cui si trova il Lab della classe. Ad esempio, se il Lab della classe si trova nella geografia degli Stati Uniti, viene replicata una versione dell'immagine in ognuna delle otto aree presenti negli Stati Uniti

Un addebito in uscita dalla rete si verifica quando una versione dell'immagine viene replicata dall'area di origine in aree di destinazione aggiuntive. L'importo addebitato è basato sulle dimensioni della versione dell'immagine quando i dati dell'immagine vengono trasferiti inizialmente in uscita dall'area di origine.  Per informazioni dettagliate sui prezzi, fare riferimento all'articolo seguente: [Dettagli prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

I clienti di [soluzioni Education](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) possono rinunciare a pagare gli addebiti in uscita. Per ulteriori informazioni, rivolgersi all'account Manager.  Per ulteriori informazioni, vedere la sezione **domande frequenti** nel documento collegato, in particolare la domanda "quali sono i programmi per i trasferimenti di dati per i clienti accademici e come si qualificano?".

#### <a name="pricing-example"></a>Esempio di prezzi

Per ricapitolare i prezzi descritti in precedenza, esaminiamo un esempio di salvataggio dell'immagine della macchina virtuale del modello nella raccolta di immagini condivise. Si presuppongano gli scenari seguenti:

- Si dispone di un'immagine di macchina virtuale personalizzata.
- Si stanno salvando due versioni dell'immagine.
- Il Lab si trova negli Stati Uniti, che è costituito da un totale di otto aree.
- Ogni versione di immagine ha una dimensione di 32 GB; di conseguenza, il prezzo del disco gestito da HDD è $1,54 al mese.

Il costo totale è stimato come:

Numero di immagini × numero di versioni × numero di repliche × prezzo del disco gestito

In questo esempio il costo è:

1 immagine personalizzata (32 GB) x 2 versioni x 8 aree statunitensi x $1,54 = $24,64 al mese

#### <a name="cost-management"></a>Gestione dei costi

È importante che l'amministratore dell'account Lab gestisca i costi eliminando periodicamente le versioni di immagine non necessarie dalla raccolta. 

Non è consigliabile eliminare la replica in aree specifiche come modo per ridurre i costi. questa opzione è disponibile nella raccolta immagini condivise. Le modifiche alla replica possono avere effetti negativi sulla capacità del servizio lab di Azure di pubblicare VM da immagini salvate in una raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione per istruzioni dettagliate per la creazione di un account Lab e di un Lab: [Guida alla configurazione](tutorial-setup-lab-account.md)
