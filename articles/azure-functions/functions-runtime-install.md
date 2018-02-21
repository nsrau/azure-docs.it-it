---
title: Installazione del runtime di Funzioni di Azure | Documentazione Microsoft
description: Come installare l'anteprima del runtime di Funzioni di Azure 2
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Installare l'anteprima del runtime di Funzioni di Azure 2

Se si desidera installare la versione di anteprima del runtime di Funzioni di Azure 2 è necessario seguire questi passaggi:

1. Assicurarsi che il computer abbia i requisiti minimi.
1. Scaricare il [programma di installazione dell'anteprima del runtime di Funzioni di Azure](https://aka.ms/azafrv2).
1. Disinstallare l'anteprima del runtime di Funzioni di Azure 1.
1. Installare l'anteprima del runtime di Funzioni di Azure 2.
1. Completare la configurazione dell'anteprima del runtime di Funzioni di Azure 2.
1. Creare la prima funzione nell'anteprima del runtime di Funzioni di Azure

## <a name="prerequisites"></a>prerequisiti

Prima di installare l'anteprima del runtime di Funzioni di Azure è necessario avere le risorse seguenti:

1. Un computer che esegue Microsoft Windows Server 2016 o Microsoft Windows 10 Creators Update (Professional o Enterprise Edition).
1. Un'istanza di SQL Server in esecuzione all'interno della rete.  L'edizione minima richiesta è SQL Server Express.

## <a name="uninstall-previous-version"></a>Disinstallare la versione precedente

Se l'anteprima del runtime di Funzioni di Azure è stata installata in precedenza è necessario disinstallarla prima di installare la versione più recente.  Disinstallare l'anteprima del runtime di Funzioni di Azure rimuovendo il programma in Installazione applicazioni di Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Installare l'anteprima del runtime di Funzioni di Azure

Il programma di installazione dell'anteprima del runtime di Funzioni di Azure guida l'utente nell'installazione dei ruoli di gestione e di lavoro dell'anteprima del runtime di Funzioni di Azure.  È possibile installare il ruolo di gestione e di lavoro nello stesso computer.  Tuttavia, quando si aggiungono altre app per le funzioni è necessario distribuire altri ruoli di lavoro nei computer aggiuntivi per poter scalare le funzioni su più ruoli di lavoro.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installare il ruolo di gestione e di lavoro nello stesso computer

1. Eseguire il programma di installazione dell'anteprima del runtime di Funzioni di Azure.

    ![Programma di installazione dell'anteprima del runtime di Funzioni di Azure][1]

1. Fare clic su **Avanti**.
1. Dopo aver letto le condizioni dell'**EULA**, **selezionare la casella** per accettare le condizioni e fare clic su **Avanti** per continuare.
1. Selezionare i ruoli che si intende installare nel computer, **Functions Management Role (ruolo di gestione per le funzioni)** e/o **Functions Worker Role (ruolo di lavoro per le funzioni)**, e fare clic su **Avanti**.

    ![Programma di installazione dell'anteprima del runtime di Funzioni di Azure - Selezione del ruolo][3]

    > [!NOTE]
    > È possibile installare il **ruolo di lavoro per le funzioni** in molti altri computer. A tale scopo, seguire le istruzioni seguenti e selezionare solo **il ruolo di lavoro per le funzioni** nel programma di installazione.

1. Fare clic su **Avanti** per avviare il processo di installazione dell'**Installazione guidata del runtime di Funzioni di Azure** nel computer.
1. Al termine, il programma di installazione avvierà lo strumento di configurazione del **runtime di Funzioni di Azure**.

    ![Programma di installazione dell'anteprima del runtime di Funzioni di Azure - Completato][6]

    > [!NOTE]
    > Se si sta eseguendo l'installazione su **Windows 10** e la funzionalità **Contenitore** non è stata abilitata in precedenza, il programma di installazione del **runtime di Funzioni di Azure** chiede di riavviare il computer per completare l'installazione.

## <a name="configure-the-azure-functions-runtime"></a>Configurare il runtime di Funzioni di Azure

Per completare l'installazione del runtime di Funzioni di Azure è necessario completare la configurazione.

1. Lo strumento di configurazione del **runtime di Funzioni di Azure** mostra i ruoli che sono installati nel computer.

    ![Anteprima del runtime di Funzioni di Azure - Strumento di configurazione][7]

1. Fare clic sulla scheda **Database**, immettere i dettagli della connessione per l'istanza di SQL Server, inclusa la specifica di una [chiave master del database](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine) e fare clic su **Applica**.  La connettività a un'istanza di SQL Server è necessaria affinché il runtime di Funzioni di Azure possa creare un database per supportare il runtime.

    ![Anteprima del runtime di Funzioni di Azure - Configurazione del database][8]

1. Fare clic sulla scheda **Credenziali**.  A questo punto è necessario creare due nuove credenziali per l'uso con una condivisione file per l'hosting di tutte le app per le funzioni.  Specificare le combinazioni di **nome utente** e **password** per il **proprietario della condivisione file** e per l'**utente della condivisione file** e quindi fare clic su **Applica**.

    ![Anteprima del runtime di Funzioni di Azure - Credenziali][9]

1. Fare clic sulla scheda **Condivisione file**.  A questo punto è necessario specificare i dettagli del percorso della condivisione file.  È possibile creare la condivisione file o usare una condivisione file esistente e fare clic su **Applica**.  Se si seleziona un nuovo percorso di condivisione file è necessario specificare una directory per l'uso da parte del runtime di Funzioni di Azure.

    ![Anteprima del runtime di Funzioni di Azure - Condivisione file][10]

1. Fare clic sulla scheda **IIS**.  Questa scheda mostra i dettagli dei siti Web in IIS che lo strumento di configurazione del runtime di Funzioni di Azure creerà.  Qui è possibile specificare un nome DNS personalizzato per il portale di anteprima del runtime di Funzioni di Azure.  Fare clic su **Applica** per completare l'operazione.

    ![Anteprima del runtime di Funzioni di Azure - IIS][11]

1. Fare clic sulla scheda **Servizi**.  Questa scheda mostra lo stato dei servizi nell'installazione del runtime di Funzioni di Azure.  Se dopo la configurazione iniziale il **servizio di attivazione host di Funzioni di Azure** non è in esecuzione fare clic su **Avvia servizio**.

    ![Anteprima del runtime di Funzioni di Azure - Configurazione completata][12]

1. Passare al **portale del runtime di Funzioni di Azure** come `https://<machinename>.<domain>/`.

    ![Anteprima del runtime di Funzioni di Azure - Portale][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Creare la prima funzione nell'anteprima del runtime di Funzioni di Azure

Per creare la prima funzione nell'anteprima del runtime di Funzioni di Azure

1. Passare al **portale del runtime di Funzioni di Azure** come https://<machinename>.<domain> ad esempio https://mycomputer.mydomain.com
1. Viene richiesto di effettuare l'**accesso**. Se distribuito in un dominio usare il nome utente e la password dell'account di dominio, altrimenti usare il nome utente e la password dell'account locale per accedere al portale.

![Anteprima del runtime di Funzioni di Azure - Accesso al portale][14]

1. Per creare app per le funzioni è necessario creare una sottoscrizione.  Nell'angolo superiore sinistro del portale fare clic sull'opzione  **+**  accanto alle sottoscrizioni

![Anteprima del runtime di Funzioni di Azure - Sottoscrizioni del portale][15]

1. Scegliere **DefaultPlan**, immettere un nome per la sottoscrizione e fare clic su **Crea**.

![Anteprima del runtime di Funzioni di Azure - Nome e piano di sottoscrizione del portale][16]

1. Tutte le app per le funzioni sono elencate nel riquadro a sinistra del portale.  Per creare una nuova app per le funzioni selezionare l'intestazione **App per le funzioni** e fare clic sull'opzione **+**.

1. Immettere un nome per l'app per le funzioni, selezionare la sottoscrizione corretta, scegliere la versione del runtime di Funzioni di Azure che si desidera programmare e fare clic su **Crea**

![Anteprima del runtime di Funzioni di Azure - Nuova app per le funzioni del portale][17]

1. La nuova app per le funzioni è elencata nel riquadro a sinistra del portale.  Selezionare le funzioni e quindi fare clic su **Nuova funzione** nella parte superiore del riquadro centrale nel portale.

![Anteprima del runtime di Funzioni di Azure - Modelli][18]

1. Selezionare la funzione Timer Trigger nel riquadro a comparsa di destra, assegnare un nome alla funzione e modificare la pianificazione per `*/5 * * * * *` (questa espressione Cron fa sì che la funzione del timer venga eseguita ogni cinque secondi), quindi fare clic su **Crea**

![Anteprima del runtime di Funzioni di Azure - Nuova configurazione della funzione del timer][19]

1. La funzione è stata creata.  È possibile visualizzare il log di esecuzione dell'app per le funzioni espandendo il riquadro del **log** nella parte inferiore del portale.

![Anteprima del runtime di Funzioni di Azure - Esecuzione della funzione][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
