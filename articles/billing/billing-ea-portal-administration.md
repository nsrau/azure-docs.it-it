---
title: Amministrazione di Azure EA Portal
description: Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: c53a051df0a0100d9209530490d910612be2f30d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849922"
---
# <a name="azure-ea-portal-administration"></a>Amministrazione di Azure EA Portal

Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal (https://ea.azure.com). Azure EA Portal è un portale di gestione online che consente ai clienti di gestire il costo dei servizi EA di Azure. Per informazioni introduttive su Azure EA Portal, vedere l'articolo [Introduzione ad Azure EA Portal](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Aggiungere un nuovo amministratore dell'organizzazione

Gli amministratori dell'organizzazione hanno il maggior numero di privilegi per la gestione di una registrazione EA di Azure. Il primo amministratore EA di Azure è stato creato al momento della configurazione del contratto Enterprise Agreement. Tuttavia, è possibile aggiungere o rimuovere nuovi amministratori in qualsiasi momento. I nuovi amministratori possono essere aggiunti solo dagli amministratori esistenti. Per altre informazioni sull'aggiunta di altri amministratori dell'organizzazione, vedere [Creare un altro amministratore dell'organizzazione](billing-ea-portal-get-started.md#create-another-enterprise-admin). Per altre informazioni sui ruoli e le attività del profilo di fatturazione, vedere [Ruoli e attività del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Aggiornare lo stato dell'utente da in sospeso ad attivo

Quando vengono aggiunti nuovi proprietari di account a una registrazione EA di Azure per la prima volta, il relativo stato viene visualizzato come _in sospeso_. Quando un nuovo proprietario dell'account riceve il messaggio di posta elettronica di benvenuto per l'attivazione, può accedere per attivare il proprio account. Una volta attivato l'account, il relativo stato viene aggiornato e passa da _In sospeso_ ad _Attivo_. Il proprietario dell'account deve leggere il messaggio di avviso e fare clic su **Continua**. Ai nuovi utenti potrebbe essere richiesto di immettere il nome e il cognome per creare un account commerciale. In tal caso, devono aggiungere le informazioni necessarie per continuare, quindi l'account viene attivato.

## <a name="add-a-department-admin"></a>Aggiungere un amministratore del reparto

Dopo che un amministratore EA di Azure ha creato un reparto, l'amministratore dell'organizzazione di Azure può aggiungere amministratori di reparto e associare ognuno a un reparto. Un amministratore del reparto può creare nuovi account. I nuovi account sono necessari per la creazione di sottoscrizioni EA di Azure.

Per altre informazioni sull'aggiunta di un amministratore di reparto, vedere [Creare un amministratore di reparto EA di Azure](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="associate-an-account-to-a-department"></a>Associare un account a un reparto

Gli amministratori dell'organizzazione possono associare account esistenti ai reparti nell'ambito della registrazione.

### <a name="to-associate-an-account-to-a-department"></a>Per associare un account a un reparto

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic su **Reparto**.
1. Passare il puntatore del mouse sulla riga relativa all'account desiderato e fare clic sull'icona della matita a destra.
1. Selezionare dall'elenco a discesa il reparto desiderato.
1. Fare clic su **Save**.

## <a name="department-spending-quotas"></a>Quote di spesa del reparto

I clienti EA possono impostare o modificare le quote di spesa per ogni reparto nell'ambito di una registrazione. L'importo delle quote di spesa è impostato per il periodo di impegno corrente. Alla fine del periodo di impegno corrente, il sistema estenderà la quota di spesa esistente al periodo di impegno successivo, a meno che i valori non vengano aggiornati.

L'amministratore del reparto può visualizzare la quota di spesa, ma l'importo della quota può essere aggiornato solo dall'amministratore dell'organizzazione. L'amministratore dell'organizzazione e l'amministratore del reparto riceveranno notifiche quando la quota raggiunge il 50%, il 75%, il 90% e il 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Per impostare la quota (amministratore dell'organizzazione):

 1. Aprire Azure EA Portal.
 1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
 1. Fare clic sulla scheda **Reparto**.
 1. Fare clic sul reparto desiderato.
 1. Fare clic sull'icona della matita nella sezione relativa ai dettagli del reparto oppure fare clic sul pulsante **+ Add Department** (Aggiungi reparto) per aggiungere una quota di spesa insieme a un nuovo reparto.
 1. Nella sezione relativa ai dettagli del reparto immettere un importo per la quota di spesa nella valuta della registrazione nella casella relativa all'importo per la quota di spesa (il valore deve essere maggiore di 0).
    - In questa fase è anche possibile modificare il nome del reparto e il centro di costo.
 1. Premere **Salva**.

