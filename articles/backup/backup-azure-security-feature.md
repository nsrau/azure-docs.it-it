---
title: "Funzionalità di sicurezza per proteggere i backup ibridi con Backup di Azure | Microsoft Docs"
description: "Informazioni su come usare le funzionalità di sicurezza in Backup di Azure per rendere più sicuri i backup"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 4a846e86fa8cefbee04e1e30078a0d2aabdd834d
ms.openlocfilehash: 1b7df71f103e8284bdcc7bc3a27fe44815b12305


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>Funzionalità di sicurezza per proteggere i backup ibridi con Backup di Azure
Sempre più clienti incorrono in problemi di sicurezza, come malware, ransomware, intrusioni e così via. Questi problemi di sicurezza comportano perdite di dati e i costi per le violazioni di sicurezza sono in aumento. Per proteggersi da tali attacchi, Backup di Azure offre ora una serie di funzionalità di sicurezza a protezione dei backup ibridi. Questo articolo descrive come abilitare e sfruttare queste funzionalità usando l'Agente di Servizi di ripristino di Microsoft Azure e il server di Backup di Microsoft Azure. Queste funzionalità sono state integrate in tre pilastri della sicurezza:

1. **Prevenzione**: aggiunta di un livello di autenticazione supplementare per ogni operazione critica eseguita, ad esempio la modifica della passphrase. Questa convalida serve a garantire che tali operazioni vengano eseguite solo dagli utenti che hanno credenziali di Azure valide.
2. **Invio di avvisi**: invio di notifiche tramite e-mail all'amministratore della sottoscrizione ogni volta che viene eseguita un'operazione critica, come l'eliminazione dei dati di backup. Questa e-mail garantisce che l'utente venga informato tempestivamente riguardo a tali azioni.
3. **Ripristino**: i dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione. In questo modo viene garantita la possibilità di recuperare i dati entro un certo periodo di tempo, così da scongiurare la perdita di dati anche in caso di attacco. In più, viene conservato un maggior numero di punti di ripristino per tutelarsi dal rischio di danneggiamento dei dati.

