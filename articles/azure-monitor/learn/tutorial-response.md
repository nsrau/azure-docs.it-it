---
title: Rispondere a eventi con avvisi di Log Analytics di Azure | Microsoft Docs
description: Questa esercitazione consente di comprendere l'invio di avvisi con Log Analytics per identificare informazioni importanti nell'area di lavoro e per segnalare problemi all'utente in modo proattivo o richiamare le azioni per tentare di correggerli.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: MGoedtel
ms.author: magoedte
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: a30b7569c14aaccb40b357cc5acb82b0e32d2895
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894651"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Rispondere agli eventi con gli avvisi di Monitoraggio di Azure
Gli avvisi in Monitoraggio di Azure possono identificare informazioni importanti nel repository di Log Analytics. Vengono creati da regole di avviso che eseguono automaticamente ricerche nei log a intervalli regolari e, se i risultati della ricerca corrispondono a determinati criteri, viene creato un record di avviso che può essere configurato per eseguire una risposta automatica.  Questa esercitazione è la continuazione dell'esercitazione [Creare e condividere i dashboard dei dati di Log Analytics](tutorial-logs-dashboards.md).   

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una regola di avviso
> * Configurare un gruppo di azioni per l'invio di notifiche tramite posta elettronica

Per completare l'esempio contenuto in questa esercitazione, è necessario disporre di una macchina virtuale esistente [connessa all'area di lavoro Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md).  

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Creare avvisi
Gli avvisi vengono creati tramite le regole di avviso in Monitoraggio di Azure e possono eseguire automaticamente query salvate o ricerche personalizzate nei log a intervalli regolari.  È possibile creare avvisi in base a metriche di prestazioni specifiche o quando vengono creati determinati eventi, in assenza di un evento o per un numero di eventi creati all'interno di un intervallo di tempo specifico.  Ad esempio, è possibile usare gli avvisi per avvisare l'utente quando l'utilizzo medio della CPU supera una determinata soglia, quando viene rilevata l'assenza di un aggiornamento o quando viene generato un evento in caso venga rilevato che un servizio di Windows specifico o un daemon Linux non è in esecuzione.  Se i risultati della ricerca nei log corrispondono a criteri specifici, viene creato un avviso. La regola può quindi eseguire automaticamente una o più azioni, ad esempio informare l'utente dell'avviso o richiamare un altro processo. 

Nell'esempio seguente viene creata una regola di avviso Unità di misura della metrica basata sulla query *Macchine virtuali di Azure - Utilizzo del processore* salvata nell'[esercitazione Visualizzare i dati](tutorial-logs-dashboards.md).  Viene creato un avviso per ogni macchina virtuale che supera la soglia del 90%.  

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nel riquadro sinistro selezionare **Avvisi** e quindi fare clic su **Nuova regola di avviso** all'inizio della pagina per creare un nuovo avviso.<br><br> ![Creazione di una nuova regola di avviso](./media/tutorial-response/alert-rule-02.png)<br>
3. Per il primo passaggio, nella sezione **Crea avviso** occorre selezionare l'area di lavoro Log Analytics come risorsa, poiché di tratta di un segnale di avviso basato su log.  Filtrare i risultati scegliendo nell'elenco a discesa la **sottoscrizione** specifica (se ne esiste più di una) che contiene la macchina virtuale e l'area di lavoro Log Analytics create in precedenza.  Filtrare **Tipo di risorsa** selezionando **Log Analytics** nell'elenco a discesa.  Infine, selezionare la **risorsa** **DefaultLAWorkspace** e fare clic su **Fine**.<br><br> ![Passaggio 1 della creazione di un avviso](./media/tutorial-response/alert-rule-03.png)<br>
4. Nella sezione **Criteri di avviso** fare clic su **Aggiungi criteri** per selezionare la query salvata e quindi specificare la logica seguita dalla regola di avviso.  Nel riquadro **Configura logica dei segnali** selezionare *Macchine virtuali di Azure - Utilizzo del processore* dall'elenco.  Il riquadro viene aggiornato in modo da presentare le impostazioni di configurazione dell'avviso.  Nella parte alta vengono visualizzati i risultati degli ultimi 30 minuti del segnale selezionato e la query di ricerca stessa.  
5. Configurare l'avviso con le informazioni seguenti:  
   a. Nell'elenco a discesa **In base a** selezionare **Unità di misura della metrica**.  Un'unità di misura della metrica creerà un avviso per ogni oggetto nella query con un valore che supera la soglia specificata.  
   b. Per **Condizione** selezionare **Maggiore di** e immettere **90** per **Soglia**.  
   c. Nella sezione "Attiva l'avviso in base a" selezionare **Violazioni consecutive**, quindi nell'elenco a discesa selezionare **Maggiore di** e immettere il valore 3.  
   d. Nella sezione Valutazione in base a modificare il valore di **Periodo** in **30** minuti. La regola verrà eseguita ogni cinque minuti e restituirà i record creati negli ultimi 30 minuti a partire dall'ora corrente.  Se si imposta il periodo di tempo su una finestra più ampia, si tiene conto del potenziale di latenza dei dati e si garantisce che la query restituisca dati per evitare un falso negativo in cui l'avviso non viene mai visualizzato.  
