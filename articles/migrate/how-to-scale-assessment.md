---
title: "Individuazione e valutazione della scalabilità tramite Azure Migrate | Microsoft Docs"
description: Questo articolo descrive come valutare un elevato numero di computer locali usando il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: 67661e03e65cde3ec2f1aafd5ef755899cf0c77b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Individuare e valutare un ambiente VMware di grandi dimensioni

Questo articolo descrive come valutare un elevato numero di macchine virtuali (VM) locali con [Azure Migrate](migrate-overview.md). Azure Migrate valuta i computer per verificarne l'idoneità per la migrazione in Azure. Il servizio valuta il dimensionamento e offre stime dei costi per l'esecuzione dei computer in Azure.

## <a name="prerequisites"></a>Prerequisiti

- **VMware**: le macchine virtuali di cui si intende eseguire la migrazione devono essere gestite dal server vCenter versione 5.5, 6.0 o 6.5. È inoltre necessario disporre di un unico host ESXi versione 5.0 o successiva per distribuire la macchina virtuale che funge da agente di raccolta.
- **Account vCenter**: occorre avere un account di sola lettura per accedere al server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.Azure Migrate usa questo account per individuare le macchine virtuali locali.
- **Autorizzazioni**: nel server vCenter è necessario disporre delle autorizzazioni per creare una macchina virtuale importando un file in formato OVA.
- **Impostazioni delle statistiche**: prima di iniziare la distribuzione, è consigliabile impostare le statistiche del server vCenter sul livello 3. Se si imposta un livello inferiore a 3, viene eseguita la valutazione, ma non vengono raccolti i dati sulle prestazioni per l'archiviazione e la rete. In questo caso, i consigli relativi alle dimensioni si baseranno sui dati delle prestazioni per la CPU e la memoria e sui dati di configurazione per le schede del disco e di rete.

## <a name="plan-azure-migrate-projects"></a>Pianificare i progetti di Azure Migrate

Pianificare le individuazioni e le valutazioni in base ai limiti seguenti:

| **Entità** | **Limite di computer** |
| ---------- | ----------------- |
| Project    | 1.500              | 
| Individuazione  | 1.000              |
| Valutazione | 400               |

- Se i computer da individuare e valutare sono meno di 400, occorre creare un singolo progetto e una sola individuazione. A seconda delle esigenze, è possibile ottenere un'unica valutazione per tutti i computer o suddividerli in più valutazioni. 
- Se i computer da individuare sono un numero compreso tra 400 e 1.000, occorre un singolo progetto e una singola individuazione. Per valutare questi computer, saranno tuttavia necessarie più valutazioni, poiché una singola valutazione può contenere un massimo di 400 computer.
- Se i computer da individuare sono un numero compreso tra 1.001 e 1.500, occorre un singolo progetto che contenga due individuazioni.
- Se i computer da individuare sono oltre 1.500, è necessario creare più progetti ed eseguire più individuazioni a seconda delle esigenze. Ad esempio: 
    - Se si dispone di 3.000 computer, è possibile configurare due progetti con due individuazioni o tre progetti con una singola individuazione.
    - Se si dispone di 5.000 computer, è possibile configurare quattro progetti: tre con un'individuazione di 1.500 computer e uno con un'individuazione di 500 computer. In alternativa, è possibile configurare cinque progetti con una singola individuazione in ciascuno. 

## <a name="plan-multiple-discoveries"></a>Pianificare più individuazioni

È possibile usare lo stesso agente di raccolta di Azure Migrate per eseguire più individuazioni in uno o più progetti. Tenere presente le considerazioni seguenti sulla pianificazione:
 
- Quando si esegue un'individuazione tramite l'agente di raccolta di Azure Migrate, è possibile impostare l'ambito di individuazione su una cartella, un data center, un cluster o un host del server vCenter.
- Per eseguire più individuazioni, verificare nel server vCenter che le macchine virtuali che si vuole individuare siano incluse in cartelle, data center, cluster o host che supportano il limite di 1.000 computer.
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
 
    Per OVA versione 1.0.8.49:

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 8779eea842a1ac465942295c988ac0c7
    SHA1 | c136c52a0f785e1fd98865e16479dd103704887d
    SHA256 | 5143b1144836f01dd4eaf84ff94bc1d2c53f51ad04b1ca43ade0d14a527ac3f9

    Per OVA versione 1.0.8.40:

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Creare la macchina virtuale dell'agente di raccolta

