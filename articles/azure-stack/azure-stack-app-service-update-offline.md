---
title: Aggiornare il servizio di App di Azure non in linea | Documenti Microsoft
description: Linee guida dettagliate per l'aggiornamento di servizio App di Azure nello Stack di Azure non in linea
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: anwestg
ms.openlocfilehash: 5b71c9fd58636e9871bf592904d5dca4bc0ec966
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Aggiornamento non in linea di servizio App di Azure nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Seguendo le istruzioni riportate in questo articolo, è possibile aggiornare il [il provider di risorse di servizio App](azure-stack-app-service-overview.md) distribuito in un ambiente dello Stack di Azure:

* non è connesso a Internet
* protetta da Active Directory Federation Services (ADFS).

> [!IMPORTANT]
> Prima di eseguire l'aggiornamento, assicurarsi di aver già completato il [distribuzione del servizio App Azure nel Provider di risorse di Azure Stack](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Eseguire l'installazione di provider di risorse di servizio App

Per aggiornare il provider di risorse del servizio App in un ambiente dello Stack di Azure, è necessario completare queste attività:

1. Scaricare il [programma di installazione del servizio App](https://aka.ms/appsvcupdate1installer)
2. Creare un pacchetto di aggiornamento non in linea.
3. Eseguire il programma di installazione di servizio App (appservice.exe) e completare l'aggiornamento.

Durante questo processo, l'aggiornamento sarà:

* Rilevare precedente distribuzione di servizio App
* Caricare in memoria
* Aggiornare tutti i ruoli del servizio App (controller, gestione, front-end, Publisher e Worker ruoli)
* Aggiornare le definizioni degli insiemi di scala di servizio App
* Aggiornare manifesto del Provider di risorse di servizio App

## <a name="create-an-offline-upgrade-package"></a>Creare un pacchetto di aggiornamento non in linea

Per eseguire l'aggiornamento di servizio App in un ambiente disconnesso, è innanzitutto necessario creare un pacchetto di aggiornamento non in linea in un computer in cui è connesso a Internet.

1. Eseguire appservice.exe come amministratore

    ![Programma di installazione del servizio App][1]

2. Fare clic su **avanzate** > **creare il pacchetto non in linea**

    ![Programma di installazione di servizio App avanzata][2]

3. Il programma di installazione di servizio App crea un pacchetto di aggiornamento offline e visualizza il percorso.  È possibile fare clic su **Apri cartella** per aprire la cartella in Esplora risorse di file.

4. Copiare il programma di installazione (AppService.exe) e il pacchetto di aggiornamento non in linea nel computer host di Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Completare l'aggiornamento del servizio App di Azure stack

> [!IMPORTANT]
> In computer in cui è possibile raggiungere l'Endpoint di gestione risorse di Azure Stack amministratore Azure, è necessario eseguire il programma di installazione di servizio App.
>
>

1. Eseguire appservice.exe come amministratore.  

    ![Programma di installazione del servizio App][1]

2. Fare clic su **avanzate** > **completare l'installazione offline o aggiornamento**.

    ![Programma di installazione di servizio App avanzata][2]

3. Passare al percorso del pacchetto di aggiornamento offline creato in precedenza e quindi fare clic su **Avanti**.

4. Verificare e accettare le condizioni di licenza Software Microsoft e quindi fare clic su **Avanti**.

5. Verificare e accettare le condizioni di licenza di terze parti e quindi fare clic su **Avanti**.

6. Assicurarsi che l'endpoint di gestione risorse di Azure Stack Azure e un Tenant di Active Directory le informazioni siano corrette. Se si utilizza le impostazioni predefinite durante la distribuzione del Kit di sviluppo dello Stack di Azure, è possibile accettare i valori predefiniti di seguito. Tuttavia, se state personalizzate le opzioni per la distribuzione di Azure Stack, è necessario modificare i valori in questa finestra in modo da riflettere che. Ad esempio, se si utilizza il suffisso del dominio *mycloud.com*, l'endpoint di Azure Stack Azure Resource Manager è necessario modificare in *management.region.mycloud.com*. Dopo aver verificato le informazioni, fare clic su **Avanti**.

    ![Informazioni sul Cloud di Azure Stack][3]

7. Nella pagina successiva:

   1. Fare clic su di **Connetti** accanto al pulsante il **sottoscrizioni di Azure Stack** casella.
        * Se si usa Azure Active Directory (Azure AD), immettere l'account amministratore di Azure AD e la password forniti quando è stato distribuito Azure Stack. Fare clic su **Accedi**.
        * Se si utilizza Active Directory Federation Services (ADFS), specificare l'account amministratore. Ad esempio, *cloudadmin@azurestack.local*. Immettere la password e fare clic su **Accedi**.
   2. Nel **sottoscrizioni di Azure Stack** , selezionare la sottoscrizione.
   3. Nel **percorsi Stack Azure** , selezionare il percorso che corrisponde all'area in cui esegue la distribuzione. Ad esempio, selezionare **locale** se la distribuzione al Kit di sviluppo dello Stack di Azure.
   4. Se viene individuata una distribuzione di servizio App esistente, l'account di archiviazione e di gruppo di risorse verrà compilato e visualizzate in grigio.
   5. Fare clic su **Avanti** per esaminare il riepilogo dell'aggiornamento.

    ![Stata rilevata un'installazione di servizio App][4]

8. Nella pagina di riepilogo:
   1. Verificare le selezioni effettuate. Per apportare modifiche, utilizzare il **precedente** pulsanti a visitare pagine precedenti.
   2. Se le configurazioni sono corrette, selezionare la casella di controllo.
   3. Per avviare l'aggiornamento, fare clic su **Avanti**.

       ![Riepilogo dell'aggiornamento del servizio App][5]

9. Pagina stato dell'aggiornamento:
    1. Monitorare lo stato di aggiornamento. La durata dell'aggiornamento di servizio App di Azure stack varia dipende dal numero di istanze del ruolo distribuito.
    2. Dopo l'aggiornamento è stata completata correttamente, fare clic su **uscita**.

        ![Stato dell'aggiornamento del servizio App][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altre [piattaforma come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

* [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)
