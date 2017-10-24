---
title: Istruzioni di installazione per gli strumenti di Analisi di flusso di Azure per Visual Studio | Microsoft Docs
description: Istruzioni di installazione per gli strumenti di Analisi di flusso di Azure per Visual Studio
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 88abf40cefaca150c67e3a1068006fb0fa254305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Istruzioni di installazione per gli strumenti di Analisi di flusso per Visual Studio
Gli strumenti di Analisi di flusso ora supportano Visual Studio 2017, 2015 e 2013. In questo documento viene descritto come installare e disinstallare gli strumenti.

[Informazioni su come usare gli strumenti di Analisi di flusso per Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="install"></a>Installa
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Scaricare [Visual Studio 2017 (versione 15.3 o successiva)](https://www.visualstudio.com/). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Gli strumenti di Analisi di flusso fanno parte del carico di lavoro di sviluppo, archiviazione dei dati e gestione di Azure in Visual Studio 2017. Abilitare uno di questi due carichi di lavoro durante l'installazione di Visual Studio.

![Installare gli strumenti di Analisi di flusso per Visual Studio 1](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)
![Installare gli strumenti di Analisi di flusso per Visual Studio 2](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Installare Visual Studio 2015, Visual Studio 2013 Update 4. Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata. 
* Installare Microsoft Azure SDK per .NET versione 2.7.1 o successiva, disponibile tramite [Installazione guidata piattaforma Web Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).
* Installare gli [strumenti di Analisi di flusso di Azure per Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Aggiornare

### <a name="visual-studio-2017"></a>Visual Studio 2017
Il promemoria della nuova versione viene visualizzato nella notifica di Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Gli strumenti di Analisi di flusso per Visual Studio installati verificano automaticamente la disponibilità di nuove versioni. Seguire le istruzioni disponibili nella finestra popup per installare la versione più recente. 


## <a name="uninstall"></a>Disinstallare

### <a name="visual-studio-2017"></a>Visual Studio 2017
Fare doppio clic sul programma di installazione di Visual Studio e selezionare **Modifica**. Deselezionare **Strumenti per Azure Data Lake e analisi di flusso** dal carico di lavoro **Elaborazione ed archiviazione dati** o **Sviluppo Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Nel pannello di controllo disinstallare gli strumenti per Microsoft Azure Data Lake e Analisi di flusso per Visual Studio.





