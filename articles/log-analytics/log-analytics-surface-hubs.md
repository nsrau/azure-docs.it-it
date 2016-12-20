---
title: Monitorare Surface Hub con Log Analytics | Documentazione Microsoft
description: "Usare la soluzione Surface Hub per monitorarne l&quot;integrità e comprenderne la modalità d&quot;uso."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 15858f7b7436536e6bae7fcfd6a50c722d2d04a2
ms.openlocfilehash: 9ba6b1b3fcdaf090eee696ba3cb33be5d94be039


---
# <a name="monitor-surface-hubs-with-log-analytics"></a>Monitorare Surface Hub con Log Analytics
In questo articolo viene descritto come usare la soluzione Surface Hub in Log Analytics per monitorare i dispositivi di Microsoft Surface Hub con Microsoft Operations Management Suite (OMS). Log Analytics aiuta a monitorare l'integrità di Surface Hub e comprenderne la modalità d'uso.

Per ogni soluzione Surface Hub viene installato il Microsoft Monitoring Agent. L'agente permette di trasferire i dati da Surface Hub a OMS. I file di log vengono letti da Surface Hub, per poi essere inviati al servizio OMS. Nel dashboard di Surface Hub in OMS vengono mostrati i vari problemi, come lo stato offline dei server, il calendario non sincronizzato, o se l'account del dispositivo non riesce ad accedere a Skype. Usando i dati presenti nel dashboard, è possibile identificare i dispositivi che non sono in esecuzione o che hanno altri problemi e, potenzialmente, è possibile apportare le opportune correzioni ai problemi rilevati.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione. Per gestire Surface Hub da Microsoft Operations Management Suite (OMS), sono richiesti:

* Una sottoscrizione valida a [OMS](http://www.microsoft.com/oms).
* Un livello di [sottoscrizione OMS](https://azure.microsoft.com/pricing/details/log-analytics/) che supporta il numero di dispositivi che si desidera monitorare. I prezzi di OMS variano a seconda del numero dei dispositivi registrati e del volume di dati in elaborazione. Tali aspetti devono essere presi in considerazione quando si pianifica la distribuzione di Surface Hub.

Successivamente, è possibile aggiungere una sottoscrizione OMS alla sottoscrizione Microsoft Azure esistente, oppure si può scegliere di creare una nuova area di lavoro direttamente tramite il portale di OMS. le istruzioni dettagliate per usare uno di questi metodi sono fornite nella sezione [Introduzione a Log Analytics](log-analytics-get-started.md). Una volta impostata la sottoscrizione OMS, ci sono due modi per registrare i dispositivi di Surface Hub:

* Automaticamente tramite InTune
* Manualmente tramite **Impostazioni** sul dispositivo di Surface Hub.

## <a name="set-up-monitoring"></a>Configurare il monitoraggio
È possibile monitorare l'integrità e l'attività di Surface Hub con Log Analytics in OMS. È possibile registrare Surface Hub in OMS tramite InTune, oppure localmente usando **Impostazioni** in Surface Hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Connettere Surface Hub a OMS mediante InTune
L'ID e la chiave dell'area di lavoro necessari per l'area di lavoro OMS incaricata di gestire Surface Hub sono disponibili nel portale di OMS.

InTune è un prodotto Microsoft che consente di gestire centralmente le impostazioni di configurazione di OMS che si applicano a uno o più dispositivi. Seguire questa procedura per configurare i dispositivi tramite InTune:

1. Accedere a InTune.
2. Andare a **Impostazioni** > **Origini connesse**.
3. Creare o modificare un criterio basato sul modello di Surface Hub.
4. Andare alla sezione OMS (Azure Operational Insights) del criterio e aggiungere l'*ID area di lavoro* e la *Chiave area di lavoro* al criterio.
5. Salvare il criterio.
6. Associare il criterio al gruppo di dispositivi appropriato.

   ![Criterio di InTune](./media/log-analytics-surface-hubs/intune.png)

A questo punto, InTune sincronizza le impostazioni di OMS con i dispositivi nel gruppo di destinazione, registrandoli nell'area di lavoro di OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Connettere Surface Hub a OMS usando l'app Impostazioni
L'ID e la chiave dell'area di lavoro necessari per l'area di lavoro OMS incaricata di gestire Surface Hub sono disponibili nel portale di OMS.

Se non si usa InTune per gestire l'ambiente, è possibile registrare i dispositivi manualmente tramite **Impostazioni** su ogni Surface Hub:

1. Da Surface Hub aprire **Impostazioni**.
2. Immettere le credenziali di amministratore del dispositivo quando richiesto.
3. Fare clic su **Questo dispositivo** e in **Monitoraggio** fare clic su **Configura impostazioni OMS**.
4. Selezionare **Abilita monitoraggio**.
5. Nella finestra di dialogo Impostazioni di OMS, immetter l'**ID area di lavoro** e la **Chiave area di lavoro**.  
   ![impostazioni](./media/log-analytics-surface-hubs/settings.png)
6. Fare clic su **OK** per portare a termine la configurazione.

Un messaggio di conferma viene visualizzato per indicare il buon esito dell'operazione o l'eventuale presenza di errori. Se la configurazione di OMS viene correttamente applicata al dispositivo, appare un messaggio per indicare che l'agente è connesso al servizio OMS. Il dispositivo inizia quindi a inviare dati a OMS dove possono essere visualizzati e gestiti secondo le proprie esigenze.

## <a name="monitor-surface-hubs"></a>Monitorare Surface Hub
Monitorare Surface Hub con OMS è molto simile all'attività di monitoraggio di qualsiasi altro dispositivo registrato.

1. Accedere al portale di OMS.
2. Andare al dashboard del pacchetto della soluzione Surface Hub.
3. Viene visualizzata l'integrità del dispositivo.

   ![Dashboard di Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

È possibile creare [avvisi](log-analytics-alerts.md) in base alle ricerche log esistenti o personalizzate. Con i dati che OMS ha raccolto da Surface Hub, è possibile cercare problemi e avvisi in base alle condizioni definite per i dispositivi.

## <a name="next-steps"></a>Passaggi successivi
* Usare le [ricerche log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati di Surface Hub.
* Creare [avvisi](log-analytics-alerts.md) che informano della presenza di problemi relativi a Surface Hub.



<!--HONumber=Nov16_HO3-->


