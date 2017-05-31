---
title: Installazione del runtime di Funzioni di Azure | Documentazione Microsoft
description: Come installare il runtime di Funzioni di Azure
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
ms.date: 05/08/2017
ms.author: anwestg
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---

# <a name="install-the-azure-functions-runtime-preview"></a>Installare l'anteprima del runtime di Funzioni di Azure

Se si desidera installare la versione di anteprima del runtime di Funzioni di Azure è necessario seguire questi passaggi:

1. Assicurarsi che il computer abbia i requisiti minimi
1. Scaricare il [programma di installazione dell'anteprima del runtime di Funzioni di Azure](https://aka.ms/azafr). 
1. Installare l'anteprima del runtime di Funzioni di Azure
1. Completare la configurazione dell'anteprima del runtime di Funzioni di Azure

## <a name="prerequisites"></a>Prerequisiti

Prima di installare la versione di anteprima del runtime di Funzioni di Azure, è necessario avere quanto segue:

1. Un computer che esegue Microsoft Windows Server 2016 o Microsoft Windows 10 Creators Update (Professional o Enterprise Edition).
1. Un'istanza di SQL Server in esecuzione all'interno della rete.  L'edizione minima richiesta è SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Installare l'anteprima del runtime di Funzioni di Azure

Il programma di installazione dell'anteprima del runtime di Funzioni di Azure guida l'utente nell'installazione dei ruoli di gestione e di lavoro dell'anteprima del runtime di Funzioni di Azure.  È possibile installare il ruolo di gestione e di lavoro nello stesso computer.  Tuttavia, quando si aggiungono altre funzioni, è necessario distribuire altri ruoli di lavoro nei computer aggiuntivi per poter scalare le funzioni su più thread di lavoro.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installare il ruolo di gestione e di lavoro nello stesso computer

1. Eseguire il programma di installazione dell'anteprima del runtime di Funzioni di Azure.

    ![Programma di installazione dell'anteprima del runtime di Funzioni di Azure][1]

1. **Fare clic su Avanti** per procedere oltre la prima fase del programma di installazione
1. Dopo aver letto le condizioni dell'**EULA**, **selezionare la casella** per accettare le condizioni e **fare clic su Avanti** per continuare.
1. Selezionare i ruoli che si desidera installare nel computer **ruolo di gestione per le funzioni** e/o **ruolo di lavoro per le funzioni** e **fare clic su Avanti**

    ![Programma di installazione dell'anteprima del runtime di Funzioni di Azure - Selezione del ruolo][3]

    > [!NOTE]
    > È possibile installare il **ruolo di lavoro per le funzioni** in molte altre macchine: a tale scopo, seguire queste istruzioni e selezionare solo il **ruolo di lavoro per le funzioni** nel programma di installazione.

1. **Fare clic su Avanti** affinché il **programma di installazione del runtime di Funzioni di Azure** esegua l'installazione nel computer.
1. Al termine, il programma di installazione avvierà lo **strumento di configurazione del runtime di Funzioni di Azure**.

    ![Programma di installazione dell'anteprima del runtime di Funzioni di Azure - Completato][5]

    > [!NOTE]
    > Se si sta eseguendo l'installazione su **Windows 10** e la funzionalità **contenitore** non è stata abilitata in precedenza, il programma di installazione del **runtime di Funzioni di Azure** chiede di riavviare il computer per completare l'installazione.

## <a name="configure-the-azure-functions-runtime"></a>Configurare il runtime di Funzioni di Azure

Per completare l'installazione del Runtime di funzioni di Azure è necessario completare la configurazione.

1. Lo **strumento di configurazione del runtime di Funzioni di Azure** mostra i ruoli che sono installati nel computer.

    ![Anteprima del runtime di Funzioni di Azure - Strumento di configurazione][6]

1. Fare clic sulla scheda **Database**, immettere i **dettagli della connessione per l'istanza di SQL Server** e **fare clic su Applica**.  Questo è necessario perché il runtime di Funzioni di Azure possa creare un database per supportare il runtime.
    
    ![Anteprima del runtime di Funzioni di Azure - Configurazione del database][7]

1. Fare clic sulla scheda **Credenziali**.  In questa schermata è necessario creare due nuove credenziali per l'uso con una condivisione file per l'hosting di tutte le funzioni di Azure.  **Specificare le combinazioni di nome utente e password** per il **proprietario della condivisione file** e per l'**utente della condivisione file** e fare clic su **Applica**.

    ![Anteprima del runtime di Funzioni di Azure - Credenziali][8]

1. Fare clic sulla scheda **Condivisione file**.  In questa schermata è necessario specificare i dettagli del **percorso della condivisione file**.  È possibile creare la condivisione file o usare una condivisione file esistente e fare clic su **Applica**.  Se si seleziona un nuovo percorso di condivisione file è necessario specificare una directory per l'uso da parte del runtime di Funzioni di Azure.
    
    ![Anteprima del runtime di Funzioni di Azure - Condivisione file][9]

1. Fare clic sulla scheda **IIS**.  Questa scheda mostra i dettagli dei siti Web in IIS che l'installazione del runtime di Funzioni di Azure creerà.  **Fare clic su Applica** per completare l'operazione.

    ![Anteprima del runtime di Funzioni di Azure - IIS][10]

1. Fare clic sulla scheda **Servizi**.  Questa scheda mostra lo stato dei servizi nell'installazione del runtime di Funzioni di Azure.  Se dopo la configurazione iniziale il **servizio di attivazione host di Funzioni di Azure** non è in esecuzione, fare clic su **avvio del servizio**

    ![Anteprima del runtime di Funzioni di Azure - Configurazione completata][11]

1. Passare infine al **portale di runtime di Funzioni di Azure** come `https://<machinename>/`

    ![Anteprima del runtime di Funzioni di Azure - Portale][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
