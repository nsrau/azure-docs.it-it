---
title: Amministrazione di Azure EA Portal
description: Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: f344436b1e1f1a97b981c1ead469d965b06a12ce
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329344"
---
# <a name="azure-ea-portal-administration"></a>Amministrazione di Azure EA Portal

Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal (https://ea.azure.com). Azure EA Portal è un portale di gestione online che consente ai clienti di gestire il costo dei servizi EA di Azure. Per informazioni introduttive su Azure EA Portal, vedere l'articolo [Introduzione ad Azure EA Portal](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Aggiungere un nuovo amministratore dell'organizzazione

Gli amministratori dell'organizzazione hanno il maggior numero di privilegi per la gestione di una registrazione EA di Azure. Il primo amministratore EA di Azure è stato creato al momento della configurazione del contratto Enterprise Agreement. Tuttavia, è possibile aggiungere o rimuovere nuovi amministratori in qualsiasi momento. I nuovi amministratori possono essere aggiunti solo dagli amministratori esistenti. Per altre informazioni sull'aggiunta di altri amministratori dell'organizzazione, vedere [Creare un altro amministratore dell'organizzazione](billing-ea-portal-get-started.md#create-another-enterprise-admin). Per altre informazioni sui ruoli e le attività del profilo di fatturazione, vedere [Ruoli e attività del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Aggiornare lo stato dell'utente da in sospeso ad attivo

Quando vengono aggiunti nuovi proprietari di account a una registrazione EA di Azure per la prima volta, il relativo stato viene visualizzato come _in sospeso_. Quando un nuovo proprietario dell'account riceve il messaggio di posta elettronica di benvenuto per l'attivazione, può accedere per attivare il proprio account. Quando attiva l'account, lo stato dell'account viene aggiornato da _in sospeso_ ad _attivo_. Ai nuovi utenti potrebbe essere richiesto di immettere il nome e il cognome per creare un account commerciale. In tal caso, devono aggiungere le informazioni necessarie per continuare, quindi l'account viene attivato.

## <a name="add-a-department-admin"></a>Aggiungere un amministratore del reparto

Dopo che un amministratore EA di Azure ha creato un reparto, l'amministratore dell'organizzazione di Azure può aggiungere amministratori di reparto e associare ognuno a un reparto. Un amministratore del reparto può creare nuovi account. I nuovi account sono necessari per la creazione di sottoscrizioni EA di Azure.

Per altre informazioni sull'aggiunta di un amministratore di reparto, vedere [Creare un amministratore di reparto EA di Azure](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="enterprise-user-roles"></a>Ruoli utente dell'organizzazione

Azure EA Portal consente di amministrare i costi e l'utilizzo del contratto Enterprise Agreement di Azure. In Azure EA Portal sono disponibili tre ruoli principali:

- Amministratore EA
- Amministratore del reparto
- Proprietario dell'account

Ogni ruolo ha un livello di accesso e autorità diverso.

Per altre informazioni sui ruoli utente, vedere Ruoli utente dell'organizzazione.

## <a name="add-an-azure-ea-account"></a>Aggiungere un account EA di Azure

L'account EA di Azure è un'unità organizzativa in Azure EA Portal usata per amministrare le sottoscrizioni e anche per la creazione di report. Per accedere e usare i servizi di Azure, è necessario creare un account o farsene creare uno.

Per altre informazioni sugli account di Azure, vedere Aggiungere un account.

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
  - In alcuni casi, Microsoft potrebbe richiedere un'ulteriore approvazione da parte di un amministratore EA della registrazione di origine
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
  - In alcuni casi, Microsoft potrebbe richiedere un'ulteriore approvazione da parte di un amministratore EA della registrazione di origine.
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



## <a name="close-an-azure-enterprise-enrollment"></a>Chiudere una registrazione EA di Azure

Se si vuole chiudere la registrazione EA di Azure, è possibile:

- Annullare tutte le sottoscrizioni associate al contratto Enterprise Agreement di Azure nel portale di Azure.
- Contattare il software advisor del cliente o il partner e chiedergli di chiudere il contratto Enterprise Agreement di Azure.

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

## <a name="manage-partner-notifications"></a>Gestire le notifiche dei partner

Gli amministratori partner possono gestire la frequenza con cui ricevono le notifiche di utilizzo per le proprie registrazioni. Ricevono automaticamente notifiche settimanali sul saldo non fatturato. Possono modificare la frequenza delle singole notifiche in mensile, settimanale, giornaliera oppure disattivarle completamente.

Se un utente non riceve una notifica, verificare che le impostazioni di notifica dell'utente siano corrette con la procedura seguente.

1. Accedere ad Azure EA Portal come amministratore partner.
2. Fare clic su **Gestisci** e quindi sulla scheda **Partner**.
3. Visualizzare l'elenco degli amministratori nella sezione **Amministratore**.
4. Per modificare le preferenze di notifica, passare il puntatore sull'amministratore appropriato e fare clic sul simbolo della matita.
5. Modificare la frequenza di notifica e le notifiche sul ciclo di vita in base alle esigenze.
6. Aggiungere un contatto, se necessario, e fare clic su **Aggiungi**.
7. Fare clic su **Save**.

![Esempio che mostra la casella Aggiunta di un contatto ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Offerta Azure Sponsorship

L'offerta Azure Sponsorship è un account di Microsoft Azure sponsorizzato con limitazioni. È disponibile per invito tramite posta elettronica solo per un numero limitato di clienti selezionati da Microsoft. Gli utenti idonei all'offerta Microsoft Azure Sponsorship riceveranno tramite posta elettronica un invito per il proprio ID account.

Per altre informazioni, vedere:

- Richiesta di supporto per l'attivazione dell'offerta Sponsorship: http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Passaggi successivi
- Leggere l'articolo sulle [istanze di macchina virtuale riservate di Azure](billing-ea-portal-vm-reservations.md) per informazioni su come risparmiare denaro.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](billing-ea-portal-troubleshoot.md).
