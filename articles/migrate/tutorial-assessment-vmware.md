---
title: Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure usando il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 12/20/2017
ms.author: raynew
ms.openlocfilehash: e2806486ffb76fa7c210c3d0ef0b8bb3f86b7cd4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure

Il servizio [Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Azure Migrate.
> * Configurare una macchina virtuale locale dell'agente di raccolta per individuare le macchine virtuali VMware locali per la valutazione.
> * Raggruppare le macchine virtuali e creare una valutazione.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

- **VMware**: le macchine virtuali di cui si intende eseguire la migrazione devono essere gestite dal server vCenter in esecuzione nella versione 5.5, 6.0 o 6.5. Inoltre, è necessario che sia in esecuzione l'host ESXi versione 5.0 o versione successiva per distribuire la macchina virtuale dell'agente di raccolta. 
 
> [!NOTE]
> Il supporto per Hyper-V è in programma e verrà abilitato a breve. 

- **Account server vCenter**: è necessario un account di sola lettura per accedere al server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.Azure Migrate usa questo account per individuare le macchine virtuali locali.
- **Autorizzazioni**: nel server vCenter è necessario avere le autorizzazioni per creare una macchina virtuale importando un file con estensione ova. 
- **Impostazioni delle statistiche**: prima di avviare la distribuzione, le statistiche del server vCenter devono essere impostate sul livello 3. Con un livello inferiore a 3, viene eseguita la valutazione, ma non vengono raccolti i dati sulle prestazioni per l'archiviazione e la rete. In questo caso le dimensioni consigliate verranno stabilite in base ai dati sulle prestazioni per i dati di CPU, memoria e configurazione per le schede disco e di rete. 

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [Portale di Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Creare un progetto

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Cercare **eseguire la migrazione di Azure**e selezionare il servizio **eseguire la migrazione di Azure (anteprima)** nei risultati della ricerca. Fare quindi clic su **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure per il progetto.
4. Creare un nuovo gruppo di risorse.
5. Specificare il percorso in cui creare il progetto, quindi fare clic su **crea**. Per questa anteprima è possibile creare un progetto di Azure Migrate solo nell'area Stati Uniti centro-occidentali, Tuttavia, è comunque possibile pianificare la migrazione per qualsiasi località di Azure di destinazione. Il percorso specificato per il progetto viene utilizzato solo per archiviare i metadati raccolti da macchine virtuali locali. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un file con estensione ova e si importa il file nel server vCenter locale per creare la macchina virtuale.

1. Nel progetto di Azure Migrate fare clic su **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Individua macchine virtuali** fare clic su **Scarica** per scaricare il file con estensione ova.
3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

    ![Scaricare il file con estensione ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Verificare che il file con estensione ova sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Il valore hash generato deve corrispondere a queste impostazioni.
    
    Per OVA versione 1.0.8.38
    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    Per OVA versione 1.0.8.40
    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Creare la macchina virtuale dell'agente di raccolta

Importare il file scaricato nel server vCenter.

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).

    ![Distribuire il modello OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. In Deploy OVF Template Wizard (Distribuzione guidata del modello OVF) > **Source** (Origine) specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la macchina virtuale dell'agente di raccolta e l'oggetto dell'inventario in cui verrà ospitata la macchina virtuale.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale dell'agente di raccolta.
7. Nell'area relativa ai dati di archiviazione specificare la destinazione di archiviazione per la macchina virtuale dell'agente di raccolta.
8. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
9. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure. 
10. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).

## <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3. Sul desktop fare clic sul collegamento **Run collector** (Esegui agente di raccolta).
4. Nel Collector di eseguire la migrazione di Azure, aprire **impostare prerequisiti**.
    - Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.
    - Se la macchina virtuale accede a Internet tramite un proxy, fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy. Se il proxy richiede l'autenticazione, specificare le credenziali.

    > [!NOTE]
    > L'indirizzo proxy deve essere immesso nel formato http://ProxyIPAddress o http://ProxyFQDN. È supportato solo il proxy HTTP.

    - L'agente di raccolta verifica che il collectorservice sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.
    - Scaricare e installare VMware PowerCLI.

5. In **specificare i dettagli del Server vCenter**, eseguire le operazioni seguenti:
    - Specificare il nome completo o l'indirizzo IP del server vCenter.
    - In **User name** (Nome utente) e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le macchine virtuali nel server vCenter.
    - In **Collection scope** (Ambito raccolta) selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1000 macchine virtuali. 
    - In **Tag category for grouping** (Categoria tag per raggruppamento) selezionare **None** (Nessuna).
6. In **il progetto di migrazione specifica**, specificare l'ID di progetto Azure di eseguire la migrazione e della chiave copiato dal portale. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto fare clic su **Individua macchine virtuali** e copiare i valori.  
7. In **visualizzare lo stato di raccolta**, il monitoraggio di individuazione e verificare che i metadati raccolti da macchine virtuali si trovi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.

> [!NOTE]
> Come lingua del sistema operativo e lingua dell'interfaccia dell'agente di raccolta è supportato solo l'inglese (Stati Uniti). Sarà presto disponibile il supporto per altre lingue.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Il tempo di individuazione dipende dal numero di macchine virtuali da individuare. In genere, per completare l'individuazione di 100 macchine virtuali, è necessario aspettare circa un'ora dopo l'esecuzione dell'agente di raccolta. 

1. Nel progetto di Azure Migrate fare clic su **Gestisci** > **Macchine virtuali**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="create-and-view-an-assessment"></a>Creare e visualizzare una valutazione

Dopo aver individuato le macchine virtuali, è possibile raggrupparle e creare una valutazione. 

1. Nella pagina **Panoramica** del progetto fare clic su **+Crea valutazione**.
2. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.
3. Creare il gruppo e specificarne il nome.
4. Selezionare le macchine virtuali da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo e la valutazione.
6. Dopo aver creato la valutazione, visualizzarla in **Panoramica** > **Dashboard**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.

### <a name="sample-assessment"></a>Esempio di valutazione

La figura seguente illustra un esempio di report di valutazione. Sono incluse informazioni sulla compatibilità delle macchine virtuali con Azure e sui costi mensili stimati. 

![Report di valutazione](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Idoneità per Azure

Questa visualizzazione mostra lo stato di idoneità di ogni macchina virtuale.

- Per le macchine virtuali idonee, Azure Migrate offre un consiglio riguardo alle dimensioni delle macchine in Azure.
- Per le macchine virtuali non idonee, Azure Migrate ne spiega il motivo e indica i passaggi correttivi da seguire.
- Azure Migrate visualizza suggerimenti sugli strumenti che è possibile usare per la migrazione. Se la macchina virtuale è adatta alla migrazione in modalità lift-and-shift, viene consigliato di usare il servizio Azure Site Recovery. Se si tratta di una macchina virtuale di database, viene consigliato di usare Servizio Migrazione del database di Azure.

  ![Idoneità per la valutazione](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Stima costo mensile

In questa visualizzazione viene mostrato il costo di calcolo e archiviazione totale dell'esecuzione delle macchine virtuali in Azure con i dettagli per ogni computer. Le stime dei costi vengono calcolate usando le impostazioni delle proprietà di valutazione e le dimensioni consigliate in base alle prestazioni per una macchina virtuale e i relativi dischi. 

> [!NOTE]
> La stima dei costi fornita da Azure Migrate si riferisce all'esecuzione delle macchine virtuali locali come macchine virtuali dell'infrastruttura distribuita come servizio (IaaS) di Azure. Migrazione di Azure non considera qualsiasi piattaforma come servizio (PaaS) o Software come i costi di un servizio (SaaS). 

I costi mensili stimati per il calcolo e l'archiviazione vengono aggregati per tutte le macchine virtuali del gruppo. 

![Valutazione dei costi delle macchine virtuali](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

È possibile eseguire il drill-down per visualizzare i dettagli relativi a una macchina specifica.

![Valutazione dei costi delle macchine virtuali](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su](how-to-scale-assessment.md) come individuare e valutare un ambiente VMware di grandi dimensioni.
- Informazioni su come creare gruppi di valutazione di confidenza elevata utilizzando [mapping dipendenza macchina](how-to-create-group-machine-dependencies.md)
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
