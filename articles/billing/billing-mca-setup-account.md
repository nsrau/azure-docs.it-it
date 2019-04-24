---
title: Configurare l'account di fatturazione per un contratto del cliente di Microsoft - Azure | Microsoft Docs
description: Informazioni su come configurare l'account di fatturazione per un contratto di clienti Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 90f832319fa2343003af58bd99eb64c0cbd94dd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371810"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurare l'account di fatturazione per un contratto di Microsoft dal cliente

Se l'iscrizione con contratto Enterprise è scaduto o sta per scadere, è possibile firmare un contratto di Microsoft dei clienti a rinnovare la registrazione. Il rinnovo include i passaggi seguenti:

1. Accettare il nuovo contratto dei clienti Microsoft. Funziona con il rappresentante di campo di Microsoft per comprendere i dettagli e accettare il contratto di nuovo.
2. Configurare il nuovo account di fatturazione che viene creato per il nuovo contratto dei clienti Microsoft.

Questo articolo vengono descritte le modifiche nella fatturazione esistente dopo l'installazione e illustra la configurazione del tuo nuovo account di fatturazione.

Per configurare l'account di fatturazione, è necessario passare la fatturazione delle sottoscrizioni di Azure dall'iscrizione con contratto Enterprise per il nuovo account. Il programma di installazione non inciderà sui servizi di Azure che sono in esecuzione nelle proprie sottoscrizioni. Tuttavia, cambia il modo in cui si intende gestire la fatturazione per le sottoscrizioni.

- Anziché il [portale EA](https://ea.azure.com), si gestiranno i servizi di Azure e la fatturazione, nelle [portale di Azure](https://portal.azure.com).
- Si otterrà una fattura mensile e digitale per i costi. È possibile visualizzare e analizzare la fattura nella pagina fatturazione + Gestione costi di Azure.
- Invece di reparti e account nell'iscrizione con contratto Enterprise, si userà la struttura e gli ambiti dal nuovo account di fatturazione per gestire e organizzare la fatturazione.

Prima di avviare il programma di installazione, è consigliabile che procedere nel modo seguente:

- **Comprendere il nuovo account di fatturazione**
  - Il nuovo account consente di semplificare la fatturazione per l'organizzazione. [Ottenere una rapida panoramica dell'account di fatturazione nuovo](billing-mca-overview.md)