Importare il file scaricato nel server vCenter:

1. Nella console di vSphere Client selezionare **File** > **Distribuire il modello OVF**.

    ![Distribuire il modello OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la macchina virtuale dell'agente di raccolta e l'oggetto dell'inventario in cui verrà ospitata la macchina virtuale.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale dell'agente di raccolta.
7. Nell'area relativa ai dati di archiviazione specificare la destinazione di archiviazione per la macchina virtuale dell'agente di raccolta.
8. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
9. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure. 
10. Rivedere e confermare le impostazioni e quindi selezionare **Fine**.

## <a name="identify-the-id-and-key-for-each-project"></a>Identificare la chiave e l'ID di ogni progetto

Se si hanno più progetti, assicurarsi di identificare l'ID e la chiave di ognuno. La chiave è necessaria quando si esegue l'agente di raccolta per individuare le macchine virtuali.

1. Nel progetto selezionare **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. 
    ![Copiare le credenziali del progetto](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Impostare il livello delle statistiche vCenter
Di seguito è riportato l'elenco dei contatori delle prestazioni che vengono raccolti durante l'individuazione. Per impostazione predefinita, i contatori sono disponibili a vari livelli nel server vCenter. 

È consigliabile impostare il livello comune più alto (livello 3) per le statistiche in modo che tutti i contatori vengano raccolti correttamente. Se vCenter è impostato su un livello inferiore, è possibile che solo alcuni contatori vengano raccolti completamente, mentre i restanti vengono impostati su 0. La valutazione potrebbe di conseguenza generare dati incompleti. 

La tabella seguente indica anche i risultati della valutazione che saranno compromessi se un determinato contatore non viene raccolto.

|Contatore                                  |Level    |Livello per dispositivo  |Impatto sulla valutazione                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |ND                |Dimensione e costi consigliati della macchina virtuale                    |
|mem.usage.average                        | 1       |ND                |Dimensione e costi consigliati della macchina virtuale                    |
|virtualDisk.read.average                 | 2       |2                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|virtualDisk.write.average                | 2       |2                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|net.received.average                     | 2       |3                 |Dimensione della macchina virtuale e costi della rete                        |
|net.transmitted.average                  | 2       |3                 |Dimensione della macchina virtuale e costi della rete                        |

> [!WARNING]
> Se è stato appena impostato un livello più alto per le statistiche, la generazione dei contatori delle prestazioni richiederà fino a un giorno. È quindi consigliabile eseguire l'individuazione dopo un giorno.

## <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

Per ogni individuazione da eseguire, è necessario eseguire l'agente di raccolta per individuare le macchine virtuali nell'ambito richiesto. Eseguire le individuazioni una di seguito all'altra. Le individuazioni simultanee non sono supportate e per ogni individuazione deve essere definito un ambito diverso.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3. Sul desktop selezionare il collegamento **Esegui agente di raccolta**.
4. Nell'agente di raccolta di Azure Migrate aprire **Set Up Prerequisites** (Configura prerequisiti) ed eseguire queste operazioni:

   a. Accettare le condizioni di licenza e leggere le informazioni di terze parti.

   L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.
   
   b. Se la macchina virtuale accede a Internet tramite un proxy, selezionare **Impostazioni proxy** e specificare l'indirizzo e la porta di ascolto del proxy. Se il proxy richiede l'autenticazione, specificare le credenziali.

   L'agente di raccolta verifica che il servizio dell'agente di raccolta sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.

   c. Scaricare e installare VMware PowerCLI.

5. In **Specify vCenter Server details** (Specificare i dettagli del Server vCenter) eseguire queste operazioni:
    - Specificare il nome completo (FQDN) o l'indirizzo IP del server vCenter.
    - In **Nome utente** e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le macchine virtuali nel server vCenter.
    - In **Ambito raccolta** selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1.000 macchine virtuali. 

6. In **Il progetto di migrazione specifica** specificare l'ID e la chiave per il progetto. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto selezionare **Individua macchine virtuali** e copiare i valori.  
7. In **Visualizzare lo stato di raccolta** monitorare il processo di individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Il tempo di individuazione dipende dal numero di macchine virtuali da individuare. L'individuazione di 100 macchine virtuali termina in genere circa un'ora dopo l'esecuzione dell'agente di raccolta. 

1. Nel progetto di Azure Migrate selezionare **Gestisci** > **Macchine virtuali**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un gruppo](how-to-create-a-group.md) per la valutazione.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
