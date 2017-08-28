---
title: Accesso ai cloud privati di Azure con Visual Studio | Microsoft Docs
description: Informazioni su come accedere alle risorse del cloud privato con Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: kraigb
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b29a3299433e57709ea31de3c7849230ea09c09a
ms.contentlocale: it-it
ms.lasthandoff: 03/21/2017

---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Accesso ai cloud privati di Azure con Visual Studio
Per impostazione predefinita, Visual Studio supporta gli endpoint REST del cloud pubblico di Azure. Questo argomento illustra come usare il certificato del cloud privato per accedere e usare il cloud privato da Visual Studio.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Per accedere a un cloud privato di Azure in Visual Studio
1. Nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885) per il cloud privato scaricare il file di impostazioni di pubblicazione o contattare l'amministratore per ottenerne uno. Nella versione pubblica di Azure, il collegamento da cui scaricare questo file è [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). L'estensione del file scaricato deve essere `.publishsettings`.

1. Aprire Visual Studio.

1. In **Esplora server** fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi selezionare **Gestisci e filtra sottoscrizioni** nel menu di scelta rapida.
   
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
   
## <a name="next-steps"></a>Passaggi successivi
- [Pubblicazione in un servizio cloud di Azure da Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Procedura: Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)