- **Verificare l'accesso per completare l'installazione**
  - Solo gli utenti con determinate autorizzazioni amministrative possono completare l'installazione. Controllare se si hanno le [accesso necessario per completare l'installazione](#access-required-to-complete-the-setup).
- **Comprendere le modifiche alla gerarchia di fatturazione**
  - Nuovi account di fatturazione è organizzata in modo diverso rispetto alla registrazione del contratto Enterprise Agreement. [Comprendere le modifiche alla gerarchia di fatturazione nell'account di nuovo](#understand-changes-to-your-billing-hierarchy).
- **Comprendere le modifiche per l'accesso degli amministratori fatturazione**
  - Gli amministratori dall'iscrizione con contratto Enterprise Ottieni l'accesso per gli ambiti di fatturazione nell'account di nuovo. [Comprendere le modifiche per il loro accesso](#understand-changes-to-your-billing-administrators-access).
- **Funzionalità di Enterprise Agreement View che vengono sostituite dal nuovo account**
  - Visualizzare le funzionalità del contratto Enterprise che vengono sostituite da funzionalità nel nuovo account.
- **Visualizzare le risposte alle domande più comuni**
  - Vista [informazioni aggiuntive](#additional-information) per altre informazioni sull'installazione di.

## <a name="access-required-to-complete-the-setup"></a>Accesso richiesto per completare l'installazione

Per completare l'installazione, è necessario l'accesso seguente:

- Proprietario del profilo di fatturazione è stato creato quando è stato firmato il contratto di Microsoft dal cliente. Per altre informazioni sui profili di fatturazione, vedere [comprendere i profili di fatturazione](billing-mca-overview.md#understand-billing-profiles).

- Amministratore dell'organizzazione della registrazione da rinnovare.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Se non si è amministratore dell'organizzazione della registrazione

È possibile richiedere agli amministratori dell'azienda della registrazione per completare la configurazione dell'account di fatturazione.

1. Accedere al portale di Azure usando il collegamento nel messaggio di posta elettronica che è stato inviato all'utente quando connesso il contratto di Microsoft dal cliente.

2. Se qualcuno nell'organizzazione firmato il contratto o non si ha l'indirizzo di posta elettronica, accedere usando il collegamento seguente. Sostituire **enrollmentNumber** con il numero di registrazione del tuo contratto enterprise che è stato rinnovato.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Selezionare gli amministratori dell'organizzazione che si desidera inviare la richiesta.

   ![Screenshot che mostra l'invito agli amministratori dell'azienda](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Selezionare **richiesta di invio**.

   Gli amministratori riceveranno un messaggio di posta elettronica con istruzioni per completare l'installazione.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Se non si ha un proprietario del profilo di fatturazione

L'utente nell'organizzazione, che ha firmato il contratto del cliente di Microsoft viene aggiunto come proprietario del profilo di fatturazione. Richiedere all'utente di aggiungere l'utente come proprietario in modo che è possibile completare l'installazione.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Informazioni sulle modifiche alla gerarchia di fatturazione

Il nuovo account di fatturazione semplifica la fatturazione per l'organizzazione, oltre che migliorata di fatturazione e le funzionalità di gestione dei costi. Il diagramma seguente illustra come la fatturazione è organizzata nel nuovo account di fatturazione.

![Immagine di ea mca-post-transizione-gerarchia](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Si usa l'account di fatturazione per gestire la fatturazione per il contratto di Microsoft dal cliente. Per altre informazioni sull'account di fatturazione, vedere [comprendere l'account di fatturazione](billing-mca-overview.md#understand-billing-account).
2. Utilizzare il profilo di fatturazione per gestire la fatturazione per l'organizzazione, simile all'iscrizione con contratto Enterprise. Gli amministratori dell'organizzazione diventano proprietari di tale profilo di fatturazione. Per altre informazioni sui profili di fatturazione, vedere [comprendere i profili di fatturazione](billing-mca-overview.md#understand-billing-profiles).
3. Si usa una sezione della fattura per organizzare i costi in base alle esigenze, simile a reparti dell'iscrizione con contratto Enterprise. Reparto diventa sezioni della fattura e gli amministratori di reparto diventano i proprietari delle sezioni della fattura corrispondente. Per altre informazioni sulle sezioni della fattura, vedere [informazioni sulle sezioni della fattura](billing-mca-overview.md#understand-invoice-sections).
4. Gli account che sono stati creati nel contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla sezione della fattura corrispondente per il dipartimento. I proprietari di account possono creare e gestire sottoscrizioni per le sezioni della fattura.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Comprendere le modifiche per l'accesso degli amministratori fatturazione

A seconda della loro l'accesso, gli amministratori fatturazione al momento dell'iscrizione di contratto Enterprise hanno accesso agli ambiti di fatturazione per il nuovo account. La tabella seguente illustra la modifica nell'accesso durante l'installazione:

| Ruolo esistente | Ruolo di transizione post |
| --- | --- |
| **Amministratore dell'organizzazione (lettura solo = No)** | **-Proprietario del profilo di fatturazione** </br> Gestire tutti gli elementi nel profilo di fatturazione </br> - **Proprietario di sezione della fattura in tutte le sezioni della fattura** </br> Gestire tutto sulle sezioni della fattura |
| **Amministratore dell'organizzazione (lettura solo = Sì)** | **-Lettore profilo di fatturazione** </br> Visualizzazione di sola lettura di tutti gli elementi in account di fatturazione</br>**-Lettore di sezione fattura nella sezione della fattura tutte le**</br> Visualizzazione di sola lettura di tutti gli elementi nella sezione della fattura|
| **Amministratore del reparto (lettura solo = No)** |**-Proprietario di sezione fattura nella sezione della fattura creata per il dipartimento corrispondente** </br>Gestire tutti gli elementi nella sezione della fattura|
| **Amministratore del reparto (lettura solo = Sì)**|**-Lettore di sezione fattura nella sezione della fattura creata per il dipartimento corrispondente**</br> Visualizzazione di sola lettura di tutti gli elementi nella sezione della fattura|
| **Proprietario dell'account** | **-Creazione sottoscrizione azure nella sezione della fattura creata per il dipartimento corrispondente** </br>  Creare sottoscrizioni di Azure per la sezione della fattura|

Un tenant di Azure Active Directory è selezionato per il nuovo account di fatturazione durante l'accesso il contratto di Microsoft dal cliente. Se un tenant non esiste per l'organizzazione, viene creato un nuovo tenant. Il tenant dell'organizzazione all'interno di Azure Active Directory: Gli amministratori di tenant globale all'interno dell'organizzazione di usare il tenant per gestire l'accesso delle applicazioni e dei dati all'interno dell'organizzazione.

Il nuovo account supporta solo gli utenti dal tenant a cui è stata selezionata durante la firma il contratto di Microsoft dal cliente. Se gli utenti con autorizzazioni di amministratore sul tuo contratto Enterprise Agreement fanno parte del tenant, si avrà accesso al nuovo account di fatturazione durante l'installazione. Se non sono parte del tenant, non sarà in grado di accedere al nuovo account di fatturazione a meno che non inviare un invito.

Quando si invitano gli utenti, essi vengono aggiunti come utenti guest nel tenant e ottenere l'accesso all'account di fatturazione. Per invitare gli utenti, l'accesso guest deve essere attivata per il tenant. Per altre informazioni, vedere [controllare l'accesso guest in Azure Active Directory](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Se l'accesso guest è disattivato, contattare gli amministratori globali del tenant per attivarla. <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>Visualizzare le funzionalità sostituite dal nuovo account di fatturazione

Le funzionalità seguenti del contratto Enterprise vengono sostituite con nuove funzionalità nell'account di fatturazione per un contratto di clienti Microsoft.

### <a name="enterprise-agreement-accounts"></a>Account contratto Enterprise Agreement

Gli account creati durante l'iscrizione con contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla sezione della fattura creata per il dipartimento corrispondente. I proprietari di account diventano gli autori di sottoscrizione di Azure e possono creare e gestire sottoscrizioni per le sezioni della fattura.

### <a name="notification-contacts"></a>Contatti di notifica

I contatti di notifica vengono inviati tramite posta elettronica comunicazioni sul contratto Enterprise di Azure. Non sono supportati nel nuovo account di fatturazione. Messaggi di posta elettronica sui crediti Azure e le fatture vengono inviate agli utenti autorizzati ad accedere ai profili di fatturazione nell'account di fatturazione.

### <a name="spending-quotas"></a>Le quote di spesa

Le quote di spese che sono state impostate per i reparti nell'iscrizione con contratto Enterprise vengono sostituite con budget nel nuovo account di fatturazione. Per ogni quota di spesa impostato per i reparti nell'iscrizione viene creato un budget definito. Per altre informazioni sui budget, vedere [creare e gestire Azure budget](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centri di costo

Centro di costo di quelle impostate nelle sottoscrizioni di Azure nella propria registrazione contratto Enterprise vengono trasferiti nel nuovo account di fatturazione. Tuttavia, non sono supportati i centri di costo per reparti e account contratto Enterprise Agreement.

## <a name="additional-information"></a>Informazioni aggiuntive

Le sezioni seguenti forniscono informazioni aggiuntive sulla configurazione di account di fatturazione.

### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

L'esecuzione dei servizi di Azure della sottoscrizione continua senza interruzioni. Viene eseguita la transizione solo della relazione di fatturazione per le sottoscrizioni di Azure. Non è previsto alcun impatto sulle risorse, sui gruppi di risorse o sui gruppi di gestione esistenti.

### <a name="user-access-to-azure-resources"></a>Accesso utente alle risorse di Azure

Accesso alle risorse di Azure che è stato impostato l'uso di Azure RBAC (controllo degli accessi basato su ruolo) non subiscono modifiche durante la transizione.

### <a name="azure-reservations"></a>Prenotazioni di Azure

Le prenotazioni di Azure nella propria registrazione contratto Enterprise viene spostata il nuovo account di fatturazione. Durante la transizione non sono previste modifiche agli sconti per la prenotazione applicati alle sottoscrizioni.

### <a name="azure-marketplace-products"></a>Prodotti di Azure Marketplace

I prodotti di Azure Marketplace nell'iscrizione di contratto Enterprise vengono spostati insieme le sottoscrizioni. Non saranno tutte le modifiche per l'accesso al servizio dei prodotti Marketplace durante la transizione.

### <a name="support-plan"></a>Piano di supporto

Non è possibile trasferire i vantaggi del supporto nell'ambito della transizione. Acquistare un nuovo piano di supporto di usufruire dei vantaggi per le sottoscrizioni di Azure nel tuo nuovo account di fatturazione.

### <a name="past-charges-and-balance"></a>Oltre i costi e saldo

Bilanciare i costi e i crediti prima della transizione può essere visualizzati nella propria registrazione Enterprise Agreement tramite il portale di Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Quando deve essere completata l'installazione?

Completare la configurazione dell'account di fatturazione prima che scada l'iscrizione con contratto Enterprise. Se scade la registrazione, services nelle sottoscrizioni di Azure verrà comunque mantenere via in esecuzione senza interruzioni. Si verranno comunque addebitati tariffe al dettaglio per i servizi.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Modifiche per l'iscrizione con contratto Enterprise dopo l'installazione

Sottoscrizioni di Azure dopo la transizione può essere spostata manualmente al nuovo account di fatturazione vengono create per l'iscrizione con contratto Enterprise. Per altre informazioni, vedere [ottenere la proprietà delle sottoscrizioni di Azure ad altri utenti di fatturazione](billing-mca-request-billing-ownership.md). Per spostare le prenotazioni di Azure che vengono acquistati dopo la transizione [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). È anche possibile fornire agli utenti l'accesso all'account di fatturazione dopo la transizione. Per altre informazioni, vedere [gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Ripristinare la transizione

La transizione non può essere annullata. Dopo che la fatturazione delle sottoscrizioni di Azure viene eseguita la transizione al nuovo account di fatturazione, è possibile ripristinare lo nuovamente per l'iscrizione con contratto Enterprise.

### <a name="closing-your-browser-during-setup"></a>Chiudere il browser durante l'installazione

Prima di fare clic su **avviare transizione**, è possibile chiudere il browser. È possibile tornare al programma di installazione usando il collegamento che è stato ottenuto nel messaggio di posta elettronica e avviare la transizione. Se si chiude il browser, dopo la transizione viene avviata, il passaggio in esecuzione continuerà. Tornare alla pagina dello stato di transizione per monitorare lo stato più recente della tua transizione. Si otterrà un messaggio di posta elettronica quando viene completata la transizione.

## <a name="complete-the-setup-in-the-azure-portal"></a>Completare la configurazione nel portale di Azure

Per completare l'installazione, è necessario accedere al nuovo account di fatturazione sia l'iscrizione con contratto Enterprise. Per altre informazioni, vedere [accesso necessario per completare il set di backup dell'account di fatturazione](#access-required-to-complete-the-setup).

1. Accedere al portale di Azure usando il collegamento nel messaggio di posta elettronica che è stato inviato all'utente quando connesso il contratto di Microsoft dal cliente.

2. Se qualcuno nell'organizzazione firmato il contratto o non si ha l'indirizzo di posta elettronica, accedere usando il collegamento seguente. Sostituire **enrollmentNumber** con il numero di registrazione del tuo contratto Enterprise che è stato rinnovato.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Selezionare **avviare transizione** nell'ultimo passaggio dell'installazione. Dopo aver selezionato la transizione di inizio:

    ![Screenshot che illustra la procedura guidata](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Viene creata una gerarchia di fatturazione corrisponde alla gerarchia di contratto Enterprise Agreement nel nuovo account di fatturazione. Per altre informazioni, vedere [comprendere le modifiche alla gerarchia di fatturazione](#understand-changes-to-your-billing-hierarchy).
    - Gli amministratori dall'iscrizione con contratto Enterprise vengono concesso l'accesso al nuovo account di fatturazione in modo da continuare a gestire la fatturazione per l'organizzazione.
    - La fatturazione delle sottoscrizioni di Azure viene eseguita la transizione al nuovo account. **Vi sarà alcun impatto sui servizi di Azure durante tale transizione. Verranno eseguiti senza alcuna interruzione**.
    - Se si dispone di prenotazioni di Azure, queste vengono spostate il nuovo account di fatturazione con la stessa dello sconto e termini. Lo sconto continuerà a essere applicato durante la transizione.

4. È possibile monitorare lo stato della transizione sul **lo stato di transizione** pagina.

   ![Screenshot che mostra lo stato di transizione](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>Convalidare la fatturazione account sia configurato correttamente

 Convalidare gli elementi seguenti per assicurarsi che il nuovo account di fatturazione sia configurato correttamente:

### <a name="azure-subscriptions"></a>Sottoscrizioni Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selezionare il profilo di fatturazione. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione. L'account di fatturazione, selezionare **fatturazione profili** e quindi il profilo di fatturazione.

4. Selezionare **le sottoscrizioni di Azure** dal lato sinistro.

   ![Screenshot che mostra l'elenco delle sottoscrizioni](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Le sottoscrizioni di Azure che vengono eseguita la transizione dall'iscrizione con contratto Enterprise per il nuovo account di fatturazione vengono visualizzate nella pagina sottoscrizioni di Azure. Se si ritiene che tutte le sottoscrizioni non sono presente, eseguire la transizione di fatturazione della sottoscrizione manualmente nel portale di Azure. Per altre informazioni, vedere [ottenere la proprietà delle sottoscrizioni di Azure ad altri utenti di fatturazione](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Prenotazioni di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selezionare una sezione della fattura. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione.  L'account di fatturazione o il profilo di fatturazione, selezionare **sezioni della fattura** e quindi una sezione della fattura.

    ![Screenshot che mostra l'elenco di transizione di post sezione della fattura](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Selezionare **tutti i prodotti** dal lato sinistro.

5. Cercare **riservato**.

    ![Screenshot che mostra l'elenco delle sottoscrizioni post transizione](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Le prenotazioni di Azure che vengono spostate dall'iscrizione con contratto Enterprise per il nuovo account di fatturazione vengono visualizzate nella pagina di tutti i prodotti. Ripetere i passaggi per tutte le sezioni della fattura verificare che tutte le prenotazioni di Azure sono state spostate dall'iscrizione con contratto Enterprise. Se si ritiene che tutte le prenotazioni Azure non è presente, [contattare il supporto tecnico Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per spostare la prenotazione per il nuovo account di fatturazione.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Accesso degli amministratori dell'organizzazione nel profilo di fatturazione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selezionare il profilo di fatturazione creato per l'iscrizione. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione.  L'account di fatturazione, selezionare **fatturazione profili** e quindi il profilo di fatturazione.

4. Selezionare **controllo di accesso (IAM)** dal lato sinistro.

   ![Screenshot che mostra l'accesso di enterprise administrators post transizione](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Gli amministratori dell'organizzazione sono elencati come proprietari profilo durante l'azienda sono elencati gli amministratori con autorizzazioni di sola lettura di fatturazione come i lettori di profilo di fatturazione. Se si ritiene che l'accesso per tutti gli amministratori dell'organizzazione è manca, è possibile assegnare loro l'accesso nel portale di Azure. Per altre informazioni, vedere [gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Accesso degli amministratori dell'organizzazione, gli amministratori di reparto e i proprietari di account sulle sezioni della fattura

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Selezionare una sezione della fattura. Le sezioni della fattura hanno lo stesso nome rispettivi reparti nelle registrazioni Enterprise Agreement. In base all'accesso, si potrebbe essere necessario selezionare un profilo di fatturazione o un account di fatturazione. Il profilo di fatturazione o l'account di fatturazione, selezionare **sezioni della fattura** e quindi selezionare una sezione della fattura.

   ![Screenshot che mostra l'elenco di transizione di post sezione della fattura](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Selezionare **controllo di accesso (IAM)** dal lato sinistro.

    ![Screenshot che mostra l'accesso del reparto e account di amministratori di accedere ai post di transizione](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Gli amministratori dell'organizzazione e gli amministratori di reparto sono elencati come proprietari sezione della fattura o i lettori di sezione della fattura mentre i proprietari di account del reparto sono elencati come autori di sottoscrizione di Azure. Ripetere il passaggio per tutte le sezioni della fattura controllare l'accesso per tutti i reparti nell'iscrizione con contratto Enterprise. I proprietari di account che non sono stati parte di qualsiasi reparto otterrà l'autorizzazione in una sezione della fattura denominata **sezione della fattura predefinita**. Se si ritiene che l'accesso per gli amministratori non è presente, è possibile assegnare loro l'accesso nel portale di Azure. Per altre informazioni, vedere [gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare con il nuovo account di fatturazione](billing-mca-overview.md)

- [Completare le attività di contratto Enterprise Agreement nell'account di fatturazione per un contratto di Microsoft dal cliente](billing-mca-enterprise-operations.md)

- [Gestire l'accesso all'account di fatturazione](billing-understand-mca-roles.md)
