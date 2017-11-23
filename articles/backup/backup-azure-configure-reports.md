---
title: Configurare report per Backup di Azure
description: In questo articolo viene illustrata la configurazione dei report di Power BI per Backup di Azure usando l'insieme di credenziali di Servizi di ripristino.
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40433df5ebe90aec3a9294f2c5a6083c4567b161
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure
In questo articolo vengono illustrati i passaggi per configurare i report per Backup di Azure usando l'insieme di credenziali di Servizi di ripristino e per accedere a questi report usando Power BI. Dopo aver seguito questa procedura, è possibile passare direttamente a Power BI per visualizzare tutti i report, personalizzare e creare report. 

## <a name="supported-scenarios"></a>Scenari supportati
1. I report di Backup di Azure sono supportati per il backup di macchine virtuali Azure e il backup di file o cartelle nel cloud mediante l'agente di Servizi di ripristino di Azure.
2. In questo momento non sono supportati i report per Azure SQL, DPM e il server di Backup di Azure.
3. È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni, se per ogni insieme è configurato lo stesso account di archiviazione. L'account di archiviazione selezionato deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.
4. La frequenza di aggiornamento pianificato per il report è 24 ore in Power BI. È anche possibile eseguire un aggiornamento ad hoc dei report in Power BI, nel qual caso i dati più aggiornati nell'account di archiviazione del cliente vengono usati per il rendering dei report. 
5. I report di Backup di Azure non sono attualmente supportati nei cloud nazionali.

