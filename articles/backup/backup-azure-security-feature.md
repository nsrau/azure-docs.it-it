---
title: Funzionalità di sicurezza che proteggono i backup ibridi
description: Informazioni su come usare le funzionalità di sicurezza in Backup di Azure per rendere più sicuri i backup
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: cbd9ee0336953b65b4e2d55d294d30309ebe0de7
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892457"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funzionalità di sicurezza per la protezione dei backup ibridi che usano Backup di Azure

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per evitare questi attacchi, Backup di Azure offre ora una serie di funzionalità di sicurezza per la protezione dei backup ibridi. Questo articolo descrive come abilitare e usare queste funzionalità tramite un agente di Servizi di ripristino di Azure e il server di Backup di Azure. Queste funzionalità includono:

- **Prevenzione**. Ogni volta che viene eseguita un'operazione critica, come la modifica di una passphrase, viene aggiunto un ulteriore livello di autenticazione. Questa convalida serve a garantire che tali operazioni possano essere eseguite solo dagli utenti che hanno credenziali di Azure valide.
- **Avviso**. Ogni volta che viene eseguita un'operazione critica, come l'eliminazione dei dati di backup, viene inviata una notifica tramite posta elettronica all'amministratore della sottoscrizione. Questo messaggio di posta elettronica garantisce che l'utente venga informato rapidamente riguardo a tali azioni.
- **Ripristino**. I dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione. In questo modo, viene garantita la possibilità di recuperare i dati entro un certo periodo di tempo, così da scongiurare la perdita di dati anche in caso di attacco. Inoltre, viene conservato un maggior numero di punti di recupero per prevenire il rischio di dati danneggiati.

> [!NOTE]
> Le funzionalità di sicurezza non devono essere abilitate se si usa il backup di macchine virtuali IaaS (Infrastructure as a Service). Queste funzionalità non sono ancora disponibili per il backup delle macchine virtuali IaaS, quindi l'abilitazione non avrà alcun effetto. Le funzionalità di sicurezza devono essere abilitate solo se si usa: <br/>
>
> - **Agente di backup di Azure**. Versione minima dell'agente: 2.0.9052. Dopo aver abilitato queste funzionalità, è necessario eseguire l'aggiornamento a questa versione dell'agente per poter eseguire operazioni critiche. <br/>
> - **Server di backup di Azure**. Versione minima dell'agente di Backup di Azure: 2.0.9052, con l'aggiornamento 1 del server di Backup di Azure. <br/>
> - **System Center Data Protection Manager**. Versione minima dell'agente di Backup di Azure: 2.0.9052, con Data Protection Manager 2012 R2 UR12 o Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Queste funzionalità sono disponibili solo per l'insieme di credenziali dei Servizi di ripristino. Tutti i nuovi insiemi di credenziali creati con i Servizi di ripristino hanno queste funzionalità abilitate per impostazione predefinita. Per gli insiemi di credenziali di Servizi di ripristino esistenti, gli utenti devono abilitare queste funzionalità tramite i passaggi descritti nella sezione seguente. Una volta abilitate, le funzionalità vengono applicate a tutti i computer dell'agente di Servizi di ripristino, le istanze del server di Backup di Azure e i server Data Protection Manager registrati con l'insieme di credenziali. L'abilitazione di questa impostazione può essere eseguita una sola volta e non è possibile disabilitare queste funzionalità dopo averle abilitate.
>

## <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza

Se si sta creando un insieme di credenziali di servizi di ripristino, è possibile usare tutte le funzionalità di sicurezza. Se si usa un insieme di credenziali esistente, abilitare le funzionalità di sicurezza attenendosi alla procedura seguente:

