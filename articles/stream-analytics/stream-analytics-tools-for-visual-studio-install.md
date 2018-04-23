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
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installare gli strumenti di Analisi di flusso di Azure per Visual Studio
Gli strumenti di Analisi di flusso di Azure supportano ora Visual Studio 2017, 2015 e 2013. Questo documento descrive come installare e disinstallare gli strumenti.

Per altre informazioni sull'uso degli strumenti, vedere [Usare gli strumenti di Analisi di flusso di Azure per Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Installa
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Scaricare [Visual Studio 2017 (versione 15.3 o successiva)](https://www.visualstudio.com/). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Gli strumenti di Analisi di flusso fanno parte dei carichi di lavoro **Sviluppo di Azure**, **Elaborazione ed archiviazione dati** in Visual Studio 2017. Abilitare uno di questi due carichi di lavoro durante l'installazione di Visual Studio.

Abilitare il carico di lavoro **Elaborazione ed archiviazione dati** come illustrato:

![Carico di lavoro Elaborazione ed archiviazione dati](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Abilitare il carico di lavoro **Sviluppo di Azure**, come illustrato:

![Carico Sviluppo di Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Installare Visual Studio 2015 o Visual Studio 2013 Update 4. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Installare Microsoft Azure SDK per .NET versione 2.7.1 o successiva usando [Installazione guidata piattaforma Web Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).
* Installare gli [strumenti di Analisi di flusso di Azure per Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Aggiornamento

### <a name="visual-studio-2017"></a>Visual Studio 2017
Il promemoria della nuova versione viene visualizzato nella notifica di Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Gli strumenti di Analisi di flusso di Azure installati per Visual Studio verificano automaticamente la disponibilità di nuove versioni. Seguire le istruzioni nella finestra popup per installare la versione più recente. 


## <a name="uninstall"></a>Disinstallare

### <a name="visual-studio-2017"></a>Visual Studio 2017
Fare doppio clic sul programma di installazione di Visual Studio e selezionare **Modifica**. Deselezionare la casella di controllo **Strumenti per Azure Data Lake e analisi di flusso** dal carico di lavoro **Elaborazione ed archiviazione dati** o **Sviluppo di Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Nel pannello di controllo disinstallare **Strumenti per Azure Data Lake e analisi di flusso**.