## <a name="prerequisites"></a>Prerequisiti
1. Creare un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) per configurarlo per i report. Questo account di archiviazione viene usato per archiviare i dati correlati ai report.
2. [Creare un account di Power BI](https://powerbi.microsoft.com/landing/signin/) per visualizzare, personalizzare e creare report personalizzati usando il portale di Power BI.
3. Registrare il provider di risorse **Microsoft.Insights** se non è stato già registrato, con la sottoscrizione dell'account di archiviazione e con la sottoscrizione dell'insieme di credenziali di Servizi di ripristino per consentire il trasferimento dei dati dei report nell'account. A tale scopo, è necessario passare al portale di Azure > Sottoscrizione > Provider di risorse e selezionare questo provider per registrarlo. 

## <a name="configure-storage-account-for-reports"></a>Configurare l'account di archiviazione per i report
Usare la procedura seguente per configurare l'account di archiviazione per l'archivio di Servizi di ripristino tramite il portale di Azure. Si tratta di una configurazione una tantum e, dopo aver configurato l'account di archiviazione, sarà possibile passare direttamente a Power BI per visualizzare il pacchetto di contenuti e usare i report.
1. Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio successivo. Se non è aperto alcun insieme di credenziali dei servizi di ripristino ma si è nel portale di Azure, nel menu dell'hub fare clic su **Esplora**.

   * Nell'elenco di risorse digitare **Servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzata l'opzione, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

      ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.
2. Dall'elenco di elementi visualizzato nell'insieme di credenziali fare clic su **Backup Reports** (Report di backup) nella sezione Monitoraggio e report per configurare l'account di archiviazione per i report.

      ![Selezionare la voce di menu Backup Reports (Report di backup) - Passaggio 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Nel pannello Report di backup fare clic sul collegamento **Impostazioni di diagnostica**. Verrà visualizzata l'interfaccia utente Impostazioni di diagnostica, usata per eseguire il push dei dati nell'account di archiviazione del cliente.

      ![Abilitare la diagnostica - Passaggio 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Fare quindi clic sul collegamento **Attiva diagnostica**. Verrà visualizzata l'interfaccia utente per la configurazione dell'account di archiviazione. 

      ![Attivare la diagnostica - Passaggio 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Immettere il nome dell'impostazione nel campo **Nome** e selezionare la casella di controllo **Archivia in un account di archiviazione** in modo da avviare il trasferimento dei dati dei report nell'account di archiviazione.

      ![Abilitare la diagnostica - Passaggio 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Fare clic sul selettore di account di archiviazione e selezionare la sottoscrizione e l'account di archiviazione rilevanti dall'elenco per l'archiviazione dei dati dei report e quindi fare clic su **OK**.

      ![Selezionare l'account di archiviazione - Passaggio 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Selezionare la casella di controllo **AzureBackupReport** nella sezione Log e spostare il dispositivo di scorrimento per selezionare il periodo di conservazione dei dati dei report. I dati dei report verranno conservati nell'account di archiviazione per il periodo di tempo selezionato con questo dispositivo di scorrimento.

      ![Salvare l'account di archiviazione - Passaggio 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Esaminare tutte le modifiche e fare clic sul pulsante **Salva** nella parte superiore, come illustrato nella figura precedente. Questa azione assicura che tutte le modifiche vengano salvate e che l'account di archiviazione sia ora configurato per l'archiviazione dei dati dei report.

9. Nella tabella impostazioni di diagnostica viene visualizzata la nuova impostazione abilitata per l'insieme di credenziali. Se non viene visualizzato, aggiornare la tabella per visualizzare l'impostazione aggiornata.

      ![Visualizzare l'impostazione di diagnostica - Passaggio 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Dopo aver configurato i report con il salvataggio dell'account di archiviazione, è consigliabile **attendere 24 ore** affinché venga completato il push di dati iniziale. È consigliabile importare il pacchetto di contenuto di Backup di Azure in Power BI solo dopo tale intervallo. Per altri dettagli vedere la [sezione delle domande frequenti](#frequently-asked-questions). 
>
>

## <a name="view-reports-in-power-bi"></a>Visualizzare i report in Power BI 
Dopo aver configurato l'account di archiviazione per i report usando l'insieme di credenziali di Servizi di ripristino, il trasferimento dei dati dei report inizierà dopo circa 24 ore. Trascorse 24 ore per la configurazione dell'account di archiviazione, usare la procedura seguente per visualizzare i report in Power BI:
1. [Eseguire l'accesso](https://powerbi.microsoft.com/landing/signin/) a Power BI.
2. Fare clic su **Recupera dati** e selezionare Recupera in **Servizi** nella libreria del pacchetto di contenuto. Usare i passaggi indicati nella [documentazione di Power BI per accedere al pacchetto di contenuto](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importare il pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-import.png)
3. Digitare **Backup di Azure** nella barra di ricerca e fare clic su **Scarica adesso**.

      ![Ottenere il pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-get.png)
4. Immettere il nome dell'account di archiviazione configurato nel passaggio 5 riportato sopra e fare clic sul pulsante **Avanti**.

    ![Immettere il nome dell'account di archiviazione](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Immettere la chiave per l'account di archiviazione. È possibile [visualizzare e copiare le chiavi di accesso agli account di archiviazione](../storage/common/storage-create-storage-account.md#manage-your-storage-account) passando al proprio account di archiviazione nel portale di Azure. 

     ![Immettere l'account di archiviazione](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Fare clic sul pulsante **Accedi**. Dopo aver eseguito l'accesso, viene visualizzata la notifica **Importazione di dati**.

    ![Importazione del pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    In un secondo momento verrà visualizzata la notifica **Operazione riuscita** al termine dell'importazione. L'importazione del pacchetto di contenuto potrebbe richiedere più tempo, se è presente una grande quantità di dati nell'account di archiviazione.
    
    ![Importazione riuscita del pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Dopo aver importato i dati, il pacchetto di contenuto **Backup di Azure** è visibile nelle **app** nel riquadro di spostamento. L'elenco mostra ora il dashboard, i report e il set di dati di Backup di Azure con una stella gialla a indicare i report appena importati. 

     ![Pacchetto di contenuto Backup di Azure](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Fare clic su **Backup di Azure** nel dashboard, che illustra un set di report con chiavi associate.

      ![Dashboard di Backup di Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Per visualizzare il set completo di report, fare clic su qualsiasi report nel dashboard.

      ![Integrità dei processi di Backup di Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Fare clic su ogni scheda nei report per visualizzarli in tale area.

      ![Schede dei report di Backup di Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Domande frequenti
1. **Come verificare se è stato avviato il trasferimento dei dati dei report nell'account di archiviazione?**
    
    È possibile passare all'account di archiviazione configurato e selezionare i contenitori. Se il contenitore include una voce per insights-logs-azurebackupreport, indica che è stato avviato il trasferimento dei dati dei report.

2. **Qual è la frequenza del push di dati nell'account di archiviazione e nel pacchetto di contenuto di Backup di Azure in Power BI?**

   Per gli utenti al primo tentativo sono necessarie circa 24 ore per il push dei dati nell'account di archiviazione. Dopo aver completato il push iniziale, i dati vengono aggiornati con la frequenza illustrata nella figura riportata di seguito. 
      * I dati correlati a **processi, avvisi, elementi di backup, insiemi di credenziali, server protetti e criteri** vengono sottoposti a push nell'account di archiviazione a ogni accesso.
      * I dati correlati all'**archiviazione** vengono sottoposti a push nell'account di archiviazione del cliente ogni 24 ore.
   
    ![Frequenza del push di dati dei report di Backup di Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI esegue un [aggiornamento pianificato una volta al giorno](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). È possibile eseguire un aggiornamento manuale dei dati in Power BI per il pacchetto di contenuto.

3. **Per quanto tempo è possibile conservare i report?** 

   Quando si configura l'account di archiviazione, è possibile selezionare il periodo di conservazione dei dati dei report nell'account di archiviazione (tramite il passaggio 6 Configurare l'account di archiviazione per la sezione dei report precedente). È anche possibile [analizzare i report in Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvarli per un periodo di conservazione più lungo, in base alle esigenze. 

4. **I dati saranno visibili nei report dopo aver configurato l'account di archiviazione?**

   Tutti i dati generati dopo aver **configurato l'account di archiviazione** verranno sottoposti a push nell'account di archiviazione e saranno disponibili nei report. Tuttavia, **i processi in corso non vengono sottoposti a push** per la creazione dei report. Un processo completato con esito positivo o negativo viene inviato ai report.

5. **Se è già stato configurato l'account di archiviazione per visualizzare i report, è possibile modificare la configurazione per usare un altro account di archiviazione?** 

   Sì, è possibile modificare la configurazione in modo che punti a un account di archiviazione diverso. Durante la connessione al pacchetto di contenuto di Backup di Azure è consigliabile usare l'account di archiviazione appena configurato. Dopo aver configurato un account di archiviazione diverso, i nuovi dati verranno trasferiti in questo account di archiviazione. Tuttavia, i dati meno recenti (prima di modificare la configurazione) rimarranno nell'account di archiviazione precedente.

6. **È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni?** 

   Sì, è possibile configurare lo stesso account di archiviazione per vari insiemi di credenziali per visualizzare i report tra loro. È anche possibile configurare lo stesso account di archiviazione per gli insiemi di credenziali nelle sottoscrizioni. È quindi possibile usare questo account di archiviazione durante la connessione al pacchetto di contenuto di Backup di Azure in Power BI per visualizzare i report. L'account di archiviazione selezionato deve tuttavia trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.
   
## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
| Dettagli errore | Risoluzione |
| --- | --- |
| Dopo avere configurato l'account di archiviazione per i report di Backup, **Account di archiviazione** rimane impostato su **Non configurato**. | Se l'account di archiviazione è stato configurato correttamente, i dati fluiranno nonostante questo problema. Per risolvere il problema, passare al portale di Azure > Altri servizi > Impostazioni di diagnostica > Insieme di credenziali dei servizi di ripristino > Modifica impostazione. Eliminare l'impostazione precedentemente configurata e crearne una nuova dallo stesso pannello. Questa volta impostare il campo **Nome** su **servizio**. Questa operazione dovrebbe determinare la visualizzazione dell'account di archiviazione configurato. |
|Al termine dell'importazione del pacchetto di contenuto di Backup di Azure in Power BI, viene visualizzato l'errore **404 - Contenitore non trovato**. | Come indicato in questo documento, è necessario attendere 24 ore dopo la configurazione dei report nell'insieme di credenziali dei Servizi di ripristino per poterli visualizzare correttamente in Power BI. Se si tenta di accedere ai report prima di 24 ore, verrà visualizzato questo errore perché non sono ancora presenti i dati completi per visualizzare report validi. |

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato l'account di archiviazione e importato il pacchetto di contenuto Backup di Azure, il passaggio successivo consiste nel personalizzare questi report e usare il modello di dati per creare report. Per maggiori dettagli, consultare gli articoli seguenti.

* [Utilizzo del modello dati di Backup di Azure](backup-azure-reports-data-model.md)
* [Filtraggio dei report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Creazione dei report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

