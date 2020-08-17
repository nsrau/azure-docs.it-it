---
title: Eseguire il backup di file in Azure Stack macchine virtuali
description: Usare Backup di Azure per eseguire il backup e ripristinare file e applicazioni di Azure Stack nell'ambiente di Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: b45e3a0916f46f247503ea336105646f5538246c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263130"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Eseguire il backup di file e applicazioni in Azure Stack

È possibile usare Backup di Azure per proteggere (o eseguire il backup) di file e applicazioni in Azure Stack. Per eseguire il backup di file e applicazioni, installare il server di Backup di Microsoft Azure come macchina virtuale in esecuzione in Azure Stack. È possibile proteggere i file in un server di Azure Stack che si trova nella stessa rete virtuale. Dopo aver installato il server di Backup di Azure, aggiungere i dischi di Azure per aumentare lo spazio di archiviazione locale disponibile per i dati di backup a breve termine. Il server di Backup di Azure usa l'archiviazione di Azure per la conservazione a lungo termine.

> [!NOTE]
> Anche se il server di Backup di Azure e System Center Data Protection Manager (DPM) sono simili, DPM non è supportato per l'uso con Azure Stack.
>

Questo articolo non illustra l'installazione di server di Backup di Azure nell'ambiente Azure Stack. Per installare il server di Backup di Azure in Azure Stack, vedere l'articolo [Installazione del server di Backup di Azure](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Eseguire il backup di file e cartelle nelle macchine virtuali di Azure Stack su Azure

Per configurare il serve di Backup di Azure per proteggere i file nelle macchine virtuali di Azure Stack, aprire la console del server di Backup di Azure. La console consente di configurare i gruppi protezione dati e di proteggere i dati nelle macchine virtuali.

