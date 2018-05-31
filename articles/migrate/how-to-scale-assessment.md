---
title: Individuazione e valutazione della scalabilità tramite Azure Migrate | Microsoft Docs
description: Questo articolo descrive come valutare un elevato numero di computer locali usando il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/18/2018
ms.author: raynew
ms.openlocfilehash: c8943aec1c81abb34b646180df48bcc55764ca24
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365332"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Individuare e valutare un ambiente VMware di grandi dimensioni

Questo articolo descrive come valutare un elevato numero di macchine virtuali (VM) locali con [Azure Migrate](migrate-overview.md). Azure Migrate valuta i computer per verificarne l'idoneità per la migrazione in Azure. Il servizio valuta il dimensionamento e offre stime dei costi per l'esecuzione dei computer in Azure.

## <a name="prerequisites"></a>prerequisiti

- **VMware**: le macchine virtuali di cui si intende eseguire la migrazione devono essere gestite dal server vCenter versione 5.5, 6.0 o 6.5. È inoltre necessario disporre di un unico host ESXi versione 5.0 o successiva per distribuire la macchina virtuale che funge da agente di raccolta.
- **Account vCenter**: occorre avere un account di sola lettura per accedere al server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.Azure Migrate usa questo account per individuare le macchine virtuali locali.
- **Autorizzazioni**: nel server vCenter è necessario disporre delle autorizzazioni per creare una macchina virtuale importando un file in formato OVA.
- **Impostazioni delle statistiche**: prima di iniziare la distribuzione, è consigliabile impostare le statistiche del server vCenter sul livello 3. Se si imposta un livello inferiore a 3, viene eseguita la valutazione, ma non vengono raccolti i dati sulle prestazioni per l'archiviazione e la rete. In questo caso, i consigli relativi alle dimensioni si baseranno sui dati delle prestazioni per la CPU e la memoria e sui dati di configurazione per le schede del disco e di rete.

## <a name="plan-azure-migrate-projects"></a>Pianificare i progetti di Azure Migrate

Pianificare le individuazioni e le valutazioni in base ai limiti seguenti:

| **Entità** | **Limite di computer** |
| ---------- | ----------------- |
| Project    | 1.500             |
| Individuazione  | 1.500             |
| Valutazione | 1.500             |

<!--
- If you have fewer than 400 machines to discover and assess, you need a single project and a single discovery. Depending on your requirements, you can either assess all the machines in a single assessment or split the machines into multiple assessments.
- If you have 400 to 1,000 machines to discover, you need a single project with a single discovery. But you will need multiple assessments to assess these machines, because a single assessment can hold up to 400 machines.
- If you have 1,001 to 1,500 machines, you need a single project with two discoveries in it.
- If you have more than 1,500 machines, you need to create multiple projects, and perform multiple discoveries, according to your requirements. For example:
    - If you have 3,000 machines, you can set up two projects with two discoveries, or three projects with a single discovery.
    - If you have 5,000 machines, you can set up four projects: three with a discovery of 1,500 machines, and one with a discovery of 500 machines. Alternatively, you can set up five projects with a single discovery in each one.
      -->

## <a name="plan-multiple-discoveries"></a>Pianificare più individuazioni

È possibile usare lo stesso agente di raccolta di Azure Migrate per eseguire più individuazioni in uno o più progetti. Tenere presente le considerazioni seguenti sulla pianificazione:

- Quando si esegue un'individuazione tramite l'agente di raccolta di Azure Migrate, è possibile impostare l'ambito di individuazione su una cartella, un data center, un cluster o un host del server vCenter.
- Per eseguire più individuazioni, verificare nel server vCenter che le macchine virtuali che si vuole individuare siano incluse in cartelle, data center, cluster o host che supportano il limite di 1.500 computer.
- Ai fini della valutazione, è consigliabile tenere i computer con interdipendenze all'interno dello stesso progetto e della stessa valutazione. Nel server vCenter verificare quindi che i computer dipendenti si trovino nello stesso data center, la stessa cartella o lo stesso cluster per la valutazione.


## <a name="create-a-project"></a>Creare un progetto

Creare un progetto di Azure Migrate in base alle necessità:

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Cercare **Azure Migrate** e selezionare il servizio **Azure Migrate (preview)** nei risultati della ricerca. Selezionare quindi **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure per il progetto.
4. Creare un nuovo gruppo di risorse.
5. Specificare il percorso in cui si vuole creare il progetto e quindi selezionare **Crea**. Si noti che è comunque possibile valutare le macchine virtuali in un percorso di destinazione diverso. Il percorso specificato per il progetto viene usato per archiviare i metadati raccolti da macchine virtuali locali.

## <a name="set-up-the-collector-appliance"></a>Configurare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un file con estensione ova e lo si importa nell'istanza del server vCenter locale.

### <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

In presenza di più progetti, occorre scaricare l'appliance dell'agente di raccolta una sola volta nel server vCenter. Dopo avere scaricato e configurato l'appliance, la si esegue per ogni progetto e si specificano la chiave e l'ID di progetto univoci.

1. Nel progetto di Azure Migrate selezionare **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Individua macchine virtuali** fare clic su **Scarica** per scaricare il file con estensione ova.
3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.


### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Prima di distribuire il file con estensione ova, verificarne la sicurezza:

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.

2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Verificare che il valore hash generato corrisponda alle impostazioni seguenti.

    Per OVA versione 1.0.9.8

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    Per OVA versione 1.0.9.7

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    Per OVA versione 1.0.9.5

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    Per OVA versione 1.0.9.2

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

## <a name="create-the-collector-vm"></a>Creare la macchina virtuale dell'agente di raccolta

Importare il file scaricato nel server vCenter:

1. Nella console di vSphere Client selezionare **File** > **Distribuire il modello OVF**.

    ![Distribuire il modello OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la macchina virtuale dell'agente di raccolta e l'oggetto dell'inventario in cui verrà ospitata la macchina virtuale.
4. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale dell'agente di raccolta.
5. Nell'area relativa ai dati di archiviazione specificare la destinazione di archiviazione per la macchina virtuale dell'agente di raccolta.
6. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
7. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure.
8. Rivedere e confermare le impostazioni e quindi selezionare **Fine**.

## <a name="identify-the-id-and-key-for-each-project"></a>Identificare la chiave e l'ID di ogni progetto

Se si hanno più progetti, assicurarsi di identificare l'ID e la chiave di ognuno. La chiave è necessaria quando si esegue l'agente di raccolta per individuare le macchine virtuali.

1. Nel progetto selezionare **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto.
    ![Copiare le credenziali del progetto](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Impostare il livello delle statistiche vCenter
Di seguito è riportato l'elenco dei contatori delle prestazioni che vengono raccolti durante l'individuazione. Per impostazione predefinita, i contatori sono disponibili a vari livelli nel server vCenter.

È consigliabile impostare il livello comune più alto (livello 3) per le statistiche in modo che tutti i contatori vengano raccolti correttamente. Se vCenter è impostato su un livello inferiore, è possibile che solo alcuni contatori vengano raccolti completamente, mentre i restanti vengono impostati su 0. La valutazione potrebbe di conseguenza generare dati incompleti.

La tabella seguente indica anche i risultati della valutazione che saranno compromessi se un determinato contatore non viene raccolto.

| Contatore                                 | Level | Livello per dispositivo | Impatto sulla valutazione                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| cpu.usage.average                       | 1     | ND               | Dimensione e costi consigliati della macchina virtuale         |
| mem.usage.average                       | 1     | ND               | Dimensione e costi consigliati della macchina virtuale         |
| virtualDisk.read.average                | 2     | 2                | Dimensione disco, costi di archiviazione e dimensione della macchina virtuale |
| virtualDisk.write.average               | 2     | 2                | Dimensione disco, costi di archiviazione e dimensione della macchina virtuale |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Dimensione disco, costi di archiviazione e dimensione della macchina virtuale |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Dimensione disco, costi di archiviazione e dimensione della macchina virtuale |
| net.received.average                    | 2     | 3                | Dimensione della macchina virtuale e costi della rete             |
| net.transmitted.average                 | 2     | 3                | Dimensione della macchina virtuale e costi della rete             |

> [!WARNING]
> Se è stato appena impostato un livello più alto per le statistiche, la generazione dei contatori delle prestazioni richiederà fino a un giorno. È quindi consigliabile eseguire l'individuazione dopo un giorno.

## <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

Per ogni individuazione da eseguire, è necessario eseguire l'agente di raccolta per individuare le macchine virtuali nell'ambito richiesto. Eseguire le individuazioni una di seguito all'altra. Le individuazioni simultanee non sono supportate e per ogni individuazione deve essere definito un ambito diverso.

1.  Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2.  Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3.  Sul desktop selezionare il collegamento **Esegui agente di raccolta**.
4.  Nell'agente di raccolta di Azure Migrate aprire **Set Up Prerequisites** (Configura prerequisiti) ed eseguire queste operazioni:

    a. Accettare le condizioni di licenza e leggere le informazioni di terze parti.

    L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.

    b. Se la macchina virtuale accede a Internet tramite un proxy, selezionare **Impostazioni proxy** e specificare l'indirizzo e la porta di ascolto del proxy. Se il proxy richiede l'autenticazione, specificare le credenziali.

    L'agente di raccolta verifica che il servizio dell'agente di raccolta sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.

    c. Scaricare e installare VMware PowerCLI.

5.  In **Specify vCenter Server details** (Specificare i dettagli del Server vCenter) eseguire queste operazioni:
    - Specificare il nome completo (FQDN) o l'indirizzo IP del server vCenter.
    - In **Nome utente** e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le macchine virtuali nel server vCenter.
    - In **Ambito raccolta** selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1.000 macchine virtuali.

6.  In **Il progetto di migrazione specifica** specificare l'ID e la chiave per il progetto. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto selezionare **Individua macchine virtuali** e copiare i valori.  
7.  In **Visualizzare lo stato di raccolta** monitorare il processo di individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Il tempo di individuazione dipende dal numero di macchine virtuali da individuare. L'individuazione di 100 macchine virtuali termina in genere circa un'ora dopo l'esecuzione dell'agente di raccolta.

1. Nel progetto di Azure Migrate selezionare **Gestisci** > **Macchine virtuali**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un gruppo](how-to-create-a-group.md) per la valutazione.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
