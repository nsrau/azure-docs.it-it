---
title: Rispondere a eventi con avvisi di Log Analytics di Azure | Microsoft Docs
description: Questa esercitazione consente di comprendere gli avvisi in Log Analytics per l'identificazione delle informazioni importanti nel repository OMS e per la notifica di problemi all'utente in modo proattivo o per richiamare le azioni per tentare di correggerle.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3ab8d32eb4b3f2748249f40139de76c8e7f4d971
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Rispondere a eventi con avvisi di Log Analytics
Gli avvisi in Log Analytics identificano informazioni importanti nel repository di Log Analytics.  Vengono creati da regole di avviso che eseguono automaticamente ricerche nei log a intervalli regolari e, se i risultati della ricerca corrispondono a determinati criteri, viene creato un record di avviso che può essere configurato per eseguire una risposta automatica.  Questa esercitazione è la continuazione dell'esercitazione [Create and share dashboards of Log Analytics data](log-analytics-tutorial-dashboards.md) (Creare e condividere dashboard di dati di Log Analytics).   

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una regola di avviso
> * Configurare una regola di avviso per l'invio di notifiche di posta elettronica

Per completare l'esempio contenuto in questa esercitazione, è necessario disporre di una macchina virtuale esistente [connessa all'area di lavoro di Log Analytics](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Creare avvisi

Gli avvisi vengono creati da regole di avviso che eseguono automaticamente ricerche log a intervalli regolari.  È possibile creare avvisi in base a metriche di prestazioni specifiche o quando vengono creati determinati eventi, in assenza di un evento o per un numero di eventi creati all'interno di un intervallo di tempo specifico.  Ad esempio, è possibile usare gli avvisi per notificare all'utente che l'utilizzo medio della CPU supera una determinata soglia o viene generato un evento quando un servizio di Windows specifico o un daemon Linux non è in esecuzione.   Se i risultati della ricerca log corrispondono a criteri specifici viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo. 

L'esempio seguente crea una regola di avviso di misurazione delle metriche che creerà un avviso per ogni computer nella query con un valore superiore alla soglia del 90%.

1. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. **Selezionare Log Analytics**.
2. Avviare il portale di OMS, selezionando Portale di OMS e sulla pagina **Panoramica** selezionare **Ricerca log**.  
3. Selezionare **Preferiti** dalla parte superiore del portale e nel riquadro **Ricerche salvate** a destra selezionare la query *Macchine virtuali di Azure - Utilizzo del processore*.  
4. Fare clic su **Avviso** nella parte superiore della pagina per aprire la schermata **Aggiungi regola di avviso**.  
5. Configurare la regola di avviso con le informazioni seguenti:  
   a. Fornire un **Nome** per l'avviso, ad esempio *Utilizzo del processore VM ha superato > 90*  
   b. Per **Intervallo di tempo**, specificare un intervallo di tempo per la query, ad esempio *30*.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  
   c. **Frequenza di avviso** specifica la frequenza con cui deve essere eseguita la query.  Per questo esempio, specificare *5* minuti, che si verificheranno entro l'intervallo di tempo specificato.  
   d. Selezionare **Unità di misura della metrica** e immettere *90* per **Valore aggregato** e *3* per **Attiva l'avviso in base a**   
   e. In **Azioni**, disabilitare la notifica di posta elettronica.
6. Fare clic su **Salva** per salvare la regola di avviso. Verrà avviata immediatamente l'esecuzione.<br><br> ![Esempio di regola di avviso](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

I record degli avvisi creati dalle regole di avviso in Log Analytics hanno un Tipo impostato su **Avviso** e SourceSystem impostato su **OMS**.<br><br> ![Esempio di eventi di avviso generati](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Azioni di avviso
È possibile eseguire azioni avanzate con gli avvisi, ad esempio creare una notifica di posta elettronica, avviare un [Runbook di automazione](../automation/automation-runbook-types.md), usare un webhook per creare un record di eventi imprevisti nel sistema di gestione degli eventi imprevisti ITSM o con la [soluzione IT Service Management Connector](log-analytics-itsmc-overview.md) come risposta quando vengono soddisfatti i criteri di avviso.   

Le azioni di posta elettronica inviano un messaggio di posta elettronica con i dettagli dell'avviso a uno o più destinatari. È possibile specificare l'oggetto del messaggio, ma il contenuto è un formato standard creato da Log Analytics.  Aggiornare la regola di avviso creata in precedenza e configurarla in modo da inviare all'utente una notifica di posta elettronica invece di monitorare attivamente il record di avviso con una ricerca log.     

1. Nel portale di OMS, nel menu in alto selezionare **Impostazioni** e quindi selezionare **Avvisi**.
2. Dall'elenco delle regole di avviso, fare clic sull'icona della matita accanto all'avviso creato in precedenza.
3. Nella sezione **Azioni** abilitare le notifiche di posta elettronica.
4. Fornire un **Oggetto** per la posta elettronica, ad esempio *L'utilizzo del processore ha superato la soglia > 90*.
5. Aggiungere gli indirizzi di uno o più destinatari di posta elettronica nel campo **Destinatari**.  Se si specifica più di un indirizzo, separare ognuno con un punto e virgola (;).
6. Fare clic su **Salva** per salvare la regola di avviso. Verrà avviata immediatamente l'esecuzione.<br><br> ![Regola di avviso con la notifica tramite posta elettronica](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come le regole di avviso possono identificare e rispondere in modo proattivo a un problema durante l'esecuzione di ricerche nei log a intervalli pianificati e trovando corrispondenze a determinati criteri.  

Seguire questo collegamento per vedere esempi di script predefiniti di Log Analytics.  

> [!div class="nextstepaction"]
> [Esempi di script di Log Analytics](powershell-samples.md)