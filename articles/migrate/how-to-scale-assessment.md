---
title: Dimensionare correttamente l'individuazione e la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come valutare un elevato numero di computer locali con il servizio Azure Migrate.
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Individuare e valutare un ambiente VMware di grandi dimensioni

Questo articolo descrive come valutare un elevato numero di computer locali con [Azure Migrate](migrate-overview.md). Azure Migrate valuta i computer per verificare se sono adatti per la migrazione ad Azure e fornisce stime riguardo a dimensioni e costi per l'esecuzione del computer in Azure.

## <a name="prerequisites"></a>Prerequisiti

- **VMware**: è necessario avere almeno una macchina virtuale VMware in un cluster o un host ESXi che esegue la versione 5.0 o successiva. L'host o il cluster deve essere gestito da un server vCenter che esegue la versione 5.5 o 6.0.
- **Account vCenter**: è necessario avere un account di sola lettura con credenziali di amministratore per il server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.
- **Autorizzazioni**: nel server vCenter è necessario avere le autorizzazioni per creare una macchina virtuale importando un file con estensione ova.
- **Impostazioni delle statistiche**: prima di avviare la distribuzione, le statistiche del server vCenter devono essere impostate su 2 o su un livello superiore.

## <a name="plan-azure-migrate-projects"></a>Pianificare i progetti di Azure Migrate

Un progetto di Azure Migrate consente di valutare fino a 1500 computer. Con una sola individuazione in un progetto è possibile individuare fino a 1000 computer.

- Se i computer da individuare sono meno di 1000, è necessario creare un singolo progetto con una sola individuazione.
- Se il numero dei computer da individuare è compreso tra 1000 e 1500, è necessario creare un singolo progetto con due individuazioni.
- Se i computer da individuare sono oltre 1500, è necessario creare più progetti ed eseguire più individuazioni a seconda delle esigenze, ad esempio:
    - Se i computer sono 3000, è possibile configurare due progetti con due individuazioni o tre progetti con una sola individuazione.
    - Se i computer sono 5000, è possibile configurare quattro progetti, due con un'individuazione di 1500 computer e uno con un'individuazione di 500 computer. In alternativa,è possibile configurare cinque progetti, ognuno contenente una sola individuazione. 
- Quando si esegue un'individuazione in Azure Migrate, è possibile impostare l'ambito su una cartella, un data center o un cluster VMware.
- Per eseguire più individuazioni, verificare in vCenter che le macchine virtuali da individuare siano incluse in cartelle, data center o cluster che supportano il limite di 1000 computer.
- Ai fini della valutazione, è consigliabile tenere i computer con interdipendenze all'interno dello stesso progetto e della stessa valutazione. In vCenter verificare quindi che i computer dipendenti siano inclusi nello stesso data center, cartella o cluster per gli scopi della valutazione.


## <a name="create-a-project"></a>Creare un progetto

Creare un progetto di Azure Migrate in base alle necessità.

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Cercare **Azure Migrate** e selezionare il servizio **Azure Migrate (preview)** nei risultati della ricerca. Fare quindi clic su **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure per il progetto.
4. Creare un nuovo gruppo di risorse.
5. Specificare l'area in cui creare il progetto e quindi fare clic su **Crea**. In questa area vengono archiviati i metadati raccolti dalle macchine virtuali locali.

## <a name="set-up-the-collector-appliance"></a>Configurare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un file con estensione ova e si importa il file nel server vCenter locale per creare la macchina virtuale.

### <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Se sono presenti più progetti, è sufficiente scaricare l'appliance dell'agente di raccolta una sola volta nel server vCenter. Dopo aver scaricato e configurato l'appliance, eseguirla per ogni progetto e specificare la chiave e l'ID di progetto univoci.

1. Nel progetto di Azure Migrate fare clic su **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Individua macchine virtuali** fare clic su **Scarica** per scaricare il file con estensione ova.
3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

   
### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Verificare che il file con estensione ova sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Il valore hash generato deve corrispondere a queste impostazioni.

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>Creare la macchina virtuale dell'agente di raccolta

Importare il file scaricato nel server vCenter.

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).

    ![Distribuire il modello OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. In Deploy OVF Template Wizard (Distribuzione guidata del modello OVF) > **Source** (Origine) specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la macchina virtuale dell'agente di raccolta e l'oggetto dell'inventario in cui verrà ospitata la macchina virtuale.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale dell'agente di raccolta.
7. Nell'area relativa ai dati di archiviazione specificare la destinazione di archiviazione per la macchina virtuale dell'agente di raccolta.
8. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
9. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure. 
10. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).

