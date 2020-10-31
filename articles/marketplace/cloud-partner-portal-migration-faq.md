---
title: Domande frequenti sulla transizione dalla portale Cloud Partner al centro per i partner-Microsoft Commercial Marketplace
description: Risposte alle domande frequenti sulle offerte di transizione da portale Cloud Partner al centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 8bedecd4a050c445af0ab0ae0250197369b9173b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131412"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Domande frequenti sulla transizione dalla portale Cloud Partner al centro per i partner

Il portale Cloud Partner è passato al centro per i partner. Il centro per i partner offre un'esperienza integrata e semplificata per la pubblicazione di nuove offerte in [Microsoft AppSource](https://appsource.microsoft.com/) o [Azure Marketplace](https://azuremarketplace.microsoft.com/) e per la gestione delle offerte esistenti di cui è stata eseguita la migrazione dalla portale cloud partner. Tutte le funzioni del portale Cloud Partner sono disponibili nel centro per i partner. Questo articolo descrive le domande frequenti su questo argomento.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Cosa significa la transizione al centro per i partner?

È possibile continuare a lavorare nel centro per i partner:

| Area<img src="" width=200px> | Modifiche |
| --- | --- |
| Account | Non è necessario creare un nuovo account del centro per i partner; è possibile usare le credenziali di portale Cloud Partner esistenti per accedere al centro per i partner, in cui si gestirà l'account, gli utenti, le autorizzazioni e la fatturazione. Il contratto di pubblicazione e le informazioni sul profilo della società vengono migrati al nuovo account del centro per i partner, insieme alle informazioni sul profilo di pagamento, agli account utente e alle autorizzazioni e alle offerte attive. Per altre informazioni, vedere [gestire l'account del Marketplace commerciale nel centro per i partner](partner-center-portal/manage-account.md). |
| Offrire un'esperienza di gestione di pubblicazione e offerta | I dati dell'offerta sono stati spostati dal portale Cloud Partner al centro per i partner. Ora potrai accedere alle tue offerte nel centro per i partner, che offre un'esperienza utente migliorata e un'interfaccia intuitiva. Informazioni su come [aggiornare un'offerta esistente nel Marketplace commerciale](partner-center-portal/update-existing-offer.md). |
| Disponibilità delle offerte nel Marketplace commerciale | Nessuna modifica. Se l'offerta è Live nel Marketplace commerciale, continuerà a essere attiva. |
| Nuovi acquisti e distribuzioni | Nessuna modifica. I clienti possono continuare l'acquisto e la distribuzione delle offerte senza interruzioni. |
| Proventi | Eventuali acquisti e distribuzioni continueranno a essere pagati come di consueto. Scopri di più su [come ricevere pagamenti nel Marketplace commerciale](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| Integrazioni di API con [API del portale Cloud Partner esistenti](cloud-partner-portal-api-overview.md) | Le API di portale Cloud Partner esistenti sono ancora supportate e le integrazioni esistenti continuano a funzionare. Per altre informazioni, vedere l'articolo relativo alle [API REST di portale cloud partner.](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analytics | Puoi continuare a monitorare le vendite, valutare le prestazioni e ottimizzare le tue offerte nel Marketplace commerciale visualizzando le analisi nel centro per i partner. Esistono differenze tra la modalità di visualizzazione dei report di analisi in CPP e nel centro per i partner. Ad esempio, **Seller Insights** in CPP dispone di una scheda **Orders & Usage** che Visualizza i dati per le offerte basate sull'utilizzo e le offerte non basate sull'utilizzo, mentre nel centro per i partner la pagina **Orders** include una scheda separata per le offerte SaaS. Per altre informazioni, vedere [Access Analytics Reports for the Commercial Marketplace in Partner Center](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>È necessario creare un nuovo account per gestire le offerte nel centro per i partner?

No, l'account verrà mantenuto. Ciò significa che se si è un partner esistente, è possibile usare le credenziali dell'account di portale Cloud Partner esistente per accedere al centro per i partner. Non creare un nuovo account o eventuali offerte create in portale Cloud Partner e non verranno associate al centro per i partner.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quali pagine del Centro per i partner corrispondono alle pagine usate nel portale Cloud Partner?

Di seguito sono riportati i collegamenti al centro per i partner per le pagine utilizzate comunemente nella portale Cloud Partner. Se sono stati salvati i collegamenti portale Cloud Partner come segnalibri, sarà necessario aggiornarli.

| Pagina del portale per Cloud Partner <img src="" width=100px>| Collegamento alla pagina portale Cloud Partner | Collegamento alla pagina del centro per i partner |
| --- | --- | --- |
| Pagina Tutte le offerte | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Pagina Tutti gli autori | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Profilo di pubblicazione | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Pagina Users (Utenti) | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Pagina Cronologia | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Il Centro per i partner non supporta ancora la funzionalità Cronologia. |
| Dashboard Informazioni cognitive dettagliate | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Report sui proventi | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Differenze del report sui proventi

Queste sono le differenze nel report dei pagamenti tra il portale Cloud Partner ritirato e il centro per partner corrente:

| Portale per Cloud Partner | Centro per i partner |
| --- | --- |
| **Link** : https://cloudpartner.azure.com/ | **Link** : https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory e https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation** : i report sui proventi sono disponibili in Insights Proventi | **Navigation** : i report sui proventi sono disponibili nel Centro per i partner - icona Proventi |
| **Ambito** :<ul><li>È visibile la transazione per ogni voce per le riscossioni in corso, incassate e pagate.</li><li>Report: tutte le voci vengono visualizzate dopo la creazione dell'ordine d'acquisto, incluse le riscossioni e le fatturazioni in corso, lo stato della riscossione e le voci non ancora idonee per il pagamento.</li></ul> | **Ambito** :<ul><li>Visualizza le voci dopo che sono state dichiarate redditi idonei.</li><li>I clienti eseguono prima il pagamento a favore di Microsoft e quindi i fornitori di software indipendenti possono iniziare a vedere il report sui proventi.</li><li>Nel report sui proventi non vengono visualizzate le riscossioni e le fatturazioni in corso.</li></ul> |
| **La transazione non è pronta per ricevere i proventi** : fatturazione in corso | **La transazione non è pronta per ricevere i proventi** : pagamento successivo stimato: i proventi sono nello stato non elaborato. |
| **Stato dei proventi** : n/d | **Stato dei proventi** :<ul><li>Non elaborato: il reddito è idoneo al pagamento.</li><li>Imminente: il reddito verrà inviato all'editore nei proventi mensili successivi.</li><li>Inviato: il pagamento è stato inviato alla banca.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Quali sono le offerte pubblicate nel portale Cloud Partner?

Le offerte sono state spostate nel centro per i partner ed è possibile accedervi dopo aver effettuato l'accesso al centro per i partner, ad eccezione del servizio gestito di Dynamics NAV e delle offerte Cortana Intelligence. Se l'offerta è stata rilasciata nel Marketplace commerciale, continuerà a essere in diretta e i clienti continueranno a acquistarla e distribuirla senza interruzioni. Vedere la domanda successiva, **quali offerte sono state spostate nel centro per i partner?** , per altri dettagli.

## <a name="what-offers-were-moved-to-partner-center"></a>Quali offerte sono state spostate nel centro per i partner?

Tutti i tipi di offerta precedentemente supportati nel portale Cloud Partner sono supportati nel centro per i partner, fatta eccezione per il servizio gestito di Dynamics NAV e per Cortana Intelligence offerte.

Per i tipi di offerta supportati nel centro per i partner, tutte le offerte sono state spostate indipendentemente dallo stato; sono state spostate anche le offerte Draft, delist e Preview.

| Tipo di offerta <img src="" width=150px>| Spostati nel centro per i partner? <img src="" width=100px>| Passaggi successivi |
| --- | --- | --- |
| SaaS | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [pianificare un'offerta SaaS per il Marketplace commerciale](plan-saas-offer.md). |
| Macchina virtuale | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [pianificare un'offerta di macchina virtuale](marketplace-virtual-machines.md). |
| Applicazione Azure | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'offerta di applicazione Azure](partner-center-portal/create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'offerta di Dynamics 365 business Central](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 per Customer Engagement & PowerApps | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un Dynamics 365 per Customer Engagement & offerta PowerApps](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'offerta di Dynamics 365 per le operazioni](partner-center-portal/create-new-operations-offer.md). |
| App Power BI | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'app Power BI per AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| Modulo IoT Edge | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni [, vedere creare, configurare e pubblicare un'offerta di modulo IOT Edge in Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Contenitore | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'offerta di contenitore di Azure](partner-center-portal/create-azure-container-offer.md). |
| Servizio di consulenza | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'offerta di servizi di consulenza](partner-center-portal/create-consulting-service-offer.md). |
| Servizio gestito | Sì | Accedere al centro per i partner per creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, vedere [creare un'offerta di servizio gestito](partner-center-portal/create-new-managed-service-offer.md). |
| Servizio gestito di Dynamics NAV | No | Microsoft ha sviluppato il servizio gestito di Dynamics NAV in [dynamics 365 business Central](/dynamics365/business-central/), quindi è stato annullato l'elenco delle offerte Live di Dynamics NAV gestito da AppSource. Queste offerte non sono più individuabili dai clienti e non sono state spostate nel centro per i partner. Per rendere disponibili le offerte in AppSource, adattarle alle offerte di Dynamics 365 business Central e inviarle al centro per i [partner](https://partner.microsoft.com/). Per altre informazioni, vedere [creare un'offerta di Dynamics 365 business Central](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | No | Microsoft ha sviluppato la mappa stradale del prodotto per Cortana Intelligence, quindi è stato annullato l'elenco Cortana Intelligence offerte Live di AppSource. Queste offerte non sono più individuabili dai clienti e non sono state spostate nel centro per i partner. Per rendere disponibili le offerte nel Marketplace commerciale, adattare le offerte alle offerte SaaS (software as a Service) e inviarle al centro per i [partner](https://partner.microsoft.com/). Per altre informazioni, vedere l'elenco di controllo per la [creazione di un'offerta SaaS nel centro](./plan-saas-offer.md) |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Le offerte di portale Cloud Partner non sono disponibili nel centro per i partner

Ciò che viene visualizzato nel centro per i partner dipende dai programmi registrati in, dagli account a cui si appartiene e dalle autorizzazioni e dai ruoli utente assegnati. Sono disponibili numerosi programmi per il centro per i partner e l'utente può essere registrato in più programmi. È anche possibile accedere a più account con le stesse credenziali utente.

Le offerte create nel portale Cloud Partner sono disponibili nel centro per i partner del programma **commerciale Marketplace** e con l'account usato per creare le offerte. Per assicurarsi di visualizzare il programma corretto e l'account corretto, attenersi alla procedura riportata di seguito. Per altri suggerimenti sulla risoluzione dei problemi, vedere [gestire l'account del centro per i partner](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Accedi al programma corretto nel centro per i partner

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) con le stesse credenziali usate per accedere al portale cloud partner. Il riquadro di spostamento a sinistra consente di visualizzare le opzioni associate ai programmi iscritti in.

    Esempio: si supponga di avere accesso a tre programmi: il programma MPN, il programma di riferimento e il programma commerciale Marketplace. Quando si accede a partner Center, questi tre programmi vengono visualizzati nel riquadro di spostamento.

2. Selezionare **Commercial Marketplace**  >  **Panoramica** sul Marketplace commerciale per accedere alle offerte.

    Se il programma Commercial Marketplace non è visibile nel riquadro di spostamento a sinistra, è possibile che l'account sia errato. Per accedere all'account corretto, seguire i passaggi della sezione successiva.

    [![Screenshot che mostra il menu di panoramica del centro per i partner](media/cpp-pc-faq/overview-menu.png "Visualizza il menu Panoramica del centro per i partner")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Accedere all'account corretto nel centro per i partner

Se si fa parte di più account, nel centro per i partner verrà visualizzato un pulsante di selezione account contrassegnato da due frecce nel menu di spostamento a sinistra. Selezionare il pulsante di selezione account per visualizzare un elenco di tutti gli account a cui si appartiene. Selezionare un account nell'elenco per passare a esso e visualizzare tutti i programmi e le informazioni relative a tale account. Se nel menu di navigazione non viene visualizzato un pulsante di selezione account, l'utente è membro di un singolo account.

[![Screenshot che mostra il pulsante di selezione account del centro per i partner.](media/cpp-pc-faq/picker-button.png "Mostra il pulsante di selezione account del centro per i partner")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Ricerca per categorie creare nuove offerte?

Accedi al programma Commercial Marketplace nel [centro](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) per i partner per creare nuove offerte. Nella pagina Panoramica selezionare **+ nuova offerta** .

[![Screenshot che mostra il menu Panoramica del centro per i partner.](media/cpp-pc-faq/new-offer.png "Visualizza il menu Panoramica del centro per i partner")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Non è possibile accedere ed è necessario aprire un ticket di supporto

Se non è possibile accedere al proprio account, è possibile aprire un [ticket di supporto](https://partner.microsoft.com/support/v2/?stage=1) qui.

## <a name="where-are-instructions-for-using-partner-center"></a>Dove sono le istruzioni per l'uso del centro per i partner?

Visitare la [documentazione del Marketplace commerciale](index.yml), quindi espandere il **portale del Marketplace commerciale nel centro per i partner** . Per visualizzare gli articoli della Guida per la creazione di offerte nel centro per i partner, espandere **creare una nuova offerta** .

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Quali sono le differenze di gestione di pubblicazione e offerta?

Di seguito sono riportate alcune differenze tra il portale Cloud Partner e il centro per i partner.

### <a name="modular-publishing-capabilities"></a>Funzionalità di pubblicazione modulari

Il Centro per i partner offre un'opzione di pubblicazione modulare che consente di selezionare le modifiche da pubblicare anziché pubblicare sempre tutti gli aggiornamenti in una sola volta. Ad esempio, la schermata seguente mostra che le uniche modifiche selezionate per la pubblicazione sono le modifiche apportate alle **Proprietà** e all'  **elenco di offerte** . Le modifiche apportate nella pagina di anteprima non verranno pubblicate.

[![Screenshot che mostra la pagina di revisione e pubblicazione del centro per i partner.](media/cpp-pc-faq/review-page.png "Mostra la pagina di revisione e pubblicazione del centro per i partner")](media/cpp-pc-faq/review-page.png#lightbox)

Gli aggiornamenti che non vengono pubblicati vengono salvati come bozze. Continuare a usare l'anteprima dell'offerta per verificare l'offerta prima di renderla pubblica.

### <a name="enhanced-preview-options"></a>Opzioni di anteprima ottimizzate

Il Centro per i partner include una [funzionalità di confronto](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) con opzioni di filtro ottimizzate. Questo consente di confrontare le versioni di anteprima e live dell'offerta.

[![Screenshot mostra la funzionalità di confronto del centro per i partner.](media/cpp-pc-faq/compare.png "Mostra la funzionalità di confronto del centro per i partner")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Personalizzazione e modifiche di navigazione

Si noteranno alcune modifiche di personalizzazione. Ad esempio, gli *SKU* vengono personalizzati come *piani* nel centro per i partner:

[![Screenshot che mostra la pagina dei piani del centro per i partner.](media/cpp-pc-faq/plans.png "Mostra la pagina dei piani del centro per i partner")](media/cpp-pc-faq/plans.png#lightbox)

Inoltre, le informazioni fornite in precedenza nel **Marketplace** o nelle pagine dei **Dettagli della vetrina**  (servizio di consulenza, Power BI app) nell'portale cloud partner vengono ora raccolte nella pagina di presentazione dell' **offerta** nel centro per i partner:

[! [Screenshot mostra la pagina dell'elenco di offerte del centro per i partner.] (media/cpp-PC-domande frequenti/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Le informazioni fornite in precedenza per gli SKU in una singola pagina del portale Cloud Partner possono ora essere raccolte in diverse pagine del centro per i partner:

- Pagina di configurazione del piano
- Pagina Elenco piani
- Pagina Disponibilità del piano
- Pianificare la pagina configurazione tecnica, come illustrato di seguito:

[![Mostra la pagina di configurazione tecnica del centro per i partner.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

L'ID offerta è ora visualizzato sulla barra di spostamento a sinistra dell'offerta:

![Mostra la località dell'ID offerta del centro per i partner](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Interrompere la vendita di un'offerta

È possibile chiedere di [interrompere la vendita di un'offerta](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) sul marketplace direttamente dal portale del Centro per i partner. L'opzione è disponibile nella pagina **Panoramica offerta** per l'offerta.

[![Screenshot che mostra la pagina Centro partner per interrompere la vendita di un'offerta.](media/cpp-pc-faq/stop-sell.png "Mostra la pagina del centro per i partner per interrompere la vendita di un'offerta")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Le API REST di portale Cloud Partner sono ancora supportate?

Le API portale Cloud Partner sono integrate con il centro per i partner e continueranno a funzionare. La transizione al centro per i partner introduce piccole modifiche. Esaminare la tabella seguente per assicurarsi che il codice continui a funzionare nel centro per i partner.

| API <img src="" width=100px>| Descrizione delle modifiche | Impatto |
| --- | --- | --- |
| Pubblicazione POST, GoLive, annullamento | Per le offerte migrate, l'intestazione della risposta avrà un formato diverso ma continuerà a funzionare allo stesso modo, indicando un percorso relativo per recuperare lo stato dell'operazione. | Quando si inviano le richieste POST corrispondenti per un'offerta, l'intestazione Location avrà uno dei due formati a seconda dello stato di migrazione dell'offerta: <ul><li>Offerte senza migrazione: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offerte migrate: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Operazione GET | Per le offerte che in precedenza supportavano un campo "Notification-email" nella risposta, questo campo sarà deprecato e non verrà più restituito per le offerte migrate. | Per le offerte migrate, non verranno più inviate notifiche all'elenco dei messaggi di posta elettronica specificati nelle richieste. Al contrario, il servizio API si allineerà con il processo di posta elettronica di notifica nel Centro per i partner per inviare messaggi di posta elettronica. In particolare, le notifiche dello stato di avanzamento dell'operazione verranno inviate all'indirizzo di posta elettronica impostato nella sezione informazioni di contatto del venditore delle impostazioni dell'account nel centro per i partner.<br><br>Verificare che l'indirizzo di posta elettronica impostato nella sezione informazioni di contatto del venditore nelle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/management) nel centro partner sia corretto per la ricezione delle notifiche. |
|||