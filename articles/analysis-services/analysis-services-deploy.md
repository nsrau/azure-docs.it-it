---
title: Eseguire la distribuzione in Azure Analysis Services con SSDT | Microsoft Docs
description: Informazioni su come distribuire un modello tabulare in un server Azure Analysis Services usando SSDT.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: fad1994791f9766282db2a9db569f64473e1c15a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="deploy-a-model-from-ssdt"></a>Distribuire un modello da SSDT
Dopo aver creato un server nella sottoscrizione di Azure, si è pronti per distribuire un database modello tabulare a tale server. È possibile usare SQL Server Data Tools (SSDT) per compilare e distribuire un progetto modello tabulare in uso. 

## <a name="prerequisites"></a>prerequisiti
Per iniziare, è necessario:

* Un **server Analysis Services** in Azure. Per altre informazioni, vedere [Creare un server Azure Analysis Services](analysis-services-create-server.md).
* Un **progetto di modello tabulare** in SSDT o un modello tabulare esistente con livello di compatibilità 1200 o superiore. Se non è mai stato creato un progetto simile, Vedere [Adventure Works Internet sales tabular modeling tutorial](https://msdn.microsoft.com/library/hh231691.aspx) (Esercitazione sul modello tabulare di vendite Internet per Adventure Works).
* Un **gateway locale**: se una o più origini dati si trovano nella rete locale dell'organizzazione, è necessario installare un [gateway dati locale](analysis-services-gateway.md). Il gateway è necessario affinché il server nel cloud possa connettersi alle origini dati locali per elaborare e aggiornare i dati nel modello.

> [!TIP]
> Prima di distribuire, assicurarsi che sia possibile elaborare i dati nelle tabelle. In SSDT fare clic su **Modello** > **Elabora** > **Elabora tutto**. Se l'elaborazione ha esito negativo, la distribuzione non potrà essere eseguita.
> 
> 

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Per distribuire un modello tabulare da SSDT

1. Prima di distribuire, è necessario ottenere il nome del server. In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
    ![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. In SSDT > **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto > **Proprietà**. Quindi in **Distribuzione** > **Server** incollare il nome del server.   
   
    ![Incollare il nome del server nelle proprietà del server di distribuzione](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Proprietà** e quindi scegliere **Distribuisci**. Verrà visualizzata la richiesta di accedere ad Azure.
   
    ![Distribuire al server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Lo stato di distribuzione viene visualizzato sia nella finestra di output che nella distribuzione.
   
    ![Stato della distribuzione](./media/analysis-services-deploy/aas-deploy-status.png)

Questo è tutto ciò che occorre fare.


## <a name="troubleshooting"></a>risoluzione dei problemi
Se la distribuzione non riesce durante la distribuzione dei metadati, probabilmente è dovuto al fatto che SSDT non può connettersi al server. Assicurarsi di potersi connettere al server usando SSMS. Verificare quindi che la proprietà del server di distribuzione per il progetto sia corretta.

Se la distribuzione non riesce in una tabella, probabilmente è dovuto al fatto che il server non ha potuto connettersi a un'origine dati. Se l'origine dati è locale nella rete dell'organizzazione, assicurarsi di installare un [gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi
Ora che il modello tabulare è stato distribuito nel server, si è pronti per la connessione. È possibile [connettersi al modello con SSMS](analysis-services-manage.md) per gestirlo. Ed è anche possibile [connettersi al modello usando uno strumento client](analysis-services-connect.md) come Power BI, Power BI Desktop o Excel e avviare la creazione di report.

