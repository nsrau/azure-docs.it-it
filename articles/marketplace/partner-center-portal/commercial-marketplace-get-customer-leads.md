---
title: Configura lead dall'offerta Microsoft Commercial Marketplace
description: Configurare i lead dei clienti nel Marketplace commerciale Microsoft.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 54c67656d7847b44c8fc83b33a4e03be3838cf76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131109"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Lead di clienti dall'offerta del marketplace

I lead sono clienti interessati o distribuiscono le offerte da Microsoft [AppSource](https://appsource.microsoft.com) e [Azure Marketplace](https://azuremarketplace.microsoft.com). Dopo la pubblicazione dell'offerta nel Marketplace, riceverai i clienti potenziali. In questo articolo viene descritto quanto segue:

* Il modo in cui l'offerta del Marketplace genera lead dei clienti per assicurarsi di non perdere le opportunità aziendali. 
* Come connettere il sistema CRM (Customer Relationship Management) all'offerta per poter gestire i lead in un'unica posizione centrale.
* I dati principali che si inviano per poter completare i clienti che hanno contattato l'utente.

## <a name="generate-customer-leads"></a>Genera lead cliente

Di seguito sono riportati i punti in cui viene generato un lead:

- Un cliente acconsente alla condivisione delle informazioni dopo aver selezionato **contattatemi** dal Marketplace commerciale. Questo è un lead *iniziale di interesse* . Condividiamo le informazioni relative al cliente che ha espresso interesse nel ricevere il prodotto. Il lead si trova al vertice del grafico a imbuto relativo all'acquisizione dei clienti.

    ![Dynamics 365 contattatemi](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Un cliente seleziona **Get it now** (oppure seleziona **Crea** nella [portale di Azure](https://portal.azure.com/)) per ottenere l'offerta. Questo è un lead *attivo* . Le informazioni relative al cliente che ha iniziato a distribuire il prodotto vengono condivise con l'utente.

    ![Pulsante Ottieni ora SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Pulsante Crea di Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Un cliente seleziona **test drive** o **versione di valutazione gratuita** per provare l'offerta. Le unità di test o le versioni di valutazione gratuite sono un'opportunità accelerata per la condivisione immediata dell'azienda con potenziali clienti senza alcuna barriera all'ingresso.

    ![Pulsante test drive di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Pulsante versione di valutazione gratuita di Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connettersi al sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informazioni sui lead

Ogni lead ricevuto durante il processo di acquisizione del cliente presenta dati in campi specifici. Il primo campo da cercare `LeadSource` è il campo che segue il formato:**offerta**di azione **Source-Action** | di origine.

**Origini**: il valore di questo campo viene popolato in base al Marketplace che ha generato il lead. I valori possibili sono `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Azioni**: il valore di questo campo viene popolato in base all'azione eseguita dal cliente nel Marketplace che ha generato il lead.

I valori possibili sono:

- **"Ins"**: sta per essere *installato*. Questa azione si trova in Azure Marketplace o in AppSource quando un cliente acquista il prodotto.
- **"PLT"**: sta per la *versione di valutazione del partner*. Questa azione è in AppSource quando un cliente seleziona l'opzione **Contact me** .
- **"DNC"**: sta per non *contattare*. Questa azione si trova in AppSource quando viene richiesto di contattare un partner che è stato incrociato nella pagina dell'app. Si condivide una notifica che questo cliente è stato elencato in modo incrociato nell'app, ma non è necessario contattarlo.
- **"Crea"**: questa azione è solo all'interno del portale di Azure e viene generata quando un cliente acquista l'offerta al proprio account.
- **"StartTestDrive"**: questa azione è solo per l'opzione **test drive** e viene generata quando un cliente avvia la test drive.

**Offerte**: è possibile che nel Marketplace commerciale siano disponibili più offerte. Il valore di questo campo viene popolato in base all'offerta che ha generato il lead. L'ID dell'editore e l'ID offerta vengono entrambi inviati in questo campo e sono valori specificati al momento della pubblicazione dell'offerta nel Marketplace.

Negli esempi seguenti vengono illustrati i valori nel `publisherid.offerid`formato previsto: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informazioni sul cliente

Le informazioni del cliente vengono inviate tramite più campi. Nell'esempio seguente vengono illustrate le informazioni sul cliente contenute in un lead:

- Nome: John
- Cognome: Smith
- E-mail: jsmith\@microsoft.com
- Telefono: 1234567890
- Paese: Stati Uniti
- Azienda: Microsoft
- Posizione: CTO

>[!NOTE]
>Non tutti i dati dell'esempio precedente sono sempre disponibili per ogni lead. Poiché i lead verranno derivati da più passaggi, come indicato nella sezione "generazione di clienti potenziali", il modo migliore per gestire i lead consiste nel deduplicare i record e personalizzare i follow-up. In questo modo, ogni cliente riceve un messaggio appropriato e si crea una relazione univoca.

## <a name="best-practices-for-lead-management"></a>Procedure consigliate per la gestione dei lead

- **Processo**: definire un processo di vendita chiaro, con attività cardine, analisi e una chiara proprietà del team.
- **Qualifica**: definire i prerequisiti, che indicano se un lead è stato completo. Assicurarsi che i rappresentanti di vendita o marketing siano qualificati con attenzione prima di eseguirli durante il processo di vendita completo.
- **Completamento**: non dimenticare di completare la procedura. Si prevede che la transazione tipica richieda da 5 a 12 chiamate di completamento.
- **Coltiva**: è possibile coltivare i lead in modo da raggiungere un margine di profitto superiore.

## <a name="leads-frequently-asked-questions"></a>Domande frequenti sui lead

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Dove è possibile ottenere assistenza per configurare la destinazione dei lead?

Attenersi alla procedura descritta nella sezione [connettersi al sistema CRM](#connect-to-your-crm-system)o inviare un ticket di supporto tramite la [Guida e il supporto](https://partner.microsoft.com/support/v2/?stage=1)tecnico del centro per i partner. Selezionare quindi **creazione** > offerta**il tipo di configurazione dell'offerta** > **Gestione lead**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>È necessario configurare una destinazione lead per pubblicare un'offerta nel Marketplace commerciale?

La risposta dipende dal tipo di offerta che si sta pubblicando. Il software come un servizio (SaaS) e il coinvolgimento dei clienti di Dynamics 365 ti **contatteranno** per elencare tutte le offerte di Dynamics 365 per Finanza e operazioni, tutte le offerte di Dynamics 365 business Central e tutte le offerte di servizi di consulenza. Di conseguenza, richiedono una connessione a una destinazione lead. Se il tipo di offerta non è presente nell'elenco, non è necessaria una connessione a una destinazione lead. Si consiglia di configurare una destinazione lead in modo da non perdere le opportunità aziendali.

### <a name="how-can-i-find-the-test-lead"></a>Come si trova il lead di test?

Cercare `"MSFT_TEST"` nella destinazione principale. Ecco un esempio di lead di test di Microsoft:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Ho un'offerta Live, ma perché non vedo alcun lead?

Verificare che la connessione alla destinazione principale sia valida. Ti invieremo un lead di test dopo aver selezionato **pubblica** sull'offerta nel centro per i partner. Se viene visualizzato il test lead, la connessione è valida. È anche possibile testare la connessione del lead provando ad acquisire l'anteprima dell'offerta durante il passaggio di anteprima. Selezionare **Ottieni ora**, **contattami**o versione di **valutazione gratuita** sull'inserzione nel Marketplace commerciale.

Assicurarsi anche di cercare i dati corretti. Il contenuto della sezione informazioni sui [Lead data](#understand-lead-data) di questo articolo descrive i dati principali inviati alla destinazione principale.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>L'archiviazione BLOB di Azure è stata configurata come destinazione principale, ma perché non è possibile vedere il responsabile?

L'archiviazione BLOB di Azure non è più supportata come destinazione principale, quindi mancano i lead dei clienti generati dall'offerta. Passare a una delle altre [Opzioni di destinazione principale](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Ho ricevuto un messaggio di posta elettronica dal Marketplace commerciale, ma perché non riesco a trovare il lead nel mio CRM?

È possibile che il dominio di posta elettronica dell'utente finale sia di tipo edu. Per motivi di privacy, le informazioni personali non vengono passate dal dominio. edu. Inviare un ticket di supporto tramite la [Guida e il supporto](https://partner.microsoft.com/support/v2/?stage=1)tecnico del centro per i partner.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>È stata configurata una tabella di Azure come destinazione principale. Come è possibile visualizzare i lead?

È possibile accedere ai dati dei lead archiviati nella tabella di Azure dalla portale di Azure. È anche possibile scaricare e installare gratuitamente [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per visualizzare i dati della tabella dell'account di archiviazione di Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>È stata configurata una tabella di Azure come destinazione principale. Posso ricevere una notifica ogni volta che viene inviato un nuovo lead del Marketplace commerciale?

Sì. Seguire le istruzioni in [configurare la gestione dei lead usando una tabella di Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) per configurare un Microsoft Flow che invia un messaggio di posta elettronica se viene aggiunto un lead alla tabella di Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Salesforce è stato configurato come destinazione principale, ma perché non è possibile trovare i lead?

Controllare se il modulo Web-to-lead è un campo obbligatorio in base a un elenco di selezione. In caso contrario, passare il campo a un campo di testo non obbligatorio.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>C'è stato un problema con la destinazione dei lead e non si sono visti alcuni lead. È possibile riceverli tramite posta elettronica?

A causa dei criteri di informazioni personali, non è possibile condividere le informazioni sui lead tramite posta elettronica non protetta.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>È stata configurata una tabella di Azure come destinazione principale. Quanto costa?

I dati di generazione dei lead sono bassi. È inferiore a 1 GB per quasi tutti i Publisher. Il costo dipende dal numero di lead ricevuti. Se, ad esempio, vengono ricevuti 1.000 lead in un mese, il costo è di circa 50 centesimi. Per altre informazioni sui prezzi di archiviazione, vedere [Panoramica dei prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Se la domanda non è stata risposta, contattare supporto tecnico Microsoft tramite la [Guida e il supporto](https://aka.ms/marketplacepublishersupport)tecnico del centro per i partner. Selezionare quindi **creazione** > offerta**il tipo di configurazione dell'offerta** > **Gestione lead**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Ricevo notifiche tramite posta elettronica quando vengono ricevuti nuovi clienti potenziali. Come è possibile configurare un altro utente per ricevere questi messaggi di posta elettronica?

Accedere alla propria offerta nel centro per i partner e passare alla pagina di **configurazione dell'offerta** >**modifica** **della gestione dei** > lead. Aggiornare gli indirizzi di posta elettronica nel campo **indirizzo di posta elettronica di contatto** .

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la configurazione tecnica, incorporare questi lead nella strategia di vendita e marketing attuale e nei processi operativi. Siamo interessati a comprendere meglio il processo di vendita globale e desideriamo collaborare con te per fornire lead di alta qualità e dati sufficienti per ottenere risultati ottimali. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato. Segnalaci se sei interessato a [inviare commenti](mailto:AzureMarketOnboard@microsoft.com) e suggerimenti per consentire al tuo team di vendita di ottenere risultati più efficaci con i lead del Marketplace commerciale.
