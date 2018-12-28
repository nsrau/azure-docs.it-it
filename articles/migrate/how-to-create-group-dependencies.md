---
title: Ridefinire un gruppo di valutazione con il mapping delle dipendenze del gruppo in Azure Migrate | Microsoft Docs
description: Descrive come ridefinire una valutazione usando il mapping delle dipendenze del gruppo nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 9f01e94eb23083ab25dd2cbd41e8bad1297abb54
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255262"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Ridefinire un gruppo usando il mapping delle dipendenze del gruppo

In questo articolo viene descritto come ridefinire un gruppo visualizzando le dipendenze di tutti i computer nel gruppo. In genere si usa questo metodo quando si vogliono ridefinire le appartenenze di un gruppo esistente, controllando in modo incrociato le dipendenze del gruppo, prima di eseguire una valutazione. Ridefinizione di un gruppo tramite la visualizzazione delle dipendenze è utile per pianificare in modo efficace la migrazione a Azure. È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme. Garantisce inoltre una migrazione completa e senza interruzioni a sorpresa.


> [!NOTE]
> I gruppi per i quali si vuole visualizzare le dipendenze non devono includere più di 10 computer. Se il gruppo contiene più di 10 computer, è consigliabile suddividerlo in gruppi più piccoli per sfruttare la funzionalità di visualizzazione delle dipendenze.


## <a name="prepare-for-dependency-visualization"></a>Prepararsi per la visualizzazione delle dipendenze
Per abilitare la visualizzazione delle dipendenze dei computer, Azure Migrate usa la soluzione Mapping dei servizi in Log Analytics.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

### <a name="associate-a-log-analytics-workspace"></a>Associare un'area di lavoro di Log Analytics
Per sfruttare i vantaggi della visualizzazione delle dipendenze è possibile associare un'area di lavoro di Log Analytics, nuova o esistente, a un progetto di Azure Migrate. È possibile creare o collegare solo un'area di lavoro nella stessa sottoscrizione in cui viene creato il progetto di migrazione.

- Per collegare un'area di lavoro di Log Analytics a un progetto, in **Panoramica** passare alla sezione **Essentials** del progetto e fare clic su **Configurazione richiesta**

    ![Associare un'area di lavoro di Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Durante l'associazione di un'area di lavoro si avrà la possibilità di creare una nuova area di lavoro o di collegarne una esistente:
    - Quando si crea una nuova area di lavoro è necessario specificare un nome per essa. L'area di lavoro viene quindi creata in una regione della stessa [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) come progetto di migrazione.
    - Quando si collega un'area di lavoro esistente, è possibile sceglierla tra tutte le aree di lavoro disponibili nella stessa sottoscrizione del progetto di migrazione. Sono elencate solo le aree di lavoro create in un'area geografica in cui [è supportato il mapping dei servizi](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Per poter collegare un'area di lavoro è necessario avere l'accesso in lettura all'area di lavoro.

> [!NOTE]
> Non è possibile modificare l'area di lavoro associata a un progetto di migrazione.

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali
Per visualizzare le dipendenze di un gruppo, è necessario scaricare e installare gli agenti in ogni computer locale appartenente al gruppo. Se si hanno computer senza accesso a Internet, è necessario scaricare e installare il [Gateway Log Analytics](../azure-monitor/platform/gateway.md).

1. In **Panoramica**, fare clic su **Gestisci** > **gruppi** e andare al gruppo desiderato.
2. Nell'elenco di computer nella colonna **Dependency agent**, fare clic su **Richiede l'installazione** per visualizzare le istruzioni su come scaricare e installare gli agenti.
3. Nella pagina **Dipendenze**, scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent in ogni macchina virtuale del gruppo.
4. Copiare l'ID e la chiave dell'area di lavoro. Questi dati sono necessari quando si installa MMA nei computer locali.

### <a name="install-the-mma"></a>Installare MMA

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro di Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.


Per installare l'agente in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>Installare Dependency Agent
1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

Altre informazioni sul supporto di Dependency Agent per sistemi operativi [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

## <a name="refine-the-group-based-on-dependency-visualization"></a>Ridefinire il gruppo in base alla visualizzazione delle dipendenze
Dopo aver installato gli agenti in tutti i computer del gruppo, è possibile visualizzare le dipendenze e ridefinirle seguendo i passaggi seguenti.

1. Nel progetto di Azure Migrate, in **Gestisci**, fare clic su  **Gruppi** e selezionare il gruppo.
2. Nella pagina relativa al gruppo fare clic su  **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.
3. La mappa delle dipendenze del gruppo mostra i dettagli seguenti:
    - Connessioni TCP in entrata (client) e in uscita (server) a/da tutti i computer che fanno parte del gruppo
        - I computer dipendenti in cui non sono installati l'agente MMA e l'agente Dependency Agent sono raggruppati in base ai numeri di porta
        - I computer dipendenti in cui sono installati l'agente MMA e l'agente Dependency Agent sono visualizzati in caselle separate
    - I processi in esecuzione sul computer: è possibile espandere ogni casella di computer per visualizzare i processi
    - Le proprietà di ogni computer, come il nome di dominio completo, il sistema operativo, l'indirizzo MAC e così via: fare clic su ogni casella di computer per visualizzare questi dettagli

     ![Visualizzazione delle dipendenze del gruppo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Per visualizzare le dipendenze a un livello più dettagliato, fare clic sull'intervallo di tempo per modificarlo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
4. Verificare i computer dipendenti, i processi in esecuzione in ciascun computer e identificare i computer da aggiungere o rimuovere dal gruppo.
5. Premere CTRL+clic per selezionare contemporaneamente più computer sulla mappa e aggiungerle o rimuoverle dal gruppo.
    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione dei computer di un gruppo invalidano le precedenti valutazioni eseguite per il gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.
5. Fare clic su **OK** per salvare il gruppo.

    ![Aggiungere o rimuovere computer](./media/how-to-create-group-dependencies/add-remove.png)

Se si vogliono verificare le dipendenze di un computer specifico che viene visualizzato nella mappa delle dipendenze del gruppo, [configurare il mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sulle domande frequenti sulla visualizzazione delle dipendenze.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
