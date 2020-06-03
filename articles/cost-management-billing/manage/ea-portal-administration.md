---
title: Amministrazione di Azure EA Portal
description: Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: a2430e29acb83945e9893ca4c6e2e0110ef6e007
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298160"
---
# <a name="azure-ea-portal-administration"></a>Amministrazione di Azure EA Portal

Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal (https://ea.azure.com). Azure EA Portal è un portale di gestione online che consente ai clienti di gestire il costo dei servizi EA di Azure. Per informazioni introduttive su Azure EA Portal, vedere l'articolo [Introduzione ad Azure EA Portal](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Associare un account a un reparto

Gli amministratori dell'organizzazione possono associare account esistenti ai reparti nell'ambito della registrazione.

### <a name="to-associate-an-account-to-a-department"></a>Per associare un account a un reparto

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Selezionare **Reparto**.
1. Passare il puntatore del mouse sulla riga relativa all'account e selezionare l'icona della matita a destra.
1. Selezionare il reparto nel menu a discesa.
1. Selezionare **Salva**.

## <a name="department-spending-quotas"></a>Quote di spesa del reparto

I clienti EA possono impostare o cambiare le quote di spesa per ogni reparto nell'ambito di una registrazione. L'importo delle quote di spesa è impostato per il periodo di impegno corrente. Alla fine del periodo di impegno corrente, il sistema estenderà la quota di spesa esistente al periodo di impegno successivo, a meno che i valori non vengano aggiornati.

L'amministratore del reparto può visualizzare la quota di spesa, ma l'importo della quota può essere aggiornato solo dall'amministratore dell'organizzazione. L'amministratore dell'organizzazione e l'amministratore del reparto riceveranno notifiche quando la quota raggiunge il 50%, il 75%, il 90% e il 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Per impostare la quota (amministratore dell'organizzazione):

 1. Aprire Azure EA Portal.
 1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
 1. Selezionare la scheda **Reparto**.
 1. Selezionare il reparto.
 1. Selezionare il simbolo della matita nella sezione relativa ai dettagli del reparto oppure selezionare il simbolo **+ Aggiungi reparto** per aggiungere una quota di spesa insieme a un nuovo reparto.
 1. Nella sezione relativa ai dettagli del reparto immettere un importo per la quota di spesa nella valuta della registrazione nella casella corrispondente (il valore deve essere maggiore di 0).
    - In questa fase è anche possibile modificare il nome del reparto e il centro di costo.
 1. Selezionare **Salva**.

La quota di spesa del reparto sarà ora visibile nella visualizzazione dell'elenco dei reparti nella scheda Reparto. Al termine dell'impegno corrente, Azure EA Portal manterrà le quote di spesa per il periodo di impegno successivo.

L'importo della quota del reparto è indipendente dall'impegno monetario corrente e gli avvisi e l'importo della quota sono validi solo per l'utilizzo da parte del produttore. La quota di spesa del reparto è solo a scopo informativo e non impone limiti di spesa.

### <a name="department-administrator-to-view-the-quota"></a>Per visualizzare la quota (amministratore del reparto):

1. Aprire Azure EA Portal.
1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Selezionare la scheda **Reparto** ed esaminare la visualizzazione elenco dei reparti con le quote di spesa.

Per i clienti indiretti le funzionalità relative ai costi devono essere abilitate dal partner di canale.

## <a name="enterprise-user-roles"></a>Ruoli utente dell'organizzazione

Azure EA Portal consente di amministrare i costi e l'utilizzo del contratto Enterprise Agreement di Azure. In Azure EA Portal sono disponibili tre ruoli principali:

- Amministratore EA
- Amministratore del reparto
- Proprietario dell'account

Ogni ruolo ha un livello di accesso e autorità diverso.

