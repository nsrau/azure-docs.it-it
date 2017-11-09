---
title: Introduzione alle raccolte di aree di lavoro di Microsoft Power BI
description: Le raccolte di aree di lavoro di Power BI sono un servizio di Azure che consente agli sviluppatori di applicazioni di aggiungere report interattivi di Power BI alle proprie applicazioni.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: asaxton
ms.openlocfilehash: 4ee113ed25142507f381d8c9d49b25ee6553c525
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Introduzione alle raccolte di aree di lavoro di Microsoft Power BI

Le **raccolte di aree di lavoro di Power BI** sono un servizio di Azure che consente agli sviluppatori di applicazioni di aggiungere report interattivi di Power BI alle proprie applicazioni. Le **raccolte di aree di lavoro di Power BI** interagiscono con le applicazioni esistenti senza che sia necessario riprogettarle o modificare la modalità di accesso da parte degli utenti.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto della raccolta di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Il provisioning delle risorse per le **raccolte di aree di lavoro di Microsoft Power BI** viene effettuato tramite le [API di Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso, la risorsa di cui si effettua il provisioning è una **Raccolta di aree di lavoro di Power BI**.

![Flusso generale di raccolte di aree di lavoro di Power BI](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Creare una raccolta di aree di lavoro

Una **Raccolta di aree di lavoro** è la risorsa di primo livello di Azure e un contenitore per il contenuto che verrà incorporato nell'applicazione. Una **Raccolta di aree di lavoro** può essere creata in due modi:

* Uso manuale del portale di Azure
* Uso delle API di Azure Resource Manager a livello di codice

Di seguito è descritta la procedura per compilare una **Raccolta di aree di lavoro** tramite il portale di Azure.

1. Aprire il **portale di Azure**ed eseguire l'accesso: [http://portal.azure.com](http://portal.azure.com).
2. Selezionare **+ Nuovo** nel pannello in alto.
   
   ![+ Nuovo nel portale di Azure](media/get-started/create-workspace-1.png)
3. In **Dati e analisi** selezionare **Raccolta di aree di lavoro di Power BI**.
4. All'interno del messaggio introduttivo, se è già disponibile una sottoscrizione esistente di Raccolta di aree di lavoro di Power BI, selezionare **Creare una raccolta di aree di lavoro** in basso.

5. In **Raccolta di aree di lavoro** immettere le informazioni necessarie.
   
   ![Creazione della raccolta di aree di lavoro](media/get-started/create-workspace-2.png)
1. Selezionare **Crea**.

Il provisioning della **Raccolta di aree di lavoro** richiede alcuni istanti. Al termine viene visualizzata la **Raccolta di aree di lavoro**.

   ![Raccolta di aree di lavoro nel portale di Azure](media/get-started/create-workspace-3.png)

I risultati della **creazione** contengono le informazioni necessarie per chiamare le API che creano le aree di lavoro e vi distribuiscono il contenuto.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Visualizzare le chiavi di accesso dell'API Power BI

Le **chiavi di accesso**sono tra le informazioni più importanti necessarie per chiamare le API REST Power BI. Vengono usate per generare i **token delle app** che servono per autenticare le richieste delle API. Per visualizzare le **chiavi di accesso** fare clic su **Chiavi di accesso** in **Impostazioni**. Per altre informazioni sui **token delle app**, vedere [Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md).

   ![Chiavi di accesso nell'impostazione Raccolta di aree di lavoro nel portale di Azure](media/get-started/access-keys.png)

Come si noterà, sono disponibili due chiavi.

   ![Due chiavi in Chiavi di accesso](media/get-started/access-keys-2.png)

Copiarle e archiviarle in modo sicuro nell'applicazione. È importante gestire queste chiavi come una password, perché forniscono l'accesso a tutto il contenuto della **Raccolta di aree di lavoro**.

Anche se sono disponibili due chiavi, è necessaria una sola chiave alla volta. La seconda chiave viene fornita per consentire di rigenerare periodicamente le chiavi senza interrompere l'accesso al servizio.

Dopo aver creato un'istanza di Power BI per l'applicazione e le **chiavi di accesso**, è possibile importare un report nell'app in uso. Prima di apprendere come importare un report, la sezione successiva descrive come creare set di dati e report di Power BI da incorporare in un'app.

## <a name="working-with-workspaces"></a>Utilizzo di aree di lavoro

Dopo aver creato la raccolta di aree di lavoro, è necessario creare un'area di lavoro che dovrà contenere i report e i set di dati. Per creare un'area di lavoro, usare l'[API REST di pubblicazione area di lavoro](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Creare set di dati e report di Power BI da incorporare in un'app usando Power BI Desktop

Dopo avere creato un'istanza di Power BI per l'applicazione e le **chiavi di accesso**, è necessario creare i set di dati e i report di Power BI da incorporare. I set di dati e i report possono essere creati con **Power BI Desktop**. È possibile scaricare [Power BI Desktop gratuitamente](https://go.microsoft.com/fwlink/?LinkId=521662). In alternativa, per iniziare rapidamente, è possibile scaricare l' [esempio Retail Analysis PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Per altre informazioni sull'uso di **Power BI Desktop**, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

**Power BI Desktop** consente di connettersi all'origine dati importando una copia dei dati in **Power BI Desktop** o tramite la connessione diretta all'origine dati con **DirectQuery**.

Di seguito sono spiegate le differenze tra l'**importazione** e la modalità **DirectQuery**.

| Importa | DirectQuery |
| --- | --- |
| Tabelle, colonne, *e dati* vengono importati o copiati in **Power BI Desktop**. Mentre si usano le visualizzazioni, **Power BI Desktop** esegue query su una copia dei dati. Per visualizzare le eventuali modifiche apportate ai dati sottostanti, è necessario aggiornare o importare di nuovo un set di dati completo e aggiornato. |Solo *tabelle e colonne* vengono importate o copiate in **Power BI Desktop**. Mentre si usano le visualizzazioni, **Power BI Desktop** esegue query sull'origine dati sottostante, quindi vengono sempre visualizzati dati aggiornati. |

Per altre informazioni sulla connessione a un'origine dati, vedere [Connettersi a un'origine dati](connect-datasource.md).

Dopo aver salvato il lavoro in **Power BI Desktop**viene creato un file PBIX. Questo file contiene il report. Se si importano dati, il PBIX include il set di dati completo, mentre se si usa **DirectQuery**, il PBIX contiene solo uno schema del set di dati. Distribuire a livello di codice il PBIX nell'area di lavoro con l' [API di importazione di Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> Le **raccolte di aree di lavoro di Power BI** includono altre API per modificare il server e il database a cui punta il set di dati e impostare le credenziali dell'account del servizio che saranno usate dal set di dati per la connessione al database. Vedere [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) (POST di SetAllConnections) e [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (PATCH dell'origine dati del gateway).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Creare set di dati e report di Power BI usando le API

### <a name="datasets"></a>DATASETS

È possibile creare set di dati all'interno delle raccolte di aree di lavoro di Power BI usando l'API REST. ed effettuare poi il push dei dati nel set di dati. In questo modo è possibile usare i dati senza Power BI Desktop. Per altre informazioni, vedere [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Pubblica set di dati).

### <a name="reports"></a>Report

È possibile creare un report da un set di dati direttamente nell'applicazione usando l'API JavaScript. Per altre informazioni, vedere [Create a new report from a dataset in Power BI Workspace Collections](create-report-from-dataset.md) (Creare un nuovo report da un set di dati nelle raccolte di aree di lavoro di Power BI).

## <a name="see-also"></a>Vedere anche

[Esempio introduttivo](get-started-sample.md)  
[Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md)  
[Incorporare un report](embed-report.md)  
[Create a new report from a dataset in Power BI Workspace Collections (Creare un nuovo report da un set di dati nelle raccolte di aree di lavoro di Power BI)](create-report-from-dataset.md)
[Salvare i report](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)

