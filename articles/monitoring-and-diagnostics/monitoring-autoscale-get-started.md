---
title: "Introduzione alla scalabilità automatica in Azure | Microsoft Docs"
description: Informazioni su come ridimensionare la risorsa in Azure.
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 4faccae708b5407d1eb64e746824a23688bc5834
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>Introduzione alla scalabilità automatica in Azure
Questo articolo descrive come configurare l'impostazione di scalabilità automatica per la ricorsa nel portale di Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a set di scalabilità di macchine virtuali (VMSS), servizi cloud, piani di servizio app e ambienti di servizio app. 

# <a name="lets-get-started"></a>Introduzione

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>Individuare le impostazioni di scalabilità automatica nelle sottoscrizioni
È possibile individuare tutte le risorse per le quali è applicabile la scalabilità automatica in Monitoraggio di Azure. Seguire questi passaggi per una procedura dettagliata.

- Aprire il [portale di Azure][1]
- Fare clic sull'icona di Monitoraggio di Azure nel riquadro di spostamento a sinistra.
  ![Avviare Monitoraggio di Azure][2]
- Fare clic sull'impostazione Scalabilità automatica per visualizzare tutte le risorse per le quali è applicabile la scalabilità automatica, insieme allo stato corrente ![Individuare l'impostazione Scalabilità automatica in Monitoraggio di Azure][3]

È possibile usare il riquadro filtro nella parte superiore per ridurre l'ambito dell'elenco e selezionare le risorse in un gruppo di risorse specifico, selezionare tipi di risorse specifici o una determinata risorsa.

Per ogni risorsa verranno indicati il numero di istanze corrente e lo stato di scalabilità automatica. Lo stato di scalabilità automatica può essere

- Non configurato: non è stata ancora abilitata la scalabilità automatica per questa risorsa
- Abilitato: è stata abilitata la scalabilità automatica per questa risorsa
- Disabilitato: la scalabilità automatica è stata disabilitata per questa risorsa

## <a name="create-your-first-auto-scale-setting"></a>Creare la prima impostazione di scalabilità automatica

Verrà ora illustrata una semplice procedura dettagliata per creare la prima impostazione di scalabilità automatica.

- Aprire il pannello 'Scalabilità automatica' in Monitoraggio di Azure e selezionare una risorsa da ridimensionare. La procedura seguente usa un piano di servizio app associato a un'app Web. È possibile [creare la prima app Web ASP.NET in Azure in cinque minuti][4]
- Nel pannello delle impostazioni di scalabilità automatica per la risorsa si noti che il numero di istanze corrente è 1. Fare clic su 'Abilita scalabilità automatica'.
  ![Impostazione di scalabilità per la nuova app Web][5]
- Specificare un nome per l'impostazione di scalabilità, quindi scegliere "Add a rule" (Aggiungi una regola). Si notino le opzioni per le regole di scalabilità visualizzate come riquadro contesto sul lato destro. Per impostazione predefinita viene applicata l'opzione per aumentare il numero di istanze di 1 se la percentuale CPU della risorsa supera il 70%. Lasciare i valori predefiniti e fare clic su Aggiungi.
  ![Creare l'impostazione di scalabilità per un'app Web][6]
- È stata così creata la prima regola di scalabilità. Si noti che l'esperienza utente indica le procedure consigliate e che 'È consigliabile includere almeno una regola di riduzione del numero di istanze'. A tale scopo fare clic su 'Add a rule' (Aggiungi una regola) e impostare 'Operatore' su 'Minore di', 'Soglia' su '20' e 'Operazione' su 'Riduci numero di'. A questo punto si avrà un'impostazione di scalabilità che aumenta/riduce il numero di istanze in base all'utilizzo della CPU.
  ![Scalabilità in base alla CPU][8]
- Fare clic su 'Salva'

A questo punto A questo punto è stata creata la prima impostazione di scalabilità automatica per l'app Web in base all'utilizzo della CPU.

> Nota: gli stessi passaggi sono applicabili ai set di scalabilità di macchine virtuali e al ruolo del servizio cloud.

# <a name="other-considerations"></a>Altre considerazioni
## <a name="scale-based-on-a-schedule"></a>Scalare in base a una pianificazione
Oltre alla scalabilità basata sempre sulla CPU, è possibile impostare la scalabilità in modo diverso per giorni specifici della settimana.

- Fare clic su 'Add a scale condition' (Aggiungi una condizione di scalabilità)
- L'impostazione della modalità e delle regole di scalabilità è uguale alla condizione predefinita
- Selezionare 'Ripeti in giorni specifici' per la pianificazione
- Selezionare i giorni e l'ora di inizio/fine per l'applicazione della condizione di scalabilità per i giorni selezionati

![Condizione di scalabilità in base alla pianificazione][9]
## <a name="scale-differently-on-specific-dates"></a>Impostare la scalabilità in modo diverso per date specifiche
Oltre alla scalabilità basata sempre sulla CPU, è possibile impostare la scalabilità in modo diverso per date specifiche.

- Fare clic su 'Add a scale condition' (Aggiungi una condizione di scalabilità)
- L'impostazione della modalità e delle regole di scalabilità è uguale alla condizione predefinita
- Selezionare 'Specificare le date di inizio/fine' per la pianificazione
- Selezionare le date di inizio/fine e l'ora di inizio/fine per l'applicazione della condizione di scalabilità per i giorni selezionati

![Condizione di scalabilità in base alle date][10]

## <a name="view-the-scale-history-of-your-resource"></a>Visualizzare la cronologia di scalabilità della risorsa
Ogni volta che vengono aumentate/ridotte le prestazioni della risorsa, viene registrato un evento nel log attività. È possibile visualizzare la cronologia della scalabilità della risorsa per le ultime 24 ore passando alla scheda 'Cronologia'.

![Cronologia di esecuzione][11]

Per visualizzare la cronologia della scalabilità completa, fino a 90 giorni, è possibile fare clic su "Fare clic qui per visualizzare altri dettagli". Si aprirà il log attività con la risorsa e la categoria 'autoscale' preselezionata.

## <a name="view-the-scale-definition-of-the-resource"></a>Visualizzare la definizione del piano della risorsa
L'impostazione di scalabilità automatica è una risorsa di Azure Resource Manager. È possibile visualizzare la definizione del piano in JSON passando alla scheda 'JSON'.

![Definizione del piano][12]

È possibile apportare modifiche direttamente in JSON, se necessario. Queste modifiche verranno applicate dopo il salvataggio.

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>Disabilitare la scalabilità automatica e ridimensionare le istanze manualmente
A volte può essere opportuno disabilitare l'impostazione di scalabilità corrente e ridimensionare la risorsa manualmente.

Fare clic sul pulsante "Disabilita scalabilità automatica" nella parte superiore.
![Disabilitare la scalabilità automatica][13]

Si noti che questa opzione disabilita la configurazione, che può essere tuttavia riattivata abilitando di nuovo la scalabilità automatica. È ora possibile impostare il numero di istanze da ridimensionare manualmente.

![Impostare la scalabilità manuale][14]

È sempre possibile impostare nuovamente la scalabilità automatica facendo clic su 'Enable autoscale' (Abilita scalabilità automatica) e quindi su 'Salva'.

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