6. Fare clic su **Fine** per completare la regola di avviso.<br><br> ![Configurazione del segnale di avviso](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Nel secondo passaggio occorre specificare un nome per l'avviso nel campo **Nome regola di avviso**, ad esempio **Percentuale CPU maggiore del 90%** .  In **Descrizione** specificare i dettagli dell'avviso e selezionare **Critico (gravità 0)** come valore di **Gravità** nelle opzioni disponibili.<br><br> ![Configurazione dei dettagli dell'avviso](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Per attivare immediatamente la regola di avviso alla creazione, accettare il valore predefinito di **Abilita regola alla creazione**.
9. Per il terzo e ultimo passaggio occorre specificare un **gruppo di azioni**, che assicura che vengano eseguite le stesse azioni ogni volta che viene attivato un avviso e che può essere usato per ogni regola definita.  Configurare un nuovo gruppo di azioni con le informazioni seguenti:  
   a. Selezionare **Nuovo gruppo di azioni** per visualizzare il riquadro **Aggiungi gruppo di azioni**.  
   b. In **Nome gruppo di azioni** specificare un nome come **Operazioni IT - Notifica** e in **Nome breve** specificare un nome come **itops-n**.  
   c. Verificare che i valori predefiniti di **Sottoscrizione** e **Gruppo di risorse** siano corretti. Se non lo sono, selezionare i valori corretti negli elenchi a discesa.   
   d. Nella sezione Azioni specificare un nome per l'azione, ad esempio **Invio di posta elettronica**, e in **Tipo di azione** selezionare **Posta elettronica/SMS/Push/Voce** nell'elenco a discesa. Il riquadro delle proprietà **Posta elettronica/SMS/Push/Voce** si apre sulla destra per fornire informazioni aggiuntive.  
   e. Nel riquadro **Posta elettronica/SMS/Push/Voce** abilitare **Posta elettronica** e specificare un indirizzo SMTP valido a cui recapitare il messaggio.  
   f. Fare clic su **OK** per salvare le modifiche.<br><br> 

    ![Creare un nuovo gruppo di azioni](./media/tutorial-response/action-group-properties-01.png)

10. Fare clic su **OK** per completare la creazione del gruppo di azioni. 
11. Fare clic su **Crea regola di avviso** per completare la creazione della regola di avviso. L'esecuzione inizia immediatamente.<br><br> ![Completamento della creazione della nuova regola di avviso](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Visualizzare gli avvisi nel portale di Azure
Una volta creato un avviso, è possibile visualizzare gli avvisi di Azure in un unico riquadro e gestire tutte le regole di avviso nelle diverse sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso (abilitate o disabilitate), che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. È incluso anche un riepilogo aggregato di tutti gli avvisi attivati e di tutte le regole di avviso configurate o abilitate.<br><br> ![Pagina di stato degli avvisi di Azure](./media/tutorial-response/azure-alerts-02.png)  

Quando l'avviso viene attivato, la tabella riflette la condizione e il numero di occorrenze nell'intervallo di tempo selezionato (in base all'impostazione predefinita, le ultime sei ore).  La posta in arrivo dovrebbe contenere un messaggio di posta elettronica corrispondente simile all'esempio seguente, che mostra la macchina virtuale che causa l'errore e i primi risultati corrispondenti alla query di ricerca in questo caso.<br><br> ![Esempio di avviso di azione di posta elettronica](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come le regole di avviso possono identificare e rispondere in modo proattivo a un problema durante l'esecuzione di ricerche nei log a intervalli pianificati e trovando corrispondenze a determinati criteri.

Seguire questo collegamento per vedere esempi di script predefiniti di Log Analytics.  

> [!div class="nextstepaction"]
> [Esempi di script di Log Analytics](../../azure-monitor/platform/powershell-samples.md)
