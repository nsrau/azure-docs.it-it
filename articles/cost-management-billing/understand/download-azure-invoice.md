---
title: Visualizzare e scaricare la fattura di Azure
description: Descrive come visualizzare e scaricare la fattura di Azure.
keywords: fatturazione, scaricare la fatture, fattura di Azure, uso di Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479003"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visualizzare e scaricare la fattura di Microsoft Azure

È possibile scaricare la fattura nel [portale di Azure](https://portal.azure.com/) o richiederne l'invio all'indirizzo di posta elettronica. I clienti di Azure con un contratto Enterprise (clienti EA) non possono scaricare la fattura dell'organizzazione. Le fatture vengono inviate all'utente configurato per la ricezione delle fatture per la registrazione.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>Quando vengono generate le fatture

Viene generata una fattura in base al tipo di account di fatturazione. Le fatture vengono create per gli account di fatturazione del Programma dei Microsoft Online Services (MOSP), del Contratto del cliente Microsoft e del Contratto Microsoft Partner. Le fatture vengono generate anche per gli account di fatturazione del contratto Enterprise (EA). Tuttavia, le fatture per gli account di fatturazione EA non vengono visualizzate nel portale di Azure.

Per altre informazioni sugli account di fatturazione e su come identificare il tipo di account di fatturazione, vedere [Visualizzare gli account di fatturazione nel portale di Azure](../manage/view-all-accounts.md).

## <a name="invoices-for-mosp-billing-accounts"></a>Fatture per gli account di fatturazione del Programma dei Microsoft Online Services

L'account di fatturazione del Programma dei Microsoft Online Services viene creato quando ci si iscrive ad Azure tramite il sito Web di Azure. ad esempio, quando ci si iscrive per ricevere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un account di [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

I clienti in determinate aree geografiche che si iscrivono tramite il sito Web di Azure per ricevere un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) possono avere un account di fatturazione per un Contratto del cliente Microsoft.

