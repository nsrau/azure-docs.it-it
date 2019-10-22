---
title: Configurare l'account di fatturazione per un Contratto del cliente Microsoft - Azure
description: Informazioni su come configurare l'account di fatturazione per un Contratto del cliente Microsoft.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: beaf04624cae1dfc970d513160fbeb7513bf30a4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376621"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurare l'account di fatturazione per un Contratto del cliente Microsoft

Se la registrazione Enterprise Agreement è scaduta o sta per scadere, è possibile firmare un Contratto del cliente Microsoft per rinnovare la registrazione. Questo articolo descrive le modifiche apportate alla fatturazione esistente dopo la configurazione e illustra la procedura di configurazione del nuovo account di fatturazione. Il rinnovo include i passaggi seguenti:

1. Accettare il nuovo Contratto del cliente Microsoft. Rivolgersi al rappresentante Microsoft per ottenere informazioni dettagliate chiarimenti e accettare il nuovo contratto.
2. Configurare il nuovo account di fatturazione creato per il nuovo Contratto del cliente Microsoft.

Per configurare l'account di fatturazione, è necessario eseguire la transizione della fatturazione delle sottoscrizioni di Azure dalla registrazione Enterprise Agreement al nuovo account. La configurazione non ha alcun effetto sui servizi di Azure in esecuzione nelle sottoscrizioni. Modifica però la modalità di gestione della fatturazione per le sottoscrizioni.