1. Accedere al portale di Azure con le credenziali di Azure.
2. Selezionare **Sfoglia** e quindi digitare **Servizi di ripristino**.

    ![Screenshot dell'opzione Sfoglia del portale di Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Selezionare un insieme di credenziali dall'elenco. Viene aperto il dashboard dell'insieme di credenziali selezionato.
3. Nell'elenco di elementi visualizzato sotto l'insieme di credenziali, in **Impostazioni**, selezionare **Proprietà**.

    ![Screenshot delle opzioni per l'insieme di credenziali di Servizi di ripristino](./media/backup-azure-security-feature/vault-list-properties.png)
4. In **impostazioni di sicurezza**selezionare **Aggiorna**.

    ![Screenshot delle proprietà dell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-security-feature/security-settings-update.png)

    Il collegamento aggiornamento apre il riquadro **impostazioni di sicurezza** , che fornisce un riepilogo delle funzionalità e consente di abilitarle.
5. Nell'elenco a discesa **È stata eseguita la configurazione di Azure Multi-Factor Authentication?** selezionare un valore per confermare se [Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) è stato abilitato. Se è abilitata, viene richiesto di eseguire l'autenticazione da un altro dispositivo, ad esempio un telefono cellulare, durante l'accesso al portale di Azure.

   Quando si eseguono operazioni critiche in Backup, è necessario immettere il PIN di sicurezza, disponibile nel portale di Azure. L'abilitazione di Azure Multi-Factor Authentication aggiunge un livello di sicurezza. Solo gli utenti autorizzati con credenziali di Azure valide e autenticati da un secondo dispositivo potranno accedere al portale di Azure.
6. Per salvare le impostazioni di sicurezza, selezionare **Abilita** e selezionare **Salva**. È possibile selezionare **Abilita** solo dopo aver selezionato un valore nell'elenco **È stata eseguita la configurazione di Azure Multi-Factor Authentication?** nel passaggio precedente.

    ![Screenshot delle impostazioni di sicurezza](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Ripristino dei dati di backup eliminati

Il backup conserva i dati di backup eliminati per altri 14 giorni e non li elimina immediatamente se viene eseguita l'operazione **Interrompi backup con Elimina dati di backup** . Per ripristinare questi dati nel periodo di 14 giorni, seguire questa procedura, a seconda di ciò che si sta usando:

Per utenti dell'**agente di Servizi di ripristino di Azure**:

1. Se il computer in cui si sono verificati i backup è ancora disponibile, riproteggere le origini dati eliminate e usare i [dati di ripristino nello stesso computer](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) in servizi di ripristino di Azure per eseguire il ripristino da tutti i punti di ripristino precedenti.
2. Se il computer non è disponibile, eseguire il [ripristino in un computer alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) per usare un altro computer dei servizi di ripristino di Azure per ottenere questi dati.

Per gli utenti che usano il **server di Backup di Azure**:

1. Se il server in cui si sono verificati i backup è ancora disponibile, riproteggere le origini dati eliminate e usare la funzionalità **Ripristina dati** per eseguire il ripristino da tutti i punti di ripristino precedenti.
2. Se questo server non è disponibile, usare il [ripristino dei dati da un altro server di backup di Azure](backup-azure-alternate-dpm-server.md) per usare un'altra istanza di server di backup di Azure per ottenere questi dati.

Per gli utenti di **Data Protection Manager**:

1. Se il server in cui si sono verificati i backup è ancora disponibile, riproteggere le origini dati eliminate e usare la funzionalità **Ripristina dati** per eseguire il ripristino da tutti i punti di ripristino precedenti.
2. Se il server non è disponibile, usare [Aggiungi DPM esterno](backup-azure-alternate-dpm-server.md) per usare un altro server Data Protection Manager per ottenere questi dati.

## <a name="prevent-attacks"></a>Prevenire gli attacchi

Sono stati aggiunti alcuni controlli per garantire che solo gli utenti validi possano eseguire diverse operazioni. Questi controlli includono l'aggiunta di un ulteriore livello di autenticazione e il mantenimento di un intervallo minimo di conservazione per scopi di ripristino.

### <a name="authentication-to-perform-critical-operations"></a>Autenticazione per eseguire operazioni critiche

Nell'ambito dell'aggiunta di un ulteriore livello di autenticazione per le operazioni critiche, viene richiesto di immettere un PIN di sicurezza quando si esegue l' **arresto della protezione con Elimina dati** e si **modificano** le operazioni di passphrase.

> [!NOTE]
>
> Attualmente, il pin di sicurezza non è supportato per l' **arresto della protezione con i dati di eliminazione** per DPM e MAB.

Per ricevere questo PIN:

1. Accedere al portale di Azure.
2. Individuare **Recovery Services vault**  >  **le proprietà delle impostazioni**dell'insieme di credenziali dei servizi di ripristino  >  **Properties**.
3. In **pin di sicurezza**selezionare **genera**. Verrà aperto un riquadro che contiene il PIN da immettere nell'interfaccia utente dell'agente di servizi di ripristino di Azure.
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

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi

| Operazione | Dettagli errore | Risoluzione |
| --- | --- | --- |
| Modifica dei criteri |Non è possibile modificare i criteri di backup. Errore: Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x29834]. Ripetere l'operazione in un secondo momento. Se il problema persiste, contattare il supporto tecnico Microsoft. |**Causa:**<br/>Questo errore viene visualizzato quando sono abilitate le impostazioni di sicurezza, si tenta di ridurre il periodo di mantenimento dati al di sotto dei valori minimi specificati in precedenza e si usa una versione non supportata. le versioni supportate sono specificate nella prima nota di questo articolo. <br/>**Azione consigliata:**<br/> In questo caso, per procedere con gli aggiornamenti relativi ai criteri è consigliabile impostare un periodo di memorizzazione maggiore del valore minimo specificato (sette giorni per il backup giornaliero, quattro settimane per il backup settimanale, tre settimane per il backup mensile e un anno per il backup annuale). Facoltativamente, un approccio consigliato consiste nell'aggiornare l'agente di backup, server di Backup di Azure e/o DPM UR per sfruttare tutti gli aggiornamenti della sicurezza. |
| Modificare la passphrase |Il PIN di sicurezza immesso non è corretto. (ID: 100130) Specificare il PIN di sicurezza corretto per completare questa operazione. |**Causa:**<br/> Questo errore si verifica quando si immette un PIN di sicurezza non valido o scaduto durante l'esecuzione di operazioni critiche, ad esempio la modifica della passphrase. <br/>**Azione consigliata:**<br/> Per completare l'operazione, è necessario immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere a portale di Azure e passare a insieme di credenziali di servizi di ripristino > impostazioni > proprietà > genera PIN di sicurezza. Usare questo PIN per modificare la passphrase. |
| Modificare la passphrase |Operazione non riuscita. ID: 120002 |**Causa:**<br/>Questo errore viene visualizzato quando sono abilitate le impostazioni di sicurezza, si tenta di modificare la passphrase e si usa una versione non supportata (le versioni valide sono state specificate nella prima nota di questo articolo).<br/>**Azione consigliata:**<br/> Per modificare la passphrase, è prima necessario aggiornare l'agente di backup alla versione minima 2.0.9052, server di Backup di Azure al numero minimo di aggiornamento 1 e/o a DPM 2012 R2 UR12 o DPM 2016 UR2 (Scarica i collegamenti seguenti), quindi immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare a insieme di credenziali di servizi di ripristino > impostazioni > proprietà > genera PIN di sicurezza. Usare questo PIN per modificare la passphrase. |

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Get started with Azure Recovery Services vault](backup-azure-vms-first-look-arm.md) (Introduzione all'insieme di credenziali di Servizi di ripristino di Azure) per abilitare queste funzionalità.
- Vedere [Download the latest Azure Recovery Services agent](https://aka.ms/azurebackup_agent) (Download dell'agente di Servizi di ripristino di Azure più recente) per proteggere i computer Windows e i dati di backup dagli attacchi.
- Vedere [Download the latest Azure Backup Server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) (Download del server di Backup di Azure più recente) per proteggere i carichi di lavoro e i dati di backup dagli attacchi.
- Vedere [Download UR12 for System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) (Download della versione UR2 per System Center 2012 R2 Data Protection Manager) o [Download UR2 for System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) (Download della versione UR2 per System Center 2016 Data Protection Manager) per proteggere i carichi di lavoro e i dati di backup dagli attacchi.
