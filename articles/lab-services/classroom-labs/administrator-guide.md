---
title: Servizi di laboratorio di Azure - Guida dell'amministratore Documenti Microsoft
description: Questa guida consente agli amministratori che creano e gestiscono account lab tramite Azure Lab Services.This guide helps administrators who create and manage lab accounts using Azure Lab Services.
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
ms.openlocfilehash: 55e319ba8aecb9205c00dda4a400e37f7c010649
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257777"
---
# <a name="azure-lab-services---administrator-guide"></a>Guida per gli amministratori di Azure Lab Services - Administrator
Gli amministratori IT (Information Technology) che gestiscono le risorse cloud di un'università sono in genere responsabili della configurazione dell'account lab per la propria scuola. Dopo aver configurato un account lab, gli amministratori o gli educatori creano lab della classe contenuti nell'account lab. Questo articolo offre una panoramica generale delle risorse di Azure coinvolte e le linee guida per la loro creazione.

![Visualizzazione di alto livello delle risorse di Azure in un account labHigh-level view of Azure resources in a lab account](../media/administrator-guide/high-level-view.png)

- Classroom labs are hosted within an Azure subscription owned by Azure Lab Services.
- Gli account lab, la raccolta di immagini condivise e le versioni delle immagini sono ospitati all'interno della sottoscrizione.
- È possibile avere l'account lab e la raccolta di immagini condivise nello stesso gruppo di risorse. In questo diagramma si trovano in gruppi di risorse diversi. 

## <a name="subscription"></a>Subscription
L'università ha una o più sottoscrizioni di Azure.Your university has one or more Azure subscriptions. Una sottoscrizione viene usata per gestire la fatturazione e la sicurezza per tutte le risorse di Azure, ovvero i servizi usati al suo interno, inclusi gli account lab.

La relazione tra un account lab e la relativa sottoscrizione è importante perché:The relationship between a lab account and its subscription is important because:

- La fatturazione viene segnalata tramite la sottoscrizione che contiene l'account lab.
- È possibile concedere agli utenti nel tenant di Azure Active Directory (AD) della sottoscrizione l'accesso aI servizi lab di Azure.You can give users in the subscription's Azure Active Directory (AD) tenant access to Azure Lab Services. Puoi aggiungere un utente come proprietario dell'account lab, collaboratore, creatore del laboratorio della classe o proprietario del laboratorio della classe.

I laboratori di classroom e le relative macchine virtuali (VM) vengono gestiti e ospitati all'interno di una sottoscrizione di proprietà di Azure Lab Services.

## <a name="resource-group"></a>Resource group
Una sottoscrizione contiene uno o più gruppi di risorse. I gruppi di risorse vengono usati per creare raggruppamenti logici di risorse di Azure usate insieme all'interno della stessa soluzione.  

Quando si crea un account lab, è necessario configurare il gruppo di risorse che contiene l'account lab. 

