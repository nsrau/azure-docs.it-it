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
ms.openlocfilehash: e87fc5b91e9e1d5f4f8449e84b17bcdab9c0b6b2
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713595"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installare gli strumenti di Analisi di flusso di Azure per Visual Studio
Gli strumenti di Analisi di flusso di Azure supportano Visual Studio 2017, 2015 e 2013. Questo articolo descrive come installare e disinstallare gli strumenti.

Per altre informazioni sull'uso degli strumenti, vedere [Usare gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installa
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Scaricare [Visual Studio 2017 (versione 15.3 o successiva)](https://www.visualstudio.com/). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Gli strumenti di Analisi di flusso fanno parte dei carichi di lavoro **Sviluppo di Azure**, **Elaborazione ed archiviazione dati** in Visual Studio 2017. Abilitare uno di questi due carichi di lavoro durante l'installazione di Visual Studio.

Abilitare il carico di lavoro **Elaborazione ed archiviazione dati** come illustrato:

![Il carico di lavoro Elaborazione ed archiviazione dati è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Abilitare il carico di lavoro **Sviluppo di Azure**, come illustrato:

![Il carico di lavoro Sviluppo di Azure è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Installare Visual Studio 2015 o Visual Studio 2013 Update 4. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Installare Microsoft Azure SDK per .NET versione 2.7.1 o successiva usando [Installazione guidata piattaforma Web Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).
* Installare gli [strumenti di Analisi di flusso di Azure per Visual Studio](http://aka.ms/asatoolsvs).

## <a name="update"></a>Aggiornamento

### <a name="visual-studio-2017"></a>Visual Studio 2017
Il promemoria della nuova versione viene visualizzato nella notifica di Visual Studio. 

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 e Visual Studio 2015
Gli strumenti di Analisi di flusso di Azure installati per Visual Studio verificano automaticamente la disponibilità di nuove versioni. Seguire le istruzioni nella finestra popup per installare la versione più recente. 


## <a name="uninstall"></a>Disinstallare

### <a name="visual-studio-2017"></a>Visual Studio 2017
Fare doppio clic sul programma di installazione di Visual Studio e selezionare **Modifica**. Deselezionare la casella di controllo **Strumenti per Azure Data Lake e analisi di flusso** dal carico di lavoro **Elaborazione ed archiviazione dati** o **Sviluppo di Azure**.

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 e Visual Studio 2015
Nel pannello di controllo disinstallare **Strumenti per Azure Data Lake e analisi di flusso**.