La quota di spesa del reparto sarà ora visibile nella visualizzazione dell'elenco dei reparti nella scheda Reparto. Al termine dell'impegno corrente, Azure EA Portal manterrà le quote di spesa per il periodo di impegno successivo.

L'importo della quota del reparto è indipendente dall'impegno monetario corrente e gli avvisi e l'importo della quota sono validi solo per l'utilizzo da parte del produttore. La quota di spesa del reparto è solo a scopo informativo e non impone limiti di spesa.

### <a name="department-administrator-to-view-the-quota"></a>Per visualizzare la quota (amministratore del reparto):

1. Aprire Azure EA Portal.
1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Reparto** ed esaminare la visualizzazione elenco dei reparti con le quote di spesa.

Per i clienti indiretti le funzionalità relative ai costi devono essere abilitate dal partner di canale.

## <a name="enterprise-user-roles"></a>Ruoli utente dell'organizzazione

Azure EA Portal consente di amministrare i costi e l'utilizzo del contratto Enterprise Agreement di Azure. In Azure EA Portal sono disponibili tre ruoli principali:

- Amministratore EA
- Amministratore del reparto
- Proprietario dell'account

Ogni ruolo ha un livello di accesso e autorità diverso.

Per altre informazioni sui ruoli utente, vedere [Ruoli utente dell'organizzazione](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Aggiungere un account EA di Azure

L'account EA di Azure è un'unità organizzativa in Azure EA Portal usata per amministrare le sottoscrizioni e anche per la creazione di report. Per accedere e usare i servizi di Azure, è necessario creare un account o farsene creare uno.

Per altre informazioni sugli account di Azure, vedere Aggiungere un account.

## <a name="enterprise-devtest-offer"></a>Offerta Sviluppo/test Enterprise

Un amministratore dell'organizzazione di Azure può ora abilitare i proprietari di account dell'organizzazione per la creazione di sottoscrizioni basate sull'offerta Sviluppo/test EA. A tale scopo, selezionare la casella di sviluppo/test relativa al proprietario dell'account in Azure EA Portal.

Dopo aver selezionato la casella di sviluppo/test, informare il proprietario dell'account in modo che possa configurare le sottoscrizioni di Sviluppo/test EA necessarie per i team di sottoscrittori di Sviluppo/test.

Questa offerta consente ai sottoscrittori di Visual Studio attivi di eseguire carichi di lavoro di sviluppo e test in Azure a tariffe di sviluppo/test speciali, con accesso alla raccolta completa di immagini di sviluppo/test, inclusi Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Per configurare l'offerta Sviluppo/test Enterprise:

1. Accedere come amministratore dell'organizzazione.
1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Account**.
1. Fare clic sulla riga relativa all'account in cui si intende abilitare l'accesso a Sviluppo/test.
1. Fare clic sull'icona della matita a destra della riga.
1. Selezionare la casella di controllo di sviluppo/test.
1. Premere **Salva**.

Quando un utente viene aggiunto come proprietario dell'account tramite Azure EA Portal, le sottoscrizioni di Azure associate al proprietario dell'account che si basano sull'offerta Sviluppo/test con pagamento in base al consumo o sulle offerte di credito mensile per i sottoscrittori di Visual Studio verranno convertite nell'offerta Sviluppo/test EA. Le sottoscrizioni associate al proprietario dell'account basate su altri tipi di offerta, ad esempio con pagamento in base al consumo, verranno convertite in offerte di Microsoft Azure Enterprise.

Al momento l'offerta Sviluppo/test non è applicabile ai clienti di Azure per enti pubblici.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Trasferire un account aziendale in una nuova registrazione

Quando si trasferisce un account aziendale in una nuova registrazione, tenere presente quanto segue:

- Vengono trasferiti solo gli account specificati nella richiesta. Se si selezionano tutti gli account, vengono trasferiti tutti.
- La registrazione di origine mantiene il proprio stato come attiva o estesa. È possibile continuare a usarla fino alla scadenza.

### <a name="effective-transfer-date"></a>Data di validità del trasferimento

La data di validità del trasferimento può essere una data corrispondente o successiva alla data di inizio della registrazione in cui si vuole eseguire il trasferimento. La registrazione in cui si sta eseguendo il trasferimento è la _registrazione di destinazione_. Dopo il trasferimento dell'account, tutte le informazioni sull'utilizzo nell'account prima della data di trasferimento rimangono nella registrazione da cui si sta eseguendo il trasferimento. La registrazione da cui si sta eseguendo il trasferimento è la _registrazione di origine_.  L'utilizzo della registrazione di origine viene addebitato in base all'impegno monetario o come eccedenza. L'utilizzo che ha luogo dopo la data di trasferimento effettiva viene trasferito alla nuova registrazione e addebitato di conseguenza.

È possibile retrodatare il trasferimento di una registrazione fino alla data di inizio della registrazione di destinazione. oppure fino alla data di inizio effettiva della registrazione di origine.

### <a name="monetary-commitment"></a>Impegno monetario

L'impegno monetario non è trasferibile tra registrazioni. I saldi dell'impegno monetario sono legati contrattualmente alla registrazione in cui è stato ordinato. L'impegno monetario non viene trasferito come parte del processo di trasferimento dell'account o della registrazione.

### <a name="services-affected"></a>Servizi interessati

Durante il trasferimento dell'account non si verifica alcun tempo di inattività. Il trasferimento può essere completato nello stesso giorno della richiesta, se vengono fornite tutte le informazioni necessarie.

### <a name="prerequisites"></a>Prerequisiti

Quando si richiede il trasferimento di un account, fornire le informazioni seguenti:


- Nome dell'account e ID del proprietario dell'account da trasferire
- Per la registrazione di origine, numero di registrazione e account da trasferire
- Per la registrazione di destinazione, numero della registrazione in cui eseguire il trasferimento
- Come data di validità del trasferimento dell'account, una data corrispondente o successiva alla data di inizio della registrazione di destinazione

Altri punti da tenere presenti prima del trasferimento di un account:

- Per la registrazione di destinazione e di origine è necessaria l'approvazione di un amministratore EA
- Se il trasferimento di un account non soddisfa le proprie esigenze, prendere in considerazione un trasferimento di registrazione.
- Con il trasferimento vengono trasferiti tutti i servizi e le sottoscrizioni correlati agli account specifici.
- Al termine del trasferimento, l'account trasferito appare inattivo nella registrazione di origine.
- Il trasferimento di un account può essere retrodatato a qualsiasi giorno entro la data di inizio della registrazione di destinazione.
- L'account mostra la data di fine corrispondente alla data del trasferimento effettivo nella registrazione di origine e come data di inizio nella registrazione di destinazione.
- Qualsiasi utilizzo che si verifica per l'account prima della data del trasferimento effettivo rimane nella registrazione di origine.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Trasferire la registrazione EA in una nuova registrazione

Quando si richiede il trasferimento di un'intera registrazione EA in un'altra registrazione, si verificano le azioni seguenti:

- Vengono trasferiti tutti i servizi di Azure, le sottoscrizioni, gli account, i reparti e l'intera struttura di registrazione, inclusi tutti gli amministratori del reparto EA.
- Lo stato della registrazione viene impostato su _Trasferito_. La registrazione trasferita è disponibile solo per la creazione di report cronologici sull'utilizzo.
- Non è possibile aggiungere ruoli o sottoscrizioni a una registrazione trasferita. Lo stato Trasferito impedisce ulteriori utilizzi per la registrazione.
- Qualsiasi saldo dell'impegno monetario rimanente nel contratto viene perso, inclusi i periodi futuri.
-   Se la registrazione da cui si esegue il trasferimento prevede acquisti di istanze riservate, la tariffa di acquisto di istanze riservate verrà mantenuta nella registrazione di origine, tuttavia tutti i vantaggi relativi alle istanze riservate verranno trasferiti per essere usati nella nuova registrazione.
-   La tariffa di costo di acquisto una tantum del Marketplace ed eventuali tariffe fisse mensili già addebitate nella registrazione precedente non verranno trasferite nella nuova registrazione. Verranno trasferiti gli addebiti per il Marketplace basati sul consumo.
-   Dopo un trasferimento di registrazione retrodatato, le eventuali tariffe una tantum degli acquisti verranno mantenute nella registrazione di origine.

### <a name="effective-transfer-date"></a>Data di validità del trasferimento

La data di validità del trasferimento può essere una data corrispondente o successiva alla data di inizio della registrazione che si vuole trasferire nella registrazione di destinazione.

L'utilizzo della registrazione di origine viene addebitato in base all'impegno monetario o come eccedenza. L'utilizzo che ha luogo dopo la data di trasferimento effettiva viene trasferito alla nuova registrazione e addebitato di conseguenza.

### <a name="effective-transfer-date-in-the-past"></a>Data di validità del trasferimento nel passato

È possibile retrodatare il trasferimento di un account fino alla data di inizio della registrazione di destinazione oppure fino alla data di inizio effettiva della registrazione di origine.

### <a name="monetary-commitment"></a>Impegno monetario

L'impegno monetario non è trasferibile tra registrazioni. I saldi dell'impegno monetario sono legati contrattualmente alla registrazione in cui è stato ordinato. L'impegno monetario non viene trasferito come parte del processo di trasferimento dell'account o della registrazione.

### <a name="services-affected"></a>Servizi interessati

Durante il trasferimento dell'account non si verifica alcun tempo di inattività. Il trasferimento può essere completato nello stesso giorno della richiesta, se vengono fornite tutte le informazioni necessarie.

### <a name="prerequisites"></a>Prerequisiti

Quando si richiede il trasferimento di una registrazione, fornire le informazioni seguenti:

- Per la registrazione di origine, numero di registrazione e account da trasferire.
- Per la registrazione di destinazione, numero della registrazione in cui eseguire il trasferimento.
- Come data di validità del trasferimento della registrazione, una data corrispondente o successiva alla data di inizio della registrazione di destinazione. La data scelta non può incidere sull'utilizzo relativo a una fattura per l'eccedenza già emessa.

Altri punti da tenere presenti prima del trasferimento di una registrazione:

- Per la registrazione di destinazione e di origine è necessaria l'approvazione di un amministratore EA.
- Se il trasferimento di una registrazione non soddisfa le proprie esigenze, prendere in considerazione un trasferimento di account.
- Vengono trasferiti solo gli account specificati. È possibile richiedere il trasferimento di tutti gli account.
- La registrazione di origine mantiene il proprio stato come attiva/estesa. È possibile continuare a usarla fino alla scadenza.

## <a name="change-account-owner"></a>Cambiare il proprietario dell'account

Azure EA Portal può trasferire le sottoscrizioni da un proprietario dell'account a un altro. Per altre informazioni, vedere [Modificare il proprietario dell'account](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Effetti del trasferimento della sottoscrizione

Quando una sottoscrizione di Azure viene trasferita a un account nello stesso tenant di Azure Active Directory, tutti gli utenti, i gruppi e le entità servizio che disponevano del [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per gestire le risorse conservano l'accesso.

Per visualizzare gli utenti con autorizzazioni di controllo degli accessi in base al ruolo alla sottoscrizione:

1. Nel portale di Azure aprire **Sottoscrizioni**.
2. Selezionare la sottoscrizione da visualizzare e quindi selezionare **Controllo di accesso (IAM)** .
3. Selezionare le **Assegnazioni di ruoli**. La pagina Assegnazioni di ruolo elenca tutti gli utenti con autorizzazioni di controllo degli accessi in base al ruolo alla sottoscrizione.

Se la sottoscrizione viene trasferita a un account in un altro tenant di Azure AD, tutti gli utenti, i gruppi e le entità servizio che disponevano del [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per gestire le risorse _perdono_ l'accesso. Anche senza le autorizzazioni di controllo degli accessi in base al ruolo, l'accesso alla sottoscrizione potrebbe essere disponibile tramite meccanismi di sicurezza, tra cui:

- Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md).
- Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md).
- Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare l'accesso alle risorse di Azure, deve prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Tutte le risorse** dal menu Hub.
3. Selezionare la risorsa.
4. Nella pagina della risorsa fare clic su **Impostazioni** per visualizzare e aggiornare i segreti esistenti.

## <a name="delete-subscription"></a>Eliminare una sottoscrizione

Per eliminare una sottoscrizione in cui si è proprietario dell'account:

1. Accedere al portale di Azure con le credenziali associate all'account.
1. Nel menu Hub selezionare **Sottoscrizioni**.
1. Nella scheda delle sottoscrizioni nell'angolo in alto a sinistra della pagina selezionare la sottoscrizione da annullare e fare clic su **Cancel Sub** (Annulla sottoscrizione) per aprire la scheda per l'annullamento.
1. Immettere il nome della sottoscrizione, scegliere un motivo per l'annullamento e fare clic sul pulsante **Cancel Sub** (Annulla sottoscrizione).

Si noti che solo gli amministratori dell'account possono annullare le sottoscrizioni.

## <a name="delete-an-account"></a>Eliminare un account

La rimozione di un account può essere completata solo per gli account attivi senza sottoscrizioni attive.

1. Da Enterprise Portal selezionare **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Account**.
1. Dalla tabella Account selezionare l'account che si intende eliminare.
1. Fare clic sull'icona X a destra della riga relativa all'account.
1. Nel momento in cui non sono presenti sottoscrizioni attive nell'account, fare clic sul pulsante **Sì** nella riga relativa all'account per confermarne la rimozione.

## <a name="update-notification-settings"></a>Aggiornare le impostazioni di notifica

Gli amministratori dell'organizzazione vengono registrati automaticamente per ricevere le notifiche di utilizzo associate alla relativa registrazione. Ogni amministratore dell'organizzazione può modificare l'intervallo delle singole notifiche oppure disattivarle completamente.

I contatti per le notifiche vengono visualizzati in Azure EA Portal nella sezione **Contatto per le notifiche**. La gestione dei contatti per le notifiche garantisce che le persone giuste dell'organizzazione ricevano le notifiche sulla registrazione EA di Azure.

Per visualizzare le impostazioni di notifica correnti:

1. In Azure EA Portal passare a **Gestisci** > **Contatto per le notifiche**.
2. Indirizzo di posta elettronica: indirizzo associato all'account Microsoft, aziendale o dell'Istituto di istruzione dell'amministratore dell'organizzazione che riceve le notifiche.
3. Unbilled Balance Notification Frequency (Frequenza delle notifiche di saldo non fatturato): frequenza impostata per l'invio delle notifiche a ogni singolo amministratore dell'organizzazione.

Per aggiungere un contatto:

1. Fare clic su **+Aggiungi contatto**.
2. Immettere l'indirizzo di posta elettronica e confermarlo.
3. Fare clic su **Save**.

Il nuovo contatto per le notifiche viene visualizzato nella sezione **Contatto per le notifiche**. Per modificare la frequenza di notifica, selezionare il contatto per la notifica e fare clic sul simbolo della matita a destra della riga selezionata. Impostare la frequenza su **giornaliera**, **settimanale**, **mensile** o **nessuna**.

È possibile eliminare le notifiche sul ciclo di vita che segnalano che si sta avvicinando la _data di fine del periodo di copertura_ e la _data di disabilitazione e deprovisioning_. Se si disabilitano le notifiche sul ciclo di vita, vengono eliminate le notifiche sulla data di fine del periodo di copertura e del contratto.

## <a name="manage-partner-administrators"></a>Gestire gli amministratori partner

Ogni amministratore partner in Azure EA Portal è in grado di aggiungere o rimuovere altri amministratori partner. Gli amministratori partner sono associati alle organizzazioni partner di registrazioni indirette e non sono associati direttamente alle registrazioni.

### <a name="add-a-partner-administrator"></a>Aggiungere un amministratore partner

Per visualizzare un elenco di tutte le registrazioni associate alla stessa organizzazione partner dell'utente corrente, fare clic sulla scheda **Registrazione** e selezionare una casella di registrazione desiderata.

1. Accedere come amministratore partner.
1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Partner**.
1. Fare clic su **+ Aggiungi amministratore** e immettere l'indirizzo di posta elettronica, il contatto per le notifiche e i dettagli delle notifiche.
1. Fare clic su **Aggiungi**.

### <a name="remove-a-partner-administrator"></a>Rimuovere un amministratore partner

Per visualizzare un elenco di tutte le registrazioni associate alla stessa organizzazione partner dell'utente corrente, fare clic sulla scheda **Registrazione** e selezionare una casella di registrazione desiderata.

1. Accedere come amministratore partner.
1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Partner**.
1. Nella sezione Amministratore selezionare la riga appropriata per l'amministratore che si intende rimuovere.
1. Fare clic sull'icona X a destra.
1. Confermare l'eliminazione.

## <a name="manage-partner-notifications"></a>Gestire le notifiche dei partner

Gli amministratori partner possono gestire la frequenza con cui ricevono le notifiche di utilizzo per le proprie registrazioni. Ricevono automaticamente notifiche settimanali sul saldo non fatturato. Possono modificare la frequenza delle singole notifiche in mensile, settimanale, giornaliera oppure disattivarle completamente.

Se un utente non riceve una notifica, verificare che le impostazioni di notifica dell'utente siano corrette con la procedura seguente.

1. Accedere ad Azure EA Portal come amministratore partner.
2. Fare clic su **Gestisci** e quindi sulla scheda **Partner**.
3. Visualizzare l'elenco degli amministratori nella sezione Amministratore.
4. Per modificare le preferenze di notifica, passare il puntatore sull'amministratore appropriato e fare clic sul simbolo della matita.
5. Modificare la frequenza di notifica e le notifiche sul ciclo di vita in base alle esigenze.
6. Aggiungere un contatto, se necessario, e fare clic su **Aggiungi**.
7. Fare clic su **Save**.

![Esempio che mostra la casella Aggiunta di un contatto ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Visualizzare le registrazioni per gli amministratori partner

Gli amministratori partner possono accedere a una visualizzazione elenco di tutte le registrazioni dirette e indirette in Azure EA Portal. Verranno visualizzate caselle contenenti una panoramica di ogni registrazione, con il numero e il nome della registrazione, nonché il saldo e gli importi in eccedenza.

### <a name="view-a-list-of-enrollments"></a>Visualizzare un elenco di registrazioni

1. Accedere come amministratore partner.
1. Fare clic su **Gestisci** nel riquadro di spostamento sul lato sinistro della pagina.
1. Fare clic sulla scheda **Registrazione**.
1. Selezionare la casella relativa alla registrazione desiderata.

Nella parte superiore della pagina è visibile una visualizzazione di tutte le registrazioni con le caselle relative a ogni registrazione. È inoltre possibile spostarsi tra le registrazioni facendo clic sul numero di registrazione corrente nel riquadro di spostamento sul lato sinistro della pagina. Verrà visualizzata una finestra popup che consente di eseguire la ricerca delle registrazioni o di selezionare una registrazione diversa facendo clic sulla casella appropriata.

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

Un proprietario di account può essere associato solo a un singolo reparto.  In questo modo è possibile garantire un monitoraggio e una ripartizione accurati di costi/spese associati al reparto con cui è allineato nella registrazione in Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Un proprietario di account di Azure può essere elencato come gruppo di sicurezza?

No, per il proprietario di una sottoscrizione deve essere applicata un'autenticazione di Azure Active Directory (AAD) o di un account Microsoft univoco (account del servizio gestito). Per tenere conto della successione all'interno dell'organizzazione, valutare l'opportunità di creare account generici e di usare AAD per gestire l'accesso alla sottoscrizione.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Un singolo utente può possedere più sottoscrizioni?

Un proprietario di account di Azure può creare e gestire un numero illimitato di sottoscrizioni.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Come è possibile accedere o visualizzare tutte le sottoscrizioni di un'organizzazione?

Attualmente questa operazione deve essere eseguita in base ai criteri, ovvero per ogni sottoscrizione creata è necessario richiedere che l'account venga aggiunto a un ruolo di sottoscrizione con l'accesso basato sui ruoli.

### <a name="where-do-i-go-to-create-a-subscription"></a>Da dove è possibile creare una sottoscrizione?

Per poter creare una sottoscrizione per l'offerta Azure Enterprise (EA), è necessario prima che l'account venga aggiunto al ruolo di proprietario dell'account dall'amministratore della registrazione EA in Azure EA Portal. Sarà quindi necessario accedere ad Azure EA Portal per ottenere i diritti per creare sottoscrizioni per il tipo di offerta EA. È consigliabile creare la prima sottoscrizione EA dal collegamento + Aggiungi sottoscrizione nella scheda relativa alle sottoscrizioni in EA Portal.  Tuttavia, una volta che l'account è stato autorizzato, potrebbe essere più facile creare sottoscrizioni in portal.azure.com nella scheda relativa alla sottoscrizione nell'angolo superiore sinistro della pagina, in cui è possibile creare e rinominare una sottoscrizione in un unico passaggio.

### <a name="who-can-create-a-subscription"></a>Chi può creare una sottoscrizione?

Per creare una sottoscrizione per il tipo di offerta Azure Enterprise, è necessario essere autorizzati nel ruolo di proprietario dell'account in [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Passaggi successivi

- Leggere l'articolo sulle [istanze di macchina virtuale riservate di Azure](billing-ea-portal-vm-reservations.md) per informazioni su come risparmiare denaro.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](billing-ea-portal-troubleshoot.md).
