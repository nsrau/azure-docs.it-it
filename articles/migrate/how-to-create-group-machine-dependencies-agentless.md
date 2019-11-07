---
title: Raggruppare i computer in Azure Migrate usando la visualizzazione delle dipendenze senza agenti
description: Viene descritto come creare gruppi usando le dipendenze del computer in modo senza agente.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: e9f9e812d5463f0a503b100780f9b988e43f748d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720260"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Configurare la visualizzazione delle dipendenze senza agenti per la valutazione

> [!NOTE]
> Se questa funzionalità non è ancora visualizzata nel portale di Azure Migrate, bloccarsi. Verrà visualizzato nella settimana successiva.

Questo articolo descrive come configurare il mapping delle dipendenze senza agente in Azure Migrate: server assessment. Questa funzionalità è attualmente disponibile in anteprima per i computer VMware individuati con un appliance Azure Migrate. 

> [!IMPORTANT]
> La visualizzazione delle dipendenze senza agenti è attualmente in anteprima per le macchine virtuali VMware di Azure individuate tramite un appliance Azure Migrate.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>Informazioni sul mapping delle dipendenze 

Il mapping delle dipendenze consente di visualizzare le dipendenze tra i computer che si desidera valutare e migrare. In genere si usa il mapping delle dipendenze quando si desidera valutare i computer con livelli di confidenza più elevati.

- In Azure Migrate: server Assessment, i computer vengono raccolti in gruppi per la valutazione. I gruppi sono in genere costituiti da computer di cui si desidera eseguire la migrazione insieme e il mapping delle dipendenze consente di controllare le dipendenze dei computer, in modo che sia possibile raggruppare i computer in modo accurato.
- Utilizzando il mapping è possibile individuare i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme. È anche possibile determinare se un sistema in esecuzione sta ancora servendo gli utenti o se è un candidato per la rimozione delle autorizzazioni anziché per la migrazione.
- La visualizzazione delle dipendenze contribuisce a garantire che non venga lasciato nulla ed evitare interruzioni delle sorprese durante la migrazione.

## <a name="about-agentless-visualization"></a>Informazioni sulla visualizzazione senza agenti

La visualizzazione delle dipendenze senza agente non richiede l'installazione di agenti nei computer. L'operazione viene eseguita acquisendo i dati di connessione TCP da computer per i quali è abilitata.

- Dopo l'avvio dell'individuazione delle dipendenze, l'appliance raccoglie i dati dai computer a un intervallo di polling di cinque minuti.
- Vengono raccolti i dati seguenti:
    - Connessioni TCP
    - Nomi dei processi con connessioni attive
    - Nomi delle applicazioni installate che eseguono i processi precedenti
    - No. delle connessioni rilevate a ogni intervallo di polling

## <a name="current-limitations"></a>Limitazioni correnti

- La visualizzazione delle dipendenze senza agenti è attualmente disponibile solo per le macchine virtuali VMware.
- In questo momento non è possibile aggiungere o rimuovere un server da un gruppo, nella visualizzazione analisi delle dipendenze. 
- La mappa delle dipendenze per un gruppo di server non è attualmente disponibile.
- Attualmente i dati sulle dipendenze non possono essere scaricati in formato tabulare.

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
- L'analisi delle dipendenze senza agenti è attualmente disponibile solo per i computer VMware.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server assessment.
- Assicurarsi di aver individuato i computer VMware in Azure Migrate; a tale scopo, è possibile configurare un appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment. [Altre informazioni](migrate-appliance.md).
- Verificare che le macchine virtuali VMware siano supportate per la visualizzazione delle dipendenze senza agenti, come riepilogato nella tabella seguente.


### <a name="supported-operating-systems"></a>Sistemi operativi supportati
 
Di seguito sono riportati i sistemi operativi supportati per la visualizzazione delle dipendenze senza agenti.

