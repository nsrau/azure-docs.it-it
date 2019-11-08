---
title: Configurare i lead dei clienti | Azure Marketplace
description: Configurare i lead dei clienti nel Marketplace commerciale.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812662"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Clienti lead dall'offerta del Marketplace

I lead sono i clienti interessati a o distribuiscono le offerte da [Azure Marketplace](https://azuremarketplace.microsoft.com) o da [AppSource](https://appsource.microsoft.com). Dopo la pubblicazione dell'offerta nel Marketplace, si riceveranno i lead dei clienti. In questo articolo viene illustrato quanto segue:

* Il modo in cui l'offerta del Marketplace genera clienti lead, assicurandosi di non perdere le opportunità aziendali. 
* Connetti il tuo CRM alla tua offerta, per poter gestire i tuoi lead in un'unica posizione centrale.
* Per comprendere i dati principali che si inviano, è possibile completare i clienti che hanno contattato l'utente.

## <a name="generate-customer-leads"></a>Genera lead cliente

Di seguito sono riportati i punti in cui viene generato un lead:

1. Quando un cliente acconsente alla condivisione delle informazioni dopo aver selezionato "Contattatemi" dal Marketplace. Questo è un lead di **interesse iniziale** , in cui vengono condivise informazioni sul cliente che ha espresso interesse nel recupero del prodotto. Il lead si trova al vertice del grafico a imbuto relativo all'acquisizione dei clienti.

      ![Dynamics 365 contattatemi](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Quando un cliente seleziona "Get it Now" o "create" (nella [portale di Azure](https://portal.azure.com/)) per ottenere l'offerta, questo è un **Lead attivo**, in cui vengono condivise le informazioni relative a un cliente che ha iniziato a distribuire il prodotto.

    ![Ottieni ora SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Creazione di Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Un cliente accetta un "test drive" o avvia una "versione di valutazione gratuita" dell'offerta. Le unità di test o le versioni di valutazione gratuite sono un'opportunità accelerata per la condivisione immediata dell'azienda con potenziali clienti senza alcuna barriera all'ingresso.

    ![Test drive di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Test drive di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connettersi al sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informazioni sui lead

Ogni lead ricevuto durante il processo di acquisizione del cliente presenta dati in campi specifici. Il primo campo da cercare è il campo `LeadSource`, che segue il formato: **origine-azione** | **offerta**.

**Origini**: il valore di questo campo viene popolato in base al Marketplace che ha generato il lead. I valori possibili sono `"AzureMarketplace"`, `"AzurePortal"`e `"AppSource (SPZA)"`.

**Azioni**: il valore di questo campo viene popolato in base all'azione eseguita dal cliente nel Marketplace, che ha generato il lead. 

I valori possibili sono:

- "INS": Installazione. Questa azione si trova in Azure Marketplace o AppSource quando un cliente acquista il prodotto.
- "PLT": significa Versione di valutazione condotta dal partner. Questa azione si trova in AppSource quando un cliente usa l'opzione "Contattami".
- "DNC": non contattare. L'azione si trova in AppSource quando un partner che è stato elencato in modo incrociato nella pagina app riceve una richiesta per essere contattato. Viene condiviso l'annuncio che il cliente è stato elencato nell'app, ma non deve essere contattato.
- "Crea": questa azione è contenuta solo all'interno del portale di Azure e viene generata quando un cliente acquista l'offerta al proprio account.
- "StartTestDrive": questa azione riguarda solo i test drive e si genera quando un cliente avvia il test drive.

**Offerte**: è possibile avere più offerte nel Marketplace. Il valore di questo campo viene popolato in base all'offerta che ha generato il lead. L'ID dell'editore e l'ID offerta vengono entrambi inviati in questo campo e sono valori specificati al momento della pubblicazione dell'offerta nel Marketplace.

Negli esempi seguenti vengono illustrati i valori di esempio nel formato previsto `publisherid.offerid`: 

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
>Non tutti i dati dell'esempio precedente sono sempre disponibili per ogni lead. Poiché si otterranno lead da più passaggi, come indicato nella sezione Customer Leads, il modo migliore per gestire i lead consiste nel deduplicare i record e personalizzare i follow-up. In questo modo, ogni cliente riceve un messaggio appropriato e si crea una relazione univoca.

## <a name="best-practices-for-lead-management"></a>Procedure consigliate per la gestione dei lead

1. *Process* : definisce un processo di vendita chiaro, con attività cardine, indicatori KPI e una chiara proprietà del team.
2. *Qualificazione* : definire i prerequisiti, che indicano se un lead è stato completo. Assicurarsi che i rappresentanti di vendita o marketing siano idonei per i lead prima di eseguirli durante il processo di vendita completo.
3. *Completamento* : non dimenticare di completare, prevedere che la transazione tipica richieda da 5 a 12 chiamate di completamento
4. *Coltiva* i tuoi lead, per consentirti di raggiungere un margine di profitto superiore.

## <a name="leads-frequently-asked-questions"></a>Domande frequenti sui lead

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Dove è possibile ottenere assistenza per la configurazione della destinazione principale?

[Qui](#connect-to-your-crm-system) è possibile trovare la documentazione o inviare un ticket di supporto tramite aka.ms/marketplacepublishersupport, quindi selezionare **"Creazione offerta"** → **il tipo di offerta** → **"configurazione della gestione dei lead".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>È necessario configurare una destinazione lead per pubblicare un'offerta sul Marketplace?

La risposta dipende dal tipo di offerta che si sta pubblicando. SaaS e Dynamics 365 per le offerte di engagement dei clienti da elencare come ' Contatta me ', tutte le offerte di Dynamics 365 per le operazioni, tutte le offerte di Dynamics 365 business Central e tutte le offerte di servizi di consulenza richiedono una connessione a una destinazione principale. Se il tipo di offerta non è presente nell'elenco, non è obbligatorio. Tuttavia, si consiglia di configurare una destinazione lead in modo da non perdere le opportunità aziendali.

### <a name="how-can-i-find-the-test-lead"></a>Come è possibile trovare il lead del test?

Cercare `"MSFT_TEST"` nella destinazione principale, ecco un esempio di lead di test di Microsoft:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Ho un'offerta Live, ma non vedo alcun lead?

Verificare che la connessione alla destinazione principale sia valida. Ti invieremo un lead di test dopo aver fatto clic su pubblica sull'offerta nel centro per i partner. Se viene visualizzato il lead di test, la connessione è valida. È anche possibile testare la connessione del lead provando ad acquisire l'anteprima dell'offerta durante il passaggio di anteprima facendo clic su "Ottieni ora", "contatti" o "versione di valutazione gratuita" nell'inserzione nel Marketplace.

Assicurarsi anche di cercare i dati corretti. Il contenuto della sezione informazioni sui [Lead data](#understand-lead-data) di questo documento descrive i dati principali inviati alla destinazione principale.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Il BLOB di Azure è stato configurato come destinazione principale, perché non viene visualizzato il lead?

La destinazione del lead BLOB di Azure non è più supportata, pertanto mancano i lead dei clienti generati dall'offerta. Passare a una delle altre [Opzioni di destinazione principale](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Ho ricevuto un messaggio di posta elettronica da Marketplace, perché non riesco a trovare il lead nel mio CRM?

È possibile che il dominio di posta elettronica dell'utente finale sia di tipo edu. Per motivi di privacy, i dati PII non vengono passati dal dominio edu. Inviare un ticket di supporto tramite aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Ho configurato la tabella di Azure come destinazione principale, come posso visualizzare i lead?

È possibile accedere ai dati dei lead archiviati nella tabella di Azure dal portale di Azure oppure è possibile scaricare e installare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratuitamente per visualizzare i dati delle tabelle dell'account di archiviazione di Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Ho configurato la tabella di Azure come destinazione principale, posso ricevere una notifica ogni volta che viene inviato un nuovo lead dal Marketplace?

Sì, seguire le istruzioni per configurare un Microsoft Flow che invia un messaggio di posta elettronica se un lead viene aggiunto alla tabella di Azure nella documentazione [qui](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Ho configurato Salesforce come destinazione principale, perché non è possibile trovare i lead?

Controllare se il modulo "Web to lead" è un campo obbligatorio in base a un elenco a discesa. Se lo è, impostarlo su un campo di testo non obbligatorio.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Si è verificato un problema con la destinazione principale e ho perso alcuni lead. Posso riceverli in un messaggio di posta elettronica?

A causa dei criteri relativi alle informazioni personali, non è possibile condividere informazioni sui lead tramite posta elettronica non protetta.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>La tabella di Azure è stata configurata come destinazione principale, qual è il costo?

I dati di generazione dei lead sono inferiori a 1 GB per quasi tutti gli editori. Il costo dipenderà dal numero di lead ricevuti. Se ad esempio si ricevono 1.000 lead in un mese, il costo è di circa 50 centesimi. Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

Se la domanda non è ancora disponibile, contattare il supporto tecnico tramite aka.ms/marketplacepublishersupport, quindi selezionare **' offerta creazione '** → **il tipo di offerta** → **' Lead Management Configuration '.** 

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la configurazione tecnica, è necessario incorporare questi lead nelle vendite correnti & strategia di marketing e processi operativi. L'intento è quello di comprendere meglio il processo complessivo di vendita e di collaborare con l'utente per creare lead di alta qualità e dati sufficienti a garantire il successo dell'utente. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato. Segnalaci se sei interessato a [inviare commenti](mailto:AzureMarketOnboard@microsoft.com) e suggerimenti per consentire al tuo team di vendita di ottenere una maggiore successo con i lead del Marketplace.
