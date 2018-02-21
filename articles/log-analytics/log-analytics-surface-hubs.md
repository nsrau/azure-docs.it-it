---
title: Monitorare dispositivi Surface Hub con Log Analytics di Azure | Documentazione Microsoft
description: "Usare la soluzione Surface Hub per monitorarne l'integrità e comprenderne la modalità d'uso."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 525b3ced979834a956f91ef8c6f647b659ca21f1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorare dispositivi Surface Hub con Log Analytics per tracciare la loro integrità

![Simbolo di Surface Hub](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

In questo articolo viene descritto come usare la soluzione Surface Hub in Log Analytics per monitorare i dispositivi di Microsoft Surface Hub. Log Analytics aiuta a monitorare l'integrità di Surface Hub e comprenderne la modalità d'uso.

Per ogni soluzione Surface Hub viene installato il Microsoft Monitoring Agent. L'agente permette di trasferire i dati da Surface Hub a Log Analytics. I file di log vengono letti da Surface Hub, per poi essere inviati a Log Analytics. Nel dashboard di Surface Hub in Log Analytics vengono mostrati i vari problemi, come lo stato offline dei server, il calendario non sincronizzato, o se l'account del dispositivo non riesce ad accedere a Skype. Usando i dati presenti nel dashboard, è possibile identificare i dispositivi che non sono in esecuzione o che hanno altri problemi e, potenzialmente, è possibile apportare le opportune correzioni ai problemi rilevati.

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le informazioni seguenti per installare e configurare la soluzione. Per gestire i Surface Hub in Log Analytics, è necessario quanto segue:

* Un livello di [sottoscrizione a Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) che supporta il numero di dispositivi che si desidera monitorare. I prezzi di Log Analytics variano a seconda del numero dei dispositivi registrati e del volume di dati in elaborazione. Tali aspetti devono essere presi in considerazione quando si pianifica la distribuzione di Surface Hub.

Si dovrà quindi aggiungere un'area di lavoro Log Analytics esistente o crearne una nuova. le istruzioni dettagliate per usare uno di questi metodi sono fornite nella sezione [Introduzione a Log Analytics](log-analytics-get-started.md). Una volta configurata l'area di lavoro di Log Analytics, esistono due modi per registrare i dispositivi Surface Hub:

* Automaticamente tramite Intune
* Manualmente tramite **Impostazioni** sul dispositivo di Surface Hub.

## <a name="set-up-monitoring"></a>Configurare il monitoraggio
È possibile monitorare l'integrità e l'attività di Surface Hub con Log Analytics. È possibile registrare Surface Hub tramite Intune, oppure localmente usando **Impostazioni** in Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Connettere i dispositivi Surface Hub a Log Analytics tramite Intune
Saranno necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics incaricata di gestire Surface Hub. È possibile ottenerle dalle impostazioni dell'area di lavoro nel portale di Azure.

InTune è un prodotto Microsoft che consente di gestire centralmente le impostazioni di configurazione di Log Analytics che si applicano a uno o più dispositivi. Seguire questa procedura per configurare i dispositivi tramite Intune:

1. Accedere a Intune.
2. Andare a **Impostazioni** > **Origini connesse**.
3. Creare o modificare un criterio basato sul modello di Surface Hub.
4. Andare alla sezione OMS (Azure Operational Insights) del criterio e aggiungere l'*ID area di lavoro* di Log Analytics e la *Chiave area di lavoro* al criterio.
5. Salvare il criterio.
6. Associare il criterio al gruppo di dispositivi appropriato.

   ![Criterio di Intune](./media/log-analytics-surface-hubs/intune.png)

A questo punto, Intune sincronizza le impostazioni di Log Analytics con i dispositivi nel gruppo di destinazione, registrandoli nell'area di lavoro di Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Connettere Surface Hub a Log Analytics usando l'app Impostazioni
Saranno necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics incaricata di gestire Surface Hub. È possibile ottenerle dalle impostazioni per l'area di lavoro di Log Analytics nel portale di Azure.

Se non si usa Intune per gestire l'ambiente, è possibile registrare i dispositivi manualmente tramite **Impostazioni** in ogni Surface Hub:

1. Da Surface Hub aprire **Impostazioni**.
2. Immettere le credenziali di amministratore del dispositivo quando richiesto.
3. Fare clic su **Questo dispositivo** e in **Monitoraggio** fare clic su **Configura impostazioni OMS**.
4. Selezionare **Abilita monitoraggio**.
5. Nella finestra di dialogo Impostazioni di OMS, immetter l'**ID area di lavoro** di Log Analytics e la **Chiave area di lavoro**.  
   ![impostazioni](./media/log-analytics-surface-hubs/settings.png)
6. Fare clic su **OK** per portare a termine la configurazione.

Un messaggio di conferma viene visualizzato per indicare il buon esito dell'operazione o l'eventuale presenza di errori. Se la configurazione di OMS viene correttamente applicata al dispositivo, appare un messaggio per indicare che l'agente è connesso a Log Analytics. Il dispositivo inizia quindi a inviare dati a Log Analytics dove possono essere visualizzati e gestiti secondo le proprie esigenze.

## <a name="monitor-surface-hubs"></a>Monitorare Surface Hub
Il monitoraggio di Surface Hub con Log Analytics è molto simile all'attività di monitoraggio di qualsiasi altro dispositivo registrato.

1. Accedere al portale di Azure.
2. Passare all'area di lavoro di Log Analytics e selezionare **Panoramica**.
2. Fare clic sul riquadro Surface Hub.
3. Viene visualizzata l'integrità del dispositivo.

   ![Dashboard di Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

È possibile creare [avvisi](log-analytics-alerts.md) in base alle ricerche log esistenti o personalizzate. Con i dati che Log Analytics ha raccolto da Surface Hub, è possibile cercare problemi e avvisi in base alle condizioni definite per i dispositivi.

## <a name="next-steps"></a>Passaggi successivi
* Usare le [ricerche log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati di Surface Hub.
* Creare [avvisi](log-analytics-alerts.md) che informano della presenza di problemi relativi a Surface Hub.
