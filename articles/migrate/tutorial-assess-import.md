---
title: Valutare i server che usano dati server importati con Valutazione server di Azure Migrate
description: Viene descritto come valutare i server locali per la migrazione ad Azure con Valutazione server di Azure Migrate con dati importati.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 86d61dbd75363f68ee4651bfb36f940810909c00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509945"
---
# <a name="assess-servers-using-imported-data"></a>Valutare i server con dati importati

Questo articolo descrive come valutare i server locali con [Azure Migrate: Valutazione server](migrate-services-overview.md#azure-migrate-server-assessment-tool), tramite l'importazione di metadati del server con CSV. Con questo metodo di valutazione, non è necessario configurare l'appliance Azure Migrate per creare una valutazione. Questa operazione è utile se: 

- Si vuole creare una rapida valutazione iniziale prima di distribuire l'appliance.
- Non è possibile distribuire l'appliance di Azure Migrate nell'organizzazione.
- Non è possibile condividere le credenziali che consentono l'accesso ai server locali.
- I vincoli di sicurezza impediscono la raccolta e l'invio di dati raccolti dall'appliance ad Azure. Con un file importato, è possibile controllare i dati condivisi e molti dati (che offrono, ad esempio, indirizzi IP) sono facoltativi.


## <a name="before-you-start"></a>Prima di iniziare

Si noti che:

- È possibile aggiungere fino a un massimo di 20000 server in un singolo file con estensione csv.
- È possibile aggiungere fino a 20000 server in un progetto Azure Migrate tramite CSV.
- È possibile caricare più volte le informazioni sul server con CSV per Valutazione server di Azure Migrate.
- Anche se la raccolta di informazioni sull'applicazione è utile nella valutazione dell'ambiente locale per la migrazione, Valutazione server di Azure Migrate attualmente non esegue la valutazione a livello di applicazione e non prende in considerazione le applicazioni nella creazione di una valutazione.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare un progetto di Azure Migrate.
> * Compilare un file con estensione csv con le informazioni sul server.
> * Importare il file per aggiungere informazioni sul server in Valutazione server di Azure Migrate.
> * Creare ed esaminare una valutazione.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere gli articoli sulle procedure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="set-azure-permissions-for-azure-migrate"></a>Impostare le autorizzazioni di Azure per Azure Migrate 

l'account Azure deve avere le autorizzazioni per creare un progetto di Azure Migrate.

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.


## <a name="set-up-an-azure-migrate-project"></a>Configurare un progetto di Azure Migrate

Per configurare un nuovo progetto di Azure Migrate, seguire questa procedura.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Individuare, valutare ed eseguire la migrazione dei server** della pagina **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server**.

    ![Individuare e valutare i server](./media/tutorial-assess-import/assess-migrate.png)

4. In **Attività iniziali** fare clic su **Aggiungi strumenti**.
5. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.     
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare.

    - [Esaminare](migrate-support-matrix.md#supported-geographies) le aree geografiche supportate. L'area geografica del progetto viene usata solo per archiviare i metadati raccolti dalle macchine virtuali locali.
    - Per la migrazione è possibile selezionare qualsiasi area di destinazione.

    ![Creare un progetto di Azure Migrate](./media/tutorial-assess-import/migrate-project.png)


7. Fare clic su **Avanti**.
8. In **Selezionare lo strumento di valutazione** selezionare **Azure Migrate: Valutazione server** > **Avanti**.

    ![Creare un progetto di Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. In **Selezione strumento di migrazione** selezionare **Ignora l'aggiunta di uno strumento di migrazione per adesso** > **Avanti**.
10. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e fare clic su **Aggiungi strumenti**.
11. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Verrà visualizzata la pagina del progetto. Se il progetto non viene visualizzato, è possibile accedervi da **Server** nel dashboard di Azure Migrate.


## <a name="prepare-the-csv"></a>Preparare il CSV

Scaricare il modello CSV e aggiungervi le informazioni sul server.


### <a name="download-the-template"></a>Scaricare il modello

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2. In **Individua macchine virtuali**selezionare **Importa tramite file CSV**.
3. Fare clic su **Scarica** per scaricare il modello di file con estensione csv. In alternativa, è possibile [scaricarlo direttamente](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![Scaricare il modello di file con estensione csv](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Aggiungere informazioni sul server

Raccogliere i dati sul server e aggiungerli al file con estensione csv.

- Per raccogliere dati, è possibile esportarli dagli strumenti usati per la gestione di server locali, ad esempio VMware vSphere o il database di gestione della configurazione.
- Per esaminare i dati di esempio, scaricare il [file di esempio](https://go.microsoft.com/fwlink/?linkid=2108405).


Nella tabella seguente vengono riepilogati i campi del file da compilare.

**Nome campo** | **Obbligatorio** | **Dettagli**
--- | --- | ---
**Nome server** | Sì | È consigliabile specificare il nome di dominio completo. 
**Indirizzo IP** | No | Indirizzo del server.
**Numero di core** | Sì | Numero di core del processore allocati al server.
**Memoria** | Sì | RAM totale (MB) allocata al server.
**Nome sistema operativo** | Sì | Sistema operativo server.
**Versione del sistema operativo** | No | Versione del sistema operativo server.
**Numero di dischi** | No | Non necessario se sono presenti i dettagli dei singoli dischi.
**Dimensioni disco 1**  | No | Dimensioni massime del disco (GB)<br/> È possibile aggiungere dettagli per altri dischi tramite[aggiunta di colonne](#add-multiple-disks) nel modello. È possibile aggiungere fino a otto versioni del disco.
**Operazioni di lettura disco 1** | No | Operazioni di lettura da disco al secondo.
**Operazioni di scrittura disco 1** | No | Operazioni di scrittura su disco al secondo.
**Velocità effettiva lettura da disco 1** | No | Dati letti da disco al secondo in MB al secondo.
**Velocità effettiva scrittura da disco 1** | No | Dati scritti su disco al secondo in MB al secondo.
**Percentuale utilizzo CPU** | No | Percentuale utilizzo della CPU.
**Percentuale utilizzo memoria** | No | Percentuale utilizzo della RAM.
**Totale operazioni di lettura dischi** | No | Operazioni di lettura da disco al secondo.
**Totale operazioni di scrittura dischi** | No | Operazioni di scrittura su disco al secondo.
**Totale velocità effettiva lettura da dischi** | No | Dati letti da disco in MB al secondo.
**Totale velocità effettiva scrittura da dischi** | No | Dati scritti su disco in MB al secondo.
**Velocità effettiva della rete in ingresso** | No | Dati ricevuti dal server in MB al secondo.
**Velocità effettiva della rete in uscita** | No | Dati trasmessi dal server in MB al secondo.
**Tipo di firmware** | No | Firmware del server. I valori possono essere "BIOS" o "UEFI"
**Tipo di server** | No | I valori possono essere "Fisico" o "Virtuale".
**Hypervisor** | No | Hypervisor in cui un computer è in esecuzione. <br/> I valori possono essere "VMware", "Hyper-V", "Xen", "AWS", "GCP" o "Altro".
**Numero di versione Hypervisor** | No | Versione Hypervisor.
**ID macchina virtuale** | No | Identificatore VM. Questo è il valore **InstanceUUid** per la VM VMware vCenter o l'**ID della macchina virtuale Hyper-V** per Hyper-V.
**ID Virtual Machine Manager** | No | Questo è il valore **InstanceUUid** per VMWare vCenter. Non necessario per Hyper-V.
**Indirizzo MAC**| No | Indirizzo MAC server.
**BIOS ID** | No | Server BIOS ID.
**ID server personalizzato**| No | ID univoci locali del server locale. <br/> Utile per il rilevamento del server importato in base all'ID locale. 
**Nome applicazione 1** | No | Nome dei carichi di lavoro in esecuzione nel server.<br/> È possibile aggiungere dettagli per altre app tramite[aggiunta di colonne](#add-multiple-applications) nel modello. È possibile aggiungere fino a cinque applicazioni.
**Tipo applicazione 1** | No | Tipo di carico di lavoro in esecuzione nel server
**Versione applicazione 1** | No | Versione del carico di lavoro in esecuzione nel server.
**Scadenza licenza applicazione 1** | No | Scadenza della licenza del per il carico di lavoro (se applicabile).
**Business unit** | No | Business Unit a cui appartiene il server.
**Titolare dell'azienda** | No | Titolare business unit.
**Nome applicazione aziendale** | No | Nome dell'applicazione cui appartiene l'app.
**Posizione** | No | Data center in cui si trova il server.
**Data di rimozione del server** | No | Rimuovere la data di rimozione del server fisico o il server fisico sottostante del server virtuale

### <a name="add-operating-systems"></a>Aggiungere sistemi operativi

La valutazione riconosce i nomi specifici del sistema operativo. Qualsiasi nome del sistema operativo specificato deve corrispondere a una delle opzioni nell'elenco [nomi supportati](#supported-operating-system-names).


### <a name="add-multiple-disks"></a>Aggiungere più dischi

Il modello fornisce campi predefiniti per il primo disco.  È possibile aggiungere colonne simili fino a 8 dischi. 

Ad esempio, per specificare tutti i campi per un secondo disco, aggiungere le colonne:

Dimensioni Disco 2 Operazioni lettura Disco 2 Operazioni scrittura Disco 2 Velocità effettiva lettura Disco 2 Velocità effettiva scrittura Disco 2

Facoltativamente, è possibile aggiungere campi specifici solo per un disco.


### <a name="add-multiple-applications"></a>Aggiungere più applicazioni

Il modello fornisce campi per una singola applicazione. È possibile aggiungere colonne simili fino a cinque app.  

Ad esempio, per specificare tutti i campi per una seconda app, aggiungere le colonne:

Nome applicazione 2 Tipo applicazione 2 Versione applicazione 2 Scadenza licenza applicazione 2


Facoltativamente, è possibile aggiungere campi specifici solo per una app.

> [!NOTE]
> Le informazioni sulle app sono utili quando si valuta l'ambiente locale per la migrazione. Tuttavia, Valutazione server di Azure Migrate non esegue attualmente la valutazione a livello di app e non prende in considerazione le app durante la creazione di una valutazione.


## <a name="upload-the-server-information"></a>Carica informazioni sul server

Dopo l'aggiunta di informazioni al modello CSV, importare i server in Azure Migrate: Valutazione server.

1. In Azure Migrate > **Individua macchine virtuali**, cercare il modello compilato.
2. Fare clic su **Importa**.
3. Viene visualizzato lo stato dell'importazione. 
    - Se gli avvisi vengono visualizzati nello stato, è possibile correggerli oppure continuare senza risolverli.
    - Il miglioramento delle informazioni sul server, come suggerito negli avvisi, migliora l'accuratezza della valutazione.
    - Per visualizzare e correggere gli avvisi in caso vengano visualizzati, fare clic su **Scarica i dettagli degli avvisi in formato CSV**. Il file con estensione csv viene scaricato con gli avvisi annessi. È possibile esaminare gli avvisi e correggere i problemi in base alle esigenze. 
    Se gli errori vengono visualizzati nello stato (lo stato dell'importazione è **Non riuscito**), è necessario correggerli prima di poter continuare con l'importazione. A tale scopo, scaricare il file con estensione csv in cui sono stati aggiunti i dettagli dell'errore. Esaminare e risolvere gli errori in base alle esigenze. Quindi, caricare nuovamente il file modificato.
4. Quando lo stato dell'importazione è **Completato**, vengono importate le informazioni sul server.


> [!NOTE]
> Per aggiornare le informazioni sul server caricate in Azure Migrate, caricare nuovamente i dati per il server usando lo stesso **Nome del server**. Si noti che il campo **Nome del server** non può essere modificato dopo l'importazione del modello. L'eliminazione di server non è attualmente supportata.

## <a name="updating-server-information"></a>Aggiornamento delle informazioni sul server

È possibile aggiornare le informazioni su un server caricando nuovamente i dati per il server con lo stesso **Nome del server**. Non è possibile modificare il campo **Nome del server**. 

L'eliminazione di server è attualmente supportata.

### <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Dopo l'individuazione è possibile verificare che i server siano visualizzati nel portale di Azure.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.
3. Fare clic sulla scheda **Basati sull'importazione**.

## <a name="set-up-an-assessment"></a>Configurare una valutazione

È possibile creare due tipi di valutazioni con Azure Migrate: Valutazione server.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui valori dei dati sulle prestazioni specificati | **Dimensioni VM consigliate**: in base ai dati sull'utilizzo di CPU e memoria.<br/><br/> **Tipo di disco consigliato (disco gestito Standard o Premium)** : in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni VM consigliate**: Basate sulle dimensioni del server specificate<br/><br> **Tipo di disco consigliato**: in base all'impostazione del tipo di archiviazione selezionata per la valutazione.


### <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Rivedere le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
2. Nella scheda **Server**, nel riquadro **Azure Migrate: Valutazione server**, fare clic su **Valuta**.

    ![Valutare](./media/tutorial-assess-physical/assess.png)

2. In **Valuta server** specificare un nome per la valutazione.
3. In **Origine individuazione** selezionare **Computer aggiunti tramite importa a Azure Migrate**
3. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Proprietà valutazione](./media/tutorial-assess-physical/view-all.png)

3. In **Selezionare o creare un gruppo** selezionare **Crea nuovo** e specificare un nome per il gruppo. Un gruppo raccoglie una o più VM per la valutazione.
4. In **Aggiungere le macchine virtuali al gruppo** selezionare i server da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    ![Creare una valutazione](./media/tutorial-assess-physical/assessment-create.png)

6. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server** > **Valutazioni**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.



## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se i server sono idonei per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione dei server in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

### <a name="view-an-assessment"></a>Visualizzare una valutazione

1. In **Obiettivi della migrazione** >  **Server** fare clic su **Valutazioni** in **Azure Migrate: Valutazione server**.
2. In **Valutazioni** fare clic su una valutazione per aprirla.

    ![Riepilogo della valutazione](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se i server sono pronti per la migrazione ad Azure.
2. Verificare lo stato:
    - **Idonea per Azure**: Azure Migrate consiglia le dimensioni e le stime dei costi per le VM nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

2. Fare clic su uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità dei server ed eseguire il drill-down per visualizzare i dettagli dei server, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-details"></a>Esaminare i dettagli dei costi

Questa visualizzazione mostra il costo stimato di calcolo e archiviazione associato all'esecuzione delle macchine virtuali in Azure.

1. Esaminare i costi mensili di calcolo e archiviazione. I costi sono aggregati per tutti i server nel gruppo valutato.

    - Le stime dei costi sono basate sulle dimensioni consigliate per una VM e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione dei server locali come VM IaaS. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

2. È possibile esaminare le stime dei costi di archiviazione mensile. Questa visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione.
3. È possibile eseguire il drill-down per visualizzare i dettagli relativi a VM specifiche.

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate alle valutazioni dei server importati in Valutazione server di Azure Migrate con CSV.


## <a name="supported-operating-system-names"></a>Nomi dei sistemi operativi supportati

NOME | NOME
--- | ---
**A - H** | 
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux 
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD 
**I - R** | 
IBM OS/2 | MS-DOS |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11 
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | 
**S-T** | 
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** | 
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional
    

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Server importati in Azure Migrate: Valutazione server con CSV.
> * È stata creata ed esaminata una valutazione

A questo punto, [distribuire un'appliance](./migrate-appliance.md) per valutazioni più accurate e riunire i server in gruppi per una valutazione più approfondita tramite [Analisi delle dipendenze](./concepts-dependency-visualization.md).
