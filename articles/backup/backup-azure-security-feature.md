---
title: "Funzionalità di sicurezza per la protezione dei backup ibridi che usano Backup di Azure | Microsoft Docs"
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
ms.date: 02/17/2017
ms.author: pajosh
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 41a7024b51bc7a3c9cf34dba97255ea61fd27924
ms.contentlocale: it-it
ms.lasthandoff: 04/07/2017


---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funzionalità di sicurezza per la protezione dei backup ibridi che usano Backup di Azure
Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per evitare questi attacchi, Backup di Azure offre ora una serie di funzionalità di sicurezza per la protezione dei backup ibridi. Questo articolo descrive come abilitare e usare queste funzionalità tramite un agente di Servizi di ripristino di Azure e il server di Backup di Azure. Queste funzionalità includono:

- **Prevenzione**. Ogni volta che viene eseguita un'operazione critica, come la modifica di una passphrase, viene aggiunto un ulteriore livello di autenticazione. Questa convalida serve a garantire che tali operazioni possano essere eseguite solo dagli utenti che hanno credenziali di Azure valide.
- **Invio di avvisi**. Ogni volta che viene eseguita un'operazione critica, come l'eliminazione dei dati di backup, viene inviata una notifica tramite posta elettronica all'amministratore della sottoscrizione. Questo messaggio di posta elettronica garantisce che l'utente venga informato rapidamente riguardo a tali azioni.
- **Ripristino**. I dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione. In questo modo, viene garantita la possibilità di recuperare i dati entro un certo periodo di tempo, così da scongiurare la perdita di dati anche in caso di attacco. Inoltre, viene conservato un maggior numero di punti di recupero per prevenire il rischio di dati danneggiati.

> [!NOTE]
> Le funzionalità di sicurezza non devono essere abilitate se si usa il backup di VM IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio). Poiché queste funzionalità non sono ancora disponibili per il backup di VM IaaS, la loro abilitazione non avrà alcun impatto. Le funzionalità di sicurezza devono essere abilitate solo se si usa: <br/>
>  * **Agente di Backup di Azure**. Versione minima dell'agente: 2.0.9052. Dopo aver abilitato queste funzionalità, è necessario eseguire l'aggiornamento a questa versione dell'agente per poter eseguire operazioni critiche. <br/>
>  * **Server di Backup di Azure**. Versione minima dell'agente di Backup di Azure: 2.0.9052, con l'aggiornamento 1 del server di Backup di Azure. <br/>
>  * **System Center Data Protection Manager**. Versione minima dell'agente di Backup di Azure: 2.0.9052, con Data Protection Manager 2012 R2 UR12 o Data Protection Manager 2016 UR2. <br/> 


> [!NOTE]
> Queste funzionalità sono disponibili solo per l'insieme di credenziali dei Servizi di ripristino. Tutti i nuovi insiemi di credenziali creati con i Servizi di ripristino hanno queste funzionalità abilitate per impostazione predefinita. Per gli insiemi di credenziali di Servizi di ripristino esistenti, gli utenti devono abilitare queste funzionalità tramite i passaggi descritti nella sezione seguente. Una volta abilitate, le funzionalità vengono applicate a tutti i computer dell'agente di Servizi di ripristino, le istanze del server di Backup di Azure e i server Data Protection Manager registrati con l'insieme di credenziali. L'abilitazione di questa impostazione è un'azione eseguibile una volta sola e in seguito non sarà più possibile disabilitare le funzionalità.
>

## <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza
Se si crea un insieme di credenziali di Servizi di ripristino, è possibile usare tutte le funzionalità di sicurezza. Se si usa un insieme di credenziali esistente, abilitare le funzionalità di sicurezza eseguendo questa procedura:

