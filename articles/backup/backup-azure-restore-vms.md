
<properties
	pageTitle="Backup di Azure: Ripristinare una macchina virtuale"
	description="Informazioni su come ripristinare una macchina virtuale di Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="trinadhk"; "jimpark"/>

# Ripristinare una macchina virtuale
È possibile ripristinare una macchina virtuale in una nuova macchina virtuale dai backup archiviati in un insieme di credenziali per il backup di Azure.

## Scegliere un elemento da ripristinare

1. Passare alla scheda **Elementi protetti** e selezionare la macchina virtuale che si desidera ripristinare in una nuova macchina virtuale.

    ![Elementi protetti](./media/backup-azure-restore-vms/protected-items.png)

    Nella colonna **Punto di ripristino** della pagina **Elementi protetti** verrà visualizzato il numero di punti di ripristino per una macchina virtuale. Nella colonna **Punto di ripristino più recente** viene visualizzata l'ora del backup più recente da cui è possibile ripristinare una macchina virtuale.

2. Fare clic su **Ripristina** per aprire la procedura guidata **Ripristina elemento**.

    ![Ripristinare un elemento](./media/backup-azure-restore-vms/restore-item.png)

## Scegliere un punto di ripristino

1. Nella schermata per la **selezione di un punto di ripristino** è possibile eseguire il ripristino dal punto più recente o da un punto precedente. L'opzione predefinita selezionata all'avvio della procedura guidata è *Punto di ripristino più recente*.

    ![Selezionare un punto di ripristino](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Per selezionare un punto precedente, scegliere l'opzione **Seleziona data** nell'elenco a discesa e selezionare una data nel controllo calendario facendo clic sull'**icona del calendario**. Nel controllo tutte le date in cui sono presenti punti di ripristino hanno un'ombreggiatura di colore grigio chiaro e sono selezionabili dall'utente.

    ![Selezionare una data](./media/backup-azure-restore-vms/select-date.png)

    Quando si fa clic su una data nel controllo calendario, i punti di ripristino disponibili in quella data verranno visualizzati nella tabella dei punti di ripristino riportata di seguito. Nella colonna **Ora** viene visualizzata l'ora in cui è stato creato lo snapshot. Nella colonna **Tipo** viene visualizzata la [coerenza](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) del punto di ripristino. L'intestazione della tabella mostra il numero di punti di ripristino disponibili per il giorno in parentesi.

    ![Punti di ripristino](./media/backup-azure-restore-vms/recovery-points.png)

3. Selezionare il punto di ripristino dalla tabella **Punti di ripristino** e fare clic sulla freccia Avanti per passare alla schermata successiva.

## Specificare un percorso di destinazione

1. Nella schermata per la **selezione di un'istanza di ripristino** specificare i dettagli del percorso in cui ripristinare la macchina virtuale.

  - Specificare il nome della macchina virtuale: in un determinato servizio cloud il nome della macchina virtuale deve essere univoco. Se si prevede di sostituire una macchina virtuale esistente con lo stesso nome, eliminare prima la macchina virtuale esistente e i relativi dischi dati, quindi ripristinare i dati da Backup di Azure.
  - Selezionare un servizio cloud per la macchina virtuale: questa operazione è necessaria per la creazione di una macchina virtuale. È possibile scegliere di usare un servizio cloud esistente o di crearne uno nuovo.

        Qualunque nome venga scelto per il servizio cloud deve essere univoco. In genere, il nome del servizio cloud viene associato a un indirizzo URL rivolto al pubblico, con un formato simile al seguente: [cloudservice].cloudapp.net.  Azure non consentirà la creazione di un nuovo servizio cloud se il nome scelto è già in uso. Se si decide di creare un nuovo servizio cloud, gli verrà dato lo stesso nome della macchina virtuale, il quale dovrebbe risultare univoco e, di conseguenza, essere applicato al servizio cloud associato.

        Verranno visualizzati soltanto servizi cloud e reti virtuali non associati a gruppi di affinità nei dettagli dell'istanza di ripristino. [Ulteriori informazioni](https://msdn.microsoft.com/it-it/library/azure/jj156085.aspx).

2. Selezionare un account di archiviazione per la macchina virtuale: questa operazione è necessaria per la creazione di una macchina virtuale. È possibile selezionare uno degli account di archiviazione esistenti nella stessa area geografica dell'insieme di credenziali di Backup di Azure. Gli account di archiviazione con ridondanza della zona o con archiviazione di tipo Premium non sono supportati.

    Se non è presente alcun account di archiviazione con una configurazione supportata, crearne uno prima di avviare l'operazione di ripristino.

    ![Selezionare una rete virtuale](./media/backup-azure-restore-vms/restore-sa.png)

3. Selezionare una rete virtuale: la rete virtuale (VNET) per la macchina virtuale dovrebbe essere selezionata al momento della creazione della macchina virtuale. L'interfaccia utente di ripristino mostra tutte le reti virtuali all'interno della sottoscrizione che possono essere usate. Non è obbligatorio selezionare una rete virtuale per la macchina virtuale ripristinata: sarà possibile connettersi alla macchina virtuale ripristinata tramite Internet, anche se non viene applicata la rete virtuale.

    Se il servizio cloud selezionato è associato a una rete virtuale, non è possibile modificare la rete virtuale.

    ![Selezionare una rete virtuale](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Selezionare una subnet: se la rete virtuale dispone di più subnet, per impostazione predefinita verrà selezionata la prima subnet. Scegliere la subnet desiderata tra le opzioni nell'elenco a discesa. Per visualizzare informazioni dettagliate sulla subnet, passare all'estensione Reti nella [home page del portale](https://manage.windowsazure.com/), passare a **Reti virtuali**, selezionare la rete virtuale ed eseguire il drill-down in Configura.

    ![Selezionare una subnet](./media/backup-azure-restore-vms/select-subnet.png)

5. Fare clic sull'icona **Invia** nella procedura guidata per inviare i dettagli e creare un processo di ripristino.

## Tenere traccia dell'operazione di ripristino
Dopo aver immesso tutte le informazioni nella procedura guidata di ripristino e averle inviate, Backup di Azure tenterà di creare un processo per tenere traccia dell'operazione di ripristino.

![Creazione di un processo di ripristino](./media/backup-azure-restore-vms/create-restore-job.png)

Se la creazione del processo ha esito positivo, verrà visualizzata una notifica di tipo avviso popup di conferma della creazione del processo. È possibile ottenere altri dettagli facendo clic sul pulsante **Visualizza processo** che consentirà di passare alla scheda **Processi**.

![Processo di ripristino creato](./media/backup-azure-restore-vms/restore-job-created.png)

Al termine dell'operazione di ripristino, l'operazione verrà contrassegnata come completata nella scheda **Processi**.

![Processo di ripristino completato](./media/backup-azure-restore-vms/restore-job-complete.png)

Dopo aver ripristinato la macchina virtuale, potrebbe essere necessario reinstallare le estensioni esistenti nella macchina virtuale originale e [modificare gli endpoint](virtual-machines-set-up-endpoints) per la macchina virtuale nel portale di Azure.

## Risoluzione dei problemi
Per la maggior parte degli errori è possibile seguire l'azione consigliata nei dettagli dell'errore. Di seguito sono riportate alcune informazioni aggiuntive per facilitare la risoluzione dei problemi:

| Operazione di backup | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Ripristino | Ripristino non riuscito con errore interno del cloud | <ol><li>Il servizio cloud in cui si sta tentando di eseguire il ripristino è configurato con le impostazioni DNS. Verificare <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se è presente un indirizzo configurato, le impostazioni DNS sono configurate.<br> <li>Il servizio cloud in cui si sta tentando di eseguire il ripristino è configurato con ReservedIP e le macchine virtuali esistenti nel servizio cloud si trovano in stato di interruzione.<br>Verificare che nel servizio cloud sia presente ReservedIP tramite i cmdlet di PowerShell seguenti:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## Passaggi successivi
- [Gestire le macchine virtuali](backup-azure-manage-vms)

<!---HONumber=July15_HO4-->