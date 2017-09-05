---
title: Ripristinare i dati da un server di Backup di Azure | Documentazione Microsoft
description: Recuperare i dati che sono stati protetti in un insieme di credenziali di Servizi di ripristino da qualsiasi server di Backup di Azure registrato in tale insieme di credenziali.
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
ms.date: 08/18/2017
ms.author: adigan;giridham;trinadhk;markgal
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 688d155b68bc2d76d53f78d251bc2f659582845f
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="recover-data-from-azure-backup-server"></a>Ripristinare i dati da un server di Backup di Azure
È possibile usare il server di Backup di Azure per recuperare i dati di un backup in un insieme di credenziali di Servizi di ripristino. Il processo per eseguire l'operazione è integrato nella console di gestione del server di Backup di Azure, quindi è simile al flusso di lavoro di ripristino per gli altri componenti di Backup di Azure.

> [!NOTE]
> Quanto descritto in questo articolo è applicabile anche a [System Center Data Protection Manager 2012 R2 con UR7 o versione successiva] (https://support.microsoft.com/en-us/kb/3065246), combinato con l'[agente di Backup di Azure più recente](http://aka.ms/azurebackup_agent).
>
>

Per ripristinare i dati da un server di Backup di Azure:

1. Dalla scheda **Ripristino** della console di gestione del server di Backup di Azure, fare clic su **"Aggiungi DPM Esterno"** (nella parte superiore sinistra della schermata).   
    ![Aggiungi DPM esterno](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Scaricare il nuovo **insieme di credenziali** da quello associato al **server di Backup di Azure** i cui dati sono in fase di ripristino, scegliere il server di Backup di Azure dall'elenco di server di Backup di Azure registrati con l'insieme delle credenziali per Servizi di ripristino e fornire la **passphrase di crittografia** associata al server i cui dati sono in fase di ripristino.

    ![Credenziali DPM esterne](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Solo i server di Backup di Azure associati allo stesso insieme di credenziali per la registrazione possono recuperare i dati.
   >
   >

    Dopo aver aggiunto il server di Backup di Azure esterno, è possibile esplorare i dati del server di Backup di Azure esterno e di quello locale dalla scheda **Ripristino**.
3. Individuare l'elenco dei server di produzione protetti dal server di Backup di Azure esterno disponibili e selezionare l'origine dati appropriata.

    ![Cercare nel Server DPM esterno](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selezionare **il mese e l'anno** dall’elenco a discesa **Punti di ripristino**, selezionare la **Data di ripristino** necessaria per quando è stato creato il punto di ripristino, quindi scegliere l'**Ora ripristino**.

    Viene visualizzato un elenco di file e cartelle nel riquadro inferiore che può essere esplorato e ripristinato in qualsiasi posizione.

    ![Punti di ripristino del Server DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Fare clic con il pulsante destro sulla voce appropriata e fare clic su **Ripristino**.

    ![Ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/recover.png)
6. Verificare la **Selezione ripristino**. Verificare la data e ora della copia di backup da ripristinare, nonché l'origine da cui è stata creata la copia di backup. Se la selezione non è corretta, fare clic su **Annulla** per tornare alla scheda di ripristino e selezionare un punto di ripristino appropriato. Se la selezione è corretta, fare clic su **Avanti**.

    ![Riepilogo del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selezionare **Ripristina in un percorso alternativo**. **Cercare** la posizione corretta per il ripristino.

    ![Posizione alternativa del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Scegliere l'opzione relativa a **Crea copia**, **Ignora** o **Sovrascrivi**.

   * **Crea copia**: crea una copia del file se si verifica un conflitto di nome.
   * **Ignora**: se si verifica un conflitto di nome, non recupera il file e lascia il file originale.
   * **Sovrascrivi**: se si verifica un conflitto di nome, sovrascrive la copia esistente del file.

     Scegliere l'opzione appropriata per **Ripristina protezione**. È possibile applicare le impostazioni di sicurezza del computer di destinazione in cui i dati vengono ripristinati o le impostazioni di sicurezza che erano applicabili al prodotto nel momento in cui è stato creato il punto di ripristino.

     Identificare se viene inviata una **Notifica** dopo che il ripristino è stato completato correttamente.

     ![Notifiche di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. La schermata di **Riepilogo** elenca le opzioni scelte finora. Quando si fa clic su **"Ripristina"**, i dati vengono ripristinati nel percorso locale appropriato.

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
| 1. |Questo server non è registrato nell'insieme di credenziali specificate per le credenziali dell'insieme di credenziali. |**Causa:** questo errore viene visualizzato quando il file di archivio dell'insieme di credenziali selezionato non appartiene a un insieme di credenziali di Servizi di ripristino associato al server di Backup di Azure in cui viene tentato il ripristino. <br> **Risoluzione:** scaricare il file di archivio delle credenziali dall'insieme di credenziali di Servizi di ripristino nel quale il server di Backup di Azure è registrato. |
| 2. |I dati ripristinabili non sono disponibili o il server selezionato non è un server DPM. |**Causa:** non sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali di Servizi di ripristino, i server non hanno ancora caricato i metadati o il server selezionato non è un server di Backup di Azure (noto come Windows Server o Client di Windows). <br> **Risoluzione:** se sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali di Servizi di ripristino, assicurarsi che sia installato l'agente di Backup di Azure più recente. <br>Se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali di Servizi di ripristino, attendere un giorno dopo l'installazione per avviare il processo di ripristino. I processi notturni caricheranno i metadati per tutti i backup protetti nel cloud. I dati saranno disponibili per il ripristino. |
| 3. |Nessun altro server DPM viene registrato nell'insieme di credenziali. |**Causa:** non ci sono altri server di Backup di Azure registrati nell'insieme di credenziali da cui si sta tentando il ripristino.<br>**Risoluzione:** se sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali di Servizi di ripristino, assicurarsi che sia installato l'agente di Backup di Azure più recente.<br>Se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali di Servizi di ripristino, attendere un giorno dopo l'installazione per avviare il processo di ripristino. I processi notturni caricano i metadati per tutti i backup protetti nel cloud. I dati saranno disponibili per il ripristino. |
| 4. |La passphrase di crittografia fornita non corrisponde alla passphrase associata al server seguente: **<server name>** |**Causa:** la passphrase di crittografia usata nel processo di crittografia dai dati del server di Backup di Azure che è stata ripristinata non corrisponde a quella specificata. L'agente non è in grado di decrittografare i dati. Di conseguenza il ripristino non riesce.<br>**Risoluzione:** specificare esattamente la stessa passphrase di crittografia associata al server di Backup di Azure i cui dati sono stati ripristinati. |

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Perché non è possibile aggiungere un server DPM esterno dopo l'installazione di UR7 e dell'agente Azure Backup più recente?

Per i server DPM con le origini dati protette nel cloud (utilizzando un aggiornamento cumulativo precedente al 7), è necessario attendere almeno un giorno dopo l'installazione di UR7 e dell'agente Azure Backup più recente per avviare **Aggiungi server DPM esterno**. Questo periodo di un giorno è necessario per caricare i metadati dei gruppi protezione DPM in Azure. I metadati dei gruppi protezione dati vengono caricati la prima volta attraverso un processo notturno.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Qual è la versione minima richiesta per l'agente di Servizi di ripristino di Microsoft Azure?

La versione minima dell'agente di Servizi di ripristino di Microsoft Azure o dell'agente di Backup di Azure richiesta per l'abilitazione di questa funzionalità è la 2.0.8719.0.  Per visualizzare la versione dell'agente: aprire il pannello di controllo **>** Tutti gli elementi del Pannello di controllo **>** Programmi e funzionalità **>** Agente di servizi di ripristino di Microsoft Azure. Se la versione è minore di 2.0.8719.0, scaricare e installare l'[agente Azure Backup più recente](https://go.microsoft.com/fwLink/?LinkID=288905).

![Deselezionare DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Passaggi successivi:
[Domande frequenti su Backup di Azure](backup-azure-backup-faq.md)