1. Nella console di server di Backup di Azure selezionare **protezione** e nella barra degli strumenti selezionare **nuovo** per aprire la procedura guidata **Crea nuovo gruppo protezione** dati.

   ![Configurare la protezione nella console del server di Backup di Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    L'apertura della procedura guidata può richiedere alcuni secondi. Una volta aperta la procedura guidata, selezionare **Avanti** per passare alla schermata **Seleziona tipo di gruppo protezione** dati.

   ![Si apre la creazione guidata del nuovo gruppo protezione dati](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Nella schermata **Selezione tipo di gruppo protezione** dati scegliere **Server** e fare clic su **Avanti**.

    ![Selezione tipo di gruppo protezione dati](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Viene visualizzata la schermata **Selezione membri del gruppo**.

    ![Seleziona membri del gruppo](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Nella schermata **Seleziona membri del gruppo** selezionare **+** per espandere l'elenco degli elementi secondari. Selezionare la casella di controllo di tutti gli elementi da proteggere. Dopo aver selezionato tutti gli elementi, fare clic su **Avanti**.

    ![Selezionare ogni elemento che si desidera proteggere](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Si consiglia di inserire tutti i dati che condivideranno un criterio di protezione in un unico gruppo protezione dati. Per informazioni complete sulla pianificazione e la distribuzione dei gruppi protezione dati, vedere l'articolo di System Center DPM relativo alla [distribuzione dei gruppi protezione dati](/system-center/dpm/create-dpm-protection-groups).

4. Nella schermata **Seleziona metodo protezione dati** digitare un nome per il gruppo protezione dati. Selezionare la casella di controllo per la **protezione dati a breve termine** e la **protezione dati online**. Selezionare **Avanti**.

    ![Selezione metodo protezione dati](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Per selezionare la **protezione dati online**, è necessario selezionare prima la **protezione dati a breve termine** con disco. Server di Backup di Azure non protegge su nastro, pertanto il disco è l'unica scelta per la protezione dati a breve termine.

5. Nella schermata **Specifica obiettivi a breve termine** indicare per quanto tempo verranno conservati i punti di recupero salvati su disco e quando salvare i backup incrementali. Selezionare **Avanti**.

    > [!IMPORTANT]
    > **Non** è necessario conservare i dati di ripristino operativo (backup) nei dischi collegati al server di Backup di Azure per più di cinque giorni.
    >

    ![Specifica obiettivi a breve termine](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Invece di selezionare un intervallo per i backup incrementali, per eseguire un backup completo rapido appena prima di ogni punto di ripristino pianificato, selezionare **immediatamente prima di un punto di ripristino**. Se si vogliono proteggere i carichi di lavoro dell'applicazione, il server di Backup di Azure crea punti di recupero per la pianificazione di frequenza di sincronizzazione, a condizione che l'applicazione supporti i backup incrementali. Se l'applicazione non supporta i backup incrementali, il server di Backup di Azure esegue un backup completo rapido.

    Per i **punti di recupero dei file**, specificare quando devono essere creati i punti di recupero. Selezionare **modifica** per impostare gli orari e i giorni della settimana in cui vengono creati i punti di ripristino.

6. Nella schermata **Verifica allocazione dischi** esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.

    Le **dimensioni dati totali** sono le dimensioni dei dati di cui si esegue il backup e lo **spazio su disco per il provisioning** nel server di Backup di Azure è lo spazio consigliato per il gruppo protezione dati. Il server di Backup di Azure sceglie il volume di backup in base alle impostazioni. Tuttavia è possibile modificare le opzioni del volume di backup nei dettagli di allocazione del disco. Per i carichi di lavoro, selezionare la risorsa di archiviazione preferita nel menu a discesa. Vengono modificati i valori per lo spazio di archiviazione totale e lo spazio di archiviazione libero nel riquadro Spazio di archiviazione su disco disponibile. Per spazio con provisioning insufficiente si intende la quantità di spazio di archiviazione che il server di Backup di Azure suggerisce di aggiungere al volume per continuare a eseguire backup uniformi in futuro.

7. In **Scelta del metodo per la creazione della replica** selezionare come gestire la replica dei dati completa iniziale. Se si decide di eseguire la replica in rete, Azure suggerisce di scegliere un orario di scarso traffico. Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati con i supporti rimovibili.

8. Nella pagina **Scelta opzioni di verifica coerenza** selezionare il modo in cui automatizzare le verifiche della coerenza. Abilitare le verifiche della coerenza in modo che vengano eseguite solo quando la replica dei dati diventa incoerente o in base a una pianificazione. Per non configurare verifiche della coerenza automatiche, eseguire una verifica manuale in qualsiasi momento:
    * Nell'area di **protezione** della console del server di Backup di Azure fare clic con il pulsante destro del mouse sul gruppo protezione dati e selezionare **Esegui verifica coerenza**.

9. Se si è scelto di eseguire il backup in Azure, nella pagina **Specifica i dati da proteggere online** verificare che siano selezionati i carichi di lavoro di cui eseguire il backup in Azure.

10. Nella pagina **Specificare la pianificazione dei backup online** indicare quando devono essere eseguiti i backup incrementali in Azure.

    È possibile pianificare l'esecuzione di backup giornalieri, settimanali, mensili e annuali nonché la data e l'ora in cui eseguirli. È possibile eseguire i backup fino a due volte al giorno. Ogni volta che viene eseguito un processo di backup, viene creato in Azure un punto di recupero dei dati dalla copia dei dati di backup archiviati nel disco del server di Backup di Azure.

11. In **Specificare i criteri di conservazione online** indicare la modalità di conservazione in Azure dei punti di recupero creati dai backup giornalieri, settimanali, mensili e annuali.

12. In **Scegliere la replica online** specificare la modalità di esecuzione della replica completa iniziale dei dati.

13. In **Riepilogo** esaminare le impostazioni. Quando si seleziona **Crea gruppo**, viene eseguita la replica iniziale dei dati. Al termine della replica dei dati, nella pagina **Stato** lo stato del gruppo protezione dati è **OK**. Il processo di backup iniziale viene eseguito in linea con le impostazioni del gruppo protezione dati.

## <a name="recover-file-data"></a>Recuperare i dati dei file

Usare la console del server di Backup di Azure per recuperare i dati nella macchina virtuale.

1. Nella console di server di Backup di Azure, sulla barra di spostamento, selezionare **ripristino** e cercare i dati che si desidera ripristinare. Selezionare i dati nel riquadro dei risultati.

2. Nel calendario della sezione dei punti di recupero le date in grassetto indicano i punti di recupero disponibili. Selezionare la data da ripristinare.

3. Nel riquadro **Elemento ripristinabile** selezionare l'elemento da recuperare.

4. Nel riquadro **azioni** selezionare **Ripristina** per aprire il ripristino guidato.

5. È possibile recuperare i dati come segue:

    * **Recupero nel percorso originale**: se il computer client è connesso via VPN, questa operazione non funziona. In alternativa, usare un percorso alternativo e quindi copiare i dati da quel percorso.
    * **Recupero in un percorso alternativo**

6. Specificare le opzioni di recupero:

    * Come **comportamento del recupero della versione esistente** selezionare **Creare copia**, **Ignora** oppure **Sovrascrivi**. La sovrascrittura è disponibile solo quando si recuperano i dati nel percorso originale.
    * Come **protezione del ripristino** scegliere di **applicare le impostazioni del computer di destinazione** oppure **applicare le impostazioni di sicurezza della versione del punto di recupero**.
    * Per la **limitazione all'utilizzo della larghezza di banda di rete**, selezionare **modifica** per abilitare la limitazione all'utilizzo della larghezza di banda.
    * **Notifica** di Selezionare **Invia messaggio di posta elettronica al termine del ripristino**e specificare i destinatari che riceveranno la notifica. Separare gli indirizzi di posta elettronica con virgole.
    * Dopo aver effettuato le selezioni, fare clic su **Avanti** .

7. Verificare le impostazioni di ripristino e selezionare **Ripristina**.

    >[!Note]
    >Mentre il processo di recupero è in corso, vengono annullati tutti i processi di sincronizzazione per gli elementi selezionati per il recupero.

Se si usa Modern Backup Storage (MBS), il recupero dati del file server gestito dall'utente non è supportato. Il recupero gestito dall'utente (EUR) delle risorse del file server ha una dipendenza nel Servizio Copia Shadow del volume, che Modern Backup Storage non usa. Se EUR è abilitato, usare la procedura seguente per recuperare i dati:

1. Passare ai file protetti, fare clic con il pulsante destro del mouse sul nome del file e selezionare **Proprietà**.

2. Nel menu **Proprietà** selezionare **versioni precedenti** e scegliere la versione che si desidera ripristinare.

## <a name="view-azure-backup-server-with-a-vault"></a>Visualizzare il server di Backup di Azure con un insieme di credenziali

Per visualizzare server di Backup di Azure entità nell'portale di Azure, attenersi alla procedura seguente:

1. Aprire l'insieme di credenziali dei Servizi di ripristino.
2. Selezionare infrastruttura di backup.
3. Visualizzare i server di gestione dei backup.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso del server di Backup di Azure per proteggere altri carichi di lavoro, vedere uno degli articoli seguenti:

* [Eseguire il backup di una farm di SharePoint](./backup-mabs-sharepoint-azure-stack.md)
* [Eseguire il backup di SQL Server](./backup-mabs-sql-azure-stack.md)
