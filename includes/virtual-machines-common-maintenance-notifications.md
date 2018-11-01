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
ms.openlocfilehash: b3f71c4710bd9711a3209dd55f8e680f63627c1b
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035156"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Visualizzare le macchine virtuali con manutenzione pianificata nel portale

Dopo la programmazione di un ciclo di manutenzione pianificata è possibile osservare l'elenco delle macchine virtuali interessate dal prossimo ciclo. 

È possibile usare il portale di Azure per cercare le macchine virtuali pianificate per la manutenzione.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro di spostamento a sinistra fare clic su **Macchine virtuali**.

3. Nel riquadro Macchine virtuali fare clic sul pulsante **Colonne** per aprire l'elenco delle colonne disponibili.

4. Selezionare e aggiungere le colonne seguenti:

   **Manutenzione**: Mostra lo stato di manutenzione della macchina virtuale. Ecco di seguito i valori possibili:
      
      | Valore | DESCRIZIONE |
      |-------|-------------|
      | Avvia ora | La macchina virtuale si trova nella finestra di manutenzione self-service, il che consente di avviare la manutenzione manualmente. Vedere di seguito per informazioni su come avviare la manutenzione della macchina virtuale. | 
      | Pianificato | La macchina virtuale è pianificata per la manutenzione senza alcuna opzione per poter avviare manutenzione. Per informazioni relative alla finestra di manutenzione, è possibile selezionare la finestra di manutenzione pianificata in questa visualizzazione oppure fare clic sulla macchina virtuale. | 
      | Già aggiornato | La macchina virtuale è già aggiornata e in questo momento non è richiesta alcuna azione ulteriore. | 
      | Riprova più tardi | La manutenzione è stata avviata, ma senza successo. Sarà possibile usare l'usare di manutenzione self-service in un secondo momento. | 
      | Riprova adesso | È possibile riprovare a eseguire una manutenzione avviata automaticamente non riuscita in precedenza. | 
      | - | La macchina virtuale non fa parte di un'ondata di manutenzione pianificata. |
      

   **Finestra Manutenzione - Self-service** - Mostra la finestra temporale in cui è possibile avviare autonomamente la manutenzione delle macchine virtuali.
   
   **Finestra Manutenzione - Pianificata**: Mostra la finestra temporale in cui Azure competerà la manutenzione della macchina virtuale. 



## <a name="notification-and-alerts-in-the-portal"></a>Notifiche e avvisi del portale

Azure comunica il programma di una manutenzione pianificata inviando un messaggio di posta elettronica al proprietario e ai comproprietari della sottoscrizione. È possibile aggiungere altri destinatari e canali a questa comunicazione creando avvisi del log attività di Azure. Per altre informazioni, vedere [Monitorare l'attività di sottoscrizione con il log attività di Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Monitoraggio** dal menu a sinistra. 
3. Nel riquadro **Monitoraggio - Avvisi (versione classica)** fare clic su **+ Aggiungi avviso del log attività**.
5. Completare le informazioni contenute sulla pagina **Aggiungi avviso del log attività** e assicurarsi di impostare i seguenti **Criteri**:
   - **Categoria evento**: Integrità dei servizi
   - **Servizi**: Set di scalabilità di macchine virtuali e Macchine virtuali
   - **Tipo**: Manutenzione pianificata 
    
Per altre informazioni su come configurare gli avvisi del log attività, vedere [Creare avvisi del log attività](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Avviare la manutenzione della macchina virtuale dal portale

Mentre si esaminano i dettagli della macchina virtuale, è possibile visualizzare ulteriori dettagli relativi alla manutenzione.  
Nella parte superiore della visualizzazione dei dettagli della macchina virtuale viene aggiunta una nuova barra delle notifiche nel caso in cui la macchina virtuale sia inclusa in un'ondata di manutenzione pianificata. Inoltre, laddove possibile, viene aggiunta una nuova opzione per avviare la manutenzione. 


Fare clic sulla notifica di manutenzione per visualizzare la pagina Manutenzione con maggiori dettagli sulla manutenzione pianificata. Da questa pagina è possibile **avviare la manutenzione** della macchina virtuale.

La manutenzione della macchina virtuale viene avviata e lo stato di manutenzione viene aggiornato per riflettere il risultato entro pochi minuti.

Se si perde la finestra Self-service, è comunque possibile visualizzarla quando viene eseguita la manutenzione della macchina virtuale da Azure. 
