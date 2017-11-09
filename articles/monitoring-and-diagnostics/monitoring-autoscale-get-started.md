---
title: "Introduzione alla scalabilità automatica in Azure | Documentazione Microsoft"
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
ms.date: 07/07/2017
ms.author: rajram
ms.openlocfilehash: 84ce3af2bef1e3204ad91567d155d7cbf90e9c7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-autoscale-in-azure"></a>Introduzione alla scalabilità automatica in Azure
Questo articolo descrive come configurare l'impostazione di scalabilità automatica per la risorsa nel portale di Microsoft Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a set di scalabilità di macchine virtuali, servizi cloud, piani di servizio app di Azure e ambienti di servizio app. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Individuare le impostazioni di scalabilità automatica nella sottoscrizione
È possibile individuare tutte le risorse per le quali è applicabile la scalabilità automatica in Monitoraggio di Azure. Eseguire i passaggi descritti di seguito per una procedura guidata:

1. Aprire il [portale di Azure.][1]
2. Fare clic sull'icona di Monitoraggio di Azure nel riquadro a sinistra.
  ![Aprire Monitoraggio di Azure][2]
3. Fare clic su **Scalabilità automatica** per visualizzare tutte le risorse per cui è applicabile, nonché il relativo stato corrente di scalabilità automatica.
  ![Individuazione della scalabilità automatica nel Monitoraggio di Azure][3]

È possibile usare il riquadro filtro nella parte superiore per ridurre l'ambito dell'elenco e selezionare le risorse in un gruppo di risorse specifico, i tipi di risorse specifici o una determinata risorsa.

Per ogni risorsa verranno indicati il numero di istanze corrente e lo stato di scalabilità automatica. Lo stato di scalabilità automatica può essere:

- **Non configurato**: non è stata ancora abilitata la scalabilità automatica per questa risorsa.
- **Configurato**: è stata abilitata la scalabilità automatica per questa risorsa.
- **Disattivato**: è stata disattivata la scalabilità automatica per questa risorsa.

## <a name="create-your-first-autoscale-setting"></a>Creare la prima impostazione di scalabilità automatica

Verrà ora illustrata una semplice procedura dettagliata per creare la prima impostazione di scalabilità automatica.

1. Aprire il pannello **Scalabilità automatica** in Monitoraggio di Azure e selezionare una risorsa da ridimensionare (la procedura seguente usa un piano di servizio app associato a un'app Web. È possibile [creare la prima app Web ASP.NET in Azure in 5 minuti][4]).
2. Notare che il numero corrente di istanze per il ruolo è 1. Fare clic su **Abilita scalabilità automatica**.
  ![Impostazione di scalabilità per la nuova app Web][5]
3. Specificare un nome per il set di scalabilità, quindi scegliere **Aggiungi una regola**. Si notino le opzioni per le regole di scalabilità visualizzate come riquadro contesto sul lato destro. Per impostazione predefinita viene applicata l'opzione per aumentare il numero di istanze di 1 se la percentuale CPU della risorsa supera il 70 per cento. Lasciare i valori predefiniti e fare clic su **Aggiungi**.
  ![Creare l'impostazione di scalabilità per un'app Web][6]
4. È stata così creata la prima regola di scalabilità. Si noti che l'esperienza utente indica le procedure consigliate e che "È consigliabile includere almeno una regola di riduzione del numero di istanze". A tale scopo, procedere come segue:
  
    a. Fare clic su **Aggiungi regola**. 

    b. Impostare **Operatore** a **Minore di**.

    c. Impostare **Soglia** su **20**.

    d. Impostare **Operazione** su **Diminuisci il numero di**.

   A questo punto si avrà un'impostazione di scalabilità che aumenta/riduce il numero di istanze in base all'utilizzo della CPU.
   ![Scalabilità in base alla CPU][8]
5. Fare clic su **Salva**.

Congratulazioni. A questo punto è stata creata la prima impostazione di scalabilità automatica per l'app Web in base all'utilizzo della CPU.

> [!NOTE] 
> Gli stessi passaggi sono applicabili ai set di scalabilità di macchine virtuali e al ruolo del servizio cloud.

## <a name="other-considerations"></a>Altre considerazioni
### <a name="scale-based-on-a-schedule"></a>Scalare in base a una pianificazione
Oltre alla scalabilità basata sempre sulla CPU, è possibile impostare la scalabilità in modo diverso per giorni specifici della settimana.

1. Fare clic su **Aggiungi una condizione di scalabilità**.
2. L'impostazione della modalità e delle regole di scalabilità è uguale alla condizione predefinita.
3. Selezionare **Ripeti in giorni specifici** per la pianificazione.
4. Selezionare i giorni e l'ora di inizio/fine per l'applicazione della condizione di scalabilità.

![Condizione di scalabilità in base alla pianificazione][9]
### <a name="scale-differently-on-specific-dates"></a>Impostare la scalabilità in modo diverso per date specifiche
Oltre alla scalabilità basata sempre sulla CPU, è possibile impostare la scalabilità in modo diverso per le date specifiche.

1. Fare clic su **Aggiungi una condizione di scalabilità**.
2. L'impostazione della modalità e delle regole di scalabilità è uguale alla condizione predefinita.
3. Selezionare **Specificare le date di inizio/fine** per la pianificazione.
4. Selezionare i giorni e l'ora di inizio/fine per l'applicazione della condizione di scalabilità.

![Condizione di scalabilità in base alle date][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visualizzare la cronologia di scalabilità della risorsa
Ogni volta che vengono aumentate o ridotte le prestazioni della risorsa, viene registrato un evento nel log attività. È possibile visualizzare la cronologia della scalabilità della risorsa per le ultime 24 ore passando alla scheda **Cronologia di esecuzione**.

![Cronologia di esecuzione][11]

Per visualizzare la cronologia della scalabilità completa (fino a 90 giorni), selezionare **Fare clic qui per visualizzare altri dettagli**. Si aprirà il log attività con Scalabilità automatica preselezionata per la risorsa e la categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Visualizzare la definizione di scalabilità della risorsa
Scalabilità automatica è una risorsa di Azure Resource Manager. È possibile visualizzare la definizione del piano in JSON passando alla scheda **JSON**.

![Definizione del piano][12]

È possibile apportare modifiche direttamente in JSON, se necessario. Queste modifiche saranno applicate dopo averle salvate.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Disabilitare la scalabilità automatica e ridimensionare le istanze manualmente
A volte può essere opportuno disabilitare l'impostazione di scalabilità corrente e ridimensionare la risorsa manualmente.

Fare clic sul pulsante **Disabilita scalabilità automatica** nella parte superiore.
![Disabilita scalabilità automatica][13]

> [!NOTE] 
> Questa opzione disabilita la configurazione. Tuttavia, è possibile ritornare dopo aver abilitato nuovamente la scalabilità automatica. 

È ora possibile impostare il numero di istanze da ridimensionare manualmente.

![Impostare la scalabilità manuale][14]

È sempre possibile impostare nuovamente la scalabilità automatica facendo clic su **Abilita scalabilità automatica** e quindi su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
- [Creare un avviso di log attività per monitorare tutte le operazioni del motore di scalabilità automatica della sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Creare un avviso di log attività per monitorare tutte le operazioni di scalabilità automatica in riduzione e in aumento non riuscite per la sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
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

