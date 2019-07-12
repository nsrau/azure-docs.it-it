---
title: Configurare report per Backup di Azure
description: Configurare report di Power BI per Backup di Azure usando un insieme di credenziali di Servizi di ripristino.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: adigan
ms.openlocfilehash: 5f656a097509e9998d6fb8f157d1910cc04b7799
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705148"
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure
Questo articolo illustra i passaggi da seguire per configurare report per Backup di Azure tramite un insieme di credenziali di Servizi di ripristino. Descrive inoltre come accedere ai report con Power BI. Dopo aver completato questi passaggi, sarà possibile passare direttamente a Power BI per visualizzare, personalizzare e creare report.

> [!IMPORTANT]
> Dall'1 novembre 2018, alcuni clienti potrebbero riscontrare problemi durante il caricamento dei dati nell'app Backup di Azure in Power BI, con il messaggio "Sono stati rilevati caratteri aggiuntivi alla fine dell'input JSON. L'eccezione è stata generata dall'interfaccia IDataReader".
Ciò è dovuto a una modifica del formato in cui i dati vengono caricati nell'account di archiviazione.
Scaricare l'app più recente (versione 1.8) per evitare questo problema.
>
>

## <a name="supported-scenarios"></a>Scenari supportati
- I report di Backup di Azure sono supportati per il backup di macchine virtuali di Azure e il backup di file e cartelle nel cloud mediante l'agente di Servizi di ripristino di Azure.
- I report per il database SQL di Azure, le condivisioni di File di Azure, Data Protection Manager e il server Backup di Azure non sono attualmente supportati.
- È possibile visualizzare i report negli insiemi di credenziali e nelle sottoscrizioni, se per ogni insieme è configurato lo stesso account di archiviazione. L'account di archiviazione selezionato deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
- La frequenza di aggiornamento pianificato per il report è 24 ore in Power BI. È anche possibile eseguire un aggiornamento ad hoc dei report in Power BI. In questo caso, vengono usati i dati più recenti nell'account di archiviazione del cliente per il rendering dei report.

