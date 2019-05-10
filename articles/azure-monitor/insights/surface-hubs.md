---
title: Monitorare Surface hub con monitoraggio di Azure | Microsoft Docs
description: Usare la soluzione Surface Hub per monitorarne l'integrità e comprenderne la modalità d'uso.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 7e0dbb4c3cd8ae4bb552e7b7f0748f1bde2f51de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232776"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitorare dispositivi Surface Hub con Monitoraggio di Azure per tracciarne l'integrità

![Simbolo di Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

In questo articolo viene descritto come usare la soluzione Surface Hub in Monitoraggio di Azure per monitorare i dispositivi Microsoft Surface Hub. La soluzione consente di monitorare l'integrità dei dispositivi Surface Hub e di capire come vengono usati.

Per ogni soluzione Surface Hub viene installato il Microsoft Monitoring Agent. L'agente permette di trasferire i dati da Surface Hub a un'area di lavoro Log Analytics in Monitoraggio di Azure. I file di log vengono letti dai dispositivi Surface Hub, per poi essere inviati a Monitoraggio di Azure. Nel dashboard di Surface Hub in Monitoraggio di Azure vengono mostrati vari problemi, ad esempio i server offline, il calendario che non si sincronizza o l'impossibilità per l'account del dispositivo di accedere a Skype. Usando i dati presenti nel dashboard, è possibile identificare i dispositivi che non sono in esecuzione o che hanno altri problemi e, potenzialmente, è possibile apportare le opportune correzioni ai problemi rilevati.

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le informazioni seguenti per installare e configurare la soluzione. Per gestire i dispositivi Surface Hub in Monitoraggio di Azure, è necessario quanto segue:

* Un livello di [sottoscrizione a Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) che supporta il numero di dispositivi che si desidera monitorare. I prezzi di Log Analytics variano a seconda del numero dei dispositivi registrati e del volume di dati in elaborazione. Tali aspetti devono essere presi in considerazione quando si pianifica la distribuzione di Surface Hub.

Si dovrà quindi aggiungere un'area di lavoro Log Analytics esistente o crearne una nuova. Le istruzioni dettagliate per l'uso di questi metodi sono disponibili nell'argomento [Creare un'area di lavoro Log Analytics nel portale di Azure](../learn/quick-create-workspace.md). Una volta configurata l'area di lavoro Log Analytics, esistono due modi per registrare i dispositivi Surface Hub:

* Automaticamente tramite Intune
* Manualmente tramite **Impostazioni** sul dispositivo di Surface Hub.

## <a name="set-up-monitoring"></a>Configurare il monitoraggio
È possibile monitorare l'integrità e l'attività di Surface Hub con Monitoraggio di Azure. È possibile registrare Surface Hub tramite Intune, oppure localmente usando **Impostazioni** in Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Connettere i dispositivi Surface Hub a Monitoraggio di Azure tramite Intune
Saranno necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics incaricata di gestire Surface Hub. È possibile ottenerle dalle impostazioni dell'area di lavoro nel portale di Azure.

Intune è un prodotto Microsoft che permette di gestire centralmente le impostazioni di configurazione dell'area di lavoro Log Analytics che si applicano a uno o più dispositivi. Seguire questa procedura per configurare i dispositivi tramite Intune:

1. Accedere a Intune.
2. Andare a **Impostazioni** > **Origini connesse**.
3. Creare o modificare un criterio basato sul modello di Surface Hub.
4. Andare alla sezione Azure Operational Insights del criterio e aggiungere al criterio i valori di *ID area di lavoro* e *Chiave dell'area di lavoro* di Log Analytics.
5. Salvare il criterio.
6. Associare il criterio al gruppo di dispositivi appropriato.

   ![Criterio di Intune](./media/surface-hubs/intune.png)

A questo punto, Intune sincronizza le impostazioni di Log Analytics con i dispositivi nel gruppo di destinazione, registrandoli nell'area di lavoro Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Connettere dispositivi Surface Hub a Monitoraggio di Azure usando l'app Impostazioni
Saranno necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics incaricata di gestire Surface Hub. È possibile ottenerle dalle impostazioni per l'area di lavoro Log Analytics nel portale di Azure.

Se non si usa Intune per gestire l'ambiente, è possibile registrare i dispositivi manualmente tramite **Impostazioni** in ogni Surface Hub:

1. Da Surface Hub aprire **Impostazioni**.
2. Immettere le credenziali di amministratore del dispositivo quando richiesto.
3. Fare clic su **Questo dispositivo** e in **Monitoraggio** fare clic su **Configure Log Analytics Settings** (Configura impostazioni di Log Analytics).
4. Selezionare **Abilita monitoraggio**.
5. Nella finestra di dialogo delle impostazioni di Log Analytics immettere i valori per **ID area di lavoro** e **Chiave dell'area di lavoro** di Log Analytics.  
   ![impostazioni](./media/surface-hubs/settings.png)
6. Fare clic su **OK** per portare a termine la configurazione.

Un messaggio di conferma viene visualizzato per indicare il buon esito dell'operazione o l'eventuale presenza di errori. Se la configurazione ha avuto esito positivo, viene visualizzato un messaggio per indicare che l'agente si è connesso a Monitoraggio di Azure. Il dispositivo inizia quindi a inviare dati a Monitoraggio di Azure dove è possibile visualizzarli e agire su di essi.

## <a name="monitor-surface-hubs"></a>Monitorare Surface Hub
Il monitoraggio di dispositivi Surface Hub con Monitoraggio di Azure è molto simile all'attività di monitoraggio di qualsiasi altro dispositivo registrato.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando si fa clic sul riquadro Surface Hub, viene visualizzata l'integrità del dispositivo.

   ![Dashboard di Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

È possibile creare [avvisi](../platform/alerts-overview.md) in base alle ricerche log esistenti o personalizzate. Con i dati che Monitoraggio di Azure ha raccolto dai dispositivi Surface Hub, è possibile cercare problemi e avvisi per le condizioni definite per i dispositivi.

## <a name="next-steps"></a>Passaggi successivi
* Usare [le query di log in Monitoraggio di Azure](../log-query/log-query-overview.md) per visualizzare dati dettagliati su Surface Hub.
* Creare [avvisi](../platform/alerts-overview.md) che informano della presenza di problemi relativi a Surface Hub.
