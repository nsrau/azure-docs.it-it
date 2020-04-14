---
title: Configurare i lead dei clienti Azure Marketplace
description: Configurare i lead dei clienti nel mercato commerciale.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 05b166b2ec46900d9e3972025efb581d9619ec6a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252632"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Lead di clienti dall'offerta del marketplace

I lead sono clienti interessati o distribuiscono le offerte da [Azure Marketplace](https://azuremarketplace.microsoft.com) o da [AppSource.](https://appsource.microsoft.com) Riceverai i lead dei clienti una volta che la tua offerta sarà pubblicata sul mercato. Questo articolo spiegherà:

* Il modo in cui la tua offerta di mercato genera lead ai clienti, assicurandoti di non perdere opportunità di business. 
* Collega il tuo CRM alla tua offerta, in modo da poter gestire i tuoi lead in un'unica posizione centrale.
* Comprendi i dati principali che ti inviamo, in modo che tu possa dare un seguito ai clienti che ti hanno contattato.

## <a name="generate-customer-leads"></a>Generare lead per i clienti

Ecco i luoghi in cui viene generato un lead:

1. Quando un cliente acconsente a condividere le proprie informazioni dopo aver selezionato "Contattami" dal marketplace. Questo lead è un lead **di interesse iniziale,** in cui condividiamo informazioni sul cliente che ha espresso interesse a ottenere il tuo prodotto. Il lead si trova al vertice del grafico a imbuto relativo all'acquisizione dei clienti.

      ![Dynamics 365 Contattami](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Quando un cliente seleziona "Ottieni ora" o "Crea" (nel portale di [Azure)](https://portal.azure.com/)per ottenere l'offerta, questo lead è un **lead attivo,** in cui vengono condivisi informazioni su un cliente che ha iniziato a distribuire il prodotto.

    ![SQL Scaricalo ora](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Creazione di Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Un cliente esegue un "Test Drive" o avvia una "Prova gratuita" dell'offerta. Test Drive o prove gratuite sono opportunità accelerate per condividere la tua attività istantaneamente con i potenziali clienti senza barriere di ingresso.

    ![Dynamics 365 Test Drive](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 Test Drive](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connettersi al sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Comprendere i dati dei lead

Ogni lead ricevuto durante il processo di acquisizione del cliente presenta dati in campi specifici. Il primo campo a cui `LeadSource` cercare è il campo, che segue questo formato: **Source-Action** | **Offer**.

**Origini**: il valore di questo campo viene popolato in base al marketplace che ha generato il lead. I valori possibili sono `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Azioni:** il valore di questo campo viene popolato in base all'azione eseguita dal cliente nel marketplace, che ha generato il lead. 

I valori possibili sono:

- "INS": Installazione. Questa azione si trova in Azure Marketplace o AppSource quando un cliente acquista il prodotto.
- "PLT": significa Versione di valutazione condotta dal partner. Questa azione si trova in AppSource quando un cliente usa l'opzione "Contattami".
- "DNC": non contattare. L'azione si trova in AppSource quando un partner che è stato elencato in modo incrociato nella pagina app riceve una richiesta per essere contattato. Viene condiviso l'annuncio che il cliente è stato elencato nell'app, ma non deve essere contattato.
- "Crea": questa azione si trova solo all'interno del portale di Azure e viene generata quando un cliente acquista l'offerta al proprio account.
- "StartTestDrive": questa azione riguarda solo i test drive e si genera quando un cliente avvia il test drive.

**Offerte**: Potresti avere più offerte sul mercato. Il valore di questo campo viene popolato in base all'offerta che ha generato il lead. L'ID editore e l'ID offerta vengono entrambi inviati in questo campo e sono valori forniti al momento della pubblicazione dell'offerta nel marketplace.

Negli esempi seguenti vengono illustrati `publisherid.offerid`valori di esempio nel formato previsto: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informazioni sui clienti

Le informazioni del cliente vengono inviate tramite più campi. Nell'esempio seguente vengono illustrate le informazioni sul cliente contenute in un lead.

- Nome: John
- Cognome: Smith
- E-mail: jsmith\@microsoft.com
- Telefono: 1234567890
- Paese: Stati Uniti
- Azienda: Microsoft
- Posizione: CTO

>[!Note]
>Non tutti i dati dell'esempio precedente sono sempre disponibili per ogni lead. Poiché si otterranno lead da più passaggi come indicato nella sezione Lead cliente, il modo migliore per gestire i lead consiste nel deduplicare i record e personalizzare i follow-up. In questo modo, ogni cliente riceve un messaggio appropriato e si crea una relazione univoca.

## <a name="best-practices-for-lead-management"></a>Best practice per la gestione dei lead

1. *Processo:* definisci un processo di vendita chiaro, con attività cardine, KPI e una chiara proprietà del team.
2. *Qualifica:* definire i prerequisiti che indicano se un lead è stato completamente qualificato. Assicurati che i rappresentanti di vendita o di marketing riscedano i lead con attenzione prima di guidarli attraverso l'intero processo di vendita.
3. *Follow-up* - Non dimenticare di seguire, aspettatevi che la transazione tipica richieda 5 a 12 chiamate di follow-up
4. *Nurture* - Nutrire i vostri lead, al fine di ottenere sulla strada per un margine di profitto più alto.

## <a name="leads-frequently-asked-questions"></a>Domande frequenti per i lead

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Dove è possibile ottenere assistenza per configurare la destinazione dei lead?

Puoi trovare la documentazione [qui](#connect-to-your-crm-system) o inviare un ticket di supporto tramite aka.ms/marketplacepublishersupport quindi selezionare **'creazione dell'offerta'** - **il tuo tipo di offerta** - 'configurazione di gestione **dei lead'.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>È necessario configurare una destinazione lead per pubblicare un'offerta sul marketplace?

La risposta dipende dal tipo di offerta che si sta pubblicando. Le offerte SaaS e Dynamics 365 for Customer Engagement vengono elencate come "Contattami", tutte le offerte di Dynamics 365 for Operations, tutte le offerte di Dynamics 365 Business Central e tutte le offerte del servizio di consulenza richiedono una connessione a una destinazione principale. Se il tipo di offerta non è stato elencato, non è obbligatorio. Tuttavia, è consigliabile configurare una destinazione lead in modo da non perdere opportunità di business.

### <a name="how-can-i-find-the-test-lead"></a>Come si trova il lead di test?

Cerca `"MSFT_TEST"` nella tua destinazione principale, ecco un esempio di test di Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Ho un'offerta dal vivo, ma non vedo nessuna pista?

Assicurarsi che la connessione alla destinazione lead sia valida. Ti invieremo un responsabile del test dopo aver raggiunto la pubblicazione dell'offerta nel Centro per i partner. Se viene visualizzato il responsabile del test, la connessione è valida. Puoi anche testare la tua connessione lead cercando di acquisire l'anteprima dell'offerta durante la fase di anteprima facendo clic su "ottieniora", "contattami" o "prova gratuita" sulla scheda nel marketplace.

Inoltre, assicurati di cercare i dati giusti. Il contenuto della sezione [Informazioni sui dati](#understand-lead-data) dei lead di questo documento descrive i dati dei lead che inviamo alla destinazione del lead.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>È stato configurato un BLOB di Azure come destinazione dei lead. Perché il lead non è visualizzato?

La destinazione di lead BLOB di Azure non è più supportata, pertanto mancano tutti i lead dei clienti generati dall'offerta. Passare a una qualsiasi delle altre opzioni di [destinazione lead.](./commercial-marketplace-get-customer-leads.md) 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>È stato ricevuto un messaggio di posta elettronica dal Marketplace, perché non si riesce a trovare il lead nel sistema CRM?

È possibile che il dominio di posta elettronica dell'utente finale sia di tipo edu. Per motivi di privacy, non passiamo informazioni private identificabili dal dominio .edu. Inviare un ticket di supporto tramite aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Ho configurato Tabella di Azure come destinazione principale, come è possibile visualizzare i lead?

È possibile accedere ai dati di lead archiviati nella tabella di Azure dal portale di Azure oppure scaricare e installare Azure Storage Explorer gratuitamente per visualizzare i dati delle tabelle dell'account di archiviazione di Azure.You can access the lead data stored in the Azure Table from the Azure portal, or you can download and install [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) for free to view your Azure storage account's tables data.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>È stata configurata una tabella di Azure come destinazione dei lead. È possibile ricevere una notifica quando viene inviato un nuovo lead dal Marketplace?

Sì, seguire le istruzioni per configurare un flusso Microsoft che invia un messaggio di posta elettronica se viene aggiunto un lead alla tabella di Azure nella documentazione [qui](./commercial-marketplace-lead-management-instructions-azure-table.md)presente.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>È stato configurato Salesforce come destinazione dei lead. Perché i lead non sono visualizzati?

Verificare se il modulo "web to lead" è un campo obbligatorio basato su un elenco a discesa. Se lo è, impostarlo su un campo di testo non obbligatorio.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>C'è stato un problema con la destinazione dei lead e non si sono visti alcuni lead. È possibile riceverli tramite posta elettronica?

A causa delle politiche private identificabili in materia di informazioni, non possiamo condividere le informazioni sui lead tramite e-mail non protette.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Ho configurato Tabella di Azure come destinazione principale, quanto costerà?

I dati di generazione dei lead sono inferiori a 1 GB per quasi tutti gli editori. Il costo dipenderà dal numero di lead ricevuti. Se ad esempio si ricevono 1.000 lead in un mese, il costo è di circa 50 centesimi. Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

Se la tua domanda non ha ancora una risposta, contatta il supporto tramite aka.ms/marketplacepublishersupport, quindi seleziona **"creazione dell'offerta"** - **il tuo tipo di offerta** - 'configurazione di gestione dei **lead'.** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Ricevo notifiche e-mail quando vengono ricevuti nuovi lead dei clienti. Come posso configurare chi ricevere queste e-mail?

Accedi alla tua offerta nel Centro per i partner e vai alla pagina **Impostazione offerta** - > **Lead Management** -> **Modifica**. Aggiornare gli indirizzi di posta elettronica nel campo Indirizzo di **posta elettronica contatto.**

## <a name="next-steps"></a>Passaggi successivi

Una volta che la configurazione tecnica è in atto, è necessario incorporare questi lead nella vostra attuale strategia di marketing & e processi operativi. L'intento è quello di comprendere meglio il processo complessivo di vendita e di collaborare con l'utente per creare lead di alta qualità e dati sufficienti a garantire il successo dell'utente. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato. Facci sapere se sei interessato a [fornire feedback](mailto:AzureMarketOnboard@microsoft.com) e suggerimenti per consentire al tuo team di vendita di avere più successo con i lead di Marketplace.
