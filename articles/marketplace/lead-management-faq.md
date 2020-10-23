---
title: Domande e risoluzione dei problemi relativi alla gestione dei lead-centro per i partner Microsoft
description: Per informazioni sugli errori e le domande comuni, vedere la pagina relativa alla configurazione di lead per Marketplace commerciali nel centro
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330921"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Domande frequenti e risoluzione dei problemi per la configurazione del lead

Questo articolo risponde ad alcune domande frequenti sulla gestione dei lead per le offerte del Marketplace commerciale. Consente inoltre di risolvere gli errori che possono verificarsi durante la configurazione di lead per il sistema CRM (Customer Relationship Management) nel centro per i partner.

## <a name="common-questions-about-lead-management"></a>Domande frequenti sulla gestione dei lead

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Dove è possibile ottenere assistenza per configurare la destinazione dei lead?

Per una panoramica su come connettere il sistema CRM alle offerte del Marketplace commerciale, vedere [clienti lead dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md) . Se si verifica un errore, consultare le indicazioni per la risoluzione dei problemi riportati di seguito. Per ulteriore supporto, inviare un ticket di supporto tramite la [Guida e il supporto](https://aka.ms/marketplacepublishersupport)tecnico del centro per i partner. Selezionare quindi **Offer creation (Creazione offerta)**  > **Your type of offer (Tipo di offerta)**  > **Lead management configuration (Configurazione di gestione dei lead)** .

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>È necessario configurare una destinazione dei lead per pubblicare un'offerta nel marketplace commerciale?

La risposta dipende dal tipo di offerta che si sta pubblicando. Software as a service (SaaS) e Dynamics 365 Customer Engagement usano il pulsante **Contatta** per elencare tutte le offerte di Dynamics 365 per Finanza e operazioni, tutte le offerte di Dynamics 365 Business Central e tutte le offerte dei Servizi di consulenza. Di conseguenza, richiedono una connessione a una destinazione lead. Se il tipo di offerta non è presente nell'elenco, non è necessaria una connessione a una destinazione lead. Si consiglia di configurare una destinazione lead in modo da non perdere le opportunità aziendali.

#### <a name="how-can-i-find-the-test-lead"></a>Come si trova il lead di test?

Cercare `"MSFT_TEST"` nella destinazione del lead. Di seguito è riportato un esempio di lead di test di Microsoft. Si noti che il formato del lead di test varia a seconda della destinazione principale.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Perché per un'offerta attiva non è visualizzato alcun lead?

Verificare che la connessione alla destinazione del lead sia valida. Microsoft invierà un lead di test dopo aver selezionato **Pubblica** sull'offerta nel Centro per i partner. Se viene visualizzato il lead di test, la connessione è valida. È anche possibile testare la connessione del lead provando ad acquisire l'anteprima dell'offerta durante il passaggio dell'anteprima. Selezionare **Scarica adesso**, **Contatta** o **Versione di valutazione gratuita** nell'inserzione nel marketplace commerciale.

Assicurarsi inoltre di cercare i dati corretti. Vedere informazioni sui [Lead](partner-center-portal/commercial-marketplace-get-customer-leads.md) per una spiegazione dei dati principali inviati alla destinazione principale.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>È stata configurata l'archiviazione BLOB di Azure come destinazione dei lead. Perché il lead non è visualizzato?

L'archiviazione BLOB di Azure non è più supportata come destinazione dei lead, quindi mancano i lead dei clienti generati dall'offerta. Passare a qualsiasi altra [opzione di destinazione dei lead](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>È stato ricevuto un messaggio di posta elettronica dal marketplace commerciale, perché non si riesce a trovare il lead nel sistema CRM?

È possibile che il dominio di posta elettronica dell'utente finale sia di tipo edu. Per motivi di privacy, le informazioni personali non vengono passate da questo tipo di domini. Inviare un ticket di supporto tramite la [Guida e il supporto tecnico del Centro per i partner](https://aka.ms/marketplacepublishersupport).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>È stata configurata una tabella di Azure come destinazione dei lead. Come è possibile visualizzare i lead?

È possibile accedere ai dati dei lead archiviati nella tabella di Azure dal portale di Azure. È possibile inoltre scaricare e installare gratuitamente [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per visualizzare i dati delle tabelle del proprio account di archiviazione di Azure.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>È stata configurata una tabella di Azure come destinazione dei lead. Posso ricevere una notifica ogni volta che viene inviato un nuovo lead del marketplace commerciale?

Sì. Seguire le istruzioni in [Usare le tabelle di Azure per configurare la gestione dei lead](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) per configurare un flusso Microsoft che invia un messaggio di posta elettronica se viene aggiunto un lead alla tabella di Azure.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>È stato configurato Salesforce come destinazione dei lead. Perché i lead non sono visualizzati?

Verificare se il modulo Web-to-Lead (Da Web a lead) è un campo obbligatorio in base a un elenco a discesa. Se lo è, impostarlo su un campo di testo non obbligatorio.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>C'è stato un problema con la destinazione dei lead e non si sono visti alcuni lead. È possibile riceverli tramite posta elettronica?

A causa dei criteri relativi ai dati personali, non è possibile condividere informazioni sui lead tramite posta elettronica non protetta.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>È stata configurata una tabella di Azure come destinazione dei lead. Quale sarà il costo?

I dati sulla generazione dei lead sono scarsi. Sono inferiori a 1 GB per quasi tutti gli autori. Il costo dipende dal numero di lead ricevuti. Se, ad esempio, vengono ricevuti 1.000 lead in un mese, il costo è di circa 50 centesimi. Per altre informazioni sui prezzi dell'archiviazione, vedere [Panoramica dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Se non è stata fornita una risposta alla domanda, contattare il supporto tecnico Microsoft tramite la [Guida e il supporto tecnico del Centro per i partner](https://aka.ms/marketplacepublishersupport). Selezionare quindi **Offer creation (Creazione offerta)**  > **Your type of offer (Tipo di offerta)**  > **Lead management configuration (Configurazione di gestione dei lead)** .

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Ricevo notifiche tramite posta elettronica quando vengono ricevuti nuovi lead. Come è possibile configurare un altro utente per la ricezione di questi messaggi di posta elettronica?

Accedere alla propria offerta nel Centro per i partner e passare alla pagina **Offer setup (Configurazione dell'offerta)** > **Lead Management (Gestione dei lead)**  > **Modifica**. Aggiornare gli indirizzi di posta elettronica nel campo **Indirizzo di posta elettronica di contatto**.

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Risoluzione degli errori di configurazione del lead

**Non è stato possibile salvare il lead in Dynamics CRM. Controllare le impostazioni dell'account Dynamics CRM. LastCRMError: Non è possibile accedere a Dynamics CRM, LastCRMException:** 

> Se è stata selezionata l'opzione Microsoft 365 autenticazione, controllare se l'account utente e la password sono validi. Se è stata selezionata l'opzione Azure Active Directory, controllare se l'ID tenant, l'ID applicazione e la chiave privata dell'applicazione corrispondono a quanto è stato configurato nel Azure Active Directory. Seguire le istruzioni [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se il nome utente e la password dell'account sono validi, verificare che l'account abbia accesso a Dynamics 365 e disponga di una licenza (passaggi 11-15 se si usa Azure Active Directory o Impostazioni di sicurezza se si usa un utente di Office). 

**Non è stato possibile salvare il lead in Dynamics CRM. L'utente non ha le autorizzazioni di creazione per l'attributo leadsourcecode nell'entità lead** 

> L'applicazione o l'utente non ha i ruoli di sicurezza su Microsoft Marketplace Lead Writer. Seguire i passaggi 11-15 se si usa Azure Active Directory o le impostazioni di sicurezza se si usa [un utente di](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)Office.

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: tenant non trovato. Questa situazione può verificarsi se non sono presenti sottoscrizioni attive per il tenant.**  

> L'ID directory specificato nella sezione relativa alla gestione dei lead non corrisponde a una directory valida. Ottenere l'ID di directory in base alle istruzioni nel passaggio 2 (in Azure Active Directory) [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Non è stato possibile salvare il lead in Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser non riuscito - nessun ruolo assegnato all'utente.**  

> Risoluzione: assegnare il ruolo di sicurezza a Microsoft Marketplace Lead Writer. Seguire le istruzioni [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) in Impostazioni di sicurezza.

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: applicazione con identificatore non trovata nella directory** 

> L'ID applicazione specificato nella sezione relativa alla gestione dei lead non corrisponde a un'applicazione valida. Ottenere l'ID applicazione seguendo le istruzioni del passaggio 8 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: identificatore tenant richiesto non valido e formato di dominio esterno non valido** 

> L'ID directory specificato nella sezione relativa alla gestione dei lead non corrisponde a una directory valida. Ottenere l'ID directory seguendo le istruzioni del passaggio 2 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Non è stato possibile salvare il lead in Dynamics CRM tramite AAD. Eccezione: errore di convalida delle credenziali. Viene fornito un segreto client non valido.** 

> Risoluzione: accedere al portale di Azure e verificare se la chiave dell'applicazione corrisponde a quella indicata nel Centro per i partner. Generare la password seguendo le istruzioni del passaggio 10 (in Azure Active Directory, [qui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Non è stato possibile salvare il lead in Dynamics CRM. LastCRMError: si è verificato il timeout del canale di richiesta durante l'attesa di una risposta dopo 00:02:00. Aumentare il valore di timeout passato alla chiamata a Request o aumentare il valore di SendTimeout nell'elemento Binding. È possibile che il tempo allocato a questa operazione fosse incluso in un timeout più lungo.**  

> Soluzione: accedere al centro per i partner, selezionare l'opzione per l'installazione >> clienti lead >> URL, controllare se si tratta di un'istanza dinamica di CRM valida.

