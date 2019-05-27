---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 50a215175d7305834a64b7e0cfbc153431b10b7c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116958"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Visualizzare le macchine virtuali con manutenzione pianificata nel portale

Dopo la programmazione di un ciclo di manutenzione pianificata è possibile osservare l'elenco delle macchine virtuali interessate dal prossimo ciclo. 

È possibile usare il portale di Azure per cercare le macchine virtuali pianificate per la manutenzione.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro di spostamento a sinistra fare clic su **Macchine virtuali**.

3. Nel riquadro Macchine virtuali fare clic sul pulsante **Colonne** per aprire l'elenco delle colonne disponibili.

4. Selezionare e aggiungere le colonne seguenti:

   **Manutenzione**: mostra lo stato di manutenzione della macchina virtuale. Ecco di seguito i valori possibili:
      
      | Value | Descrizione |
      |-------|-------------|
      | Inizia adesso | La macchina virtuale si trova nella finestra di manutenzione self-service, il che consente di avviare la manutenzione manualmente. Vedere di seguito per informazioni su come avviare la manutenzione della macchina virtuale. | 
      | Pianificata | La macchina virtuale è pianificata per la manutenzione senza alcuna opzione per poter avviare manutenzione. Per informazioni relative alla finestra di manutenzione, è possibile selezionare la finestra di manutenzione pianificata in questa visualizzazione oppure fare clic sulla macchina virtuale. | 
      | Già aggiornato | La macchina virtuale è già aggiornata e in questo momento non è richiesta alcuna azione ulteriore. | 
      | Riprova più tardi | La manutenzione è stata avviata, ma senza successo. Sarà possibile usare l'usare di manutenzione self-service in un secondo momento. | 
      | Riprova adesso | È possibile riprovare a eseguire una manutenzione avviata automaticamente non riuscita in precedenza. | 
      | - | La macchina virtuale non fa parte di un'ondata di manutenzione pianificata. |
      

   **Finestra Manutenzione - Self-service**: mostra la finestra temporale in cui è possibile avviare autonomamente la manutenzione delle macchine virtuali.
   
   **Finestra Manutenzione - Pianificata**: mostra la finestra temporale in cui Azure completerà la manutenzione della macchina virtuale. 



## <a name="notification-and-alerts-in-the-portal"></a>Notifiche e avvisi del portale

Azure comunica il programma di una manutenzione pianificata inviando un messaggio di posta elettronica al proprietario e ai comproprietari della sottoscrizione. È possibile aggiungere altri destinatari e canali a questa comunicazione creando avvisi del log attività di Azure. Per maggiori informazioni, vedere [Creare gli avvisi del log attività per le notifiche del servizio](../articles/azure-monitor/platform/alerts-activity-log-service-notifications.md).

Assicurarsi di impostare **Tipo di evento** su **Manutenzione pianificata** e **Servizi** su **Set di scalabilità di macchine virtuali** e/o **Macchine virtuali**
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Avviare la manutenzione della macchina virtuale dal portale

Mentre si esaminano i dettagli della macchina virtuale, è possibile visualizzare ulteriori dettagli relativi alla manutenzione.  
Nella parte superiore della visualizzazione dei dettagli della macchina virtuale viene aggiunta una nuova barra delle notifiche nel caso in cui la macchina virtuale sia inclusa in un'ondata di manutenzione pianificata. Inoltre, laddove possibile, viene aggiunta una nuova opzione per avviare la manutenzione. 


Fare clic sulla notifica di manutenzione per visualizzare la pagina Manutenzione con maggiori dettagli sulla manutenzione pianificata. Da questa pagina è possibile **avviare la manutenzione** della macchina virtuale.

La manutenzione della macchina virtuale viene avviata e lo stato di manutenzione viene aggiornato per riflettere il risultato entro pochi minuti.

Se si perde la finestra Self-service, è comunque possibile visualizzarla quando viene eseguita la manutenzione della macchina virtuale da Azure. 
