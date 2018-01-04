---
title: "Scalabilità di individuazione e valutazione utilizzando la migrazione di Azure | Documenti Microsoft"
description: Viene descritto come valutare un numero elevato di computer locali tramite il servizio di eseguire la migrazione di Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: 9b457252fdb7a1ad62b7e6038b341451df2e1590
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Individuare e valutare un ambiente VMware di grandi dimensioni

In questo articolo viene descritto come valutare un numero elevato di macchine virtuali locali (VM) utilizzando [eseguire la migrazione di Azure](migrate-overview.md). Migrazione Azure valuta macchine per verificare se stanno adatti per la migrazione a Azure. Il servizio fornisce stime di ridimensionamento e costi per le macchine in esecuzione in Azure.

## <a name="prerequisites"></a>Prerequisiti

- **VMware**: di macchine virtuali che si intende eseguire la migrazione devono essere gestite da vCenter Server versione 6.5, 6.0 o 5.5. Inoltre, occorre una versione in esecuzione dell'host ESXi 5.0 o versione successiva per distribuire l'agente di raccolta macchina virtuale.
- **account vCenter**: È necessario un account di sola lettura per accedere ai Server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.Azure Migrate usa questo account per individuare le macchine virtuali locali.
- **Autorizzazioni**: In vCenter Server, sono necessarie autorizzazioni per creare una macchina virtuale importando un file in formato ovuli.
- **Le impostazioni delle statistiche**: le impostazioni di statistiche per Server vCenter devono essere impostate a livello 3 prima di iniziare la distribuzione. Se il livello è inferiore a 3, la valutazione funzionerà, ma non verranno raccolti dati sulle prestazioni per l'archiviazione e rete. Le dimensioni consigliate in questo caso si baserà su dati sulle prestazioni per CPU e memoria e i dati di configurazione per le schede di rete e disco.

## <a name="plan-azure-migrate-projects"></a>Pianificare i progetti di Azure Migrate

Pianificare le individuazioni e una valutazione in base ai limiti seguenti:

| **Entità** | **Limite del computer** |
| ---------- | ----------------- |
| Project    | 1,500              | 
| Individuazione  | 1.000              |
| Valutazione | 400               |

- Se si dispone di meno di 400 macchine per individuare e valutare, è necessario un singolo progetto e una singola individuazione. A seconda dei requisiti, è possibile valutare tutti i computer in una singola valutazione o suddividere le macchine in diverse valutazioni. 
- Se si hanno macchine 400 a 1.000 per individuare, è necessario un singolo progetto con un singolo individuazione. Ma sarà necessario più di valutazione per valutare questi computer, perché una singola valutazione può contenere fino a 400 macchine.
- Se si hanno macchine 1,001 di 1.500, è necessario un singolo progetto con due individuazioni in essa contenuti.
- Se si hanno più di 1.500 macchine, è necessario creare più progetti ed eseguire più individuazioni, in base alle esigenze. Ad esempio: 
    - Se si hanno 3.000 macchine, è possibile impostare due progetti con due individuazioni o tre progetti con un singolo individuazione.
    - Se si hanno 5.000 macchine, è possibile impostare quattro progetti: due con un'individuazione di 1.500 macchine e uno con un'individuazione di 500 computer. In alternativa, è possibile impostare i cinque progetti con un singolo individuazione in ognuno di essi. 

## <a name="plan-multiple-discoveries"></a>Pianificare più individuazioni

È possibile utilizzare l'agente di raccolta stesso eseguire la migrazione di Azure per eseguire individuazioni più di uno o più progetti. Tenere presente le considerazioni sulla pianificazione:
 