## <a name="prerequisites"></a>Prerequisiti
- Creare un [account di archiviazione di Azure](../storage/common/storage-quickstart-create-account.md) per configurarlo per i report. Questo account di archiviazione viene usato per archiviare i dati correlati ai report.
- [Creare un account di Power BI](https://powerbi.microsoft.com/landing/signin/) per visualizzare, personalizzare e creare report personalizzati usando il portale di Power BI.
- Registrare il provider di risorse **Microsoft.Insights**, se non è già registrato. Usare le sottoscrizioni per l'account di archiviazione e l'insieme di credenziali di Servizi di ripristino in modo da consentire il flusso dei dati dei report nell'account di archiviazione. A tale scopo, passare al portale di Azure, selezionare **Sottoscrizione** > **Provider di risorse** e scegliere questo provider per registrarlo.

## <a name="configure-storage-account-for-reports"></a>Configurare l'account di archiviazione per i report
Usare la procedura seguente per configurare l'account di archiviazione per un insieme di credenziali di Servizi di ripristino tramite il portale di Azure. Si tratta di una configurazione una tantum. Dopo aver configurato l'account di archiviazione, sarà possibile passare direttamente a Power BI per visualizzare il pacchetto di contenuto e usare i report.

1. Se l'insieme di credenziali di Servizi di ripristino è già aperto, procedere al passaggio successivo. Se non è presente un insieme di credenziali di Servizi di ripristino aperto, nel portale di Azure selezionare **Tutti i servizi**.

   * Nell'elenco di risorse digitare **Servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.
   * Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.
2. Dall'elenco di elementi visualizzato nell'insieme di credenziali, nella sezione **Monitoraggio e report**, selezionare **Report di backup** per configurare l'account di archiviazione per i report.

      ![Selezionare la voce di menu Backup Reports (Report di backup) - Passaggio 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Nel pannello **Report di backup** selezionare il collegamento **Impostazioni di diagnostica**. Verrà visualizzata l'interfaccia utente **Impostazioni di diagnostica**, usata per eseguire il push dei dati nell'account di archiviazione di un cliente.

      ![Abilitare la diagnostica - Passaggio 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Selezionare **Abilita diagnostica** per aprire un'interfaccia utente da usare per configurare un account di archiviazione.

      ![Attivare la diagnostica - Passaggio 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Nella casella **Nome** immettere un nome per l'impostazione. Selezionare la casella di controllo **Archivia in un account di archiviazione** in modo da avviare il trasferimento dei dati dei report nell'account di archiviazione.

      ![Abilitare la diagnostica - Passaggio 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Selezionare **Account di archiviazione**, la sottoscrizione e l'account di archiviazione rilevanti dall'elenco per l'archiviazione dei dati dei report e quindi fare clic su **OK**.

      ![Selezionare l'account di archiviazione - Passaggio 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Nella sezione **Log** selezionare la casella di controllo **AzureBackupReport**. Spostare il dispositivo di scorrimento per selezionare un periodo di conservazione per i dati dei report. I dati dei report verranno conservati nell'account di archiviazione per il periodo di tempo selezionato con questo dispositivo di scorrimento.

      ![Salvare l'account di archiviazione - Passaggio 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Rivedere tutte le modifiche e selezionare **Salva**. Questa azione assicura che tutte le modifiche vengano salvate e che l'account di archiviazione sia ora configurato per l'archiviazione dei dati dei report.

9. Nella tabella **Impostazioni di diagnostica** viene visualizzata la nuova impostazione abilitata per l'insieme di credenziali. Se non viene visualizzata, aggiornare la tabella per visualizzare l'impostazione aggiornata.

      ![Visualizzare l'impostazione di diagnostica - Passaggio 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Dopo aver configurato i report con il salvataggio dell'account di archiviazione, *attendere 24 ore* affinché venga completato il push di dati iniziale. Importare l'app Backup di Azure in Power BI solo dopo tale intervallo. Per altre informazioni, vedere la [sezione delle domande frequenti](backup-azure-monitor-alert-faq.md).
>
>

## <a name="view-reports-in-power-bi"></a>Visualizzare i report in Power BI
Dopo aver configurato un account di archiviazione per i report usando un insieme di credenziali di Servizi di ripristino, il trasferimento dei dati dei report inizierà dopo circa 24 ore. Trascorse 24 ore per la configurazione dell'account di archiviazione, seguire questa procedura per visualizzare i report in Power BI.
Per personalizzare e condividere il report, creare un'area di lavoro e seguire questa procedura

1. [Eseguire l'accesso](https://powerbi.microsoft.com/landing/signin/) a Power BI.
2. Selezionare **Recupera dati**. In **Altri modi per creare contenuto personalizzato** selezionare **Pacchetti di contenuto di servizi**. Seguire i passaggi nella [documentazione di Power BI per connettersi a un servizio](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Nella barra di **ricerca** immettere **Backup di Azure** e selezionare **Scarica adesso**.

      ![Ottenere il pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-get.png)
4. Immettere il nome dell'account di archiviazione configurato nel passaggio 5 precedente e selezionare **Avanti**.

    ![Immettere il nome dell'account di archiviazione](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Usando il metodo di autenticazione "Chiave", immettere la chiave per l'account di archiviazione. Per [visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../storage/common/storage-account-manage.md#access-keys), passare al proprio account di archiviazione nel portale di Azure.

     ![Immettere l'account di archiviazione](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Fare clic su **Accedi**. Dopo aver eseguito l'accesso, viene visualizzata una notifica di importazione di dati.

    ![Importazione del pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Al termine dell'importazione viene visualizzata una notifica di **operazione riuscita**. Se è presente una grande quantità di dati nell'account di archiviazione, l'importazione del pacchetto di contenuto potrebbe richiedere più tempo.

    ![Importazione riuscita del pacchetto di contenuto](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Dopo aver importato i dati, il pacchetto di contenuto **Backup di Azure** è visibile nelle **app** nel riquadro di spostamento. In **Dashboard**, **Report** e **Set di dati** l'elenco mostra ora Backup di Azure.

8. In **Dashboard** selezionare **Backup di Azure**, che illustra un set di report con chiavi associate.

      ![Dashboard di Backup di Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Per visualizzare il set completo di report, selezionare qualsiasi report nel dashboard.

      ![Integrità dei processi di Backup di Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Selezionare ogni scheda nei report per visualizzarli in tale area.

      ![Schede dei report di Backup di Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
| Dettagli errore | Risoluzione |
| --- | --- |
| Dopo avere configurato l'account di archiviazione per i report di Backup, **Account di archiviazione** rimane impostato su **Non configurato**. | Se l'account di archiviazione è stato configurato correttamente, i dati fluiranno nonostante questo problema. Per risolvere il problema, accedere al portale di Azure e selezionare **Tutti i servizi** > **Impostazioni di diagnostica** > **insieme di credenziali di Servizi di ripristino** > **Modifica l'impostazione**. Eliminare l'impostazione precedentemente configurata e crearne una nuova nello stesso pannello. In questo caso, nella casella **Nome**, selezionare **servizio**. Viene ora visualizzato l'account di archiviazione configurato. |
|Dopo aver importato il pacchetto di contenuto Backup di Azure in Power BI, verrà visualizzato il messaggio di errore "404 - Non è possibile trovare il contenitore". | Come indicato in precedenza, è necessario attendere 24 ore dopo la configurazione dei report nell'insieme di credenziali di Servizi di ripristino per poterli visualizzare correttamente in Power BI. Se si tenta di accedere ai report prima di 24 ore, verrà visualizzato questo messaggio di errore perché non sono ancora presenti i dati completi per visualizzare report validi. |

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato l'account di archiviazione e importato il pacchetto di contenuto Backup di Azure, il passaggio successivo consiste nel personalizzare i report e usare il modello di dati per creare report. Per altre informazioni, vedere gli articoli seguenti.

* [Utilizzo del modello dati di Backup di Azure](backup-azure-reports-data-model.md)
* [Filtraggio dei report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Creazione dei report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