Se non si è certi del tipo di account di fatturazione, vedere [Controllare il tipo di account di fatturazione](../manage/view-all-accounts.md#check-the-type-of-your-account) prima di seguire le istruzioni riportate in questo articolo. 

Un account di fatturazione Programma dei Microsoft Online Services può avere le fatture seguenti:

**Addebiti per i servizi di Azure**: viene generata una fattura per ogni sottoscrizione di Azure che contiene le risorse di Azure usate dalla sottoscrizione. La fattura contiene gli addebiti per un periodo di fatturazione. Il periodo di fatturazione è determinato dal giorno del mese in cui viene creata la sottoscrizione.

Ad esempio, Giorgio crea *Sottoscrizione di Azure 01* il 5 marzo e *Sottoscrizione di Azure 02* il 10 marzo. La fattura per *Sottoscrizione di Azure 01* sarà addebitata dal quinto giorno del mese al quarto giorno del mese successivo. La fattura per *Sottoscrizione di Azure 02* sarà addebitata dal decimo giorno del mese al nono giorno del mese successivo. Le fatture per tutte le sottoscrizioni di Azure vengono normalmente generate nel giorno del mese in cui è stato creato l'account o al più tardi entro i due giorni successivi. In questo esempio, se Giorgio ha creato il proprio account il 2 febbraio, le fatture per *Sottoscrizione di Azure 01* e *Sottoscrizione di Azure 02* verranno normalmente generate il secondo giorno di ogni mese o al più tardi entro i due giorni successivi.

**Azure Marketplace, prenotazioni e VM spot**: viene generata una fattura per le prenotazioni, i prodotti del Marketplace e le VM spot acquistate tramite una sottoscrizione. La fattura mostra tutti gli addebiti del mese precedente. Ad esempio, Giorgio ha acquistato una prenotazione il 1 marzo e un'altra prenotazione il 30 marzo. Verrà generata una singola fattura per entrambe le prenotazioni in aprile. La fattura per Azure Marketplace, le prenotazioni e le VM spot viene sempre generata intorno al nono giorno del mese.

Se per Azure si paga con una carta di credito e si acquista la prenotazione, Azure genera una fattura immediata. Tramite questa fattura, tuttavia, viene addebitata la prenotazione per la fattura mensile successiva.

**Piano di supporto di Azure**: viene generata una fattura ogni mese per la sottoscrizione del piano di supporto. La prima fattura viene generata il giorno di acquisto o entro i due giorni successivi. Le fatture per il piano di supporto successive vengono normalmente generate nel giorno del mese in cui è stato creato l'account o al più tardi entro i due giorni successivi.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Scaricare la fattura della sottoscrizione del Programma dei Microsoft Online Services (MOSP) di Azure

Viene generata una fattura solo per una sottoscrizione che appartiene a un account di fatturazione per il Programma dei Microsoft Online Services (MOSP). [Controllare l'accesso a un account del Programma dei Microsoft Online Services (MOSP)](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Per scaricare la fattura, è necessario disporre di un ruolo amministratore account per una sottoscrizione. Gli utenti con i ruoli proprietario, collaboratore o lettore possono scaricare la fattura solo se l'amministratore dell'account ha concesso loro l'autorizzazione. Per altre informazioni, vedere [Consentire agli utenti di scaricare le fatture](../manage/manage-billing-access.md#opt-in).

1. Selezionare la sottoscrizione nella [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure.
1. Selezionare **Fatture** nella sezione relativa alla fatturazione.  
    ![Screenshot che mostra un utente che seleziona l'opzione Fatture per una sottoscrizione](./media/download-azure-invoice/select-subscription-invoice.png)
1. Selezionare **Scarica** per scaricare una versione PDF della fattura, quindi selezionare **Scarica** nella sezione relativa alla fattura.  
    [![Screenshot che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. È anche possibile scaricare un dettaglio giornaliero delle quantità utilizzate e degli addebiti facendo clic su **Scarica** nella sezione relativa ai dettagli di utilizzo. La preparazione del file CSV potrebbe richiedere alcuni minuti.  
    ![Screenshot che mostra la pagina per scaricare la fattura e i dati di utilizzo](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](../understand/review-individual-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](../manage/getting-started.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Scaricare la fattura del piano di supporto del programma dei Microsoft Online Services (MOSP)

Viene generata una fattura solo per un piano di supporto che appartiene a un account di fatturazione per il Programma dei Microsoft Online Services (MOSP). [Controllare l'accesso a un account del Programma dei Microsoft Online Services (MOSP)](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Per scaricare la fattura, è necessario disporre di un ruolo amministratore account per una sottoscrizione del piano di supporto.

1.  Accedere al [portale di Azure](https://portal.azure.com).
1.  Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/download-azure-invoice/search-cmb.png)
1.  Selezionare **Fatture** sul lato sinistro.
1.  Selezionare la sottoscrizione del piano di supporto e quindi **Scarica**.  
    [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  Selezionare **Scarica** per scaricare una versione PDF della fattura.  
    ![Screenshot che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>Consentire ad altri utenti di scaricare la fattura di sottoscrizione per il Programma dei Microsoft Online Services (MOSP)

La versione PDF della fattura contiene informazioni personali sull'amministratore dell'account, quindi l'amministratore dell'account per questa sottoscrizione deve concedere ad altri utenti l'autorizzazione per scaricare la fattura. Una volta concessa l'autorizzazione, le persone e gli utenti seguenti con i ruoli appropriati possono scaricare le fatture delle sottoscrizioni:

- Utenti
- Gruppi
- Entità servizio con un proprietario
- Collaboratore
- Reader
- Amministratore accesso utente
- Fatturazione per lettore
- Coamministratore
- Amministratore del servizio

Per scaricare una fattura:

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore account per la sottoscrizione.
1.  Cercare **Gestione dei costi e fatturazione**.  
1.  Selezionare **Fatture** sul lato sinistro.
1.  Selezionare la sottoscrizione di Azure e quindi **Accesso alla fattura**.  
    [![Screenshot che mostra la selezione di Accesso alla fattura](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Selezionare **Sì** e quindi **Salva** nella parte superiore della pagina.  
    ![Screenshot che mostra la selezione dell'opzione Sì per Accesso alla fattura](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Ricevere la fattura della sottoscrizione del Programma dei Microsoft Online Services (MOSP) tramite posta elettronica

È necessario avere un ruolo di amministratore dell'account per una sottoscrizione o un piano di supporto per acconsentire esplicitamente a ricevere la fattura tramite posta elettronica. Le fatture tramite posta elettronica sono disponibili solo per le sottoscrizioni e i piani di supporto, non per le prenotazioni o per gli acquisti in Azure Marketplace. Dopo aver fornito il consenso, è possibile aggiungere altri destinatari per la ricezione della fattura tramite posta elettronica.

1.  Accedere al [portale di Azure](https://portal.azure.com).
1.  Cercare **Gestione dei costi e fatturazione**.  
1.  Selezionare **Fatture** sul lato sinistro.
1.  Selezionare la sottoscrizione di Azure o la sottoscrizione del piano di supporto e quindi selezionare **Fattura tramite posta elettronica**.  
    [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. Selezionare **Acconsenti esplicitamente** e accettare le condizioni.  
    ![Screenshot che illustra il passaggio 2 del flusso per il consenso esplicito](./media/download-azure-invoice/invoice-article-step02.png)
1. La fattura viene inviata all'indirizzo di posta elettronica di comunicazione preferito. È possibile aggiornare l'indirizzo di posta elettronica nelle [informazioni di contatto dell'account di fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties).  
    ![Screenshot che illustra il passaggio 3 del flusso per il consenso esplicito](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>Condividere le fatture del Programma dei Microsoft Online Services (MOSP) con altri utenti tramite posta elettronica

È possibile condividere le fatture per la sottoscrizione e il piano di supporto ogni mese con il team di contabilità oppure inviarle a uno degli altri indirizzi di posta elettronica.

1. A tale scopo, seguire la procedura illustrata in [Ricevere le fatture della sottoscrizione e del piano di supporto tramite posta elettronica](#get-mosp-subscription-invoice-in-email) e selezionare **Configura destinatari**.  
    ![Screenshot che mostra un utente che seleziona Configura destinatari](./media/download-azure-invoice/invoice-article-step03.png)
1. Immettere un indirizzo di posta elettronica e quindi selezionare **Aggiungi destinatario**. È possibile aggiungere più indirizzi di posta elettronica.  
    ![Screenshot che mostra un utente che aggiunge altri destinatari](./media/download-azure-invoice/invoice-article-step04.png)
1. Dopo aver aggiunto tutti gli indirizzi di posta elettronica, selezionare **Fine** nella parte inferiore della schermata.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Fatture per gli account di fatturazione del Contratto del cliente Microsoft e del Contratto Microsoft Partner

L'account di fatturazione del Contratto del cliente Microsoft viene creato quando l'organizzazione collabora con un rappresentante Microsoft per firmare un Contratto del cliente Microsoft. Alcuni clienti in determinate aree geografiche che si iscrivono tramite il sito Web di Azure per ricevere un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) possono anche avere un account di fatturazione per un Contratto del cliente Microsoft. Per altre informazioni, vedere [Introduzione all'account di fatturazione del Contratto del cliente Microsoft](../understand/mca-overview.md).

Un account di fatturazione per un contratto Microsoft Partner viene creato per i partner Cloud Solution Provider (CSP) per consentire loro di gestire i clienti nella nuova esperienza commerciale. I partner devono avere almeno un cliente con un [piano di Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) per gestire il relativo account di fatturazione nel portale di Azure. Per altre informazioni, vedere [Introduzione all'account di fatturazione del Contratto Microsoft Partner](../understand/mpa-overview.md).

Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene gli addebiti rispettivi per tutte le sottoscrizioni di Azure e gli altri acquisti del mese precedente. Ad esempio, Giorgio crea *Sottoscrizione di Azure 01* il 5 marzo e *Sottoscrizione di Azure 02* il 10 marzo. Ha acquistato la sottoscrizione *Supporto tecnico di Azure 01* il 28 marzo usando il *Profilo di fatturazione 01*. Giorgio otterrà una singola fattura all'inizio di aprile che conterrà gli addebiti sia per le sottoscrizioni di Azure che per il piano di supporto.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Scaricare una fattura del profilo di fatturazione Contratto del cliente Microsoft o Contratto Microsoft Partner

Per scaricare le fatture dal portale di Azure, è necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione. Gli utenti con un ruolo di proprietario, collaboratore o lettore per un account di fatturazione possono scaricare le fatture per tutti i profili di fatturazione nell'account.

1.  Accedere al [portale di Azure](https://portal.azure.com).
1.  Cercare **Gestione dei costi e fatturazione**.  
1. Selezionare **Fatture** sul lato sinistro.
    [![Screenshot che mostra la pagina Fatture per un account di fatturazione del Contratto del cliente Microsoft](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. Nella tabella delle fatture selezionare la fattura da scaricare.
1. Selezionare **Scarica il file PDF della fattura** nella parte superiore della pagina.  
    ![Screenshot che illustra il download del file PDF della fattura](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. È anche possibile scaricare un file CSV contenente il dettaglio giornaliero delle quantità utilizzate e degli addebiti stimati facendo clic su **Scarica l'utilizzo di Azure**. La preparazione del file CSV potrebbe richiedere alcuni minuti.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Ricevere la fattura del profilo di fatturazione tramite posta elettronica

Per aggiornare la preferenza per la ricezione delle fatture tramite posta elettronica, è necessario disporre di un ruolo di proprietario o di collaboratore sul profilo di fatturazione o sul relativo account di fatturazione. Dopo aver fornito il consenso esplicito, tutti gli utenti con ruolo proprietario, collaboratore, lettore o gestione fatture in un profilo di fatturazione riceveranno la fattura tramite posta elettronica. 

1.  Accedere al [portale di Azure](https://portal.azure.com).
1.  Cercare **Gestione dei costi e fatturazione**.  
1.  Selezionare **Fatture** dal riquadro a sinistra e quindi selezionare **Fattura tramite posta elettronica** nella parte superiore della pagina.  
    [![Screenshot che mostra la pagina Fatture per un account di fatturazione del Contratto del cliente Microsoft](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Se sono presenti più profili di fatturazione, selezionare un profilo di fatturazione e quindi selezionare **Acconsenti esplicitamente**.  
    ![Screenshot che mostra la pagina Fatture per un account di fatturazione del Contratto del cliente Microsoft](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Selezionare **Aggiorna**.

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>Concedere ad altri utenti l'accesso alle fatture del Contratto del cliente Microsoft o del Contratto Microsoft Partner

È possibile concedere ad altri utenti l'accesso per la visualizzazione, il download e il pagamento delle fatture assegnando a tali utenti il ruolo di gestione fatture per un profilo di fatturazione del Contratto del cliente Microsoft o del Contratto Microsoft Partner. Se è stato fornito il consenso esplicito per ricevere la fattura tramite posta elettronica, anche questi utenti riceveranno le fatture tramite posta elettronica.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.  
1. Selezionare **Profili di fatturazione** sul lato sinistro. Nell'elenco dei profili di fatturazione selezionare il profilo di fatturazione al quale si vuole assegnare un ruolo di gestione fatture.  
   ![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Selezionare **Controllo di accesso (IAM)** sul lato sinistro e quindi selezionare **Aggiungi** nella parte superiore della pagina.  
    ![Screenshot che visualizza la pagina Controllo di accesso](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Nell'elenco a discesa Ruolo selezionare **Gestione fatture**. Immettere l'indirizzo di posta elettronica dell'utente a cui concedere l'accesso. Selezionare **Salva** per assegnare il ruolo.  
   ![Screenshot che visualizza l'aggiunta di un utente con il ruolo Gestione fatture](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> Perché la fattura non è visualizzata

Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure. 
- Azure fattura alcuni giorni dopo la fine del periodo di fatturazione. È possibile quindi che una fattura non sia stata ancora generata.
- Non si è autorizzati a visualizzare le fatture.
    Se si ha un account di fatturazione del Contratto del cliente Microsoft o del Contratto Microsoft Partner, è necessario disporre di un ruolo di proprietario, collaboratore, lettore o gestione fatture nel profilo di fatturazione o del ruolo di proprietario, collaboratore o lettore nell'account di fatturazione per visualizzare le fatture. Per le altre sottoscrizioni, è possibile che le fatture non vengano visualizzate se non si è l'amministratore dell'account. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](../manage/manage-billing-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).
- Se si ha un account di fatturazione del Programma dei Microsoft Online Services (MOSP) ed è stata effettuata l'iscrizione per un account Azure gratuito o una sottoscrizione con un importo del credito mensile, si riceverà la fattura solo quando viene superato l'importo del credito mensile. Se si ha un account di fatturazione del Contratto del cliente Microsoft o del Contratto Microsoft Partner, si riceverà sempre una fattura.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sugli addebiti, vedere:

- [Visualizzare e scaricare l'utilizzo e gli addebiti di Microsoft Azure](download-azure-daily-usage.md)
- [Comprendere la fattura per Microsoft Azure](review-individual-bill.md)
- [Informazioni sui termini della fattura di Azure](understand-invoice.md)

Se si dispone di un Contratto del cliente Microsoft, vedere:

- [Informazioni sugli addebiti nella fattura del profilo di fatturazione](review-customer-agreement-bill.md)
- [Informazioni sui termini nella fattura per il profilo di fatturazione](mca-understand-your-invoice.md)
- [Informazioni sul file dei dati di utilizzo e addebiti di Azure per il profilo di fatturazione](mca-understand-your-usage.md)


