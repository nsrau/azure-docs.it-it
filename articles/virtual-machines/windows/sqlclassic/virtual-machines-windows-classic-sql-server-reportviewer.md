---
title: Usare ReportViewer in un sito Web | Microsoft Docs
description: In questo argomento viene descritto come compilare un sito Web di Microsoft Azure con il controllo ReportViewer di Visual Studio che visualizza un report archiviato in una macchina virtuale di Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8f85a4cee6a59316eb5c321007b5b9d562c71302
ms.lasthandoff: 03/25/2017


---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Usare ReportViewer in un sito Web ospitato in Azure
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

È possibile compilare un sito Web di Microsoft Azure con il controllo ReportViewer di Visual Studio che visualizza un report archiviato in una macchina virtuale di Microsoft Azure. Il controllo ReportViewer si trova in un'applicazione Web creata mediante il modello di applicazione Web ASP.NET.

> [!IMPORTANT]
> I modelli di applicazione Web MVC ASP.NET non supportano il controllo ReportViewer.

Per incorporare ReportViewer nel sito Web di Microsoft Azure, è necessario completare le attività seguenti.

* **Aggiungere** gli assembly al pacchetto di distribuzione
* **Configurare** l'autenticazione e l'autorizzazione
* **Pubblicare** l'applicazione Web ASP.NET in Azure

## <a name="prerequisites"></a>Prerequisiti
Consultare la sezione "Indicazioni generali e procedure consigliate" in [SQL Server Business Intelligence in Macchine virtuali di Azure](../classic/ps-sql-bi.md).

> [!NOTE]
> I controlli ReportViewer vengono offerti con Visual Studio Standard Edition o versione successiva. Se si usa Web Developer Express Edition, è necessario installare [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) per usare le funzionalità di runtime di ReportViewer.
> 
> ReportViewer configurato in modalità di elaborazione locale non è supportato in Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Aggiunta di assembly al pacchetto di distribuzione
Quando si ospita l'applicazione ASP.NET in locale, gli assembly di ReportViewer vengono solitamente installati direttamente nella Global Assembly Cache (GAC) del server IIS durante l'installazione di Visual Studio e sono accessibili direttamente dall'applicazione. Tuttavia, quando si ospita l'applicazione ASP.NET nel cloud, Microsoft Azure non consente alcuna installazione nella Global Assembly Cache, pertanto è necessario verificare che gli assembly di ReportViewer siano disponibili localmente per l'applicazione. È possibile eseguire questa operazione aggiungendo i riferimenti agli assembly nel progetto e configurandoli per essere copiati localmente.

In modalità di elaborazione remota il controllo ReportViewer usa gli assembly seguenti:

* **Microsoft.ReportViewer.WebForms.dll**: contiene il codice di ReportViewer necessario per usare ReportViewer nella pagina. Un riferimento per questo assembly viene aggiunto al progetto quando si elimina un controllo ReportViewer in una pagina ASP.NET del progetto.
* **Microsoft.ReportViewer.Common.dll**: contiene le classi usate dal controllo ReportViewer in fase di esecuzione. Non viene automaticamente aggiunto al progetto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Per aggiungere un riferimento a Microsoft.ReportViewer.Common
* Fare clic con il pulsante destro del mouse sul nodo **Riferimenti** del progetto e quindi scegliere **Aggiungi riferimento**, selezionare l'assembly nella scheda .NET e fare clic su **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Per rendere gli assembly accessibili localmente dall'applicazione ASP.NET
1. Nella cartella **Riferimenti** fare clic sull'assembly Microsoft.ReportViewer.Common in modo che vengano visualizzate le proprietà nel riquadro Proprietà.
2. Nel riquadro Proprietà impostare **Copia locale** su True.
3. Ripetere i passaggi 1 e 2 per Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Per ottenere ReportViewer Language Pack
1. Installare Microsoft Report Viewer 2012 Runtime Redistributable Package appropriatamente dall' [Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Selezionare la lingua dall'elenco a discesa. La pagina viene reindirizzata alla pagina dell'area download corrispondente.
3. Fare clic su **Download** per avviare il download di ReportViewerLP.exe.
4. Dopo aver scaricato ReportViewerLP.exe, fare clic su **Esegui** per eseguire immediatamente l'installazione oppure su **Salva** per salvare il file nel computer. Se si fa clic su **Salva**, tenere a mente il nome della cartella in cui si salva il file.
5. Individuare la cartella in cui è stato salvato il file. Fare clic con il pulsante destro del mouse su ReportViewerLP.exe, scegliere **Esegui come amministratore** e quindi fare clic su **Sì**.
6. Dopo aver eseguito ReportViewerLP.exe, si può notare che in c:\windows\assembly sono presenti i file di risorse **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Per configurare il controllo ReportViewer localizzato
1. Scaricare e installare Microsoft Report Viewer 2012 Runtime Redistributable Package attenendosi alle istruzioni specificate in precedenza.
2. Creare la cartella <language> nel progetto e copiare i file di assembly di risorse associati. I file di assembly di risorse da copiare sono: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selezionare i file di assembly di risorse e nel riquadro Proprietà impostare **Copia nella directory di output** su "**Copia sempre**".
3. Impostare la lingua e la lingua dell'interfaccia utente per il progetto Web. Per altre informazioni su come impostare la lingua e la lingua dell'interfaccia utente per una pagina Web ASP.NET, vedere [Procedura: Impostare la lingua e la lingua dell'interfaccia utente per la globalizzazione di pagine Web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configurazione dell'autenticazione e dell'autorizzazione
ReportViewer deve usare le credenziali appropriate per l'autenticazione con il server di report e le credenziali devono essere autorizzate dal server di report per accedere ai report desiderati. Per informazioni sull'autenticazione, vedere il white paper [Controllo visualizzatore di report del servizio di creazione report e server di report basati sulle macchine virtuali di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Pubblicare l'applicazione Web ASP.NET in Azure
Per istruzioni sulla pubblicazione di un'applicazione Web ASP.NET in Azure, vedere la [procedura per la migrazione e la pubblicazione di un'applicazione Web in Azure da Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e l'articolo di [introduzione alle app Web e ASP.NET](../../../app-service-web/web-sites-dotnet-get-started.md).

> [!IMPORTANT]
> Se il comando Aggiungi progetto di distribuzione di Azure o Aggiungi progetto di servizio cloud Azure non viene visualizzato nel menu di scelta rapida in Esplora soluzioni, potrebbe essere necessario modificare il framework di destinazione per il progetto in .NET Framework 4.
> 
> I due comandi offrono essenzialmente la stessa funzionalità. Uno dei due comandi viene visualizzato nel menu di scelta rapida in base alla versione di Microsoft Azure SDK installata.
> 
> 

## <a name="resources"></a>Risorse
[Report di Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence in Macchine virtuali di Azure](../classic/ps-sql-bi.md)

[Usare PowerShell per creare una macchina virtuale di Azure con un server di report in modalità nativa](../classic/ps-sql-report.md)


