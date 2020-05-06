---
title: Domande frequenti sulla migrazione al centro per i partner | Azure Marketplace
description: Questo articolo descrive le domande frequenti sulla migrazione di offerte da portale Cloud Partner al centro per i partner.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "81274380"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Domande frequenti per la migrazione dal portale Cloud Partner al centro per i partner

Questo articolo descrive le domande frequenti sulla migrazione di offerte dal portale Cloud Partner al centro per i partner.

## <a name="what-does-offer-migration-mean"></a>Cosa significa la migrazione dell'offerta?

I dati dell'offerta vengono spostati dal portale Cloud Partner al centro per i partner, con modifiche nell'esperienza di pubblicazione e gestione delle offerte.

| Area  | Modifiche  |
|-------|----------|
| **Esperienza di gestione di pubblicazione e offerta** | Potrai avere un'esperienza utente migliorata con un'interfaccia intuitiva nel centro per i partner. Per informazioni dettagliate, vedere [quali sono le differenze tra il centro per i partner e la portale cloud partner?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilità delle offerte nel Marketplace** | Nessuna modifica. Se l'offerta è Live nel Marketplace, continuerà a essere attiva durante e al termine della migrazione. |
| **Nuovi acquisti e distribuzioni** | Nessuna modifica. I clienti continueranno a essere in grado di acquistare e distribuire le offerte senza interruzioni. |
| **Proventi** | Eventuali acquisti e distribuzioni che si verificano durante o dopo la migrazione continueranno a essere pagati come di consueto. |
|**Integrazioni di API con [API di portale cloud partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) esistenti** | Le API di portale Cloud Partner esistenti continueranno a essere supportate dopo la migrazione e le integrazioni esistenti continueranno a funzionare. Per informazioni dettagliate, vedere [la pagina relativa alle API REST di portale cloud partner essere supportate dopo la migrazione.](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>È ancora possibile accedere alla portale Cloud Partner e gestire le offerte durante la migrazione?

Le offerte verranno migrate al centro per i partner a partire dal 13 aprile per tutto il mese di maggio. Durante questo periodo di tempo potrai accedere al portale Cloud Partner come di consueto, ad eccezione del momento in cui l'offerta è stata pianificata per la migrazione.
Mentre è in corso la migrazione delle offerte, lo stato sarà "bloccato-passaggio al centro per i partner", come illustrato nello screenshot seguente. Il periodo di migrazione deve essere inferiore a 24 ore. Durante questo periodo, non sarà possibile apportare aggiornamenti alle offerte. Dopo aver completato la migrazione delle offerte, si riceverà una notifica tramite posta elettronica.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Viene illustrato lo stato delle offerte migrate.":::

Dopo la migrazione delle offerte, saranno in modalità di sola lettura **per un periodo di tempo limitato** nel portale cloud partner. Il relativo stato mostrerà "spostato al centro per i partner" e includerà un collegamento all'offerta nel centro per i partner, come illustrato nelle schermate seguenti. Da questo punto, sarà possibile gestire gli aggiornamenti a tutte le offerte o creare nuove offerte esclusivamente tramite il centro per i partner,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Viene illustrato il messaggio fornito per le offerte di cui è stata eseguita la migrazione al centro per i partner":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Viene illustrata la pagina portale Cloud Partner per un'offerta migrata.":::

## <a name="how-will-i-create-new-offers"></a>In che modo è possibile creare nuove offerte?

Dal portale Cloud Partner si verrà reindirizzati alla creazione di nuove offerte nel centro per i partner

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Viene illustrato il menu per creare una nuova offerta in portale Cloud Partner":::

Dopo aver selezionato una nuova offerta, verrà visualizzato un messaggio simile al seguente.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustra il messaggio ricevuto durante la creazione di una nuova offerta in CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>È necessario creare un nuovo account per gestire le offerte nel centro per I partner?

No. L'account sottostante verrà mantenuto e dovrebbe essere già gestito nel centro per i partner. Ciò significa che, se si è un partner esistente, è possibile usare le credenziali dell'account di portale Cloud Partner esistenti per accedere alla post-migrazione di partner Center. Solo le offerte e l'esperienza di gestione associata passano dalla portale Cloud Partner al centro per i partner. Si chiede di non creare nuovi account perché le offerte non saranno associate al nuovo account.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Viene visualizzato un messaggio nel portale Cloud Partner per attivare il mio account, che cosa significa?

Per eseguire correttamente la migrazione dell'account al centro per i partner, sono necessari altri dettagli per consentire la gestione delle offerte nel centro per i partner al termine della migrazione dell'offerta. Per ulteriori informazioni sull'attivazione dell'account, vedere la pagina relativa [alla migrazione degli account da portale cloud partner al centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

I passaggi necessari per completare l'attivazione dell'account variano in base al ruolo dell'account.

| Ruolo account | Procedura di attivazione |
|--------------|----------------|
|Proprietario | Passare alla pagina del [profilo di pubblicazione](https://cloudpartner.azure.com/#profile) nel portale cloud partner e quindi fare clic sul collegamento nel banner per attivarlo. Si verrà reindirizzati al centro per i partner per completare l'attivazione dell'account. |
| Collaboratore | L'account può essere attivato solo da un utente dell'account con un ruolo proprietario. Contattare i proprietari dell'account per completare l'attivazione dell'account. I proprietari dell'account devono essere elencati nel messaggio del banner. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Si verificano problemi durante l'accesso all'account e l'apertura di un ticket di supporto

Se non è possibile accedere all'account, è possibile aprire un [ticket di supporto](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Dove è possibile trovare la documentazione sulla nuova esperienza di pubblicazione del centro per i partner?

Visitare la [documentazione del Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/). Espandere il **portale del Marketplace commerciale nel centro**  > per i partner per**creare una nuova offerta** per visualizzare gli argomenti della Guida per la creazione di ogni tipo di offerta.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustra gli argomenti della Guida per il centro per i partner":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quali sono le differenze tra il centro per i partner e la portale Cloud Partner?

È possibile notare le differenze seguenti tra il portale Cloud Partner e il centro per i partner.

### <a name="modular-publishing-capabilities"></a>Funzionalità di pubblicazione modulari

Il centro per i partner fornisce un'opzione di pubblicazione modulare che consente di selezionare le modifiche che si desidera pubblicare anziché pubblicare sempre tutti gli aggiornamenti in una sola volta. Ad esempio, lo screenshot seguente mostra che le uniche modifiche selezionate per la pubblicazione sono le modifiche apportate alle **Proprietà** e all' **elenco di offerte**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustra la pagina di anteprima e pubblicazione":::

Gli aggiornamenti che non vengono pubblicati vengono salvati come bozze. Continuare a usare l'anteprima dell'offerta per verificare l'offerta prima di renderla pubblica.

### <a name="rich-text-format"></a>Formato RTF

Migliorare l'offerta e la descrizione del piano usando l'editor di testo completo nella pagina elenco offerte e elenco di piani.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustra l'editor di testo RTF":::

### <a name="enhanced-preview-options"></a>Opzioni di anteprima avanzate

Il centro per i partner include una [funzionalità di confronto](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) con opzioni di filtro migliorate. Questo consente di confrontare le versioni in anteprima e in tempo reale dell'offerta.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustra la funzionalità di confronto":::

### <a name="branding-and-navigation-changes"></a>Personalizzazione e modifiche di navigazione

Si noteranno alcune modifiche di personalizzazione. Ad esempio, "SKU" sono marchiati come "piani" nel centro per i partner.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Viene illustrata la panoramica del piano.":::

Inoltre, le informazioni utilizzate per fornire le pagine dei dettagli del **Marketplace** o del**torefront** (servizio di consulenza, app Power BI) nell'portale cloud partner vengono raccolte nella pagina di presentazione dell' **offerta** nel centro per i partner.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Viene illustrata la pagina di inserzione dell'offerta.":::

Le informazioni usate per fornire gli SKU in una singola pagina del portale Cloud Partner possono ora essere raccolte in diverse pagine del centro per i partner:

* Pagina di configurazione del piano
* Pagina di elenco del piano
* Pagina della disponibilità del piano
* Pianificare la pagina di configurazione tecnica, come illustrato in questa schermata.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Viene illustrata la pagina configurazione tecnica del piano.":::

L'ID offerta viene ora visualizzato sulla barra di spostamento a sinistra dell'offerta.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Viene illustrato il menu di spostamento a sinistra con l'ID offerta.":::

### <a name="stop-selling-an-offer"></a>Interrompi la vendita di un'offerta

È possibile richiedere di [interrompere la vendita di un'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) sul Marketplace direttamente dal portale del centro per i partner. L'opzione è disponibile nella pagina **Panoramica** dell'offerta per l'offerta.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Viene illustrata la pagina Panoramica dell'offerta con l'opzione stop selling.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quali pagine del centro per i partner corrispondono alle pagine utilizzate nella portale Cloud Partner?

La tabella seguente illustra i collegamenti corrispondenti tra i due portali.

| Pagina | Collegamento portale Cloud Partner | Collegamento al centro per i partner |
|------|---------------------------|---------------------|
| **Pagina Tutte le offerte** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Pagina Tutti gli autori** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profilo di pubblicazione** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Pagina Users (Utenti)** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Pagina Cronologia** | https://cloudpartner.azure.com/#history | La funzionalità cronologia non è ancora supportata nel centro per i partner. |
| **Dashboard di Insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Report sui pagamenti** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Le API REST di portale Cloud Partner saranno supportate dopo la migrazione?

Le API portale Cloud Partner sono integrate con il centro per i partner e continueranno a funzionare dopo la migrazione delle offerte al centro per i partner. L'integrazione introduce piccole modifiche. Esaminare le modifiche nella tabella seguente per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

| **API** | **Descrizione modifica:** | **Impatto** |
| ------- | ---------------------- | ---------- |
| POST Publish, tentare, Cancel | Per le offerte migrate, l'intestazione della risposta avrà un formato diverso ma continuerà a funzionare nello stesso modo, indicando un percorso relativo per recuperare lo stato dell'operazione. | Quando si inviano le richieste POST corrispondenti per un'offerta, l'intestazione Location avrà uno dei due formati seguenti a seconda dello stato di migrazione dell'offerta:<ul><li>Offerte non migrate<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offerte migrate<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operazione GET | Per le offerte che in precedenza supportano il campo ' Notification-email ' nella risposta, questo campo sarà deprecato e non verrà più restituito per le offerte migrate. | Per le offerte migrate, non verranno più inviate notifiche all'elenco dei messaggi di posta elettronica specificati nelle richieste. Al contrario, il servizio API si allineerà con il processo di posta elettronica di notifica nel centro partner per inviare messaggi di posta elettronica. In particolare, le notifiche verranno inviate all'indirizzo di posta elettronica impostato nella sezione informazioni di contatto del venditore delle impostazioni dell'account nel centro per i partner, per notificare l'avanzamento dell'operazione.<br><br>Controllare l'indirizzo di posta elettronica impostato nella sezione informazioni di contatto del venditore nelle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/management) nel centro per i partner per assicurarsi che venga fornita la posta elettronica corretta per le notifiche.  |
| | | |
