---
title: Aggiornare l'agente di Servizi di ripristino di Microsoft Azure (MARS)
description: Informazioni su come aggiornare l'agente di Servizi di ripristino di Microsoft Azure (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 1f4fd5074b0c6195742935e0cf0e742a00a3549e
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827325"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Aggiornare l'agente di Servizi di ripristino di Microsoft Azure (MARS)

In questo articolo viene spiegato come:

* Identificare i server con le versioni precedenti dell'agente MARS
* Aggiornare le installazioni MARS in questi server

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identificare i server con le versioni precedenti dell'agente MARS

Per le installazioni dell'agente di backup di Azure e del server di backup di Azure:

1. Passare all'insieme di credenziali dei servizi di ripristino in cui sono stati registrati server di cui è potenzialmente stato eseguito il backup da versioni precedenti dell'agente. È possibile trovare un elenco rappresentativo di insiemi di credenziali con agenti di backup di Azure precedenti negli avvisi di aggiornamento di backup di Azure da Azure.
1. Nella sezione **Impostazioni** di sinistra dell'insieme di credenziali di servizi di ripristino selezionare **infrastruttura di backup** nella sezione **Gestisci** .
1. Per individuare gli agenti di backup di Azure installati come parte delle installazioni del server di backup di Azure, passare a **Backup Management Servers** in **Management Servers**. In questo elenco vengono elencati i server in cui sono installate le installazioni del server di backup di Azure con il numero di versione dell'agente di backup di Azure associato.

    ![Elenco di agenti MARS installati come parte delle installazioni del server di backup di Azure](./media/upgrade-mars-agent/backup-management-servers.png)

1. Per controllare le versioni dell'agente per le installazioni di agenti Servizi di ripristino di Microsoft Azure (MARS) o Azure Backup Agent, passare a **server protetti** in **server di gestione**. Quindi selezionare **agente di backup di Azure** in tipo di gestione di backup. In questo elenco vengono elencati i server con installazioni di Azure Backup Agent insieme al numero di versione per l'installazione.

    ![Elenco di server in cui è installato l'agente MARS](./media/upgrade-mars-agent/protected-servers.png)

1. Ordinare la colonna versione dell'agente di backup di Azure facendo clic sulla colonna **versione agente** per le installazioni dell'agente Mars o sulla colonna **versione dell'agente di backup** di Azure per le installazioni del server di backup di Azure.

1. Il passaggio precedente fornirà l'elenco dei server con agenti di backup di Azure con versioni inferiori a 2.0.9083.0 o versioni dell'agente elencate come spazi vuoti. Si tratta dei server in cui è necessario aggiornare gli agenti di backup di Azure.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Aggiornare l'installazione dell'agente MARS nel server

Dopo aver identificato i server che necessitano di un aggiornamento dell'agente di backup di Azure, seguire questa procedura per ogni server identificato, usando il server di backup di Azure o l'agente MARS. [Scaricare la versione più recente dell'agente di backup di Azure prima di](https://aka.ms/azurebackup_agent) seguire questa procedura.

1. Fare clic su una riga con l'agente di backup di Azure inferiore a 2.0.9083.0 o vuoto. Verrà visualizzata la schermata dei dettagli del server.

    ![Server protetti con versioni obsolete degli agenti](./media/upgrade-mars-agent/old-agent-version.png)

    ![Server di backup di Azure con versioni obsolete degli agenti](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Fare clic su **Connetti** per ricevere un file di connessione Desktop remoto per connettersi al server o connettersi direttamente al server tramite la connessione Desktop remoto sul server.

    ![Connetti al server tramite connessione Desktop remoto](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Se il server elencato non esiste o è stato ritirato, è possibile ignorare i passaggi rimanenti seguenti e passare al server successivo.

1. Immettere i dettagli di accesso amministrativi ed eseguire l'accesso.

1. Se il server o il proxy del server ha accesso a Internet limitato, verificare che le impostazioni del firewall nel server/proxy siano configurate in modo da consentire l'URL appropriato per il cloud di Azure in uso:

    Cloud di Azure | URL
    -- | ---
    Cloud di Azure (pubblico) |   `https://login.windows.net`
    Azure Cina 21Vianet cloud   | `https://login.chinacloudapi.cn`
    Cloud Azure Governo degli Stati Uniti |   `https://login.microsoftonline.us`
    Cloud di Azure per la Germania  |  `https://login.microsoftonline.de`

1. Copiare il programma di installazione dell'aggiornamento dell'agente di backup di Azure nel server.

    ![Copia programma di installazione dell'aggiornamento dell'agente di backup di Azure nel server](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Eseguire il programma di installazione. Verrà avviato l'aggiornamento guidato dell'agente di Servizi di ripristino di Microsoft Azure.

    ![Aggiornamento guidato dell'agente di Servizi di ripristino di Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Fare clic su **Next** (Avanti).

1. Fare clic su **Aggiorna**.

    ![Installazione dell'agente di Servizi di ripristino di Microsoft Azure](./media/upgrade-mars-agent/upgrade-installation.png)

1. La schermata di conferma finale indica che l'agente di backup di Azure è stato aggiornato correttamente.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Per i clienti di System Center Data Protection Manager (SC DPM)

Se sono stati installati gli agenti di backup di Azure nei server System Center Data Protection Manager (SC DPM), è necessario attenersi alla procedura seguente per determinare se i server DPM necessitano di un aggiornamento dell'agente di backup di Azure:

1. Accedere al server SC DPM come amministratore.
2. Aprire la console DPM.
3. Fare clic su **gestione** nell'angolo inferiore sinistro della console.
4. All'interno delle informazioni visualizzate nel percorso di spostamento a sinistra, cercare le informazioni sulla versione dell'agente di backup di Azure.
5. Se la versione è inferiore a 2.0.9083.0, scaricare il programma di installazione più recente dell'agente di backup di Azure ed eseguire il programma di installazione nel server DPM per aggiornare l'agente di backup di Azure.

Ripetere i passaggi precedenti per tutti i server DPM nell'ambiente in uso.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di computer Windows con l'agente Mars di backup di Azure](backup-windows-with-mars-agent.md)
