---
title: Ripristinare i dati da un server di Backup di Azure | Documentazione Microsoft
description: Recuperare i dati che sono stati protetti in un insieme di credenziali di Backup di Azure da qualsiasi server di Backup di Azure registrato in tale insieme di credenziali.
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: adigan;giridham;trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 14cc190a7d1cde1181a6f26ef83095bc601f7fbb
ms.openlocfilehash: 36c4e1865c99dd1c55be798e1d310a02f2af0864


---
# <a name="recovering-data-from-another-azure-backup-server-in-the-backup-vault"></a>Ripristino dei dati da un altro server di Backup di Azure nell'insieme di credenziali per il backup
Ora è possibile recuperare i dati che sono stati protetti in un insieme di credenziali di Backup di Azure da qualsiasi server di Backup di Azure registrato in tale insieme di credenziali. Il processo per farlo è completamente integrato nella console di gestione ABS ed è simile agli altri flussi di lavoro di ripristino.

> [!NOTE]
> Quanto descritto in questo articolo e nei passaggi seguenti è applicabile anche a [Aggiornamento cumulativo&7; per System Center Data Protection Manager] (https://support.microsoft.com/en-us/kb/3065246) con l'[agente di Backup di Azure più recente](http://aka.ms/azurebackup_agent).
>
>

## <a name="recover-data-from-another-azure-backup-server"></a>Ripristinare i dati da un altro server di Backup di Azure
Per ripristinare i dati da un altro server di Backup di Azure:

1. Dalla scheda **Ripristino** della console di gestione ABS, fare clic su **"Aggiungi DPM Esterno"** (nella parte superiore a sinistra della schermata).   
    ![Aggiungi DPM esterno](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Scaricare il nuovo **insieme di credenziali** da quello associato al **server di Backup di Azure** i cui dati sono in fase di ripristino, scegliere il server di Backup di Azure dall'elenco di server di Backup di Azure registrati con l'insieme delle credenziali per il backup e fornire la **passphrase di crittografia** associata al server cui dati sono in fase di ripristino.

    ![Credenziali DPM esterne](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Solo i server di Backup di Azure associati allo stesso insieme di credenziali per la registrazione possono recuperare i dati.
   >
   >

    Dopo aver aggiunto il server di Backup di Azure esterno, è possibile esplorare i dati del server di Backup di Azure esterno e di quello locale dalla scheda **Ripristino**.
3. Individuare l'elenco dei server di produzione protetti dal server di Backup di Azure esterno disponibili e selezionare l'origine dati appropriata.

    ![Cercare nel Server DPM esterno](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selezionare **il mese e l'anno** dall’elenco a discesa **Punti di ripristino**, selezionare la **Data di ripristino** necessaria per quando è stato creato il punto di ripristino, quindi scegliere l'**Ora ripristino**.

    Verrà visualizzato un elenco di file e cartelle nel riquadro inferiore che può essere esplorato e ripristinato in qualsiasi posizione.

    ![Punti di ripristino del Server DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Fare clic con il pulsante destro sulla voce appropriata e fare clic su **Ripristino**.

    ![Ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/recover.png)
6. Verificare la **Selezione ripristino**. Verificare la data e ora della copia di backup da ripristinare, nonché l'origine da cui è stata creata la copia di backup. Se la selezione non è corretta, fare clic su **Annulla** per tornare alla scheda di ripristino e selezionare un punto di ripristino appropriato. Se la selezione è corretta, fare clic su **Avanti**.

    ![Riepilogo del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selezionare **Ripristina in un percorso alternativo**. **Cercare** la posizione corretta per il ripristino.

    ![Posizione alternativa del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Scegliere l'opzione relativa a **Crea copia**, **Ignora** o **Sovrascrivi**.

   * **Crea copia** creerà una copia del file nel caso in cui si verifichi un conflitto di nome.
   * **Ignora** ignorerà il ripristino del file nel caso in cui si verifichi un conflitto di nome.
   * **Sovrascrivi** sovrascriverà la copia esistente nel percorso specificato nel caso di un conflitto di nomi.

     Scegliere l'opzione appropriata per **Ripristina protezione**. È possibile applicare le impostazioni di sicurezza del computer di destinazione in cui i dati vengono ripristinati o le impostazioni di sicurezza che erano applicabili al prodotto nel momento in cui è stato creato il punto di ripristino.

     Identificare se una **Notifica** verrà inviata dopo che il ripristino viene completato correttamente.

     ![Notifiche di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. La schermata di **Riepilogo** elenca le opzioni scelte finora. Quando si fa clic su **'Ripristinare'**, i dati verranno ripristinati nel percorso locale appropriato.

    ![Riepilogo opzioni di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Il processo di ripristino può essere monitorato nella scheda **Monitoraggio** del server di Backup di Azure.
   >
   >

    ![Monitoraggio del ripristino](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. È possibile fare clic su **Deselezionare DPM esterno** nella scheda **Ripristino** del server DPM per rimuovere la visualizzazione del server DPM esterno.

    ![Deselezionare DPM esterno](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Risoluzione dei problemi relativi a messaggi di errore
| di serie | Messaggio di errore | Passaggi per la risoluzione dei problemi |
|:---:|:--- |:--- |
| 1. |Questo server non è registrato nell'insieme di credenziali specificate per le credenziali dell'insieme di credenziali. |**Causa:** questo errore viene visualizzato quando il file di archivio dell’insieme di credenziali selezionato non appartiene a un insieme di credenziali di backup associato al server di Backup di Azure in cui viene tentato il ripristino. <br> **Risoluzione:** scaricare il file di archivio delle credenziali dall'archivio di backup nel quale il server di Backup di Azure è registrato. |
| 2. |I dati ripristinabili non sono disponibili o il server selezionato non è un server DPM. |**Causa:** non sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali per il backup, i server non hanno ancora caricato i metadati o il server selezionato non è un server di Backup di Azure (noto come Windows Server o Client di Windows). <br> **Risoluzione:** se sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali per il backup, assicurarsi che l'agente di Backup di Azure più recente sia installato. <br>Se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali per il backup, attendere un giorno dopo l'installazione per avviare il processo di ripristino. I processi notturni caricheranno i metadati per tutti i backup protetti nel cloud. I dati saranno disponibili per il ripristino. |
| 3. |Nessun altro server DPM viene registrato nell'insieme di credenziali. |**Causa:** non ci sono altri server di Backup di Azure registrati nell'insieme di credenziali da cui si sta tentando il ripristino.<br>**Risoluzione:** se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali per il backup, assicurarsi che l'agente di Backup di Azure più recente sia installato.<br>Se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali per il backup, attendere un giorno dopo l'installazione per avviare il processo di ripristino. I processi notturni caricheranno i metadati per tutti i backup protetti nel cloud. I dati saranno disponibili per il ripristino. |
| 4. |La passphrase di crittografia fornita non corrisponde alla passphrase associata al server seguente: **<server name>** |**Causa:** la passphrase di crittografia usata nel processo di crittografia dai dati del server di Backup di Azure che è stata ripristinata non corrisponde a quella specificata. L'agente non è in grado di decrittografare i dati. Di conseguenza il ripristino non riesce.<br>**Risoluzione:** specificare esattamente la stessa passphrase di crittografia associata al server di Backup di Azure i cui dati sono stati ripristinati. |

## <a name="frequently-asked-questions"></a>Domande frequenti
1. **Impossibile aggiungere un server DPM esterno da un altro server DPM dopo l'installazione di UR7 e dell'agente Azure Backup più recente? (Applicabile nel caso in cui si stia usando SC DPM 2012 R2)**

    A) Per i server DPM esistenti con le origini dati protette nel cloud (utilizzando un aggiornamento cumulativo precedente aggiornamento cumulativo 7), è necessario attendere almeno un giorno dopo l'installazione di UR7 e dell'agente Azure Backup più recente per avviare *Aggiungi server DPM esterno*. È necessario per caricare i metadati dei gruppi protezione DPM in Azure. Ciò si verifica la prima volta tramite un processo notturno.
2. **Qual è la versione minima necessaria dell’agente Azure Backup?**

    A) la versione minima dell'agente di Backup di Azure per abilitare questa funzionalità è 2.0.8719.0.  La Versione dell'agente Azure Backup può essere verificata passando al pannello di controllo **>** tutti gli elementi del Pannello di controllo **>** programmi e funzionalità **>** agente di servizi di ripristino di Microsoft Azure. Se la versione è minore di 2.0.8719.0, scaricare l’ [agente Azure Backup più recente](https://go.microsoft.com/fwLink/?LinkID=288905) e installare.

    ![Deselezionare DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Passaggi successivi:
[Domande frequenti su Backup di Azure](backup-azure-backup-faq.md)



<!--HONumber=Jan17_HO4-->


