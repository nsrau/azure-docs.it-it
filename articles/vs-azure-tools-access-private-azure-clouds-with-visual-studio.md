---
title: Accesso ai cloud privati di Azure con Visual Studio | Microsoft Docs
description: Informazioni su come accedere alle risorse del cloud privato con Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 7067a9fddbb10f3c94d1104a5b81aeeaad6d0291
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968639"
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Accesso ai cloud privati di Azure con Visual Studio

Per impostazione predefinita, Visual Studio supporta gli endpoint REST del cloud di Azure. Questo articolo illustra come usare il certificato del cloud privato per accedere e usare il cloud privato da Visual Studio.

1. Nel portale di Azure per il cloud privato scaricare il file di impostazioni di pubblicazione o contattare l'amministratore per ottenerne uno. Il file ha l'estensione `.publishsettings`.

1. In **Esplora server** di Visual Studio fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi selezionare **Gestisci e filtra sottoscrizioni**.

    ![Comando Gestisci sottoscrizioni](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Nella finestra di dialogo **Gestisci sottoscrizioni Microsoft Azure** selezionare la scheda **Certificati** e quindi selezionare **Importa**.

    ![Importazione di certificati di Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Nella finestra di dialogo **Importa sottoscrizioni Microsoft Azure** selezionare **Sfoglia**.

    ![Pulsante Sfoglia nella finestra di dialogo Importa sottoscrizioni Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. Nella finestra di dialogo **Apri** passare alla directory in cui è stato salvato il file di impostazioni di pubblicazione, selezionare il file e quindi selezionare **Apri**.

    ![Selezionare il file di impostazioni di pubblicazione](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Dopo essere tornati nella finestra di dialogo **Importa sottoscrizioni Microsoft Azure** selezionare **Importa**.

    ![Importare il file di impostazioni di pubblicazione](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    I certificati vengono importati dal file di impostazioni di pubblicazione in Visual Studio, consentendo così di interagire con le risorse del cloud privato.

