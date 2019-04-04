---
title: Eseguire la distribuzione in Azure Analysis Services con Visual Studio (SSDT) | Microsoft Docs
description: Informazioni su come distribuire un modello tabulare in un server Azure Analysis Services usando SSDT.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b9740d74a25964286ea92b4238684db81a64c9e2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893606"
---
# <a name="deploy-a-model-from-visual-studio"></a>Distribuire un modello da Visual Studio

Dopo aver creato un server nella sottoscrizione di Azure, si è pronti per distribuire un database modello tabulare a tale server. È possibile usare SQL Server Data Tools (SSDT) per compilare e distribuire un progetto modello tabulare in uso. 

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Un **server Analysis Services** in Azure. Per altre informazioni, vedere [Creare un server Azure Analysis Services](analysis-services-create-server.md).
* Un **progetto di modello tabulare** in SSDT o un modello tabulare esistente con livello di compatibilità 1200 o superiore. Se non è mai stato creato un progetto simile, Vedere [Adventure Works Internet sales tabular modeling tutorial](/sql/analysis-services/tabular-modeling-adventure-works-tutorial) (Esercitazione sul modello tabulare di vendite Internet per Adventure Works).
* Un **gateway locale**: se una o più origini dati si trovano nella rete locale dell'organizzazione, è necessario installare un [gateway dati locale](analysis-services-gateway.md). Il gateway è necessario affinché il server nel cloud possa connettersi alle origini dati locali per elaborare e aggiornare i dati nel modello.

> [!TIP]
> Prima di distribuire, assicurarsi che sia possibile elaborare i dati nelle tabelle. In SSDT fare clic su **Modello** > **Elabora** > **Elabora tutto**. Se l'elaborazione ha esito negativo, la distribuzione non potrà essere eseguita.
> 
> 

## <a name="get-the-server-name"></a>Ottenere il nome del server

In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Per distribuire da SSDT

1. In SSDT > **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto > **Proprietà**. Quindi in **Distribuzione** > **Server** incollare il nome del server.   
   
    ![Incollare il nome del server nelle proprietà del server di distribuzione](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Proprietà** e quindi scegliere **Distribuisci**. Verrà visualizzata la richiesta di accedere ad Azure.
   
    ![Distribuire al server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Lo stato di distribuzione viene visualizzato sia nella finestra di output che nella distribuzione.
   
    ![Stato della distribuzione](./media/analysis-services-deploy/aas-deploy-status.png)

Questo è tutto ciò che occorre fare.


## <a name="troubleshooting"></a>risoluzione dei problemi

Se la distribuzione non riesce durante la distribuzione dei metadati, probabilmente è dovuto al fatto che SSDT non può connettersi al server. Assicurarsi di potersi connettere al server usando SSMS. Verificare quindi che la proprietà del server di distribuzione per il progetto sia corretta.

Se la distribuzione non riesce in una tabella, probabilmente è dovuto al fatto che il server non ha potuto connettersi a un'origine dati. Se l'origine dati è locale nella rete dell'organizzazione, assicurarsi di installare un [gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che il modello tabulare è stato distribuito nel server, si è pronti per la connessione. È possibile [connettersi al modello con SSMS](analysis-services-manage.md) per gestirlo. Ed è anche possibile [connettersi al modello usando uno strumento client](analysis-services-connect.md) come Power BI, Power BI Desktop o Excel e avviare la creazione di report.

