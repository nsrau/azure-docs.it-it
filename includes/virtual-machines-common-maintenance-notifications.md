
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Visualizzare le macchine virtuali con manutenzione pianificata nel portale

Dopo la pianificazione di un'ondata di manutenzione e l'invio delle relative notifiche, è possibile osservare l'elenco delle macchine virtuali interessate dalla prossima ondata. 

È possibile usare il portale di Azure per cercare le macchine virtuali pianificate per la manutenzione.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro di spostamento a sinistra fare clic su **Macchine virtuali**.

3. Nel riquadro Macchine virtuali fare clic sul pulsante **Colonne** per aprire l'elenco delle colonne disponibili.

4. Selezionare e aggiungere le colonne seguenti:

   **Manutenzione** - Mostra lo stato di manutenzione della macchina virtuale. Ecco di seguito i valori possibili:
      
      | Valore | DESCRIZIONE |
      |-------|-------------|
      | Avvia ora | La macchina virtuale si trova nella finestra di manutenzione self-service, il che consente di avviare la manutenzione manualmente. Vedere di seguito per informazioni su come avviare la manutenzione della VM. | 
      | Pianificato | La macchina virtuale è pianificata per la manutenzione senza alcuna opzione per poter avviare manutenzione. Per informazioni relative alla finestra di manutenzione, è possibile selezionare la finestra della pianificazione automatica in questa visualizzazione oppure fare clic sulla macchina virtuale. | 
      | Completed | La manutenzione della macchina virtuale è stata avviata e completata correttamente. | 
      | Skipped| Si è scelto di avviare la manutenzione, ma senza successo. Non sarà in grado di utilizzare l'opzione di self-service di manutenzione. La macchina virtuale dovrà essere riavviato da Azure durante la fase di manutenzione pianificata. | 

   **Finestra Manutenzione - Attiva** - Mostra la finestra temporale in cui è possibile avviare autonomamente la manutenzione delle macchine virtuali.
   
   **Maintenance Scheduled** (Manutenzione pianificata) - Mostra la finestra temporale in cui Azure riavvierà la macchina virtuale per completare la manutenzione. 




## <a name="notification-and-alerts-in-the-portal"></a>Notifiche e avvisi del portale

Azure comunica il programma di una manutenzione pianificata inviando un messaggio di posta elettronica al proprietario e ai comproprietari della sottoscrizione. È possibile aggiungere altri destinatari e canali a questa comunicazione creando avvisi del log attività di Azure. Per altre informazioni, vedere [Monitorare l'attività di sottoscrizione con il log attività di Azure] (../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Monitoraggio** dal menu a sinistra. 
3. Nel riquadro **Monitoraggio - Log attività** selezionare **Avvisi**.
4. Nel riquadro **Monitoraggio - Avvisi** fare clic su **+ Aggiungi avviso del log attività**.
5. Completare le informazioni nella pagina **Aggiungi avviso del log attività** e assicurarsi di impostare i valori seguenti in **Criteri**: **Tipo**: Manutenzione **Stato**: Tutti (non impostare lo stato su Attivo o Risolto) **Livello**: Tutti
    
Per altre informazioni su come configurare gli avvisi del log attività, vedere [Creare avvisi del log attività](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Avviare la manutenzione della macchina virtuale dal portale

Mentre si esaminano i dettagli della macchina virtuale, è possibile visualizzare ulteriori dettagli relativi alla manutenzione.  
Nella parte superiore della visualizzazione dei dettagli della macchina virtuale viene aggiunta una nuova barra delle notifiche nel caso in cui la macchina virtuale sia inclusa in un'ondata di manutenzione pianificata. Inoltre, laddove possibile, viene aggiunta una nuova opzione per avviare la manutenzione. 


Fare clic sulla notifica di manutenzione per visualizzare la pagina Manutenzione con maggiori dettagli sulla manutenzione pianificata. Da questa pagina è possibile **avviare la manutenzione** della macchina virtuale.

Dopo l'avvio della manutenzione, la macchina virtuale viene riavviata e lo stato di manutenzione viene aggiornato per riflettere il risultato entro pochi minuti.

Se si perde la finestra in cui è possibile avviare manutenzione, è comunque possibile visualizzarla quando la macchina virtuale viene riavviata da Azure. 