- I servizi e la fatturazione di Azure verranno gestiti nel [portale di Azure](https://portal.azure.com) anziché in [EA Portal](https://ea.azure.com).
- Si riceverà una fattura digitale mensile per gli addebiti. È possibile visualizzare e analizzare la fattura nella pagina Gestione dei costi e fatturazione di Azure.
- Al posto dei reparti e dell'account nella registrazione Enterprise Agreement, si useranno gli ambiti e la struttura di fatturazione del nuovo account per gestire e organizzare la fatturazione.

Prima di iniziare la configurazione, è consigliabile eseguire le operazioni seguenti:

- **Comprendere lo scopo del nuovo account di fatturazione**
  - Il nuovo account semplifica la fatturazione per l'organizzazione. [Ottenere una rapida panoramica del nuovo account di fatturazione](billing-mca-overview.md)
- **Verificare l'accesso per completare la configurazione**
  - Solo gli utenti con determinate autorizzazioni amministrative possono completare la configurazione. Verificare di disporre dell'[accesso richiesto per completare la configurazione](#access-required-to-complete-the-setup).
- **Comprendere le modifiche apportate alla gerarchia di fatturazione**
  - Il nuovo account di fatturazione è organizzato in modo diverso rispetto alla registrazione Enterprise Agreement. [Comprendere le modifiche apportate alla gerarchia di fatturazione nel nuovo account](#understand-changes-to-your-billing-hierarchy).
- **Comprendere le modifiche apportate all'accesso degli amministratori fatturazione**
  - Gli amministratori della registrazione Enterprise Agreement ottengono l'accesso agli ambiti di fatturazione nel nuovo account.[Comprendere le modifiche apportate all'accesso](#changes-to-billing-administrator-access).
- **Visualizzare le funzionalità del contratto Enterprise Agreement sostituite dal nuovo account**
  - Visualizzare le funzionalità della registrazione Enterprise Agreement sostituite dalle funzionalità del nuovo account.
- **Visualizzare le risposte alle domande più comuni**
  - Per saperne di più sulla configurazione, vedere le [informazioni aggiuntive](#additional-information).

## <a name="access-required-to-complete-the-setup"></a>Accesso richiesto per completare la configurazione

Per completare la configurazione, è necessario l'accesso seguente:

- Proprietario del profilo di fatturazione creato alla firma del Contratto del cliente Microsoft. Per saperne di più sui profili di fatturazione, vedere le [informazioni sui profili di fatturazione](billing-mca-overview.md#billing-profiles).

- Amministratore dell'organizzazione per la registrazione rinnovata.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Se non si è un amministratore dell'organizzazione per la registrazione

È possibile richiedere agli amministratori dell'organizzazione della registrazione di completare la configurazione dell'account di fatturazione.

1. Accedere al portale di Azure usando il collegamento nel messaggio di posta elettronica che è stato inviato alla firma del Contratto del cliente Microsoft.

2. Se non il messaggio di posta elettronica non è disponibile, accedere usando il collegamento seguente. Sostituire `<enrollmentNumber>` con il numero di registrazione del contratto Enterprise rinnovato.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Selezionare gli amministratori dell'organizzazione ai quali inviare la richiesta.

   ![Screenshot che mostra come invitare gli amministratori dell'organizzazione](./media/mca-setup-account/ea-mca-invite-admins.png)

4. Selezionare **Invia richiesta**.

   Gli amministratori riceveranno un messaggio di posta elettronica con le istruzioni per completare la configurazione.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Se non si è proprietari del profilo di fatturazione

L'utente dell'organizzazione che ha firmato il Contratto del cliente Microsoft viene aggiunto come proprietario nel profilo di fatturazione. Richiedere all'utente di essere aggiunti come proprietari per poter completare la configurazione.

## <a name="understand-changes-to-your-billing-hierarchy"></a>Comprendere le modifiche apportate alla gerarchia di fatturazione

Il nuovo account di fatturazione semplifica la fatturazione per l'organizzazione, offrendo al tempo stesso funzionalità avanzate di gestione dei costi e della fatturazione. Il diagramma seguente illustra la modalità di organizzazione della fatturazione nel nuovo account di fatturazione.

![Immagine della gerarchia post transizione contratto Enterprise Agreement-Contratto del cliente Microsoft](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. Si usa l'account di fatturazione per gestire la fatturazione per il Contratto del cliente Microsoft. Gli amministratori dell'organizzazione diventano proprietari dell'account di fatturazione. Per saperne di più sull'account di fatturazione, vedere le [informazioni sull'account di fatturazione](billing-mca-overview.md#your-billing-account).
2. Si usa il profilo di fatturazione per gestire la fatturazione per l'organizzazione, in modo analogo alla registrazione Enterprise Agreement. Gli amministratori dell'organizzazione diventano proprietari del profilo di fatturazione. Per saperne di più sui profili di fatturazione, vedere le [informazioni sui profili di fatturazione](billing-mca-overview.md#billing-profiles).
3. Si usa una sezione della fattura per organizzare i costi in base alle esigenze, in modo analogo ai reparti nella registrazione Enterprise Agreement. Il reparto viene convertito nelle sezioni della fattura e gli amministratori del reparto diventano proprietari delle rispettive sezioni della fattura. Per saperne di più sulle sezioni della fattura, vedere le [informazioni sulle sezioni della fattura](billing-mca-overview.md#invoice-sections).
4. Gli account creati nel contratto Enterprise Agreement non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla rispettiva sezione della fattura per il relativo reparto. I proprietari dell'account possono creare e gestire le sottoscrizioni per le sezioni della fattura.

## <a name="changes-to-billing-administrator-access"></a>Modifiche apportate all'accesso degli amministratori fatturazione

Gli amministratori fatturazione della registrazione Enterprise Agreement ottengono l'accesso agli ambiti di fatturazione nel nuovo account a seconda dell'accesso disponibile. La tabella seguente illustra la modifica apportata all'accesso durante la configurazione:

| Ruolo esistente | Ruolo post-transizione |
| --- | --- |
| **Amministratore dell'organizzazione (sola lettura = no)** | **- Proprietario dell'account di fatturazione** </br> Gestione di tutti gli elementi presenti nell'account di fatturazione </br> **- Proprietario del profilo di fatturazione** </br> Gestione di tutti gli elementi presenti nel profilo di fatturazione </br> **- Proprietario delle sezioni della fattura in tutte le sezioni della fattura** </br> Gestione di tutti gli elementi presenti nelle sezioni della fattura |
| **Amministratore dell'organizzazione (sola lettura = sì)** | **- Lettore per l'account di fatturazione** </br> Fornisce una visualizzazione di sola lettura di tutti gli elementi nell'account di fatturazione</br> **- Lettore per il profilo di fatturazione** </br> Fornisce una visualizzazione di sola lettura di tutti gli elementi nel profilo di fatturazione</br>**- Ruolo con autorizzazione di lettura per la sezione della fattura in tutte le sezioni della fattura**</br> Fornisce una visualizzazione di sola lettura di tutti gli elementi nelle sezioni della fattura|
| **Amministratore del reparto (sola lettura = no)** |**- Proprietario della sezione della fattura nella sezione della fattura creata per il rispettivo reparto** </br>Gestione di tutti gli elementi presenti nella sezione della fattura|
| **Amministratore del reparto (sola lettura = sì)**|**- Ruolo con autorizzazione di lettura per la sezione della fattura nella sezione della fattura creata per il rispettivo reparto**</br> Visualizzazione di sola lettura di tutti gli elementi presenti nella sezione della fattura|
| **Proprietario dell'account** | **- Autore di sottoscrizioni di Azure nella sezione della fattura creata per il rispettivo reparto** </br>  Creare sottoscrizioni di Azure per la sezione della fattura|

Durante l'accettazione del Contratto del cliente Microsoft viene selezionato un tenant di Azure Active Directory (AD) per il nuovo account di fatturazione. Se non esiste alcun tenant per l'organizzazione, ne viene creato uno nuovo. Il tenant rappresenta l'organizzazione all'interno di Azure Active Directory. Gli amministratori del tenant globali dell'organizzazione usano il tenant per gestire l'accesso di applicazioni e dati nell'organizzazione.

Il nuovo account supporta solo gli utenti del tenant selezionato durante la firma del Contratto del cliente Microsoft. Se fanno parte del tenant, gli utenti con autorizzazione di amministrazione per il contratto Enterprise Agreement otterranno l'accesso al nuovo account di fatturazione durante la configurazione. Se non fanno parte del tenant, non potranno accedere al nuovo account di fatturazione a meno che non vengano invitati.

Gli utenti invitati vengono aggiunti al tenant come utenti guest e ottengono l'accesso all'account di fatturazione. Per invitare gli utenti, per il tenant deve essere attivato l'accesso guest. Per altre informazioni, vedere la sezione sul [controllo dell'accesso guest in Azure Active Directory](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Se l'accesso guest è disattivato, contattare gli amministratori globali del tenant per attivarlo. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Visualizzare le funzionalità sostituite

Le funzionalità seguenti del contratto Enterprise Agreement vengono sostituite dalle nuove funzionalità dell'account di fatturazione per un Contratto del cliente Microsoft.

### <a name="enterprise-agreement-accounts"></a>Account del contratto Enterprise Agreement

Gli account creati nella registrazione Enterprise Agreement non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla sezione della fattura creata per il rispettivo reparto. I proprietari dell'account diventano autori di sottoscrizioni di Azure e possono creare e gestire le sottoscrizioni per le sezioni della fattura.

### <a name="notification-contacts"></a>Contatti per le notifiche

Ai contatti per le notifiche vengono inviate comunicazioni sul contratto Enterprise Agreement di Azure tramite posta elettronica. Tali comunicazioni non sono supportate nel nuovo account di fatturazione. I messaggi di posta elettronica relativi ai crediti e alle fatture di Azure vengono inviati agli utenti che hanno accesso ai profili di fatturazione nell'account di fatturazione.

### <a name="spending-quotas"></a>Quote di spesa

Le quote di spesa impostate per i reparti nella registrazione Enterprise Agreement sono sostituite dai budget nel nuovo account di fatturazione. Viene creato un budget per ogni quota di spesa impostata per i reparti nella registrazione. Per altre informazioni sui budget, vedere la sezione sulla [creazione e gestione dei budget di Azure](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centri di costo

I centri di costo impostati per le sottoscrizioni di Azure nella registrazione Enterprise Agreement vengono mantenuti nel nuovo account di fatturazione. I centri di costo per i reparti e gli account del contratto Enterprise Agreement non sono tuttavia supportati.

## <a name="additional-information"></a>Informazioni aggiuntive

Le sezioni seguenti includono ulteriori informazioni sulla configurazione dell'account di fatturazione.

### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

L'esecuzione dei servizi di Azure della sottoscrizione continua senza interruzioni. Viene eseguita la transizione solo della relazione di fatturazione per le sottoscrizioni di Azure. Non è previsto alcun impatto sulle risorse, sui gruppi di risorse o sui gruppi di gestione esistenti.

### <a name="user-access-to-azure-resources"></a>Accesso degli utenti alle risorse di Azure

La transizione non influisce sull'accesso alle risorse di Azure impostate con il controllo degli accessi in base al ruolo di Azure.

### <a name="azure-reservations"></a>Prenotazioni di Azure

Eventuali prenotazioni di Azure presenti nella registrazione Enterprise Agreement verranno spostate al nuovo account di fatturazione. Durante la transizione non sono previste modifiche agli sconti per la prenotazione applicati alle sottoscrizioni.

### <a name="azure-marketplace-products"></a>Prodotti di Azure Marketplace

Tutti i prodotti di Azure Marketplace inclusi nella registrazione Enterprise Agreement vengono spostati insieme alle sottoscrizioni. Durante la transizione non verranno apportate modifiche all'accesso ai servizi dei prodotti di Azure Marketplace.

### <a name="support-plan"></a>Piano di supporto

La transizione non prevede il trasferimento dei vantaggi del supporto. Acquistare un nuovo piano di supporto per accedere ai vantaggi per le sottoscrizioni di Azure nel nuovo account di fatturazione.

### <a name="past-charges-and-balance"></a>Saldo e addebiti precedenti

È possibile visualizzare il saldo di addebiti e crediti precedenti alla transizione nella registrazione Enterprise Agreement tramite il portale di Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Quando deve essere completata la configurazione?

Completare la configurazione dell'account di fatturazione prima della scadenza della registrazione Enterprise Agreement. Se la registrazione scade, i servizi nelle sottoscrizioni di Azure continueranno a funzionare senza alcuna interruzione. Verranno tuttavia addebitate le tariffe con pagamento in base al consumo per i servizi.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Modifiche apportate alla registrazione Enterprise Agreement dopo la configurazione

Le sottoscrizioni di Azure create per la registrazione Enterprise Agreement dopo la transizione possono essere spostate manualmente nel nuovo account di fatturazione. Per altre informazioni, vedere [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure da altri account](billing-mca-request-billing-ownership.md). Per spostare le prenotazioni di Azure acquistate dopo la transizione, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). È anche possibile offrire agli utenti l'accesso all'account di fatturazione dopo la transizione. Per altre informazioni, vedere [Gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Ripristinare la transizione

Non è possibile ripristinare la transizione. Dopo la transizione delle sottoscrizioni di Azure al nuovo account di fatturazione non è possibile tornare alla registrazione Enterprise Agreement.

### <a name="closing-your-browser-during-setup"></a>Chiusura del browser durante la configurazione

Prima di fare clic su **Avvia la transizione**, è possibile chiudere il browser. Per tornare alla configurazione, usare il collegamento ricevuto nel messaggio di posta elettronica e avviare la transizione. Se si chiude il browser, dopo l'avvio della transizione, questa non verrà interrotta. Tornare alla pagina dello stato della transizione per monitorare lo stato più recente della transizione. Al completamento della transizione, si riceverà un messaggio di posta elettronica.

## <a name="complete-the-setup-in-the-azure-portal"></a>Completare la configurazione nel portale di Azure

Per completare la configurazione, è necessario accedere sia al nuovo account di fatturazione che alla registrazione Enterprise Agreement. Per ulteriori informazioni, vedere la sezione sull'[accesso richiesto per completare la configurazione dell'account di fatturazione](#access-required-to-complete-the-setup).

1. Accedere al portale di Azure usando il collegamento nel messaggio di posta elettronica che è stato inviato alla firma del Contratto del cliente Microsoft.

2. Se non il messaggio di posta elettronica non è disponibile, accedere usando il collegamento seguente. Sostituire `<enrollmentNumber>` con il numero di registrazione del contratto Enterprise rinnovato.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Selezionare **Avvia la transizione** nell'ultimo passaggio della configurazione. Dopo aver selezionato Avvia la transizione:

    ![Screenshot che mostra la configurazione guidata](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - Nel nuovo account di fatturazione viene creata una gerarchia di fatturazione corrispondente alla gerarchia del contratto Enterprise Agreement. Per altre informazioni, vedere [Comprendere le modifiche apportate alla gerarchia di fatturazione](#understand-changes-to-your-billing-hierarchy).
    - Agli amministratori della registrazione Enterprise Agreement viene concesso l'accesso al nuovo account di fatturazione, in modo che possano continuare a gestire la fatturazione per l'organizzazione.
    - Viene eseguita la transizione della fatturazione delle sottoscrizioni di Azure al nuovo account. **La transizione non avrà alcun effetto sui servizi di Azure, che continueranno a funzionare senza alcuna interruzione**.
    - Le eventuali prenotazioni di Azure vengono spostate nel nuovo account di fatturazione senza alcuna modifica in termini di vantaggi o periodo di validità. 

4. È possibile monitorare lo stato della transizione nella pagina **Stato della transizione**.

   ![Screenshot che mostra lo stato della transizione](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Convalidare la configurazione dell'account di fatturazione

 Per assicurarsi che il nuovo account di fatturazione sia configurato correttamente, verificare quanto segue:

### <a name="azure-subscriptions"></a>Sottoscrizioni Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/mca-setup-account/search-cmb.png)

3. Selezionare l'account di fatturazione. L'account di fatturazione sarà del tipo **Contratto del cliente Microsoft**.

4. Selezionare **Sottoscrizioni di Azure** sul lato sinistro.

   ![Screenshot che mostra l'elenco delle sottoscrizioni](./media/mca-setup-account/mca-subscriptions-post-transition.png)

Le sottoscrizioni di Azure che sono state spostate dalla registrazione Enterprise Agreement al nuovo account di fatturazione vengono visualizzate nella pagina Sottoscrizioni di Azure. Se si ritiene che manchino delle sottoscrizioni, eseguire manualmente la transizione della fatturazione della sottoscrizione nel portale di Azure. Per altre informazioni, vedere [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure da altri account](billing-mca-request-billing-ownership.md).

### <a name="azure-reservations"></a>Prenotazioni di Azure

Eventuali prenotazioni di Azure presenti nella registrazione del contratto Enterprise verranno spostate al nuovo account di fatturazione senza alcuna modifica in termini di vantaggi o periodo di validità. Le transazioni completate prima della transizione non verranno visualizzate nel nuovo account di fatturazione. Tuttavia, è possibile verificare che i vantaggi delle prenotazioni siano applicati alle sottoscrizioni visitando la [pagina Prenotazioni di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>Accesso degli amministratori dell'organizzazione nell'account di fatturazione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/mca-setup-account/search-cmb.png)

3. Selezionare l'account di fatturazione per il **Contratto del cliente Microsoft**.

4. Selezionare **Controllo di accesso (IAM)** sul lato sinistro.

   ![Screenshot che mostra l'accesso degli amministratori dell'organizzazione post-transizione](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

Gli amministratori dell'organizzazione sono elencati come proprietari dell'account di fatturazione mentre gli amministratori dell'organizzazione con autorizzazioni di sola lettura sono elencati come lettori dell'account di fatturazione. Se si ritiene che manchi l'accesso per un qualsiasi amministratore dell'organizzazione, è possibile concedere loro l'accesso nel portale di Azure. Per altre informazioni, vedere [Gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Accesso degli amministratori dell'organizzazione nel profilo di fatturazione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/mca-setup-account/search-cmb.png)

3. Selezionare il profilo di fatturazione creato per la registrazione. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione. Dall'account di fatturazione selezionare Profili di fatturazione e quindi il profilo di fatturazione.

4. Selezionare **Controllo di accesso (IAM)** sul lato sinistro.

   ![Screenshot che mostra l'accesso degli amministratori dell'organizzazione post-transizione](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

Gli amministratori dell'organizzazione sono elencati come proprietari del profilo di fatturazione mentre gli amministratori dell'organizzazione con autorizzazioni di sola lettura sono elencati come lettori per il profilo di fatturazione. Se si ritiene che manchi l'accesso per un qualsiasi amministratore dell'organizzazione, è possibile concedere loro l'accesso nel portale di Azure. Per altre informazioni, vedere [Gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Accesso di amministratori dell'organizzazione, amministratori del reparto e proprietari dell'account nelle sezioni della fattura

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/mca-setup-account/search-cmb.png).

3. Selezionare una sezione della fattura. Le sezioni della fattura hanno lo stesso nome dei rispettivi reparti nelle registrazioni del contratto Enterprise Agreement. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione. Dall'account di fatturazione selezionare **Profili di fatturazione** e quindi **Sezioni della fattura**. Nell'elenco delle sezioni della fattura selezionare una sezione della fattura.

   ![Screenshot che mostra l'elenco delle sezioni della fattura post-transizione](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. Selezionare **Controllo di accesso (IAM)** sul lato sinistro.

    ![Screenshot che mostra l'accesso degli amministratori di reparto e account post-transizione](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

Gli amministratori dell'organizzazione e gli amministratori del reparto sono elencati come proprietari delle sezioni della fattura o utenti con il ruolo con autorizzazione di lettura per la sezione della fattura, mentre i proprietari dell'account del reparto sono elencati come autori di sottoscrizioni di Azure. Ripetere il passaggio per tutte le sezioni della fattura per verificare l'accesso per tutti i reparti nella registrazione Enterprise Agreement. I proprietari dell'account che non facevano parte di alcun reparto riceveranno l'autorizzazione per una sezione della fattura denominata **Default invoice section** (Sezione della fattura predefinita). Se si ritiene che manchi l'accesso per un qualsiasi amministratore, è possibile concedere loro l'accesso nel portale di Azure. Per altre informazioni, vedere [Gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare il nuovo account di fatturazione](billing-mca-overview.md)

- [Completare le attività del contratto Enterprise Agreement nell'account di fatturazione per un Contratto del cliente Microsoft](billing-mca-enterprise-operations.md)

- [Gestire l'accesso all'account di fatturazione](billing-understand-mca-roles.md)