Per altre informazioni sui ruoli utente, vedere [Ruoli utente dell'organizzazione](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Aggiungere un account EA di Azure

L'account Azure EA è un'unità organizzativa in EA Portal di Azure. Viene usato per l'amministrazione delle sottoscrizioni e anche per la creazione di report. Per accedere e usare i servizi di Azure, è necessario creare un account o farsene creare uno.

Per altre informazioni sugli account di Azure, vedere Aggiungere un account.

## <a name="enterprise-devtest-offer"></a>Offerta Sviluppo/test Enterprise

Un amministratore dell'organizzazione di Azure può abilitare i proprietari di account dell'organizzazione per la creazione di sottoscrizioni basate sull'offerta Sviluppo/test EA. A tale scopo, selezionare la casella Sviluppo/test per il proprietario dell'account in Azure EA Portal.

Dopo aver selezionato la casella Sviluppo/test, informare il proprietario dell'account in modo che possa configurare le sottoscrizioni di Sviluppo/test EA necessarie per i team di sottoscrittori di Sviluppo/test.

Questa offerta consente ai sottoscrittori attivi di Visual Studio di eseguire carichi di lavoro di sviluppo e test in Azure a tariffe speciali. Fornisce l'accesso alla raccolta completa di immagini di Sviluppo/test, tra cui Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Per configurare l'offerta Sviluppo/test Enterprise:

1. Accedere come amministratore dell'organizzazione.
1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Fare clic sulla scheda **Account**.
1. Selezionare la riga relativa all'account in cui si intende abilitare l'accesso a Sviluppo/test.
1. Selezionare il simbolo della matita a destra della riga.
1. Selezionare la casella di controllo di sviluppo/test.
1. Selezionare **Salva**.

Quando un utente viene aggiunto come proprietario dell'account tramite Azure EA Portal, le sottoscrizioni di Azure associate al proprietario dell'account che si basano sull'offerta Sviluppo/test con pagamento in base al consumo o sulle offerte di credito mensile per i sottoscrittori di Visual Studio verranno convertite nell'offerta Sviluppo/test EA. Le sottoscrizioni associate al proprietario dell'account basate su altri tipi di offerta, ad esempio con pagamento in base al consumo, verranno convertite in offerte di Microsoft Azure Enterprise.

Al momento l'offerta Sviluppo/test non è applicabile ai clienti di Azure per enti pubblici.

## <a name="delete-subscription"></a>Eliminare una sottoscrizione

Per eliminare una sottoscrizione in cui si è proprietario dell'account:

1. Accedere al portale di Azure con le credenziali associate all'account.
1. Nel menu Hub selezionare **Sottoscrizioni**.
1. Nella scheda delle sottoscrizioni nell'angolo in alto a sinistra della pagina selezionare la sottoscrizione da annullare e quindi **Cancel Sub** (Annulla sottoscrizione) per aprire la scheda per l'annullamento.
1. Immettere il nome della sottoscrizione, scegliere un motivo per l'annullamento e selezionare il pulsante **Cancel Sub** (Annulla sottoscrizione).

Solo gli amministratori dell'account possono annullare le sottoscrizioni.

Per altre informazioni, vedere [Cosa accade quando si annulla la sottoscrizione](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Eliminare un account

La rimozione di un account può essere completata solo per gli account attivi senza sottoscrizioni attive.

1. In Enterprise Portal selezionare **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Account**.
1. Dalla tabella Account selezionare l'account che si intende eliminare.
1. Selezionare il simbolo X a destra della riga relativa all'account.
1. Nel momento in cui non sono presenti sottoscrizioni attive nell'account, selezionare **Sì** nella riga relativa all'account per confermarne la rimozione.

## <a name="update-notification-settings"></a>Aggiornare le impostazioni di notifica

Gli amministratori dell'organizzazione vengono registrati automaticamente per ricevere le notifiche di utilizzo associate alla relativa registrazione. Ogni amministratore dell'organizzazione può modificare l'intervallo delle singole notifiche oppure disattivarle completamente.

I contatti per le notifiche vengono visualizzati in Azure EA Portal nella sezione **Contatto per le notifiche**. La gestione dei contatti per le notifiche garantisce che le persone giuste dell'organizzazione ricevano le notifiche sulla registrazione EA di Azure.

Per visualizzare le impostazioni di notifica correnti:

1. In Azure EA Portal passare a **Gestisci** > **Contatto per le notifiche**.
2. Indirizzo di posta elettronica: indirizzo associato all'account Microsoft, aziendale o dell'Istituto di istruzione dell'amministratore dell'organizzazione che riceve le notifiche.
3. Unbilled Balance Notification Frequency (Frequenza delle notifiche di saldo non fatturato): frequenza impostata per l'invio delle notifiche a ogni singolo amministratore dell'organizzazione.

Per aggiungere un contatto:

1. Selezionare **+Aggiungi contatto**.
2. Immettere l'indirizzo di posta elettronica e confermarlo.
3. Selezionare **Salva**.

Il nuovo contatto per le notifiche viene visualizzato nella sezione **Contatto per le notifiche**. Per cambiare la frequenza di notifica, selezionare il contatto per la notifica e quindi il simbolo della matita a destra della riga selezionata. Impostare la frequenza su **giornaliera**, **settimanale**, **mensile** o **nessuna**.

È possibile eliminare le notifiche sul ciclo di vita che segnalano che si sta avvicinando la _data di fine del periodo di copertura_ e la _data di disabilitazione e deprovisioning_. Se si disabilitano le notifiche sul ciclo di vita, vengono eliminate le notifiche sulla data di fine del periodo di copertura e del contratto.

## <a name="azure-sponsorship-offer"></a>Offerta Azure Sponsorship

L'offerta Azure Sponsorship è un account Microsoft Azure sponsorizzato con limitazioni. È disponibile per invito tramite posta elettronica solo per un numero limitato di clienti selezionati da Microsoft. Gli utenti idonei per l'offerta Microsoft Azure Sponsorship riceveranno tramite posta elettronica un invito per il proprio ID account.

Per altre informazioni, creare una [richiesta di supporto per l'attivazione sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversione all'autenticazione account aziendale o dell'istituto di istruzione

Gli utenti di Azure Enterprise possono eseguire la conversione da un tipo di autenticazione account Microsoft (account del servizio gestito o Live ID) a un tipo di autenticazione account aziendale o dell'istituto di istruzione (che usa Active Directory in Azure).

Per iniziare:

1. Aggiungere l'account aziendale o dell'istituto di istruzione ad Azure EA Portal nei ruoli necessari.
1. Se si verificano errori, è possibile che l'account non sia valido in Active Directory.  Azure usa il nome dell'entità utente (UPN), che non è sempre identico all'indirizzo di posta elettronica.
1. Eseguire l'autenticazione in Azure EA Portal usando l'account aziendale o dell'istituto di istruzione.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Per convertire le sottoscrizioni da account Microsoft in account aziendali o dell'istituto di istruzione:

1. Accedere al portale di gestione usando l'account Microsoft proprietario delle sottoscrizioni.
1. Usare il trasferimento della proprietà dell'account per passare al nuovo account.
1. A questo punto l'account Microsoft dovrebbe essere privo di sottoscrizioni attive e può essere eliminato.
1. Gli account eliminati rimarranno visibili nel portale con uno stato inattivo per motivi di fatturazione cronologica.  È possibile escluderli dalla visualizzazione selezionando una casella di controllo per la visualizzazione solo degli account attivi.

## <a name="account-subscription-ownership-faq"></a>Domande frequenti sulla proprietà delle sottoscrizioni degli account

Questo documento risponde alle domande frequenti relative alla proprietà delle sottoscrizioni degli account.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quanti proprietari di account Azure possono esistere per ogni sottoscrizione?

Per ogni sottoscrizione è consentito un solo proprietario di account.  È possibile aggiungere altri ruoli tramite l'accesso in base al ruolo o Controllo di accesso (IAM) nella scheda relativa alle sottoscrizioni nell'angolo superiore sinistro della pagina in [portal.azure.com] (https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Un proprietario di account di Azure può essere elencato in più reparti?

No, un proprietario di account può essere associato solo a un singolo reparto. Questo criterio garantisce un monitoraggio e una ripartizione accurati di costi e spese associati al reparto con cui è allineato nella registrazione EA in Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Un proprietario di account di Azure può essere elencato come gruppo di sicurezza?

No, per il proprietario di una sottoscrizione deve essere applicata un'autenticazione di Azure Active Directory (AAD) o di un account Microsoft univoco (account del servizio gestito). Per tenere conto della successione all'interno dell'organizzazione, valutare l'opportunità di creare account generici e di usare AAD per gestire l'accesso alla sottoscrizione.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Un singolo utente può possedere più sottoscrizioni?

Un proprietario di account di Azure può creare e gestire un numero illimitato di sottoscrizioni.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Come è possibile accedere o visualizzare tutte le sottoscrizioni di un'organizzazione?

Attualmente questa operazione deve essere eseguita in base ai criteri, ovvero per ogni sottoscrizione creata è necessario richiedere che l'account venga aggiunto a un ruolo di sottoscrizione con l'accesso basato sui ruoli.

### <a name="where-do-i-go-to-create-a-subscription"></a>Da dove è possibile creare una sottoscrizione?

Per poter creare una sottoscrizione per l'offerta Azure Enterprise (EA), è necessario che l'account venga prima aggiunto al ruolo di proprietario dell'account dall'amministratore della registrazione EA in Azure EA Portal. Sarà quindi necessario accedere ad Azure EA Portal per ottenere l'entitlement per creare sottoscrizioni per il tipo di offerta EA. È consigliabile creare la prima sottoscrizione EA dal collegamento '+ Aggiungi sottoscrizione' nella scheda relativa alle sottoscrizioni in EA Portal.  Tuttavia, una volta che l'account è stato autorizzato, potrebbe essere più facile creare sottoscrizioni in portal.azure.com nella scheda relativa alla sottoscrizione nell'angolo superiore sinistro della pagina, in cui è possibile creare e rinominare una sottoscrizione in un unico passaggio.

### <a name="who-can-create-a-subscription"></a>Chi può creare una sottoscrizione?

Per creare una sottoscrizione per il tipo di offerta Azure Enterprise, è necessario essere autorizzati nel ruolo di proprietario dell'account in [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Passaggi successivi

- Leggere l'articolo sulle [istanze di macchina virtuale riservate di Azure](ea-portal-vm-reservations.md) per informazioni su come risparmiare denaro.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](ea-portal-troubleshoot.md).
