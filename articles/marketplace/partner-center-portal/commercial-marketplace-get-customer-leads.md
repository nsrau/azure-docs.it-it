---
title: Gestione dei lead nel marketplace commerciale Microsoft
description: Informazioni sulla generazione e la ricezione di lead dei clienti provenienti dalle offerte Microsoft AppSource e Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 9ee433f226b37c8ffd6ad466cca7cbd844d53524
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535978"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Lead di clienti tramite l'offerta del marketplace commerciale

I lead sono i clienti interessati a distribuire le offerte da [Microsoft AppSource](https://appsource.microsoft.com) e [Azure Marketplace](https://azuremarketplace.microsoft.com). È possibile ricevere i lead dei clienti dopo la pubblicazione dell'offerta nel marketplace commerciale. In questo articolo vengono descritti i concetti di gestione dei lead seguenti:

* il modo in cui l'offerta del marketplace commerciale genera lead dei clienti per assicurarsi di non perdere opportunità aziendali; 
* come connettere il sistema CRM (Customer Relationship Management) all'offerta per poter gestire i lead in un'unica posizione centrale;
* i dati dei lead inviati da Microsoft per poter seguire i clienti da cui si è stati contattati.

## <a name="generate-customer-leads"></a>Generare lead clienti

Di seguito sono riportati i punti in cui viene generato un lead.

- Un cliente acconsente a condividere le sue informazioni dopo aver selezionato **Contatta** dal marketplace commerciale. Questo lead è un lead di *interesse iniziale*. Le informazioni relative al cliente che ha espresso interesse nel ricevere il prodotto vengono condivise con l'utente. Il lead si trova al vertice del grafico a imbuto relativo all'acquisizione dei clienti.

    ![Dynamics 365, Contatta](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Un cliente seleziona **Scarica adesso** (oppure seleziona **Crea** nel [portale di Azure](https://portal.azure.com/)) per ricevere l'offerta. Si tratta di un lead *attivo*. Le informazioni relative al cliente che ha iniziato a distribuire il prodotto vengono condivise con l'utente.

    ![SQL Pulsante Scarica adesso](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Pulsante Crea di Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Un cliente seleziona **test drive** o **versione di valutazione gratuita** per provare l'offerta. I test drive o le versioni di valutazione gratuita sono opportunità accelerate per condividere immediatamente la propria attività con potenziali clienti senza limiti di ingresso.

    ![Pulsante Test drive di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Pulsante Versione di valutazione gratuita di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connettersi al sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informazioni sui dati dei lead

Ogni lead ricevuto durante il processo di acquisizione del cliente presenta dati in campi specifici. Il primo campo da cercare è il campo `LeadSource`, che segue il formato: **Origine-Azione** | **Offerta**.

**Origini**: Il valore di questo campo viene popolato in base al marketplace che ha generato il lead. I valori possibili sono `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Azioni**: Il valore di questo campo viene popolato in base all'azione svolta dal cliente nel marketplace che ha generato il lead.

I valori possibili sono:

- **"INS"** : è l'acronimo per *installazione*. Questa azione si trova in Azure Marketplace o AppSource quando un cliente acquisisce il prodotto.
- **"PLT"** : significa *versione di valutazione condotta dal partner*. Questa azione si trova in AppSource quando un cliente seleziona l'opzione **Contatta**.
- **"DNC"** : sta per *non contattare*. L'azione si trova in AppSource quando un partner che è stato elencato in modo incrociato nella pagina app riceve una richiesta per essere contattato. Viene condiviso l'annuncio che il cliente è stato elencato nell'app, ma non deve essere contattato.
- **"Crea"** : questa azione si trova solo nel portale di Azure e si genera quando un cliente acquista l'offerta dal proprio account.
- **"StartTestDrive"** : questa azione riguarda solo l'opzione **Test drive** e si genera quando un cliente avvia il test drive.

**Offerte**: nel marketplace commerciale potrebbero esistere più offerte. Il valore di questo campo viene popolato in base all'offerta che ha generato il lead. L'ID editore e l'ID offerta vengono entrambi inviati in questo campo e sono valori specificati al momento della pubblicazione dell'offerta nel marketplace.

Negli esempi seguenti vengono illustrati i valori nel formato previsto `publisherid.offerid`: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informazioni sul cliente

Le informazioni sul cliente vengono inviate tramite più campi. L'esempio seguente mostra le informazioni sul cliente contenute in un lead.

- FirstName: John
- LastName: Smith
- E-mail: jsmith\@microsoft.com
- Telefono: 1234567890
- Paese: Stati Uniti
- Società: Microsoft
- Titolo: CTO

>[!NOTE]
>Non tutti i dati dell'esempio precedente sono sempre disponibili per ogni lead. Poiché si otterranno lead dai vari passaggi, come specificato nella sezione "Generare lead clienti", il modo migliore per gestire i lead è deduplicare i record e personalizzare i follow-up. In questo modo, ogni cliente riceve un messaggio appropriato e si crea una relazione univoca.

## <a name="best-practices-for-lead-management"></a>Procedure consigliate per la gestione dei lead

Di seguito sono riportati alcuni suggerimenti per guidare i lead nel ciclo di vendita:

- **Processo**: definire un processo di vendita chiaro, con attività cardine, analisi e una chiara proprietà del team.
- **Qualificazione**: definire i prerequisiti, che indicano se un lead era completo. Assicurarsi che i rappresentanti di vendita o marketing qualifichino i lead con attenzione prima di prenderli in considerazione per il processo di vendita completo.
- **Completamento**: non dimenticare di seguirli entro 24 ore. Si otterrà il lead nel CRM preferito immediatamente dopo la distribuzione di un test drive da parte del cliente; inviare un messaggio di posta elettronica all'interno di mentre sono ancora a caldo. Richiede la pianificazione di una telefonata per comprendere meglio se il prodotto è una soluzione ottimale per il problema. Si prevede che la transazione tipica richieda numerose chiamate di completamento.
- **Seguire**: consolidare i lead in modo da raggiungere un margine di profitto superiore. Archiviarli senza bombardarli. Si consiglia di inviare messaggi di posta elettronica almeno alcune volte prima di chiuderli; non abbandonarsi dopo il primo tentativo. Tenere presente che questi clienti hanno partecipato direttamente al prodotto e hanno dedicato tempo a una versione di valutazione gratuita; sono ottime prospettive.

## <a name="common-questions-about-lead-management"></a>Domande frequenti sulla gestione dei lead

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Dove è possibile ottenere assistenza per configurare la destinazione dei lead?

Attenersi alla procedura illustrata nella sezione [Connettersi al sistema CRM](#connect-to-your-crm-system) o inviare un ticket di supporto tramite la [Guida e il supporto tecnico del Centro per i partner](https://aka.ms/marketplacepublishersupport). Selezionare quindi **Offer creation (Creazione offerta)**  > **Your type of offer (Tipo di offerta)**  > **Lead management configuration (Configurazione di gestione dei lead)** .

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>È necessario configurare una destinazione dei lead per pubblicare un'offerta nel marketplace commerciale?

La risposta dipende dal tipo di offerta che si sta pubblicando. Software as a service (SaaS) e Dynamics 365 Customer Engagement usano il pulsante **Contatta** per elencare tutte le offerte di Dynamics 365 per Finanza e operazioni, tutte le offerte di Dynamics 365 Business Central e tutte le offerte dei Servizi di consulenza. Di conseguenza, richiedono una connessione a una destinazione lead. Se il tipo di offerta non è presente nell'elenco, non è necessaria una connessione a una destinazione lead. Si consiglia di configurare una destinazione lead in modo da non perdere le opportunità aziendali.

### <a name="how-can-i-find-the-test-lead"></a>Come si trova il lead di test?

Cercare `"MSFT_TEST"` nella destinazione del lead. Di seguito è riportato un esempio di lead di test di Microsoft. Si noti che il formato del lead di test varia a seconda della destinazione principale.

```
{
    "UserDetails": {
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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Perché per un'offerta attiva non è visualizzato alcun lead?

Verificare che la connessione alla destinazione del lead sia valida. Microsoft invierà un lead di test dopo aver selezionato **Pubblica** sull'offerta nel Centro per i partner. Se viene visualizzato il lead di test, la connessione è valida. È anche possibile testare la connessione del lead provando ad acquisire l'anteprima dell'offerta durante il passaggio dell'anteprima. Selezionare **Scarica adesso**, **Contatta** o **Versione di valutazione gratuita** nell'inserzione nel marketplace commerciale.

Assicurarsi inoltre di cercare i dati corretti. Il contenuto della sezione [Informazioni sui dati dei lead](#understand-lead-data) di questo articolo descrive i dati inviati alla destinazione dei lead.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>È stata configurata l'archiviazione BLOB di Azure come destinazione dei lead. Perché il lead non è visualizzato?

L'archiviazione BLOB di Azure non è più supportata come destinazione dei lead, quindi mancano i lead dei clienti generati dall'offerta. Passare a qualsiasi altra [opzione di destinazione dei lead](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>È stato ricevuto un messaggio di posta elettronica dal marketplace commerciale, perché non si riesce a trovare il lead nel sistema CRM?

È possibile che il dominio di posta elettronica dell'utente finale sia di tipo edu. Per motivi di privacy, le informazioni personali non vengono passate da questo tipo di domini. Inviare un ticket di supporto tramite la [Guida e il supporto tecnico del Centro per i partner](https://aka.ms/marketplacepublishersupport).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>È stata configurata una tabella di Azure come destinazione dei lead. Come è possibile visualizzare i lead?

È possibile accedere ai dati dei lead archiviati nella tabella di Azure dal portale di Azure. È possibile inoltre scaricare e installare gratuitamente [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per visualizzare i dati delle tabelle del proprio account di archiviazione di Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>È stata configurata una tabella di Azure come destinazione dei lead. Posso ricevere una notifica ogni volta che viene inviato un nuovo lead del marketplace commerciale?

Sì. Seguire le istruzioni in [Usare le tabelle di Azure per configurare la gestione dei lead](./commercial-marketplace-lead-management-instructions-azure-table.md) per configurare un flusso Microsoft che invia un messaggio di posta elettronica se viene aggiunto un lead alla tabella di Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>È stato configurato Salesforce come destinazione dei lead. Perché i lead non sono visualizzati?

Verificare se il modulo Web-to-Lead (Da Web a lead) è un campo obbligatorio in base a un elenco a discesa. Se lo è, impostarlo su un campo di testo non obbligatorio.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>C'è stato un problema con la destinazione dei lead e non si sono visti alcuni lead. È possibile riceverli tramite posta elettronica?

A causa dei criteri relativi ai dati personali, non è possibile condividere informazioni sui lead tramite posta elettronica non protetta.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>È stata configurata una tabella di Azure come destinazione dei lead. Quale sarà il costo?

I dati sulla generazione dei lead sono scarsi. Sono inferiori a 1 GB per quasi tutti gli autori. Il costo dipende dal numero di lead ricevuti. Se, ad esempio, vengono ricevuti 1.000 lead in un mese, il costo è di circa 50 centesimi. Per altre informazioni sui prezzi dell'archiviazione, vedere [Panoramica dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Se non è stata fornita una risposta alla domanda, contattare il supporto tecnico Microsoft tramite la [Guida e il supporto tecnico del Centro per i partner](https://aka.ms/marketplacepublishersupport). Selezionare quindi **Offer creation (Creazione offerta)**  > **Your type of offer (Tipo di offerta)**  > **Lead management configuration (Configurazione di gestione dei lead)** .

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Ricevo notifiche tramite posta elettronica quando vengono ricevuti nuovi lead. Come è possibile configurare un altro utente per la ricezione di questi messaggi di posta elettronica?

Accedere alla propria offerta nel Centro per i partner e passare alla pagina **Offer setup (Configurazione dell'offerta)** > **Lead Management (Gestione dei lead)**  > **Modifica**. Aggiornare gli indirizzi di posta elettronica nel campo **Indirizzo di posta elettronica di contatto**.

## <a name="next-steps"></a>Passaggi successivi

Dopo che è stata eseguita la configurazione tecnica, incorporare i lead nella strategia di vendita e marketing e nei processi operativi correnti. L'intento è quello di comprendere meglio il processo complessivo di vendita e di collaborare con l'utente per creare lead di alta qualità e dati sufficienti a garantire il successo dell'utente. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato. Sono graditi [feedback](mailto:AzureMarketOnboard@microsoft.com) e suggerimenti che consentano al team di vendita di sviluppare i lead del marketplace commerciale in modo ottimale.
