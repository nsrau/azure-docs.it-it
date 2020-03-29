---
title: Aggiornare l'agente di Microsoft Azure Recovery Services (MARS)
description: Informazioni su come aggiornare l'agente di Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672803"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Aggiornare l'agente di Microsoft Azure Recovery Services (MARS)

In questo articolo viene spiegato come:

* Identificare i server con versioni precedenti dell'agente MARS
* Aggiornare le installazioni MARS su tali server

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identificare i server con versioni precedenti dell'agente MARS

Per le installazioni dell'agente di Backup di Azure e del server di backup di Azure:For Installations of Azure Backup agent and Azure backup server:

1. Passare all'insieme di credenziali dei servizi di ripristino in cui sono stati registrati server che potrebbero essere sottoposti a backup da versioni precedenti dell'agente. È possibile trovare un elenco rappresentativo di insiemi di credenziali con agenti di Backup di Azure meno recenti negli avvisi di aggiornamento di Backup di Azure da Azure.You can find a representative list of vaults with older Azure Backup agents in the Azure Backup Update alerts from Azure.
1. Nella sezione Left-side Settings del Vault Recovery Services selezionare **Backup Infrastructure (Infrastruttura** di backup) nella sezione **Manage (Gestisci).** **Settings**
1. Per individuare gli agenti di backup di Azure installati come parte delle installazioni del server Backup di Azure, passare a **Server di gestione di Backup** in Server di **gestione.** Verranno elencati i server con installazioni del server di Backup di Azure insieme al numero di versione per l'agente di Backup di Azure associato.

    ![Elenco degli agenti MARS installati come parte delle installazioni del server di Backup di Azure](./media/upgrade-mars-agent/backup-management-servers.png)

1. Per controllare le versioni degli agenti per le installazioni degli agenti di Microsoft Azure Recovery Services (MARS) o dell'agente di Backup di Azure, passare a **Server protetti** in Server **di gestione**. Selezionare quindi **L'agente di Backup** di Azure in Tipo di gestione backup. Verranno elencati i server con installazioni dell'agente di Backup di Azure insieme al numero di versione per l'installazione.

    ![Elenco dei server in cui è installato l'agente MARS](./media/upgrade-mars-agent/protected-servers.png)

1. Ordinare la colonna Versione agente di Backup di Azure facendo clic sulla colonna Versione agente per le installazioni dell'agente MARS o sulla colonna **Versione agente di Backup di Azure** per le installazioni del server di backup di Azure.Sort the Azure Backup Agent column by clicking on the Agent **Version** for MARS Agent installations or the Azure Backup Agent Version column for Azure backup server installations.

1. Il passaggio precedente fornirà l'elenco dei server con agenti di Backup di Azure con versioni inferiori alla 2.0.9083.0 o versioni dell'agente elencate come spazi vuoti. Questi sono i server in cui gli agenti di Backup di Azure devono essere aggiornati.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Aggiornare l'installazione dell'agente MARS sul server

Dopo aver identificato i server che richiedono un aggiornamento dell'agente di Backup di Azure, eseguire la procedura seguente per ogni server identificato (utilizzando il server di backup di Azure o l'agente MARS). [Scaricare la versione più recente dell'agente di backup di Azure](https://aka.ms/azurebackup_agent) prima di eseguire la procedura seguente.

1. Fare clic su una riga con un agente di Backup di Azure inferiore a 2.0.9083.0 o vuoto. Si aprirà la schermata dei dettagli del server.

    ![Server protetti con versioni di agenti non aggiornate](./media/upgrade-mars-agent/old-agent-version.png)

    ![Server di backup di Azure con versioni non aggiornate degli agenti](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Fare clic su **Connetti** per ricevere un file di connessione desktop remoto per connettersi al server o connettersi direttamente al server tramite la connessione Desktop remoto sul server.

    ![Connettersi al server tramite connessione desktop remoto](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Se il server elencato non esiste o è stato rimosso, è possibile ignorare i passaggi rimanenti riportati di seguito e passare al server successivo.

1. Inserisci i tuoi dati di accesso amministrativi e accedi.

1. Se il proxy del server o del server ha un accesso a Internet limitato, verificare che le impostazioni del firewall nel server/proxy siano configurate per consentire l'URL appropriato per il cloud di Azure in uso:

    Cloud di Azure | URL
    -- | ---
    Cloud di Azure (pubblico)Azure Cloud (Public) |   `https://login.windows.net`
    Azure China 21Vianet Cloud   | `https://login.chinacloudapi.cn`
    Azure US Government Cloud |   `https://login.microsoftonline.us`
    Cloud di Azure per la Germania  |  `https://login.microsoftonline.de`

1. Copiare il programma di installazione dell'aggiornamento dell'agente di backup di Azure nel server.

    ![Copiare il programma di installazione dell'aggiornamento dell'agente di backup di Azure nel serverCopy Azure backup agent update installer to server](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Eseguire il programma di installazione. Verrà visualizzata la procedura guidata di aggiornamento dell'agente servizi di ripristino di Microsoft Azure.The Microsoft Azure Recovery Services Agent Upgrade wizard opens.

    ![Aggiornamento guidato dell'agente dei servizi di ripristino di Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Fare clic su **Avanti**.

1. Fare clic su **Aggiorna**.

    ![Installazione dell'agente dei servizi di ripristino di Microsoft Azure](./media/upgrade-mars-agent/upgrade-installation.png)

1. La schermata di conferma finale indica che l'agente di Backup di Azure è stato aggiornato correttamente.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Per i clienti di System Center Data Protection Manager (SC DPM)

Se gli agenti di backup di Azure sono stati installati nei server System Center Data Protection Manager (SC DPM), è necessario eseguire la procedura seguente per identificare se i server DPM necessitano di un aggiornamento dell'agente di backup di Azure:

1. Accedere al server DPM SC come amministratore.
2. Aprire la Console Data Protection Manager.
3. Fare clic su **Gestione** nella barra di navigazione in basso a sinistra della console.
4. All'interno delle informazioni visualizzate nel riquadro di spostamento sinistro cercare le informazioni sulla versione dell'agente di Backup di Azure.In the information that appears on the left navigation, look for the Azure Backup agent version information.
5. Se la versione è inferiore alla 2.0.9083.0, scaricare il programma di installazione dell'agente di backup di Azure più recente ed eseguire il programma di installazione nel server DPM per aggiornare l'agente di Backup di Azure.If the version is lower than 2.0.9083.0, download the latest Azure backup agent installer and run the installer on the DPM server to update the Azure Backup agent.

Ripetere i passaggi precedenti per tutti i server DPM nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il backup di [computer Windows usando l'agente MARS](backup-windows-with-mars-agent.md) di Backup di Azure
