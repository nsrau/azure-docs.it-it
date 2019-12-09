---
title: "Guida introduttiva: Creare un'app ASP.NET Core in C#"
description: Informazioni su come eseguire app Web nel servizio app di Azure distribuendo il modello predefinito di app Web ASP.NET Core in C# di Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: seodec18
ms.openlocfilehash: 285e4cc1f38dd2adb5934e49d87b43e09d74ce11
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672117"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Creare un'app Web ASP.NET Core in Azure

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Windows. Per la distribuzione nel servizio app in _Linux_, vedere [Creare un'app Web .NET Core nel servizio app in Linux](./containers/quickstart-dotnetcore.md).
>

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione.

Questa guida introduttiva illustra come distribuire la prima app Web ASP.NET Core in Servizio app di Azure. Al termine, si avrà un gruppo di risorse costituito da un piano di servizio app e da un'app del servizio app con un'applicazione Web distribuita.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, installare <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> con il carico di lavoro **Sviluppo ASP.NET e Web**.

Se Visual Studio 2019 è già installato:

- Installare gli aggiornamenti più recenti in Visual Studio selezionando **?**  > **Controlla aggiornamenti**.
- Aggiungere il carico di lavoro selezionando **Strumenti** > **Ottieni strumenti e funzionalità**.

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

Creare un'app Web ASP.NET Core seguendo questa procedura:

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Crea un nuovo progetto** trovare e selezionare **Applicazione Web ASP.NET Core** per C#, quindi selezionare **Avanti**.

1. In **Configura il nuovo progetto** assegnare all'applicazione il nome _myFirstAzureWebApp_ e quindi selezionare **Crea**.

   ![Configurare il progetto di app Web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Per questa guida di avvio rapido scegliere il modello **Applicazione Web**. Verificare che l'autenticazione sia impostata su **Nessuna autenticazione** e che non sia selezionata alcuna altra opzione. Selezionare **Create** (Crea).

   ![Selezionare ASP.NET Core Razor Pages per questa esercitazione](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    È possibile distribuire qualsiasi tipo di app Web ASP.NET Core in Azure.

1. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire l'app Web in locale.

   ![Eseguire l'app in locale](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Pubblicare l'app Web

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **myFirstAzureWebApp** e scegliere **Pubblica**.

1. Scegliere **Servizio app** e quindi selezionare **Pubblica**.

   ![Pubblicare dalla pagina di panoramica progetto](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. In **Servizio app - Crea nuovo** le opzioni disponibili variano a seconda che sia già stato eseguito l'accesso ad Azure e si abbia un account di Visual Studio collegato a un account di Azure. Selezionare **Aggiungi un account** o **Accedi** per accedere alla sottoscrizione di Azure. Se è già stato effettuato l'accesso, selezionare l'account da usare.

   > [!NOTE]
   > Se si è già connessi, non selezionare ancora l'opzione **Crea**.
   >

   ![Accedere ad Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Per **Gruppo di risorse** selezionare **Nuovo**.

1. In **Nome nuovo gruppo di risorse** immettere *myResourceGroup* e scegliere **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Per **Piano di hosting** selezionare **Nuovo**.

1. Nella finestra di dialogo **Configura piano di hosting** immettere i valori della tabella seguente e quindi scegliere **OK**.

   | Impostazione | Valore consigliato | DESCRIZIONE |
   |-|-|-|
   |Piano di servizio app| myAppServicePlan | Nome del piano di servizio app. |
   | Location | Europa occidentale | Data center in cui è ospitata l'app Web. |
   | Dimensione | Gratuito | [Piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting. |

   ![Creare un piano di servizio app](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. In **Nome** immettere un nome univoco per l'app che includa solo i caratteri validi, ossia `a-z`, `A-Z`, `0-9` e `-`. È possibile accettare il nome univoco generato automaticamente. L'URL dell'app Web è `http://<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app.

   ![Configurare il nome dell'app](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Selezionare **Crea** per avviare la creazione delle risorse di Azure.

Al termine della procedura guidata, l'app Web ASP.NET Core viene pubblicata in Azure e avviata nel browser predefinito.

![App Web ASP.NET pubblicata in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Il nome dell'app specificato nella pagina **Servizio app - Crea nuovo** viene usato come prefisso dell'URL nel formato `http://<app_name>.azurewebsites.net`.

**Congratulazioni** L'app Web ASP.NET Core è ora in esecuzione nel servizio app di Azure.

## <a name="update-the-app-and-redeploy"></a>Aggiornare e ridistribuire l'app

1. Nel progetto in **Esplora soluzioni** aprire **Pagine** > **Index.cshtml**.

1. Sostituire i due tag `<div>` con il codice seguente:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Per la ridistribuzione in Azure, fare clic con il pulsante destro del mouse sul progetto **myFirstAzureWebApp** in **Esplora soluzioni** e selezionare **Pubblica**.

1. Nella pagina di riepilogo **Pubblica** selezionare **Pubblica**.

   ![Pagina di riepilogo della pubblicazione di Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Al termine del processo di pubblicazione, Visual Studio avvia un browser sull'URL dell'app Web.

![App Web ASP.NET aggiornata in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Per gestire l'app Web, passare al [portale di Azure](https://portal.azure.com), quindi cercare e selezionare **Servizi app**.

![Selezionare Servizi app](./media/app-service-web-get-started-dotnet/app-services.png)

Nella pagina **Servizi app** selezionare il nome dell'app Web.

![Passaggio all'app di Azure nel portale](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione.

![Servizio app nel portale di Azure](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [ASP.NET Core con database SQL](app-service-web-tutorial-dotnetcore-sqldb.md)
