---
title: Domande frequenti sulla migrazione al Centro per i partner - Marketplace commerciale Microsoft
description: Risposte alle domande frequenti sulla migrazione di offerte dal portale Cloud Partner al Centro per i partner.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727479"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Domande frequenti sulla migrazione dal portale Cloud Partner al Centro per i partner

Questo articolo illustra alcune domande frequenti sulla migrazione di offerte dal portale Cloud Partner al Centro per i partner.

## <a name="what-does-offer-migration-mean"></a>Cosa si intende per migrazione di un'offerta?

I dati dell'offerta vengono spostati dal portale Cloud Partner al Centro per i partner, con modifiche nell'esperienza di pubblicazione e gestione dell'offerta.

| Area  | Modifiche  |
|-------|----------|
| **Esperienza di gestione e pubblicazione di offerte** | Sarà possibile godere di un'esperienza utente migliorata con un'interfaccia intuitiva nel Centro per i partner. Per informazioni dettagliate, vedere [Quali sono le differenze tra il Centro per i partner e il portale Cloud Partner?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilità delle offerte nel marketplace** | Nessuna modifica. Se l'offerta è attiva nel marketplace, continuerà a esserlo durante e al termine della migrazione. |
| **Nuovi acquisti e distribuzioni** | Nessuna modifica. I clienti continueranno a poter acquistare e distribuire le offerte senza interruzioni. |
| **Proventi** | Eventuali acquisti e distribuzioni che si verificano durante o dopo la migrazione continueranno a essere pagati come di consueto. |
|**Integrazioni di API con [API del portale Cloud Partner esistenti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Le API del portale Cloud Partner esistenti continueranno a essere supportate dopo la migrazione e le integrazioni esistenti continueranno a funzionare. Per informazioni dettagliate, vedere [Le API REST del portale Cloud Partner sono supportate dopo la migrazione?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>È ancora possibile accedere al portale Cloud Partner e gestire le offerte durante la migrazione?

Tutte le offerte sono state migrate nel Centro per i partner. Il periodo di migrazione per ogni offerta è stato inferiore a 24 ore. Dopo la migrazione dell'offerta, è stata ricevuta una notifica di posta elettronica.

Dopo la migrazione, le offerte saranno in modalità di sola lettura **per un periodo di tempo limitato** nel portale Cloud Partner. Il relativo stato mostrerà "Spostato nel Centro per i partner" e includerà un collegamento all'offerta nel Centro per i partner, come illustrato nelle schermate seguenti. Da questo punto in poi, sarà possibile gestire gli aggiornamenti a tutte le offerte o creare nuove offerte esclusivamente tramite il Centro per i partner.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Illustra il messaggio fornito per le offerte di cui è stata eseguita la migrazione al Centro per i partner":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Illustra la pagina del portale Cloud Partner per un'offerta migrata.":::

## <a name="how-will-i-create-new-offers"></a>In che modo è possibile creare nuove offerte?

Dal portale Cloud Partner si verrà reindirizzati alla creazione di nuove offerte nel Centro per i partner

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Illustra il menu per creare una nuova offerta nel portale Cloud Partner":::

Dopo aver selezionato una nuova offerta, verrà visualizzato un messaggio simile al seguente.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustra il messaggio ricevuto durante la creazione di una nuova offerta in CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>È necessario creare un nuovo account per gestire le offerte nel Centro per I partner?

No. L'account sottostante verrà mantenuto e sarà già gestito nel Centro per i partner. Ciò significa che, se si è un partner esistente, è possibile usare le credenziali dell'account del portale Cloud Partner esistenti per accedere alla post-migrazione nel Centro per i partner. Dal portale Cloud Partner al Centro per i partner vengono trasferite solo le offerte e l'esperienza di gestione associata. Non creare nuovi account, poiché le offerte non saranno associate ad essi.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Nel portale Cloud Partner viene visualizzato un messaggio che chiede di attivare l'account. Che cosa significa?

Per eseguire correttamente la migrazione dell'account al Centro per i partner sono necessari altri dettagli, così da consentire la gestione delle offerte nel Centro per i partner al termine della migrazione dell'offerta. Per altri dettagli sull'attivazione dell'account, vedere [Migrazione degli account dal portale Cloud Partner al Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

I passaggi necessari per completare l'attivazione dell'account variano in base al ruolo dell'account.

| Ruolo dell'account | Passaggi di attivazione |
|--------------|----------------|
|Proprietario | Passare alla pagina [Profilo di pubblicazione](https://cloudpartner.azure.com/#profile) nel portale Cloud Partner, quindi fare clic sul collegamento nel banner per attivarlo. Per completare l'attivazione dell'account si verrà reindirizzati al Centro per i partner. |
| Collaboratore | L'account può essere attivato solo da un utente dell'account con un ruolo proprietario. Per completare l'attivazione dell'account, contattare i proprietari dell'account. I proprietari dell'account devono essere elencati nel messaggio del banner. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Si verificano problemi durante l'accesso all'account e l'apertura di un ticket di supporto

Se non è possibile accedere all'account, è possibile aprire un [ticket di supporto](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Dove è possibile trovare la documentazione relativa alle nuove esperienze di pubblicazione del Centro per i partner?

Consultare la [documentazione del marketplace commerciale](https://docs.microsoft.com/azure/marketplace/). Espandere quindi **Portale del marketplace commerciale nel Centro per i partner**  > **Crea una nuova offerta** per visualizzare gli argomenti della guida per la creazione di ogni tipo di offerta.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustra gli argomenti della guida del Centro per i partner":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quali sono le differenze tra il Centro per i partner e il portale Cloud Partner?

Tra il portale Cloud Partner e il Centro per i partner è possibile notare le differenze seguenti.

### <a name="modular-publishing-capabilities"></a>Funzionalità di pubblicazione modulari

Il Centro per i partner offre un'opzione di pubblicazione modulare che consente di selezionare le modifiche da pubblicare anziché pubblicare sempre tutti gli aggiornamenti in una sola volta. Ad esempio, lo screenshot seguente mostra che le uniche modifiche selezionate per la pubblicazione sono le modifiche apportate a **Proprietà** ed **Elenco di offerte**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustra la pagina di anteprima e pubblicazione":::

Gli aggiornamenti che non vengono pubblicati vengono salvati come bozze. Continuare a usare l'anteprima dell'offerta per verificare l'offerta prima di renderla pubblica.

### <a name="rich-text-format"></a>Formato RTF

Migliorare l'offerta e la descrizione del piano usando l'editor di testo completo nella pagina Presentazione offerta ed Elenco piani.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustra l'editor di testo RTF":::

### <a name="enhanced-preview-options"></a>Opzioni di anteprima ottimizzate

Il Centro per i partner include una [funzionalità di confronto](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) con opzioni di filtro ottimizzate. Questo consente di confrontare le versioni di anteprima e live dell'offerta.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustra la funzionalità di confronto":::

### <a name="branding-and-navigation-changes"></a>Personalizzazione e modifiche di navigazione

Si noteranno alcune modifiche di personalizzazione. Ad esempio, gli "SKU" sono marchiati come "Piani" nel Centro per i partner.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Illustra la panoramica del piano.":::

Le informazioni usate per le pagine **Marketplace** o **Dettagli vetrina** (servizio di consulenza, app Power BI) del portale Cloud Partner vengono raccolte nella pagina **Presentazione offerta** nel Centro per i partner.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Illustra la pagina Presentazione offerta.":::

Le informazioni usate per gli SKU in una singola pagina del portale Cloud Partner possono ora essere raccolte in diverse pagine del Centro per i partner:

* Pagina Configura piano
* Pagina Elenco piani
* Pagina Disponibilità del piano
* Pagina Configurazione tecnica del piano, come illustrata in questa schermata.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Illustra la pagina Configurazione tecnica del piano.":::

L'ID offerta viene ora visualizzato sulla barra di navigazione a sinistra dell'offerta.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Illustra l'immagine del menu di spostamento di sinistra, contenente l'ID offerta.":::

### <a name="stop-selling-an-offer"></a>Interrompere la vendita di un'offerta

È possibile chiedere di [interrompere la vendita di un'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) sul marketplace direttamente dal portale del Centro per i partner. L'opzione è disponibile nella pagina **Panoramica offerta** per l'offerta.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Illustra la pagina Panoramica offerta con l'opzione di interruzione della vendita.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quali pagine del Centro per i partner corrispondono alle pagine usate nel portale Cloud Partner?

La tabella seguente illustra i collegamenti corrispondenti tra i due portali.

| Pagina | Collegamento al portale Cloud Partner | Collegamento al Centro per i partner |
|------|---------------------------|---------------------|
| **Pagina Tutte le offerte** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Pagina Tutti gli autori** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profilo di pubblicazione** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Pagina Utenti** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Pagina Cronologia** | https://cloudpartner.azure.com/#history | Il Centro per i partner non supporta ancora la funzionalità Cronologia. |
| **Dashboard Informazioni cognitive dettagliate** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Report sui proventi** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Le API REST del portale Cloud Partner sono supportate dopo la migrazione?

Le API del portale Cloud Partner sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte in esso. L'integrazione introduce piccole modifiche. Esaminare le modifiche nella tabella seguente per assicurarsi che il codice continui a funzionare dopo la migrazione al Centro per i partner.

| **API** | **Descrizione modifica** | **Impatto** |
| ------- | ---------------------- | ---------- |
| Pubblicazione POST, GoLive, annullamento | Per le offerte migrate, l'intestazione della risposta avrà un formato diverso ma continuerà a funzionare allo stesso modo, indicando un percorso relativo per recuperare lo stato dell'operazione. | Quando si inviano richieste POST corrispondenti per un'offerta, l'intestazione Percorso avrà uno dei due formati seguenti, a seconda dello stato di migrazione dell'offerta:<ul><li>Offerte non migrate<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offerte migrate<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operazione GET | Per le offerte che in precedenza supportavano il campo "Messaggio di posta elettronica di notifica" nella risposta, questo campo sarà deprecato e non verrà più visualizzato per le offerte migrate. | Per le offerte migrate, non verranno più inviate notifiche all'elenco dei messaggi di posta elettronica specificati nelle richieste. Al contrario, il servizio API si allineerà con il processo di posta elettronica di notifica nel Centro per i partner per inviare messaggi di posta elettronica. In particolare, le notifiche verranno inviate all'indirizzo di posta elettronica impostato nella sezione Informazioni account nel Centro per i partner, per notificare l'avanzamento dell'operazione.<br><br>Controllare l'indirizzo di posta elettronica impostato nella sezione informazioni di contatto del venditore [Impostazioni account](https://partner.microsoft.com/dashboard/account/management) nel Centro per i partner per assicurarsi che venga fornito l'indirizzo di posta elettronica corretta per le notifiche.  |
| | | |
