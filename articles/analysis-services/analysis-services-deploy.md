---
title: Distribuire un modello per Azure Analysis Services usando Visual Studio | Microsoft Docs
description: Informazioni su come distribuire un modello tabulare in un server di Azure Analysis Services usando Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572886"
---
# <a name="deploy-a-model-from-visual-studio"></a>Distribuire un modello da Visual Studio

Dopo aver creato un server nella sottoscrizione di Azure, si è pronti per distribuire un database modello tabulare a tale server. È possibile utilizzare Visual Studio con i progetti Analysis Services per compilare e distribuire un progetto di modello tabulare su cui si sta lavorando. 

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Un **server Analysis Services** in Azure. Per altre informazioni, vedere [Creare un server Azure Analysis Services](analysis-services-create-server.md).
* **Progetto di modello tabulare** in Visual Studio o un modello tabulare esistente a livello di compatibilità 1200 o superiore. Se non è mai stato creato un progetto simile, Vedere [Adventure Works Internet sales tabular modeling tutorial](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) (Esercitazione sul modello tabulare di vendite Internet per Adventure Works).
* Un **gateway locale**: se una o più origini dati si trovano nella rete locale dell'organizzazione, è necessario installare un [gateway dati locale](analysis-services-gateway.md). Il gateway è necessario affinché il server nel cloud possa connettersi alle origini dati locali per elaborare e aggiornare i dati nel modello.

> [!TIP]
> Prima di distribuire, assicurarsi che sia possibile elaborare i dati nelle tabelle. In Visual Studio fare clic su **modello** > **processo** > **elabora tutto**. Se l'elaborazione ha esito negativo, la distribuzione non potrà essere eseguita.
> 
> 

## <a name="get-the-server-name"></a>Ottenere il nome del server

In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Per eseguire la distribuzione da Visual Studio

1. In Visual Studio > **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto > **Proprietà**. Quindi in **Distribuzione** > **Server** incollare il nome del server.   
   
    ![Incollare il nome del server nelle proprietà del server di distribuzione](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Proprietà** e quindi scegliere **Distribuisci**. Verrà visualizzata la richiesta di accedere ad Azure.
   
    ![Distribuire al server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Lo stato di distribuzione viene visualizzato sia nella finestra di output che nella distribuzione.
   
    ![Stato della distribuzione](./media/analysis-services-deploy/aas-deploy-status.png)

Questo è tutto ciò che occorre fare.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se la distribuzione non riesce durante la distribuzione dei metadati, è probabile che Visual Studio non sia in grado di connettersi al server. Assicurarsi di potersi connettere al server usando SSMS. Verificare quindi che la proprietà del server di distribuzione per il progetto sia corretta.

Se la distribuzione non riesce in una tabella, probabilmente è dovuto al fatto che il server non ha potuto connettersi a un'origine dati. Se l'origine dati è locale nella rete dell'organizzazione, assicurarsi di installare un [gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che il modello tabulare è stato distribuito nel server, si è pronti per la connessione. È possibile [connettersi ad esso con SQL Server Management Studio (SSMS)](analysis-services-manage.md) per gestirlo. Ed è anche possibile [connettersi al modello usando uno strumento client](analysis-services-connect.md) come Power BI, Power BI Desktop o Excel e avviare la creazione di report.

