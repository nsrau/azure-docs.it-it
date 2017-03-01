---
title: Informazioni di riferimento per l&quot;esplorazione del portale di Azure
description: Apprendere le diverse esperienze utente per il servizio per app Web tra il portale di gestione e il portale di Azure
services: app-service
documentationcenter: 
author: jaime-espinosa
manager: erikre
editor: jimbe
ms.assetid: 0cc6a3cc-bd89-4a96-9177-d25f6fb737bb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: jaime-espinosa
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 00617f4d508c843000d69c02464a1bef36320a8e
ms.lasthandoff: 01/20/2017


---
# <a name="reference-for-navigating-the-azure-portal"></a>Informazioni di riferimento per l'esplorazione del portale di Azure
Siti Web di Azure è ora denominato [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). Stiamo aggiornando tutta la documentazione per evidenziare questo cambiamento di nome e per fornire istruzioni per il Portale di Azure. Fino al completamento di tale processo, è possibile usare questo documento come guida per lavorare con le app Web nel portale di Azure.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="the-future-of-the-azure-classic-portal"></a>Il futuro del portale di Azure classico
Sebbene le modifiche di personalizzazione siano visibili anche nel portale di Azure classico, è in corso la sostituzione di quest'ultimo con il portale di Azure. Il portale classico verrà eventualmente eliminato e tutte le nuove attività correlate allo sviluppo verranno spostate e proseguiranno nel portale di Azure. Tutte le nuove funzionalità per app Web in arrivo verranno ospitate nel portale di Azure. Iniziare subito a usare il portale di Azure per sfruttare i vantaggi offerti dalle ultime e migliori app Web.

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Differenze del layout tra il portale di Azure classico e il portale di Azure
Nel portale classico, tutti i servizi di Azure sono elencati nella parte sinistra. La navigazione nel portale classico segue una struttura ad albero, in cui si inizia dal servizio e si naviga all'interno di ogni elemento. Questa struttura funziona bene quando si gestiscono componenti indipendenti. Le applicazioni create in Azure, tuttavia, sono una raccolta di servizi interconnessi e questa struttura ad albero non risulta ideale per lavorare con raccolte di servizi. 

Il portale di Azure semplifica la creazione di applicazioni end-to-end con componenti provenienti da più servizi. Il portale è organizzato in *esplorazioni*. Un'*esplorazione* è una serie di *pannelli*, ovvero contenitori per i diversi componenti. La configurazione della scalabilità automatica per un'app Web, ad esempio, è un'*esplorazione* che ospita diversi pannelli, come illustrato nell'esempio seguente: il pannello del **sito Web** (con il titolo non ancora aggiornato alla nuova terminologia), il pannello **Impostazioni** e il pannello **Aumenta istanze**. In questo esempio la scalabilità automatica viene configurata secondo l'uso della CPU ed è pertanto presente anche un pannello **Percentuale CPU** . I componenti all'interno dei *pannelli* sono denominati *parti* e sono simili ai riquadri. 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>Esempio di navigazione: creare un'app Web
La creazione di app Web resta un'operazione semplicissima. La figura seguente illustra il portale classico e il portale affiancati e dimostra che ben poco è cambiato quanto al numero di passaggi necessari per creare un'app Web e renderla operativa. 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

Nel portale è possibile scegliere tra i tipi di app Web più comuni, comprese applicazioni note da raccolte come WordPress. Per un elenco completo delle applicazioni disponibili, visitare [Azure Marketplace].

Per creare un'app Web si specificano l'URL, il piano di servizio app e la posizione nel portale esattamente come avviene nel portale classico. 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

Nel portale è inoltre possibile definire altre impostazioni comuni. Ad esempio i [gruppi di risorse](../azure-resource-manager/resource-group-overview.md) semplificano la visualizzazione e la gestione delle risorse di Azure correlate. 

## <a name="navigation-example-settings-and-features"></a>Esempio di navigazione: impostazioni e funzionalità
Tutte le impostazioni e funzionalità sono ora raggruppate logicamente in un unico riquadro, da cui è possibile avviare una navigazione.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Ad esempio, è possibile creare domini personalizzati facendo clic su **Domini personalizzati ed SSL** nel pannello **Impostazioni**.

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Per impostare una notifica di monitoraggio, fare clic su **Richieste ed errori** e quindi su **Aggiungi avviso**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Per abilitare la diagnostica, fare clic su **Log di diagnostica** nel pannello **Impostazioni**.

![](./media/app-service-web-app-azure-portal/Diagnostics.png)

Per configurare le impostazioni dell'applicazione, fare clic su **Impostazioni dell'applicazione** nel pannello **Impostazioni**. 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Oltre al nome del marchio, alcuni altri elementi nel portale sono stati rinominati o raggruppati in modo diverso per semplificarne l'individuazione. Ad esempio, quella riportata di seguito è una schermata della pagina corrispondente a quella delle impostazioni dell'app (**Configura**) nel portale classico.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>Altre risorse
[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)