Un gruppo di risorse è necessario anche quando si crea una [raccolta immagini condivisa.](#shared-image-gallery) È possibile scegliere di inserire l'account lab e la raccolta di immagini condivise in due gruppi di risorse separati, il che è tipico se si prevede di condividere la raccolta immagini tra soluzioni diverse. In alternativa, è possibile scegliere di inserirli nello stesso gruppo di risorse.

Quando si crea un account lab, è possibile creare e allegare automaticamente una raccolta di immagini condivise contemporaneamente.  Questa opzione determina la creazione dell'account lab e della raccolta di immagini condivise in gruppi di risorse separati. Si noterà questo comportamento quando si utilizza la procedura descritta in questa [esercitazione: Configurare la raccolta di immagini condivise al momento della creazione dell'account lab](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Anche l'immagine all'inizio di questo articolo usa questa configurazione. 

Ti consigliamo di investire il tempo in anticipo per pianificare la struttura dei gruppi di risorse, poiché *non* è possibile modificare il gruppo di risorse di un account lab o di una raccolta di immagini condivise dopo la creazione. Se è necessario modificare il gruppo di risorse per queste risorse, è necessario eliminare e ricreare l'account lab e o la raccolta di immagini condivise.

## <a name="lab-account"></a>Account di laboratorio
Un account di laboratorio funge da contenitore per uno o più laboratori di classe. Quando si inizia a usare Azure Lab Services, è comune avere un solo account lab. Man mano che l'utilizzo del lab viene ridimensionato, è possibile scegliere in un secondo momento di creare più account lab.

Nell'elenco seguente vengono evidenziati gli scenari in cui possono essere utili più account lab:The following list highlights scenarios where more than one lab account may be beneficial:

- **Gestire i diversi requisiti di policy nei laboratori di classe** 
    
    Quando si configura un account lab, si impostano i criteri che si applicano a *tutti i* lab della classe con l'account lab, ad esempio:
    - Rete virtuale di Azure con risorse condivise a cui il lab della classe può accedere. Ad esempio, è possibile disporre di un set di lab della classe che devono accedere a un set di dati condivisi all'interno di una rete virtuale.
    - Le immagini delle macchine virtuali (VM) che i laboratori della classe possono usare per creare macchine virtuali. Ad esempio, potresti avere un set di laboratori per classi che devono accedere all'immagine [data science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace. 
    
    Se si dispone di laboratori in aula con requisiti di criteri univoci l'uno dall'altro, potrebbe essere utile creare account lab separati per gestire questi laboratori di classe separatamente.

- **Budget separato per conto di laboratorio**
  
    Invece di segnalare tutti i costi del laboratorio in classe tramite un singolo account di laboratorio, potrebbe essere necessario un budget più chiaramente separato. Ad esempio, è possibile creare account lab per il reparto matematica dell'università, il dipartimento di informatica e così via, per separare il budget tra i reparti.  È quindi possibile visualizzare il costo per ogni singolo account lab usando Gestione costi di [Azure.](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)
    
- **Isolare i laboratori pilota dai laboratori di produzione attivi**
  
    Potrebbero verificarsi casi in cui si desidera pilotare le modifiche dei criteri per un account lab senza influire potenzialmente sui laboratori di produzione attivi. In questo tipo di scenario, la creazione di un account lab separato per scopi pilota consente di isolare le modifiche. 

## <a name="classroom-lab"></a>Laboratorio di classroom
Un lab di classe contiene macchine virtuali (VM) assegnate a un singolo studente. In generale, ci si può aspettare di:

- Avere un laboratorio in classe per ogni classe.
- Crea un nuovo set di laboratori in aula ogni semestre (o per ogni intervallo di tempo in cui viene offerta la tua classe). In genere per le classi che hanno le stesse esigenze di immagine, è consigliabile usare una raccolta di [immagini condivise](#shared-image-gallery) per riutilizzare le immagini tra laboratori e semestri.

Quando si determina come strutturare i laboratori della classe, tenere presente quanto segue:

- **Tutte le macchine virtuali all'interno di un laboratorio di classe vengono distribuite con la stessa immagine pubblicata**

    Di conseguenza, se si dispone di una classe che richiede immagini di laboratorio diverse essere pubblicato contemporaneamente, è necessario creare laboratori di classe separati per ognuno di essi.
  
- **La quota di utilizzo viene impostata a livello di laboratorio e si applica a tutti gli utenti all'interno del lab**
    
    Per impostare quote diverse per gli utenti, è necessario creare lab di classe separati. Tuttavia, è possibile aggiungere più ore a un utente specifico dopo aver impostato la quota.
  
- **La pianificazione di avvio o arresto viene impostata a livello di laboratorio e si applica a tutte le macchine virtuali all'interno del lab**

    Simile al punto precedente, se è necessario impostare pianificazioni diverse per gli utenti, è necessario creare laboratori di classe separati. 

## <a name="shared-image-gallery"></a>Raccolta di immagini condivise
Una raccolta di immagini condivise è collegata a un account lab e funge da archivio centrale per l'archiviazione delle immagini. Un'immagine viene salvata nella raccolta quando un docente sceglie di esportare dalla macchina virtuale (VM) modello di un lab della classe. Ogni volta che un docente apporta modifiche alla macchina virtuale del modello e alle esportazioni, vengono salvate nuove versioni dell'immagine mantenendo le versioni precedenti.

Gli istruttori possono pubblicare una versione dell'immagine dalla raccolta immagini condivisa quando creano un nuovo laboratorio della classe. Sebbene la raccolta possa archiviare più versioni di un'immagine, gli educatori possono selezionare solo la versione più recente durante la creazione del lab.

La raccolta di immagini condivise è una risorsa facoltativa che potrebbe non essere necessaria immediatamente quando si inizia con solo pochi laboratori di classe. Tuttavia, l'uso della raccolta di immagini condivise offre molti vantaggi utili per la scalabilità in base a più laboratori in classe:

- **Consente di salvare e gestire le versioni di un'immagine di macchina virtuale modello**

    È utile creare un'immagine personalizzata o apportare modifiche (software, configurazione e così via) a un'immagine dalla galleria Marketplace pubblica.  Ad esempio, è comune per gli educatori richiedere l'installazione di software diverso, ovvero strumenti. Anziché richiedere agli studenti di installare manualmente questi prerequisiti per conto proprio, è possibile esportare versioni diverse dell'immagine della macchina virtuale del modello in una raccolta immagini condivisa. Queste versioni delle immagini possono quindi essere usate durante la creazione di nuovi laboratori per le classi.
- **Consente la condivisione: riutilizzo delle immagini di macchine virtuali modello nei laboratori di classe**

    È possibile salvare e riutilizzare un'immagine in modo da non dover configurare l'immagine da zero ogni volta che si crea un nuovo laboratorio della classe. Ad esempio, se vengono offerte più classi che richiedono la stessa immagine, questa immagine deve essere creata una sola volta ed esportata nella raccolta di immagini condivise in modo che possa essere condivisa tra i laboratori della classe.
- **Garantisce la disponibilità delle immagini tramite la replicaEnsures image availability through replication**

    Quando si salva nella raccolta di immagini condivise da un laboratorio di classe, l'immagine viene automaticamente replicata in altre [aree all'interno della stessa area geografica.](https://azure.microsoft.com/global-infrastructure/regions/) Nel caso in cui si sia verificato un'interruzione di un'area, la pubblicazione dell'immagine nel laboratorio della classe non è influenzata dal momento che è possibile usare una replica dell'immagine da un'altra area.  La pubblicazione di macchine virtuali da più repliche può essere utile anche per le prestazioni.

Per raggruppare logicamente le immagini condivise, sono disponibili due opzioni:

- Crea più gallerie di immagini condivise. Ogni account lab può connettersi a una sola raccolta di immagini condivise, pertanto questa opzione richiederà anche la creazione di più account lab.
- In alternativa, è possibile utilizzare una singola raccolta di immagini condivise condivisa condivisa da più account lab. In questo caso, ogni account lab può abilitare solo le immagini applicabili ai lab della classe in esso contenuti.

## <a name="naming"></a>Denominazione
Per iniziare a usare Azure Lab Services, è consigliabile stabilire convenzioni di denominazione per i gruppi di risorse, gli account lab, i lab della classe e la raccolta di immagini condivise. Mentre le convenzioni di denominazione stabilite saranno univoche per le esigenze dell'organizzazione, nella tabella seguente vengono descritte le linee guida generali.

| Tipo di risorsa | Ruolo | Schema consigliato | Esempi |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contiene uno o più account lab e una o più raccolte di immagini condivise | \<organizzazione nome\>-\<\>breve ambiente -rg<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**L'ambiente** identifica l'ambiente per la risorsa, ad esempio Pilota o Produzione</li><li>**Rg** è l'acronimo di resource type: resource group.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Account di laboratorio | Contiene uno o più laboratori | \<organizzazione nome\>-\<\>breve ambiente -la<ul><li>**Nome breve organizzazione** identifica il nome dell'organizzazione supportata dal gruppo di risorse</li><li>**L'ambiente** identifica l'ambiente per la risorsa, ad esempio Pilota o Produzione</li><li>**La** è l'acronimo del tipo di risorsa: account lab.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratorio di classroom | Contiene una o più macchine virtuali |\<nome\>-\<classe\>-\<timeframe educatore identificatore\><ul><li>**Nome classe** identifica il nome della classe supportata dal lab.</li><li>**Timeframe** identifica l'intervallo di tempo in cui viene offerta la classe.</li>**L'identificatore di istruzione** identifica l'educatore proprietario del lab.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-primavera2019-johndoe | 
| Raccolta di immagini condivise | Contiene una o più versioni dell'immagine della macchina virtuale | \<galleria di\>nomi brevi dell'organizzazione | contosouniversitylabsgallery |

Per altre informazioni sulla denominazione di altre risorse di Azure, vedere Convenzioni di denominazione per le risorse di Azure.For more information on naming other Azure resources, see [Naming conventions for Azure resources.](/azure/architecture/best-practices/naming-conventions)

## <a name="regionslocations"></a>Regioni- località

Quando si configurano le risorse di Azure Lab Services, è necessario fornire un'area o una posizione del data center che ospiterà la risorsa. Di seguito sono riportati ulteriori dettagli sull'impatto dell'area su ogni risorsa coinvolta nella configurazione di un lab.

### <a name="resource-group"></a>Resource group

L'area specifica il data center in cui sono archiviate le informazioni sul gruppo di risorse. Le risorse di Azure contenute nel gruppo di risorse possono trovarsi in aree diverse dall'elemento padre.

### <a name="lab-account"></a>Account di laboratorio

La posizione di un account lab indica l'area in cui è presente questa risorsa.  

### <a name="classroom-lab"></a>Laboratorio di classroom
    
La posizione in cui è presente un laboratorio in aula varia in base ai seguenti fattori:

  - **L'account lab è sottoposto a peered a una rete virtuale (VNet)Lab account is peered to a virtual network (VNet)**
  
    È possibile [eseguire il peered](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) di un account lab con una rete virtuale quando si trovano nella stessa area.  Quando si esegue il peered di un account lab con una rete virtuale, i laboratori della classe vengono creati automaticamente nella stessa area sia dell'account lab che della rete virtuale.

    > [!NOTE]
    > Quando si esegue il peered di un account lab con una rete virtuale, l'impostazione **Consenti al creatore del lab** di selezionare il percorso del lab è disabilitata. Ulteriori informazioni su questa impostazione sono disponibili nell'articolo: Consentire al creatore del lab di [selezionare il percorso per il lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nessuna rete virtuale viene sottoposta a peered ***e*** gli autori di lab non sono autorizzati a selezionare la posizione del lab**
  
    Quando non è presente **alcuna** rete virtuale sottoposta a peerizzazione con l'account lab *e* agli autori di [lab **non** è consentito selezionare la posizione del lab,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)i laboratori della classe vengono creati automaticamente in un'area con capacità di macchina virtuale disponibile.  In particolare, Azure Lab Services cerca la disponibilità nelle [aree che si trovano nella stessa area geografica dell'account lab.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Nessuna rete virtuale è sottoposta a peered ***e*** i creatori di laboratorio sono autorizzati a selezionare la posizione del lab**
       
    Quando non è presente **alcun** peered della rete virtuale e i creatori del [lab sono autorizzati a selezionare la posizione del lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), i percorsi che possono essere selezionati dall'autore del lab sono basati sulla capacità disponibile.

> [!NOTE]
> Per garantire che la capacità della macchina virtuale sia sufficiente per un'area, è importante richiedere prima la capacità tramite l'account lab o durante la creazione del lab.

Una regola generale consiste nell'impostare l'area di una risorsa su una più vicina agli utenti. Per i laboratori in aula, questo significa creare il laboratorio di classe più vicino ai tuoi studenti. Per i corsi online in cui gli studenti si trovano in tutto il mondo, è necessario utilizzare il giudizio migliore per creare un laboratorio di classe che si trova in posizione centrale. In alternativa, suddividi un corso in più laboratori in aula in base all'area geografica degli studenti.

### <a name="shared-image-gallery"></a>Raccolta di immagini condivise

L'area indica l'area di origine in cui viene archiviata la prima versione dell'immagine prima che venga replicata automaticamente nelle aree di destinazione.

## <a name="vm-sizing"></a>Dimensionamento delle macchine virtuali
Quando gli amministratori o i creatori di laboratori creano un lab della classe, possono scegliere tra le dimensioni delle macchine virtuali seguenti in base alle esigenze della classe. Tenere presente che le dimensioni di calcolo disponibili dipendono dall'area geografica in cui si trova l'account lab:

| Dimensione | Specifiche | Serie | Uso consigliato |
| ---- | ----- | ------ | ------------- |
| Piccolo| <ul><li>2 core</li><li>3,5 GB di RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Questa dimensione è più adatta per la riga di comando, l'apertura del browser web, i server web a basso traffico, i database di piccole e medie dimensioni. |
| Media | <ul><li>4 Nuclei</li><li>7 GB di RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Questa dimensione è più adatta per i database relazionali, la memorizzazione nella cache in memoria e l'analisi. |
| Medio (virtualizzazione annidata) | <ul><li>4 Nuclei</li><li>16 GB di RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Questa dimensione è più adatta per i database relazionali, la memorizzazione nella cache in memoria e l'analisi.  Questa dimensione supporta anche la virtualizzazione annidata. |
| large | <ul><li>8 core</li><li>32 GB di RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Questa dimensione è più adatta per le applicazioni che richiedono CPU più veloci, migliori prestazioni del disco locale, database di grandi dimensioni, cache di memoria di grandi dimensioni.  Questa dimensione supporta anche la virtualizzazione annidata. |
| GPU piccolo (visualizzazione) | <ul><li>6 Nuclei</li><li>56 GB di RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Questa dimensione è più adatta per la visualizzazione remota, lo streaming, il gioco, la codifica utilizzando framework come OpenGL e DirectX. |
| GPU piccola (Compute) | <ul><li>6 Nuclei</li><li>56 GB di RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Questa dimensione è più adatta per applicazioni ad alta intensità di computer come l'intelligenza artificiale e il Deep Learning. |
| GPU medio (visualizzazione) | <ul><li>12 Nuclei</li><li>112 GB di RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Questa dimensione è più adatta per la visualizzazione remota, lo streaming, il gioco, la codifica utilizzando framework come OpenGL e DirectX. |

## <a name="manage-identity"></a>Gestire l'identità
Usando il [controllo degli accessi in base](https://docs.microsoft.com/azure/role-based-access-control/overview)al ruolo di Azure, è possibile assegnare i ruoli seguenti per concedere l'accesso agli account lab e ai laboratori in aula:

- **Proprietario dell'account del lab**

    L'amministratore che crea l'account lab viene aggiunto automaticamente al ruolo **Proprietario** dell'account lab.  Un amministratore a cui è assegnato il ruolo **Proprietario** può:
     - Modificare le impostazioni dell'account lab.
     - Concedere ad altri amministratori l'accesso all'account lab come proprietari o collaboratori. 
     - Concedi agli educatori l'accesso ai laboratori in aula come creatori, proprietari o collaboratori.
     - Crea e gestisci tutti i laboratori della classe all'interno dell'account del laboratorio.

- **Collaboratore dell'account lab**

    Un amministratore a cui è assegnato il ruolo **Collaboratore** può:
    - Modificare le impostazioni dell'account lab.
    - Crea e gestisci tutti i laboratori della classe all'interno dell'account lab.
    
    Tuttavia, *non possono* concedere ad altri utenti l'accesso agli account lab o ai laboratori in aula.

- **Creatore del laboratorio di Classroom**

    Per creare laboratori in aula all'interno di un account lab, un docente deve essere membro del ruolo **Lab Creator.**  Quando un docente crea un lab della classe, viene automaticamente aggiunto come proprietario del lab.  Fare riferimento all'esercitazione su come [aggiungere un utente al ruolo **Creatore lab** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Proprietario del laboratorio di Classroom: collaboratore**
  
    Un docente può visualizzare e modificare le impostazioni di un lab della classe quando è membro del ruolo **Proprietario** o **Collaboratore** di un lab. devono anche essere membri del ruolo **Lettore** dell'account lab.

    Una differenza fondamentale tra i ruoli **Proprietario** e **Collaboratore** di un lab consiste nel fatto che un collaboratore *non può* concedere ad altri utenti l'accesso per gestire il lab: solo i proprietari possono concedere ad altri utenti l'accesso per gestire il lab.
    
    Inoltre, un educatore *non può* creare nuovi laboratori in aula a meno che non siano anche membri del ruolo **Lab Creator.**

- **Raccolta di immagini condivise**
    
    Quando si allega una raccolta di immagini condivise a un account lab, ai proprietari degli account lab, ai collaboratori e ai creatori di laboratori, ai proprietari e ai collaboratori, viene automaticamente concesso l'accesso per visualizzare e salvare le immagini nella raccolta. 

Ecco alcuni suggerimenti per facilitare l'assegnazione dei ruoli:
   - In genere, solo gli amministratori devono essere membri dei ruoli **Proprietario** o **Collaboratore** di un account lab. è possibile che si verifichino più di un proprietario/collaboratore.

   - Per dare a un educatore la possibilità di creare nuovi laboratori in aula e gestire i laboratori che creano; è sufficiente assegnare l'accesso al ruolo **Lab Creator.**
   
   - Per dare a un educatore la possibilità di gestire laboratori specifici in classe, ma *non* la possibilità di creare nuovi laboratori; è necessario assegnare l'accesso al ruolo **Proprietario** o **Collaboratore** per ognuno dei lab della classe che gestiranno.  Ad esempio, è possibile consentire sia a un professore che a un assistente didattico di co-possedere un laboratorio in classe.  Fare riferimento alla guida su come [aggiungere un utente come proprietario a un laboratorio](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)di classe .

## <a name="pricing"></a>Prezzi

### <a name="azure-lab-services"></a>Azure Lab Services
I prezzi per Azure Lab Services sono descritti nell'articolo seguente: Prezzi di [Azure Lab Services.](https://azure.microsoft.com/pricing/details/lab-services/)

È inoltre necessario considerare i prezzi per la raccolta di immagini condivise se si prevede di utilizzarla per l'archiviazione e la gestione delle versioni delle immagini. 

### <a name="shared-image-gallery"></a>Raccolta di immagini condivise
La creazione di una galleria di immagini condivise e il suo collegamento al tuo account lab sono gratuiti. I costi non vengono sostenuti fino a quando non si salva una versione dell'immagine nella raccolta. In genere, i prezzi per l'utilizzo di una raccolta di immagini condivise sono abbastanza trascurabili, ma è importante comprendere come viene calcolato poiché non è incluso nei prezzi per Azure Lab Services.  

#### <a name="storage-charges"></a>Spese di stoccaggio
Per archiviare le versioni delle immagini, una raccolta immagini condivise utilizza dischi standard gestiti dall'UNITÀ DISCO. La dimensione del disco gestito dall'HDD utilizzato dipende dalle dimensioni della versione dell'immagine archiviata. Vedere il seguente articolo per visualizzare i prezzi: [Managed disks pricing](https://azure.microsoft.com/pricing/details/managed-disks/).


#### <a name="replication-and-network-egress-charges"></a>Replica e addebiti per la uscita di rete
Quando si salva una versione dell'immagine usando una macchina virtuale (VM) modello del lab della classe, Azure Lab Services la archivia prima in un'area di origine e quindi replica automaticamente la versione dell'immagine di origine in una o più aree di destinazione. È importante notare che Azure Lab Services replica automaticamente la versione dell'immagine di origine in tutte le aree di destinazione [all'interno dell'area geografica](https://azure.microsoft.com/global-infrastructure/regions/) in cui si trova il lab della classe. Ad esempio, se il laboratorio della classe si trova nell'area geografica degli Stati Uniti, una versione dell'immagine viene replicata in ognuna delle otto aree presenti negli Stati Uniti.

Un addebito per l'uscita di rete si verifica quando una versione dell'immagine viene replicata dall'area di origine in altre aree di destinazione. L'importo addebitato si basa sulle dimensioni della versione dell'immagine quando i dati dell'immagine vengono inizialmente trasferiti in uscita dall'area di origine.  Per informazioni dettagliate sui prezzi, vedere il seguente articolo: [Dettagli sui prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

[Soluzioni didattiche](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) i clienti possono essere revocati dal pagamento di spese in uscita. Parla con il tuo account manager per saperne di più.  Per ulteriori informazioni, vedere la sezione **Domande frequenti** nel documento collegato, in particolare la domanda "Quali programmi di trasferimento dati esistono per i clienti accademici e come posso qualificare?".

#### <a name="pricing-example"></a>Esempio di determinazione dei prezzi
Per ricapitolare i prezzi descritti in precedenza, esaminiamo un esempio di salvataggio dell'immagine della macchina virtuale modello nella raccolta di immagini condivise. Si supponga che gli scenari seguenti:

- Si dispone di un'immagine di macchina virtuale personalizzata.
- Si stanno salvando due versioni dell'immagine.
- Il tuo laboratorio è negli Stati Uniti, che ha un totale di otto regioni.
- Ogni versione dell'immagine ha una dimensione di 32 GB; di conseguenza, il prezzo del disco gestito da HDD è di 1,54 dollari al mese.

Il costo totale è stimato come:

Numero di immagini - numero di versioni - numero di repliche - prezzo del disco gestito

In questo esempio, il costo è:

1 immagine personalizzata (32 GB) x 2 versioni x 8 regioni u.S.A. x 1,54 USD - 24,64 USD al mese

#### <a name="cost-management"></a>Gestione dei costi
È importante che l'amministratore dell'account lab gestisca i costi eliminando regolarmente le versioni delle immagini non necessarie dalla raccolta. 

Non è consigliabile eliminare la replica in aree specifiche per ridurre i costi (questa opzione esiste nella raccolta di immagini condivise). Le modifiche alla replica possono avere effetti negativi sulla capacità del servizio Lab di Azure di pubblicare macchine virtuali da immagini salvate in una raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'esercitazione per istruzioni dettagliate per creare un account lab e un lab: [Guida alla configurazione](tutorial-setup-lab-account.md)
