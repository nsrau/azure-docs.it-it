<properties
	pageTitle="Ripristino dei dati da un altro server DPM nell'archivio di backup | Microsoft Azure"
	description="Recuperare i dati che sono stati protetti in un archivio di Backup di Azure da un server DPM registrato in tale archivio."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="giridham;jimpark"/>

# Ripristino dei dati da un altro server DPM nell'archivio di backupRipristino dei dati da un altro server DPM nell'archivio di backup
Ora è possibile recuperare i dati protetti in un archivio di Backup di Azure da un server DPM registrato in tale archivio. Il processo per farlo è completamente integrato nella console di gestione DPM ed è simile agli altri flussi di lavoro di ripristino.

Per ripristinare i dati da un altro server DPM nell'archivio di backup è necessario [System Center Data Protection Manager UR7](https://support.microsoft.com/it-IT/kb/3065246) e [l’agente di Backup di Azure più recente](http://aka.ms/azurebackup_agent).

## Ripristinare i dati da un altro Server DPM
Per ripristinare i dati da un altro server DPM:

1. Dalla scheda **Ripristino** della console di gestione DPM, fare clic su **'Aggiungi DPM Esterno'** (nella parte superiore a sinistra della schermata).

    ![DPM esterno di Active Directory](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)

2. Scaricare nuove **credenziali di insieme** dall’insieme di credenziali associato al **server DPM**, i cui dati sono in fase di ripristino, scegliere il server DPM dall'elenco di server DPM registrati con l'archivio di backup e fornire la **passphrase di crittografia** associata al server DPM contenente i dati in fase di ripristino.

    ![Credenziali DPM esterne](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

    >[AZURE.NOTE] Solo i server DPM associati con lo stesso archivio di registrazione possono recuperare i dati.

    Una volta che viene aggiunto il server DPM esterno, è possibile esplorare i dati del server DPM esterno e il server DPM locale dalla scheda **Ripristino**.

3. Individuare l'elenco dei server di produzione protetti dal server DPM esterno disponibili e selezionare l'origine dati appropriata.

    ![Cercare nel Server DPM esterno](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)

4. Selezionare **il mese e anno** dall’elenco a discesa **Punti di ripristino**, selezionare la **Data di ripristino** necessaria per quando è stato creato il punto di ripristino, quindi scegliere il **Tempo di recupero**.

    Verrà visualizzato un elenco di file e cartelle nel riquadro inferiore che può essere esplorato e ripristinato in qualsiasi posizione.

    ![Punti di ripristino del Server DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)

5. Fare clic con il pulsante destro sulla voce appropriata e fare clic su **Ripristino**.

    ![Ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/recover.png)

6. Verificare la **Selezione ripristino**. Verificare la data e ora della copia di backup da ripristinare, nonché l'origine da cui è stata creata la copia di backup. Se la selezione non è corretta, fare clic su **Annulla** per tornare alla scheda di ripristino e selezionare un punto di ripristino appropriato. Se la selezione è corretta, fare clic su **Avanti**.

    ![Riepilogo del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)

7. Selezionare **Ripristina in un percorso alternativo**. **Cercare** la posizione corretta per il ripristino.

    ![Posizione alternativa del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)

8. Scegliere l'opzione relativa a **Crea copia**, **Ignora** o **Sovrascrivi**.
    - **Crea copia** creerà una copia del file nel caso in cui si verifichi un conflitto di nome.
    - **Ignora** ignorerà il ripristino del file nel caso in cui si verifichi un conflitto di nome.
    - **Sovrascrivi** sovrascriverà la copia esistente nel percorso specificato nel caso di un conflitto di nomi.

    Scegliere l'opzione appropriata per **Ripristina protezione**. È possibile applicare le impostazioni di sicurezza del computer di destinazione in cui i dati vengono ripristinati o le impostazioni di sicurezza che erano applicabili al prodotto nel momento in cui è stato creato il punto di ripristino.

    Identificare se una **Notifica** verrà inviata dopo che il ripristino viene completato correttamente.

    ![Notifiche di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)

9. La schermata di **Riepilogo** elenca le opzioni scelte finora. Quando si fa clic su **'Ripristinare'**, i dati verranno ripristinati nel percorso locale appropriato.

    ![Riepilogo opzioni di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

    >[AZURE.NOTE] Il processo di ripristino può essere monitorato nella scheda **Monitoraggio** del server DPM.

    ![Monitoraggio del ripristino](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)

10. È possibile fare clic su **Deselezionare DPM esterni** nella scheda **Ripristino** del server DPM per rimuovere la visualizzazione del server DPM esterno.

    ![Deselezionare DPM esterno](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## Risoluzione dei problemi relativi a messaggi di errore
|No. |	Messaggio di errore |	Passaggi per la risoluzione dei problemi |
| :-------------: |:-------------| :-----|
|1\.|		Questo server non è registrato nell'insieme di credenziali specificate per le credenziali dell'insieme di credenziali.|	**Causa:** questo errore viene visualizzato quando il file di archivio dell’insieme di credenziali selezionato non appartiene a un insieme di credenziali di backup associato al server DPM in cui viene tentato il ripristino. <br> **Risoluzione:** scaricare il file di archivio delle credenziali dall'archivio di backup nel quale il server DPM è registrato.|
|2\.|		I dati ripristinabili non sono disponibili o il server selezionato non è un server DPM.|	**Causa:** non sono presenti altri server DPM con DPM 2012 R2 UR7 registrati per l'archivio di backup, i server DPM con DPM 2012 R2 UR7 non hanno ancora caricato i metadati o il server selezionato è non server DPM (noto come Windows Server o Client di Windows). <br> **Risoluzione:** se sono presenti altri server DPM registrati per l'archivio di backup, assicurarsi che SCDPM 2012 R2 UR7 e l'agente di Backup di Azure più recente siano installati. <br>Se sono presenti altri server DPM registrati nell'archivio di backup con DPM 2012 R2 UR7, attendere un giorno dopo l'installazione di UR7 per avviare il processo di ripristino. I processi notturni caricheranno i metadati per tutti i backup precedentemente protetti nel cloud. I dati saranno disponibili per il ripristino.|
|3\.|		Nessun altro server DPM viene registrato nell'insieme di credenziali.|	**Causa:** non esistono altri server DPM con DPM 2012 R2 UR7 o precedente che siano registrati nell'archivio da cui si sta tentando il ripristino.<br> **Risoluzione:** se sono presenti altri server DPM registrati per l'archivio di backup, assicurarsi che SCDPM 2012 R2 UR7 e l'agente di Backup di Azure più recente siano installati.<br>Se sono presenti altri server DPM registrati negli archivi di backup con DPM 2012 R2 UR7, attendere un giorno dopo l'installazione di UR7 per avviare il processo di ripristino. I processi notturni caricheranno i metadati per tutti i backup precedentemente protetti nel cloud. I dati saranno disponibili per il ripristino.|
|4\.|		La passphrase di crittografia fornita non corrisponde alla passphrase associata ai server seguenti: **<server name>**|	**Causa:** la passphrase di crittografia utilizzata nel processo di crittografia dai dati del server DPM che è stata ripristinata non corrisponde alla passphrase di crittografia fornita. L'agente non è in grado di decrittografare i dati. Di conseguenza il ripristino non riesce.<br> **Risoluzione:** specificare la stessa passphrase di crittografia esatta associata al server DPM i cui dati sono stati ripristinati.|

## Domande frequenti
1. **Impossibile aggiungere un server DPM esterno da un altro server DPM dopo l'installazione di UR7 e dell'agente Azure Backup più recente?**

    A) Per i server DPM esistenti con le origini dati protette nel cloud (utilizzando un aggiornamento cumulativo precedente aggiornamento cumulativo 7), è necessario attendere almeno un giorno dopo l'installazione di UR7 e dell'agente Azure Backup più recente per avviare *Aggiungi server DPM esterno*. È necessario per caricare i metadati dei gruppi protezione DPM in Azure. Ciò si verifica la prima volta tramite un processo notturno.

2. **Qual è la versione minima necessaria dell’agente Azure Backup?**

    A) la versione minima dell'agente di Backup di Azure per abilitare questa funzionalità è 2.0.8719.0. La Versione dell'agente Azure Backup può essere verificata passando al pannello di controllo **>** tutti gli elementi del Pannello di controllo **>** programmi e funzionalità **>** agente di servizi di ripristino di Microsoft Azure. Se la versione è minore di 2.0.8719.0, scaricare l’[agente Azure Backup più recente](https://go.microsoft.com/fwLink/?LinkID=288905) e installare.

    ![Deselezionare DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## Passaggi successivi:
[Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0511_2016-->