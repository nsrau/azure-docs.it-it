---
title: Installare gli strumenti di Analisi di flusso di Azure per Visual Studio
description: Questo articolo descrive i requisiti di installazione degli strumenti di Analisi di flusso di Azure per Visual Studio e illustra come installare questi ultimi.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343911"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installare gli strumenti di Analisi di flusso di Azure per Visual Studio
Gli strumenti di Analisi di flusso di Azure supportano Visual Studio 2017, 2015 e 2013. Questo articolo descrive come installare e disinstallare gli strumenti.

Per altre informazioni sull'uso degli strumenti, vedere [Usare gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installa
### <a name="recommended-visual-studio-2019-and-2017"></a>Consigliato: Visual Studio 2019 e 2017
* Scaricare [Visual Studio 2019 (Preview 2 o versione successiva) e Visual Studio 2017 (15.3 o versione successiva)](https://www.visualstudio.com/). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. Visual Studio 2017 su Mac non è supportato. 
* Gli strumenti di Analisi di flusso fanno parte dei carichi di lavoro **Sviluppo di Azure**, **Elaborazione ed archiviazione dati** in Visual Studio 2017. Abilitare uno di questi due carichi di lavoro durante l'installazione di Visual Studio.

Abilitare il carico di lavoro **Elaborazione ed archiviazione dati** come illustrato:

![Il carico di lavoro Elaborazione ed archiviazione dati è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Abilitare il carico di lavoro **Sviluppo di Azure**, come illustrato:

![Il carico di lavoro Sviluppo di Azure è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* Scegliere **Estensioni e aggiornamenti** dal menu Strumenti. Trovare gli strumenti di Azure Data Lake e Analisi di flusso nelle estensioni installate e fare clic su **Aggiorna** per installare l'estensione più recente. 

![Estensioni e aggiornamenti di Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Installare Visual Studio 2015 o Visual Studio 2013 Update 4. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Installare Microsoft Azure SDK per .NET versione 2.7.1 o successiva usando [Installazione guidata piattaforma Web Microsoft](https://www.microsoft.com/web/downloads/platform.aspx).
* Installare gli [strumenti di Analisi di flusso di Azure per Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aggiornamento

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 e 2017
Il promemoria della nuova versione viene visualizzato nella notifica di Visual Studio.

![Promemoria della nuova versione di Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 e 2013
Gli strumenti di Analisi di flusso di Azure installati per Visual Studio verificano automaticamente la disponibilità di nuove versioni. Seguire le istruzioni nella finestra popup per installare la versione più recente. 


## <a name="uninstall"></a>Disinstallare

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 e 2017
Fare doppio clic sul programma di installazione di Visual Studio e selezionare **Modifica**. Deselezionare la casella di controllo **Strumenti per Azure Data Lake e analisi di flusso** dal carico di lavoro **Elaborazione ed archiviazione dati** o **Sviluppo di Azure**.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 e 2013
Nel pannello di controllo disinstallare **Strumenti per Azure Data Lake e analisi di flusso**.