- Quando si esegue un'individuazione tramite l'agente di raccolta eseguire la migrazione di Azure, è possibile impostare l'ambito di individuazione di una cartella del Server vCenter, Data Center, cluster o host.
- Per eseguire l'individuazione di più, verificare in vCenter che si trovano in cartelle, i Data Center, cluster o host che supportano il limite di 1000 macchine le macchine virtuali che si desidera individuare.
- È consigliabile per scopi di valutazione, mantenere le macchine con interdipendenze all'interno dello stesso progetto e valutazione. In vCenter Server, assicurarsi che le macchine dipendenti siano nella stessa cartella, datacenter o del cluster per la valutazione.


## <a name="create-a-project"></a>Creare un progetto

Creare un progetto Azure di eseguire la migrazione in base alle esigenze:

1. Nel portale di Azure, selezionare **creare una risorsa**.
2. Cercare **eseguire la migrazione di Azure**e selezionare il servizio **eseguire la migrazione di Azure (anteprima)** nei risultati della ricerca. Selezionare quindi **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure per il progetto.
4. Creare un nuovo gruppo di risorse.
5. Specificare il percorso in cui si desidera creare il progetto e quindi selezionare **crea**. Si noti che è comunque possibile valutare le macchine virtuali per un percorso di destinazione diverso. Il percorso specificato per il progetto viene utilizzato per archiviare i metadati raccolti da macchine virtuali locali.

## <a name="set-up-the-collector-appliance"></a>Configurare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale consente di individuare le macchine virtuali VMware in locale e invia i metadati relativi al servizio di eseguire la migrazione di Azure. Per impostare il dispositivo di agente di raccolta, si scaricare un file ovuli e importarlo per l'istanza locale del Server vCenter.

### <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Se si dispone di più progetti, è necessario scaricare lo strumento agente di raccolta dati una sola volta in vCenter Server. Dopo aver scaricato e configurare il dispositivo, si esegue per ogni progetto e si specifica l'ID univoco del progetto e la chiave.

1. Nel progetto, eseguire la migrazione di Azure selezionare **Introduzione** > **Discover & valutazione** > **individuare macchine**.
2. In **individuare macchine**selezionare **scaricare**, scaricare il file ovuli.
3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

   
### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Verificare che il file ovuli sia sicuro prima di distribuire:

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Verificare che il valore hash generato corrisponda le impostazioni seguenti.
 
    Per la versione di ovuli 1.0.8.38:
    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    Per la versione di ovuli 1.0.8.40:
    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad



## <a name="create-the-collector-vm"></a>Creare la macchina virtuale dell'agente di raccolta

Importare il file scaricato in vCenter Server:

1. Nella console del Client di vSphere, selezionare **File** > **distribuire OVF modello**.

    ![Distribuire il modello OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Nella distribuzione guidata modello OVF > **origine**, specificare il percorso del file ovuli.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la macchina virtuale dell'agente di raccolta e l'oggetto dell'inventario in cui verrà ospitata la macchina virtuale.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale dell'agente di raccolta.
7. Nell'area relativa ai dati di archiviazione specificare la destinazione di archiviazione per la macchina virtuale dell'agente di raccolta.
8. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
9. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete necessita della connettività internet per inviare i metadati in Azure. 
10. Rivedere e confermare le impostazioni e quindi selezionare **fine**.

## <a name="identify-the-id-and-key-for-each-project"></a>Identificare l'ID e la chiave per ogni progetto

Se si dispone di più progetti, assicurarsi di identificare l'ID e la chiave per ciascuna di esse. La chiave è necessaria quando si esegue l'agente di raccolta per individuare le macchine virtuali.

1. Nel progetto, selezionare **Introduzione** > **Discover & valutazione** > **individuare macchine**.
2. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. 
    ![Copiare le credenziali di progetto](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Impostare il livello di statistiche vCenter
Ecco l'elenco di contatori delle prestazioni raccolti durante il processo di individuazione. I contatori sono per impostazione predefinita disponibile a vari livelli in vCenter Server. 

È consigliabile impostare il livello più alto comune (3) per il livello di statistiche in modo che tutti i contatori vengono raccolti in modo corretto. Se si dispone di vCenter impostare un livello inferiore, solo alcuni contatori potrebbero essere raccolte completamente, con gli altri impostati su 0. La valutazione potrebbe quindi Mostra i dati incompleti. 

Nella tabella seguente sono elencati anche i risultati di valutazione che saranno interessati se un particolare contatore non vengono raccolti.

|Contatore                                  |Level    |Livello per ogni dispositivo  |Impatto sulla valutazione                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |ND                |Dimensioni delle macchine Virtuali e i costi consigliato                    |
|mem.usage.average                        | 1       |ND                |Dimensioni delle macchine Virtuali e i costi consigliato                    |
|virtualDisk.read.average                 | 2       |2                 |Dimensioni del disco, il costo di archiviazione e dimensioni delle macchine Virtuali         |
|virtualDisk.write.average                | 2       |2                 |Dimensioni del disco, il costo di archiviazione e dimensioni delle macchine Virtuali         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Dimensioni del disco, il costo di archiviazione e dimensioni delle macchine Virtuali         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Dimensioni del disco, il costo di archiviazione e dimensioni delle macchine Virtuali         |
|net.received.average                     | 2       |3                 |Costo di dimensioni e la rete VM                        |
|net.transmitted.average                  | 2       |3                 |Costo di dimensioni e la rete VM                        |

> [!WARNING]
> Se è appena stato impostato un livello più elevato di statistiche, richiede sarà al giorno per generare i contatori delle prestazioni. In tal caso, è consigliabile eseguire il processo di individuazione dopo un giorno.

## <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

Per ogni individuazione che si desidera eseguire, si esegue l'agente di raccolta per individuare le macchine virtuali nell'ambito richiesto. Eseguire le individuazioni una di seguito all'altra. Le individuazioni simultanee non sono supportate e per ogni individuazione deve essere definito un ambito diverso.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3. Sul desktop, selezionare il **eseguire collector** scelta rapida.
4. Nell'agente di raccolta eseguire la migrazione di Azure, aprire **impostare prerequisiti** e quindi:

   a. Accettare le condizioni di licenza e leggere le informazioni di terze parti.

   L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.
   
   b. Se la macchina virtuale accede a internet tramite un proxy, selezionare **le impostazioni del Proxy**e specificare l'indirizzo del proxy e la porta di ascolto. Se il proxy richiede l'autenticazione, specificare le credenziali.

   L'agente di raccolta verifica che il servizio agente di raccolta dati sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.

   c. Scaricare e installare VMware PowerCLI.

5. In **specificare i dettagli del Server vCenter**, eseguire le operazioni seguenti:
    - Specificare il nome FQDN o indirizzo IP del Server vCenter.
    - In **nome utente** e **Password**, specificare le credenziali dell'account di sola lettura che l'agente di raccolta verrà utilizzato per individuare le macchine virtuali in vCenter Server.
    - In **selezionare ambito**, selezionare un ambito per l'individuazione della macchina virtuale. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, Ma non deve contenere più di 1.000 macchine virtuali. 
    - In **categoria di tag di vCenter per il raggruppamento**selezionare **Nessuno**.

    ![Seleziona ambito](./media/how-to-scale-assessment/select-scope.png)

6. In **il progetto di migrazione specifica**, specificare l'ID e la chiave per il progetto. Se non vengono copiati, aprire il portale di Azure dall'agente di raccolta di macchine Virtuali. Il progetto **Panoramica** selezionare **individuare macchine** e copiare i valori.  
7. In **visualizzare lo stato di raccolta**, monitorare il processo di individuazione e verificare che i metadati raccolti da macchine virtuali si trovi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Il tempo di individuazione dipende dal numero di macchine virtuali da individuare. In genere, per macchine virtuali di 100, l'individuazione completa circa un'ora dopo che l'agente di raccolta al termine dell'esecuzione. 

1. Nel progetto di pianificazione della migrazione, selezionare **Gestisci** > **macchine**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un gruppo](how-to-create-a-group.md) per la valutazione.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