1. Accedere al portale di Azure con le credenziali di Azure.
2. Selezionare **Sfoglia** e quindi digitare **Servizi di ripristino**.

    ![Screenshot dell'opzione Sfoglia del portale di Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Verrà visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino. Selezionare un insieme di credenziali dall'elenco. Viene aperto il dashboard dell'insieme di credenziali selezionato.
3. Nell'elenco degli elementi visualizzato sotto l'insieme di credenziali fare clic su **Proprietà** in **Impostazioni**.

    ![Screenshot delle opzioni per l'insieme di credenziali di Servizi di ripristino](./media/backup-azure-security-feature/vault-list-properties.png)
4. Fare clic su **Aggiorna** in **Impostazioni di sicurezza**.

    ![Screenshot delle proprietà dell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-security-feature/security-settings-update.png)

    Il collegamento Aggiorna apre il pannello **Impostazioni di sicurezza**, che contiene un riepilogo delle funzionalità e permette di abilitarle.
5. Nell'elenco a discesa **È stata eseguita la configurazione di Azure Multi-Factor Authentication?** selezionare un valore per confermare se [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) è stato abilitato. Se è stata abilitato, verrà chiesto di eseguire l'autenticazione da un altro dispositivo, ad esempio uno smartphone, quando si accede al portale di Azure.

   Quando si eseguono operazioni critiche in Backup, è necessario immettere il PIN di sicurezza, disponibile nel portale di Azure. L'abilitazione di Azure Multi-Factor Authentication aggiunge un livello di sicurezza. Solo gli utenti autorizzati con credenziali di Azure valide e autenticati da un secondo dispositivo potranno accedere al portale di Azure.
6. Per salvare le impostazioni di sicurezza, selezionare **Abilita** e fare clic su **Salva**. È possibile selezionare **Abilita** solo dopo aver selezionato un valore nell'elenco **È stata eseguita la configurazione di Azure Multi-Factor Authentication?** nel passaggio precedente.

    ![Screenshot delle impostazioni di sicurezza](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Ripristino dei dati di backup eliminati
Backup conserva i dati di backup eliminati per altri 14 giorni anziché eliminarli immediatamente quando viene eseguita l'**interruzione del backup con l'eliminazione dei dati di backup**. Per ripristinare i dati entro i 14 giorni previsti, eseguire la procedura seguente, a seconda del componente usato:

Per utenti dell'**agente di Servizi di ripristino di Azure**:

1. Se il computer in cui sono stati eseguiti i backup è ancora disponibile, usare [Ripristinare i dati nello stesso computer](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) in Servizi di ripristino di Azure per recuperare i dati da tutti i punti di recupero precedenti.
2. Se invece il computer non è disponibile, scegliere [Ripristinare in un altro computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) per usare un altro computer con Servizi di ripristino di Azure per ottenere questi dati.

Per gli utenti che usano il **server di Backup di Azure**:

1. Se il server in cui sono stati eseguiti i backup è ancora disponibile, proteggere di nuovo le origini dati eliminate e usare la funzionalità di ripristino dei **dati per recuperare** i dati da tutti i punti di recupero precedenti.
2. Se questo server non è disponibile, scegliere [Ripristinare i dati da un altro server di Backup di Azure](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) per usare un'altra istanza del server di Backup di Azure per ottenere questi dati.

Per gli utenti di **Data Protection Manager**:

1. Se il server in cui sono stati eseguiti i backup è ancora disponibile, proteggere di nuovo le origini dati eliminate e usare la funzionalità di ripristino dei **dati per recuperare** i dati da tutti i punti di recupero precedenti.
2. Se questo server non è disponibile, usare [Aggiungi DPM esterno](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) per usare un altro server Data Protection Manager per ottenere questi dati.

## <a name="prevent-attacks"></a>Prevenire gli attacchi
Sono stati aggiunti alcuni controlli per garantire che solo gli utenti validi possano eseguire diverse operazioni. Questi controlli includono l'aggiunta di un ulteriore livello di autenticazione e il mantenimento di un intervallo minimo di conservazione per scopi di ripristino.

### <a name="authentication-to-perform-critical-operations"></a>Autenticazione per eseguire operazioni critiche
Come parte dell'aggiunta di un ulteriore livello di autenticazione per le operazioni critiche, viene chiesto di immettere un PIN di sicurezza quando si eseguono le operazioni **Arresta protezione dei dati con eliminazione dei dati** e **Modifica passphrase**.

Per ricevere questo PIN:

1. Accedere al portale di Azure.
2. Passare a **Insiemi di credenziali dei servizi di ripristino** > **Impostazioni** > **Proprietà**.
3. Fare clic su **Genera** in **PIN di sicurezza**. Verrà visualizzato un pannello contenente il PIN da immettere nell'interfaccia utente dell'agente di Servizi di ripristino di Azure.
    Questo PIN è valido solo per cinque minuti e viene generato automaticamente dopo questo periodo.

### <a name="maintain-a-minimum-retention-range"></a>Mantenimento di un intervallo minimo di conservazione
Per garantire che sia sempre disponibile un numero valido di punti di recupero, sono stati aggiunti i controlli seguenti:

- Per la conservazione giornaliera, è necessario garantire almeno **sette** giorni.
- Per la conservazione settimanale, è necessario garantire almeno **quattro** settimane.
- Per la conservazione mensile, è necessario garantire almeno **tre** mesi.
- Per la conservazione annuale, è necessario garantire almeno **un** anno.

## <a name="notifications-for-critical-operations"></a>Notifiche relative alle operazioni critiche
In genere, quando viene eseguita un'operazione critica, l'amministratore della sottoscrizione riceve una notifica tramite posta elettronica con i dettagli dell'operazione. È possibile configurare altri destinatari di posta elettronica per queste notifiche usando il portale di Azure.

Le funzionalità di sicurezza descritte in questo articolo offrono meccanismi di difesa da attacchi mirati. Un aspetto ancora più importante è che se si verifica un attacco, queste funzionalità permettono di ripristinare i dati.

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Get started with Azure Recovery Services vault](backup-azure-vms-first-look-arm.md) (Introduzione all'insieme di credenziali di Servizi di ripristino di Azure) per abilitare queste funzionalità.
* Vedere [Download the latest Azure Recovery Services agent](http://aka.ms/azurebackup_agent) (Download dell'agente di Servizi di ripristino di Azure più recente) per proteggere i computer Windows e i dati di backup dagli attacchi.
* Vedere [Download the latest Azure Backup Server](https://aka.ms/latest_azurebackupserver) (Download del server di Backup di Azure più recente) per proteggere i carichi di lavoro e i dati di backup dagli attacchi.
* Vedere [Download UR12 for System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) (Download della versione UR2 per System Center 2012 R2 Data Protection Manager) o [Download UR2 for System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) (Download della versione UR2 per System Center 2016 Data Protection Manager) per proteggere i carichi di lavoro e i dati di backup dagli attacchi.