> [!NOTE]
> Le funzionalità di sicurezza devono essere abilitate solo se si usa: <br/>
> * **Agente MAB** (versione minima 2.0.9052). Dopo aver abilitato queste funzionalità è necessario aggiornare a questa versione dell'agente per poter eseguire operazioni critiche, come la modifica della passphrase o l'interruzione del backup con cancellazione dei dati <br/>
> * **Azure Backup Server** (versione minima dell'agente MAB 2.0.9052 con Server di Backup di Azure Aggiornamento 1) <br/>
> * **DPM** (non abilitare queste funzionalità). Queste funzioni saranno presto disponibili per i prossimi UR, quindi abilitandole non si influirà minimamente sulla funzionalità esistente. <br/>
> * **Backup delle macchine virtuali IaaS** (non abilitare queste funzionalità). Queste funzionalità non sono ancora disponibili per il backup delle macchine virtuali IaaS, pertanto abilitandole non si avrà alcun effetto negativo.
> * Dopo averle abilitate, le funzionalità di sicurezza saranno applicabili a tutte le macchine dell'agente di Servizi di ripristino di Azure (MARS) e a tutti i server di Backup di Azure registrati con l'insieme di credenziali. <br/>
> * L'attivazione di questa impostazione è un'azione eseguibile una volta sola e in seguito non sarà più possibile disabilitare queste funzionalità. <br/>
> * Questa funzionalità è disponibile solo per l'insieme di credenziali dei servizi di ripristino.
>
>

## <a name="enabling-security-features"></a>Abilitazione delle funzionalità di sicurezza
Gli utenti che creano l'insieme di credenziali per i servizi di ripristino dovrebbero disporre di tutte le funzionalità di sicurezza. Per quanto riguarda l'insieme di credenziali per i servizi di ripristino esistente, è necessario attenersi alla procedura seguente per abilitare queste funzionalità:

1. Accedere al portale di Azure con le credenziali di Azure
2. Digitare Servizi di ripristino nel menu Hub per accedere all'elenco dei servizi di ripristino.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Verrà visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino. Selezionare un insieme di credenziali dall'elenco.

    Viene aperto il dashboard dell'insieme di credenziali selezionato.
3. Nell'elenco di elementi visualizzato sotto l'insieme di credenziali fare clic su **Proprietà** sotto **Impostazioni**.

    ![Proprietà dell'insieme di credenziali](./media/backup-azure-security-feature/vault-list-properties.png)
4. Fare clic su **Aggiorna** in **Impostazioni di sicurezza**.

    ![Impostazioni di sicurezza](./media/backup-azure-security-feature/security-settings-update.png)

    Il collegamento Aggiorna apre il pannello Impostazioni di sicurezza, che consente di abilitare queste funzioni e offre un riepilogo della funzionalità.
5. Selezionare un valore dall'elenco a discesa **È stata eseguita la configurazione di Azure multi-Factor Authentication?** per confermare di aver abilitato [Azure multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Se l'opzione è stata abilitata, verrà chiesto di eseguire l'autenticazione da un altro dispositivo (ad esempio, un cellulare) quando si accede al portale di Azure.

   Come parte delle funzionalità di sicurezza, quando si eseguono operazioni critiche in Backup di Azure è necessario immettere il PIN di sicurezza disponibile nel portale di Azure. Abilitando Azure Multi-Factor Authentication si aggiunge un livello di sicurezza, garantendo la possibilità di accedere al portale di Azure ed eseguire operazioni critiche solo agli utenti autorizzati con credenziali di Azure valide che si sono autenticati dal secondo dispositivo.
6. Usare il pulsante di attivazione/disattivazione per **abilitare** l'opzione e fare clic sul pulsante **Salva** nella parte superiore della schermata per salvare le impostazioni di sicurezza, come illustrato nella figura. È possibile selezionare **Abilita** solo dopo aver selezionato un valore dall'elenco a discesa "È stata eseguita la configurazione di Azure multi-Factor Authentication?" .

    ![Abilitazione delle impostazioni di sicurezza](./media/backup-azure-security-feature/enable-security-settings.png)

## <a name="recovering-deleted-backup-data"></a>Ripristino dei dati di backup eliminati
Come misura di sicurezza, Backup di Azure conserva i dati di backup eliminati per altri 14 giorni anziché eliminarli immediatamente quando viene eseguita l'interruzione del backup con la cancellazione dei dati di backup. Per ripristinare i dati entro i 14 giorni previsti, attenersi alla procedura seguente:

Per gli utenti che usano l'**agente di Servizi di ripristino di Microsoft**:

1. Se il computer su cui erano in esecuzione le operazioni di backup è ancora disponibile, usare l'azione [Ripristinare i dati nello stesso computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-the-same-machine) in MARS per recuperare i dati da tutti i punti di ripristino precedenti.
2. Se invece il computer non è disponibile, scegliere l'azione [Ripristinare in un altro computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) per ottenere i dati usando un'altra macchina MARS.

Per gli utenti che usano il **server di Backup di Azure**:

1. Se il server in cui erano in esecuzione le operazioni di backup è ancora disponibile, proteggere di nuovo le origini dati eliminate e usare la funzionalità Ripristina dati per recuperare i dati da tutti i punti di ripristino precedenti.
2. Se invece il computer non è disponibile, scegliere l'azione [Ripristinare i dati da un altro server di Backup di Azure](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) per ottenere i dati usando un altro server di Backup di Azure.

## <a name="preventing-attacks"></a>Prevenzione degli attacchi
Questa funzionalità è stata integrata da una serie di controlli per assicurare che solo gli utenti validi possano eseguire svariate operazioni.

### <a name="authentication-to-perform-critical-operations"></a>Autenticazione per eseguire operazioni critiche
Nell'ambito dell'aggiunta di un livello di autenticazione supplementare per le operazioni critiche viene richiesto l'inserimento del PIN di sicurezza quando si eseguono le operazioni Arresta protezione dati con eliminazione dei dati e Modifica passphrase.

Per ricevere il PIN di sicurezza, attenersi alla procedura seguente:

1. Accedere al portale di Azure.
2. Selezionare Insieme di credenziali di servizi di ripristino > Impostazioni > Proprietà.
3. Fare clic su **Genera** in PIN di sicurezza. Il collegamento Genera apre un pannello che contiene il PIN di sicurezza da immettere nell'interfaccia utente dell'agente di Servizi di ripristino di Azure.
    Questo PIN è valido solo per 5 minuti e viene generato automaticamente dopo tale periodo.

### <a name="maintaining-minimum-retention-range"></a>Gestione dell'intervallo minimo di conservazione
Al fine di garantire sempre la disponibilità di un numero valido di punti di ripristino sono stati aggiunti i controlli seguenti:

1. Per la conservazione giornaliera, è necessario garantire almeno **sette** giorni di conservazione
2. Per la conservazione settimanale, è necessario garantire almeno **quattro** settimane di conservazione
3. Per la conservazione mensile, è necessario garantire almeno **tre** mesi di conservazione
4. Per la conservazione annuale, è necessario garantire almeno **un** anno di conservazione

## <a name="notifications-for-critical-operations"></a>Notifiche relative alle operazioni critiche
Per ogni operazione critica eseguita, l'amministratore della sottoscrizione riceve una notifica tramite e-mail con i dettagli dell'operazione. Se si vuole configurare altri ID e-mail per la ricezione di notifiche, è possibile usare il portale di Azure.

Le funzionalità di sicurezza descritte in questo articolo offrono meccanismi di difesa dagli attacchi che mirano a colpire i backup e, cosa ancora più importante, permettono di ripristinare i dati in caso di attacco.

## <a name="next-steps"></a>Passaggi successivi
* Consultare [Introduzione all'insieme di credenziali per i servizi di ripristino di Azure](backup-azure-vms-first-look-arm.md) per abilitare queste funzionalità
* Consultare [Scaricare l'agente di Servizi di ripristino di Azure più recente](http://aka.ms/azurebackup_agent) per proteggere i computer Windows e i dati di backup da eventuali attacchi
* Consultare [Scaricare il server di Backup di Azure più recente](https://aka.ms/latest_azurebackupserver) per proteggere i carichi di lavoro e i dati di backup da eventuali attacchi



<!--HONumber=Feb17_HO1-->


