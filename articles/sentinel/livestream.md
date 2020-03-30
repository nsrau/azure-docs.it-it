---
title: Usare la caccia Livestream in Azure Sentinel per rilevare le minacce Documenti Microsoft
description: Questo articolo descrive come usare la caccia Livestream in Azure Sentinel per tenere traccia dei dati.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582127"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Usare la trasmissione di caccia in Azure Sentinel per rilevare le minacceUse hunting livestream in Azure Sentinel to detect threats

> [!IMPORTANT]
> La caccia in livestream in Azure Sentinel è attualmente in anteprima pubblica e viene implementata gradualmente ai tenant.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Usa la caccia livestream per creare sessioni interattive che ti consentono di testare le query appena create quando si verificano eventi, ricevere notifiche dalle sessioni quando viene trovata una partita e avviare indagini se necessario. È possibile creare rapidamente una sessione livestream usando qualsiasi query di Log Analytics.You can quickly create a livestream session using any Log Analytics query.

- **Testare le query appena create quando si verificano eventi**
    
    È possibile testare e regolare le query senza conflitti sulle regole correnti che vengono applicate attivamente agli eventi. Dopo aver verificato il funzionamento di queste nuove query come previsto, è facile inserirle a regole di avviso personalizzate selezionando un'opzione che eleva la sessione a un avviso.

- **Ricevere una notifica quando si verificano minacce**
    
    È possibile confrontare i feed di dati sulle minacce con i dati di log aggregati e ricevere una notifica quando si verifica una corrispondenza. I feed di dati sulle minacce sono flussi di dati in corso correlati a minacce potenziali o correnti, pertanto la notifica potrebbe indicare una potenziale minaccia per l'organizzazione. Creare una sessione livestream anziché una regola di avviso personalizzata quando si desidera ricevere una notifica di un potenziale problema senza i costi generali di gestione di una regola di avviso personalizzata.

- **Avviare indagini**
    
    Se è presente un'indagine attiva che coinvolge un asset, ad esempio un host o un utente, è possibile visualizzare un'attività specifica (o qualsiasi) nei dati di log come si verifica su tale asset. È possibile ricevere una notifica quando si verifica tale attività.


## <a name="create-a-livestream-session"></a>Creare una sessione livestreamCreate a livestream session

È possibile creare una sessione livestream da una query di caccia esistente o creare la sessione da zero.

1. Nel portale di Azure passare a **Sentinel** > **Threat management** > **Hunting**.

2. Per creare una sessione livestream da una query di ricerca:
    
    1. Nella scheda **Query** individuare la query di ricerca da utilizzare.
    2. Fare clic con il pulsante destro del mouse sulla query e selezionare **Aggiungi a livestream**. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![creare una sessione Livestream dalla query di caccia di Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Per creare una sessione livestream da zero: 
    
    1. Selezionare la scheda **Livestream**
    2. Selezionare **Vai al livestream**.
    
4. Nel riquadro **Livestream:**
    
    - Se livestream è stato avviato da una query, esaminare la query e apportare le modifiche desiderate.
    - Se hai iniziato a eseguire il livestream da zero, crea la query. 

5. Selezionare **Riproduci** dalla barra dei comandi.
    
    La barra di stato sotto la barra dei comandi indica se la sessione livestream è in esecuzione o in pausa. Nell'esempio seguente, la sessione è in esecuzione:In the following example, the session is running:
    
    > [!div class="mx-imgBorder"]
    > ![creare una sessione livestream dalla caccia ad Azure Sentinel](./media/livestream/livestream-session.png)

6. Selezionare **Salva** dalla barra dei comandi.
    
    A meno che non si selezioni Pausa , la sessione continua a essere eseguita fino alla disconnessione dal portale di Azure.Unless you select **Pause**, the session continues to run until you be signed out from the Azure portal.

## <a name="view-your-livestream-sessions"></a>Visualizzare le sessioni livestream

1. Nel portale di Azure passare alla scheda **Sentinel** > **Threat management** > **Hunting** > **Livestream.**

2. Seleziona la sessione livestream che desideri visualizzare o modificare. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![creare una sessione livestream dalla query di caccia di Azure Sentinel](./media/livestream/livestream-tab.png)
    
    La sessione livestream selezionata si apre per poter riprodurre, mettere in pausa, modificare e così via.

## <a name="receive-notifications-when-new-events-occur"></a>Ricevere notifiche quando si verificano nuovi eventi

Poiché le notifiche livestream per i nuovi eventi usano le notifiche del portale di Azure, queste notifiche vengono visualizzate ogni volta che si usa il portale di Azure.Because livestream notifications for new events use Azure portal notifications, you see these notifications whenever you use the Azure portal. Ad esempio:

![Notifica del portale di Azure per livestreamAzure portal notification for livestream](./media/livestream/notification.png)

Selezionare la notifica per aprire il riquadro **Livestream.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevare una sessione livestream a un avviso

Puoi promuovere una sessione livestream a un nuovo avviso selezionando **Eleva per avvisare** dalla barra dei comandi della sessione livestream pertinente:

> [!div class="mx-imgBorder"]
> ![Elevare la sessione livestream a un avviso](./media/livestream/elevate-to-alert.png)

Questa azione apre la procedura guidata di creazione della regola, prepopolata con la query associata alla sessione livestream.

## <a name="next-steps"></a>Passaggi successivi

In this article, you learned how to use hunting livestream in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Caccia proattivamente alle minacce](hunting.md)
- [Utilizzare i blocchi appunti per eseguire campagne di caccia automatizzate](notebooks.md)
