---
title: Introduzione a Microsoft Power BI Embedded
description: Power BI Embedded, aggiungere report di Power BI interattivi nell&quot;applicazione di Business Intelligence
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 02/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: bd7925f3fa9a717cbe0649bf899cdd00511d5ca6
ms.openlocfilehash: b9dff45d1bb60d50c882c6daf363fca86a7f8f4c
ms.lasthandoff: 02/22/2017


---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introduzione a Microsoft Power BI Embedded
**Power BI Embedded** è un servizio di Azure che consente agli sviluppatori di applicazioni di aggiungere report interattivi di Power BI alle proprie applicazioni. **Power BI Embedded** interagisce con le applicazioni esistenti senza che sia necessario riprogettarle o modificare la modalità di accesso da parte degli utenti.

Il provisioning delle risorse di **Microsoft Power BI Embedded** viene effettuato tramite le [API di Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso, la risorsa di cui si effettua il provisioning è una **Raccolta di aree di lavoro di Power BI**.

![](media/power-bi-embedded-get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Creare una raccolta di aree di lavoro
Una **Raccolta di aree di lavoro** è la risorsa di primo livello di Azure e un contenitore per il contenuto che verrà incorporato nell'applicazione. Una **Raccolta di aree di lavoro** può essere creata in due modi:

* Uso manuale del portale di Azure
* Uso delle API di Azure Resource Manager a livello di codice

Di seguito è descritta la procedura per compilare una **Raccolta di aree di lavoro** tramite il portale di Azure.

1. Aprire il **portale di Azure**ed eseguire l'accesso: [http://portal.azure.com](http://portal.azure.com).
2. Fare clic su **+ Nuovo** nel pannello in alto.
   
   ![](media/power-bi-embedded-get-started/create-workspace-1.png)
3. In **Dati e analisi** fare clic su **Power BI Embedded**.
4. Nel **pannello della Raccolta di aree di lavoro** immettere le informazioni necessarie. Per **Prezzi**, vedere [Prezzi di Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).
   
   ![](media/power-bi-embedded-get-started/create-workspace-2.png)
5. Fare clic su **Create**.

Il provisioning della **Raccolta di aree di lavoro** richiederà alcuni istanti. Al termine verrà visualizzato il **pannello della Raccolta di aree di lavoro**.

   ![](media/power-bi-embedded-get-started/create-workspace-3.png)

Questo **pannello di creazione** contiene le informazioni necessarie per chiamare le API che creano le aree di lavoro e vi distribuiscono il contenuto.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Visualizzare le chiavi di accesso dell'API Power BI
Le **chiavi di accesso**sono tra le informazioni più importanti necessarie per chiamare le API REST Power BI. Vengono usate per generare i **token delle app** che servono per autenticare le richieste delle API. Per visualizzare le **chiavi di accesso** fare clic su **Chiavi di accesso** nel pannello **Impostazioni**. Per altre informazioni sui **token delle app**, vedere [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md).

   ![](media/power-bi-embedded-get-started/access-keys.png)

Come si noterà, sono disponibili due chiavi.

   ![](media/power-bi-embedded-get-started/access-keys-2.png)

Copiarle e archiviarle in modo sicuro nell'applicazione. È molto importante gestire queste chiavi come una password, perché forniscono l'accesso a tutto il contenuto della **Raccolta di aree di lavoro**.

Anche se sono disponibili due chiavi, è necessaria una sola chiave alla volta. La seconda chiave viene fornita per consentire di rigenerare periodicamente le chiavi senza interrompere l'accesso al servizio.

Dopo aver creato un'istanza di Power BI per l'applicazione e le **chiavi di accesso**, è possibile importare un report nell'app in uso. Prima di apprendere come importare un report, la sezione successiva descrive come creare set di dati e report di Power BI da incorporare in un'app.

## <a name="working-with-workspaces"></a>Utilizzo di aree di lavoro

Dopo aver creato la raccolta di aree di lavoro, è necessario creare un'area di lavoro che dovrà contenere i report e i set di dati. Per creare un'area di lavoro, usare l'[API REST di pubblicazione area di lavoro](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Creare set di dati e report di Power BI da incorporare in un'app
Dopo avere creato un'istanza di Power BI per l'applicazione e le **chiavi di accesso**, è necessario creare i set di dati e i report di Power BI da incorporare. I set di dati e i report possono essere creati con **Power BI Desktop**. È possibile scaricare [Power BI Desktop gratuitamente](https://go.microsoft.com/fwlink/?LinkId=521662). In alternativa, per iniziare rapidamente, è possibile scaricare l' [esempio Retail Analysis PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Per altre informazioni sull'uso di **Power BI Desktop**, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

**Power BI Desktop** consente di connettersi all'origine dati importando una copia dei dati in **Power BI Desktop** o tramite la connessione diretta all'origine dati con **DirectQuery**.

Di seguito sono spiegate le differenze tra l'**importazione** e la modalità **DirectQuery**.

| Importa | DirectQuery |
| --- | --- |
| Tabelle, colonne, *e dati* vengono importati o copiati in **Power BI Desktop**. Mentre si usano le visualizzazioni, **Power BI Desktop** esegue query su una copia dei dati. Per visualizzare le eventuali modifiche apportate ai dati sottostanti, è necessario aggiornare o importare di nuovo un set di dati completo e aggiornato. |Solo *tabelle e colonne* vengono importate o copiate in **Power BI Desktop**. Mentre si usano le visualizzazioni, **Power BI Desktop** esegue query sull'origine dati sottostante, quindi vengono sempre visualizzati dati aggiornati. |

Per altre informazioni sulla connessione a un'origine dati, vedere [Connettersi a un'origine dati](power-bi-embedded-connect-datasource.md).

Dopo aver salvato il lavoro in **Power BI Desktop**viene creato un file PBIX. Questo file contiene il report. Se si importano dati, il PBIX include il set di dati completo, mentre se si usa **DirectQuery**, il PBIX contiene solo uno schema del set di dati. Distribuire a livello di codice il PBIX nell'area di lavoro con l' [API di importazione di Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Power BI Embedded** include altre API per modificare il server e il database a cui punta il set di dati e impostare le credenziali dell'account del servizio che saranno usate dal set di dati per la connessione al database. Vedere [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) (POST di SetAllConnections) e [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (PATCH dell'origine dati del gateway).

## <a name="next-steps"></a>Passaggi successivi
Nei passaggi precedenti è stata creata una raccolta di aree di lavoro e i primi report e set di dati. A questo punto è possibile apprendere come scrivere codice per **Power BI Embedded**. Per iniziare è stata creata un'app Web da usare come [esempio introduttivo](power-bi-embedded-get-started-sample.md). L'esempio illustra come:

* Effettuare il provisioning del contenuto
  * Creare un'area di lavoro
  * Importare un file PBIX
  * Aggiornare le stringhe di connessione e impostare le credenziali per i set di dati.
* Incorporare un report in modo sicuro

## <a name="see-also"></a>Vedere anche
* [Esempio introduttivo](power-bi-embedded-get-started-sample.md)
* [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)