**Tipo** | **Sistemi operativi supportati**
--- | --- 
**Windows** | Microsoft Windows Server 2016 <br/> Microsoft Windows Server 2012 R2 <br/> Microsoft Windows Server 2012 <br/> Microsoft Windows Server 2008 R2 (64 bit) 
**Linux** | Red Hat Enterprise Linux 7, 6, 5 <br/> Ubuntu Linux 14,04, 16,04 <br/> Debian 7, 8 <br/> Oracle Linux 6, 7 <br/> CentOS 5, 6, 7  


## <a name="create-a-user-account-for-discovery"></a>Creazione di un account utente per l'individuazione

Configurare un account utente che disponga delle autorizzazioni necessarie in modo che server assessment possa accedere alla macchina virtuale per l'individuazione. È possibile specificare un account utente.

- **Autorizzazione necessaria per le macchine virtuali Windows**: l'account utente richiede l'accesso "Guest".
- **Autorizzazione necessaria per le macchine virtuali Linux**: il privilegio radice è obbligatorio per l'account. In alternativa, l'account utente richiede queste due funzionalità nei file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Aggiungere l'account utente al dispositivo

È necessario aggiungere l'account utente al dispositivo.

Aggiungere l'account come segue:

1. Aprire l'app gestione Appliance. Passare al pannello **specificare i dettagli di vCenter** .
2. Nella sezione **individuare l'applicazione e le dipendenze dalle macchine virtuali** fare clic su **Aggiungi credenziali** .
3. Scegliere il **sistema operativo**. 
4. Consente di specificare un nome descrittivo per l'account.
5. Specificare il **nome utente** e la **password**
6. Fare clic su **Save**.
7. Fare clic su **Salva e avvia individuazione**.

    ![Aggiungi account utente VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Avvia individuazione dipendenza

Scegliere i computer in cui si desidera abilitare l'individuazione delle dipendenze.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Avvia individuazione dipendenza**.
3. Nella pagina **Avvia individuazione dipendenza** , scegliere l'appliance che sta individuando i computer pertinenti.
4. Dall'elenco computer selezionare le macchine virtuali.
5. Fare clic su **Avvia individuazione dipendenza**.

    ![Avvia individuazione dipendenza](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Sarà possibile visualizzare le dipendenze 6 ore dopo l'avvio dell'individuazione delle dipendenze.

## <a name="visualize-dependencies"></a>Visualizza le dipendenze

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Cercare il computer per il quale si desidera visualizzare la mappa delle dipendenze.
3. Fare clic su **Visualizza dipendenze** nella colonna **dipendenze** .
4. Modificare il periodo di tempo per cui si desidera visualizzare la mappa utilizzando l'elenco a discesa **durata tempo** .
5. Espandere il gruppo **client** per elencare i computer che hanno una dipendenza nel computer selezionato. 
6. Espandere il gruppo **porta** per elencare i computer che hanno una dipendenza dal computer selezionato. 
7. Per passare alla visualizzazione mappa di uno dei computer dipendenti, fare clic sul nome del computer e quindi fare clic su **Carica mappa server** .

    ![Espandere il gruppo di porte del server e caricare la mappa del server](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Espandi gruppo di client ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Espandere il computer selezionato per visualizzare i dettagli a livello di processo per ogni dipendenza.

    ![Espandere il server per visualizzare i processi](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Le informazioni sul processo per una dipendenza non sono sempre disponibili. Se non è disponibile, la dipendenza viene rappresentata con il processo contrassegnato come "processo sconosciuto".

## <a name="stop-dependency-discovery"></a>Arresta individuazione dipendenza

Scegliere i computer in cui si desidera arrestare l'individuazione delle dipendenze.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Arresta individuazione dipendenza**.
3. Nella pagina **Arresta individuazione dipendenze** scegliere il **dispositivo** che sta individuando le macchine virtuali in cui si desidera arrestare l'individuazione delle dipendenze.
4. Dall'elenco computer selezionare le macchine virtuali.
5. Fare clic su **Arresta individuazione dipendenza**


## <a name="next-steps"></a>Passaggi successivi

[Raggruppare i computer](how-to-create-a-group.md)
