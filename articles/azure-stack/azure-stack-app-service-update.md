---
title: Aggiorna servizio App di Azure in Azure Stack | Microsoft Docs
description: Indicazioni dettagliate per l'aggiornamento di servizio App di Azure in Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: anwestg
ms.openlocfilehash: f12eb7d74d9c47567c72cddda8d2813f394fb1de
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139612"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Aggiorna servizio App di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!IMPORTANT]
> Applicare l'aggiornamento 1807 al sistema integrato Azure Stack o distribuire il kit di sviluppo di Azure Stack più recente prima della distribuzione del servizio App di Azure 1.3.
>
>

Seguendo le istruzioni riportate in questo articolo, è possibile aggiornare il [provider di risorse del servizio App](azure-stack-app-service-overview.md) distribuito in un ambiente Azure Stack che è connesso a Internet.

> [!IMPORTANT]
> Prima di eseguire l'aggiornamento, assicurarsi di aver già completato la [distribuzione del servizio App di Azure nel Provider di risorse di Azure Stack](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Eseguire l'installazione di provider risorse servizio App

Durante questo processo, l'aggiornamento sarà:

* Rilevare distribuzione precedente del servizio App
* Preparare tutti i pacchetti di aggiornamenti e nuove versioni di tutte le librerie di software Open Source per la distribuzione
* Caricare in archiviazione
* Aggiornare tutti i ruoli del servizio App (i controller, gestione, front-end, Publisher e Worker ruoli)
* Aggiorna definizioni del set di scalabilità del servizio app
* Aggiorna manifesto del Provider di risorse del servizio App

> [!IMPORTANT]
> Il programma di installazione del servizio App deve essere eseguito in un computer che può raggiungere l'endpoint di Azure Stack amministratore Azure Resource Manager.
>
>

Per aggiornare la distribuzione del servizio App in Azure Stack, seguire questa procedura:

1. Scaricare il [programma di installazione del servizio App](https://aka.ms/appsvcupdate3installer)

2. Eseguire appservice.exe come amministratore

    ![Programma di installazione del servizio App][1]

3. Fare clic su **distribuzione servizio App o l'aggiornamento alla versione più recente.**

4. Verificare e accettare le condizioni di licenza Software Microsoft e quindi fare clic su **successivo**.

5. Verificare e accettare le condizioni di licenza di terze parti e quindi fare clic su **successivo**.

6. Assicurarsi che l'endpoint di Azure Stack Azure Resource Manager e il Tenant di Active Directory le informazioni siano corrette. Se si usa le impostazioni predefinite durante la distribuzione di Azure Stack Development Kit, è possibile accettare i valori predefiniti di seguito. Tuttavia, se le opzioni sono personalizzate quando è stato distribuito Azure Stack, è necessario modificare i valori in questa finestra per riflettere il valore. Ad esempio, se si usa il suffisso del dominio *mycloud.com*, necessario modificare l'endpoint di Azure Stack Azure Resource Manager *management.region.mycloud.com*. Dopo aver verificato le informazioni, fare clic su **successivo**.

    ![Informazioni sul Cloud di Azure Stack][2]

7. Nella pagina successiva:

   1. Fare clic sui **Connect** accanto alle **sottoscrizioni di Azure Stack** casella.
        * Se si usa Azure Active Directory (Azure AD), immettere l'account amministratore di Azure AD e la password specificata quando è stato distribuito Azure Stack. Fare clic su **Accedi**.
        * Se si usa Active Directory Federation Services (ADFS), specificare l'account di amministratore. Ad esempio, *cloudadmin@azurestack.local*. Immettere la password e fare clic su **Accedi**.
   2. Nel **delle sottoscrizioni di Azure Stack** , quindi selezionare la **sottoscrizione del Provider predefinito**.
   3. Nel **località di Azure Stack** selezionare il percorso che corrisponde all'area di cui si esegue la distribuzione. Ad esempio, selezionare **locale** se la distribuzione in Azure Stack Development Kit.
   4. Se viene individuata una distribuzione di servizio App esistente, quindi l'account di archiviazione e del gruppo di risorse verrà popolata e in grigio.
   5. Fare clic su **successivo** per esaminare il riepilogo dell'aggiornamento.

    ![Installazione del servizio App rilevate][3]

8. Nella pagina di riepilogo:
   1. Verificare le selezioni effettuate. Per apportare modifiche, usare il **Previous** pulsanti visitare le pagine precedenti.
   2. Se le configurazioni siano corrette, selezionare la casella di controllo.
   3. Per avviare l'aggiornamento, fare clic su **successivo**.

       ![Riepilogo dell'aggiornamento del servizio App][4]

9. Pagina stato dell'aggiornamento:
    1. Monitorare lo stato di avanzamento. La durata dell'aggiornamento del servizio App in Azure Stack varia in base alle numerose istanze del ruolo distribuite.
    2. Dopo l'aggiornamento viene completato correttamente, fare clic su **Exit**.

        ![Stato dell'aggiornamento del servizio App][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altri [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

* [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)