## <a name="identify-the-key-and-id-for-each-project"></a>Identificare la chiave e l'ID di ogni progetto

Se si hanno più progetti, identificare l'ID e la chiave di ognuno. La chiave è necessaria quando si esegue l'agente di raccolta per individuare le macchine virtuali.

1. Nel progetto fare clic su **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. 
    ![ID progetto](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>Livello delle statistiche di vCenter per raccogliere i contatori delle prestazioni
Di seguito è riportato l'elenco dei contatori che vengono raccolti durante l'individuazione. Per impostazione predefinita, i contatori sono disponibili a vari livelli nel server vCenter. È consigliabile impostare il livello comune più alto (livello 3) per le statistiche in modo che tutti i contatori vengano raccolti correttamente. Se vCenter è impostato a un livello più basso, è possibile che solo alcuni contatori vengano raccolti completamente, mentre i restanti vengono impostati su 0. Di conseguenza, la valutazione potrebbe generare dati incompleti. La tabella seguente indica anche i risultati della valutazione che saranno compromessi se un determinato contatore non viene raccolto.

|Contatore                                  |Livello    |Livello per dispositivo  |Impatto sulla valutazione                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |ND                |Dimensioni e costi consigliati della macchina virtuale                    |
|mem.usage.average                        | 1       |ND                |Dimensioni e costi consigliati della macchina virtuale                    |
|virtualDisk.read.average                 | 2       |2                 |Dimensione disco, costi di archiviazione e dimensioni della macchina virtuale         |
|virtualDisk.write.average                | 2       |2                 |Dimensione disco, costi di archiviazione e dimensioni della macchina virtuale         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Dimensione disco, costi di archiviazione e dimensioni della macchina virtuale         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Dimensione disco, costi di archiviazione e dimensioni della macchina virtuale         |
|net.received.average                     | 2       |3                 |Dimensioni della macchina virtuale e costi di rete                        |
|net.transmitted.average                  | 2       |3                 |Dimensioni della macchina virtuale e costi di rete                        |

> [!WARNING]
> Se è appena stato impostato un livello più elevato per le statistiche, sarà necessario aspettare fino a un giorno per ottenere i contatori delle prestazioni. Di conseguenza, è consigliabile eseguire l'individuazione dopo un giorno.

## <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

Per ogni individuazione da eseguire, è necessario avviare l'agente di raccolta per individuare le macchine virtuali nell'ambito richiesto. Eseguire le individuazioni una di seguito all'altra. Le individuazioni simultanee non sono supportate e per ogni individuazione deve essere definito un ambito diverso.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3. Sul desktop fare clic sul collegamento **Run collector** (Esegui agente di raccolta).
4. In Agente di raccolta di Azure Migrate aprire **Set Up Prerequisites** (Configura prerequisiti).
    - Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.
    - Se la macchina virtuale accede a Internet tramite un proxy, fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy. Se il proxy richiede l'autenticazione, specificare le credenziali.
    - L'agente di raccolta verifica che il servizio profiler di Windows sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.
    - Scaricare e installare VMware PowerCLI.
. In **Discover Virtual Machines** (Individua macchine virtuali) eseguire queste operazioni:
    - Specificare il nome completo o l'indirizzo IP del server vCenter.
    - In **User name** (Nome utente) e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le macchine virtuali nel server vCenter.
    - In **Collection scope** (Ambito raccolta) selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1000 macchine virtuali. 
    - In **Tag category for grouping** (Categoria tag per raggruppamento) selezionare **None** (Nessuna).

        ![Seleziona ambito](./media/how-to-scale-assessment/select-scope.png)

1. In **Select Project** (Seleziona progetto) specificare l'ID e la chiave per il progetto. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto fare clic su **Individua macchine virtuali** e copiare i valori.  
In **Complete Discovery** (Completa individuazione) monitorare il processo di individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Il tempo di individuazione dipende dal numero di macchine virtuali da individuare. In genere, per completare l'individuazione di 100 macchine virtuali, è necessario aspettare circa un'ora dopo l'esecuzione dell'agente di raccolta. 

1. Nel progetto di Azure Migrate fare clic su **Gestisci** > **Macchine virtuali**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un gruppo](how-to-create-a-group.md) per la valutazione.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.