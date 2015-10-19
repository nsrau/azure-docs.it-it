<properties
	pageTitle="Gestire server e insiemi di credenziali di Backup di Azure | Microsoft Azure"
	description="Utilizzare questa esercitazione per imparare a gestire server e insieme di credenziali di Backup di Azure."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="jimpark; aashishr; giridham"/>


# Gestire server e insiemi di credenziali di Backup di Azure
In questo articolo è disponibile una panoramica delle attività di gestione dei backup disponibili tramite il portale di gestione.

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Servizi di ripristino**, quindi fare clic sul nome dell'insieme di credenziali per il backup per visualizzare la relativa pagina Avvio rapido.

    Selezionando le opzioni nella parte superiore della pagina avvio rapido, è possibile visualizzare le attività di gestione disponibili.

    ![Elementi protetti](./media/backup-azure-manage-windows-server/RS_tabs.png)

## Dashboard
Fare clic su **Dashboard** per visualizzare la panoramica sull'utilizzo del server. Nella parte inferiore del dashboard è possibile eseguire le operazioni seguenti:

- **Manage certificate**. Se per registrare un server è stato usato un certificato, eseguire questa azione per aggiornare il certificato corrente. Se si usano le credenziali di insieme, non usare la funzionalità **Gestisci certificato**.
- **Delete**. Consente di eliminare l'insieme di credenziali per il backup corrente. Se un insieme di credenziali per il backup non è più in uso, è possibile eliminarlo per liberare spazio di archiviazione. L'opzione di eliminazione viene abilitata solo dopo l'eliminazione di tutti i server registrati dall'insieme di credenziali.
- **Vault credentials**. Usare questa voce del menu di riepilogo rapido per configurare le credenziali di insieme.

## Elementi protetti
Fare clic su **Elementi protetti** per visualizzare gli elementi di cui è stato eseguito il backup dai server. Questo elenco è solo a scopo informativo.

![Elementi protetti](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## Elementi registrati
Fare clic su **Elementi registrati** per visualizzare i nomi dei server registrati nell'insieme di credenziali.

![Deleted Server](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

Da questo punto è possibile eseguire le attività seguenti:

- **Consentire una nuova registrazione** - Quando questa opzione è selezionata per un server, è possibile usare la **Procedura di registrazione guidata** nell'agente per registrare nuovamente il server con l'insieme di credenziali per il backup. La ri-registrazione di un server può essere necessaria per un errore nel certificato, oppure se è stato necessario ricreare il server.
- **Eliminare** - Consente di eliminare un server dall'insieme di credenziali per il backup. Tutti i dati archiviati associati al server verranno eliminati immediatamente.

## Passaggi successivi
- [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
- Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](backup-introduction-to-azure-backup.md)
- Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=Oct15_HO2-->