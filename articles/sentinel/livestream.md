---
title: Usare la caccia livestream in Sentinel di Azure per rilevare le minacce | Microsoft Docs
description: Questo articolo descrive come usare la caccia a livestream in Azure Sentinel per tenere traccia dei dati.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: cabailey
ms.openlocfilehash: 5b347848c9c6a58a70ab1093a6f9c70b62f3f769
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900450"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Usare la caccia a livestream in Azure Sentinel per rilevare le minacce

> [!IMPORTANT]
> La caccia a livestream in Azure Sentinel è attualmente disponibile in anteprima pubblica e viene gradualmente implementata nei tenant.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Usare la caccia di livestream per creare sessioni interattive che consentono di testare le query appena create quando si verificano gli eventi, ricevere notifiche dalle sessioni quando viene trovata una corrispondenza e avviare indagini se necessario. È possibile creare rapidamente una sessione livestream usando qualsiasi Log Analytics query.

- **Testare le query appena create quando si verificano eventi**
    
    È possibile testare e modificare le query senza conflitti con le regole correnti applicate attivamente agli eventi. Dopo aver confermato che queste nuove query funzionano come previsto, è facile innalzarle di livello alle regole di avviso personalizzate selezionando un'opzione che eleva la sessione a un avviso.

- **Ricevi notifiche quando si verificano minacce**
    
    È possibile confrontare i feed di dati sulle minacce ai dati di log aggregati e ricevere notifiche quando si verifica una corrispondenza. I feed di dati sulle minacce sono flussi di dati in corso correlati a minacce potenziali o correnti, quindi la notifica potrebbe indicare una potenziale minaccia per l'organizzazione. Creare una sessione livestream anziché una regola di avviso personalizzata quando si desidera ricevere una notifica di un potenziale problema senza l'overhead della gestione di una regola di avviso personalizzata.

- **Avvia indagini**
    
    Se è presente un'indagine attiva che coinvolge un asset, ad esempio un host o un utente, è possibile visualizzare un'attività specifica (o qualsiasi) nei dati del log, come avviene in tale asset. È possibile ricevere una notifica quando si verifica tale attività.


## <a name="create-a-livestream-session"></a>Creare una sessione livestream

È possibile creare una sessione Livestream da una query di caccia esistente o creare la sessione da zero.

1. Nella portale di Azure passare a **Sentinel** > **Threat Management** > **Hunting**.

2. Per creare una sessione Livestream da una query di ricerca:
    
    1. Dalla scheda **query** individuare la query di ricerca da usare.
    2. Fare clic con il pulsante destro del mouse sulla query e scegliere **Aggiungi a livestream**. ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![creare una sessione livestream dalla query di ricerca di Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Per creare una sessione Livestream da zero: 
    
    1. Selezionare la scheda **livestream**
    2. Selezionare **Vai a livestream**.
    
4. Nel riquadro **livestream** :
    
    - Se è stato avviato Livestream da una query, rivedere la query e apportare le modifiche desiderate.
    - Se è stato avviato Livestream da zero, creare la query. 

5. Selezionare **Riproduci** sulla barra dei comandi.
    
    La barra di stato sotto la barra dei comandi indica se la sessione livestream è in esecuzione o sospesa. Nell'esempio seguente, la sessione è in esecuzione:
    
    > [!div class="mx-imgBorder"]
    > ![creare una sessione Livestream da Azure Sentinel Hunting](./media/livestream/livestream-session.png)

6. Selezionare **Salva** dalla barra dei comandi.
    
    A meno che non si selezioni **Sospendi**, la sessione continua a essere eseguita fino a quando non viene disconnesso dal portale di Azure.

## <a name="view-your-livestream-sessions"></a>Visualizza le sessioni di livestream

1. Nella portale di Azure passare a **Sentinel** > **Threat Management** > **Hunting** > **livestream** Tab.

2. Selezionare la sessione livestream che si vuole visualizzare o modificare. ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![creare una sessione livestream dalla query di ricerca di Azure Sentinel](./media/livestream/livestream-tab.png)
    
    Viene visualizzata la sessione livestream selezionata, che consente di riprodurre, sospendere, modificare e così via.

## <a name="receive-notifications-when-new-events-occur"></a>Ricevi notifiche quando si verificano nuovi eventi

Poiché le notifiche livestream per i nuovi eventi usano portale di Azure notifiche, queste notifiche vengono visualizzate ogni volta che si usa il portale di Azure. ad esempio:

![Notifica portale di Azure per Livestream](./media/livestream/notification.png)

Selezionare la notifica per aprire il riquadro **livestream** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevare una sessione livestream a un avviso

È possibile alzare di livello una sessione di livestream a un nuovo avviso selezionando **eleva a avviso** dalla barra dei comandi della sessione livestream pertinente:

> [!div class="mx-imgBorder"]
> ![elevare la sessione livestream a un avviso](./media/livestream/elevate-to-alert.png)

Questa azione consente di aprire la creazione guidata regola, che viene prepopolata con la query associata alla sessione livestream.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare la caccia a livestream in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i notebook per eseguire campagne di caccia automatiche](notebooks.md)
