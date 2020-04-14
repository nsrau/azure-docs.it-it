---
title: Domande frequenti sulla migrazione al Centro per i partner Azure Marketplace
description: In questo articolo vengono illustrate le domande frequenti sulla migrazione delle offerte dal portale per i partner cloud al Centro per i partner.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274380"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Domande frequenti sulla migrazione dal portale per i partner cloud al Centro per i partner

In questo articolo vengono illustrate le domande frequenti sulla migrazione delle offerte dal portale per i partner cloud al Centro per i partner.

## <a name="what-does-offer-migration-mean"></a>Che cosa significa "migrazione dell'offerta"?

Stiamo spostando i dati dell'offerta dal portale Cloud Partner al Centro per i partner con modifiche nell'esperienza di pubblicazione e gestione dell'offerta.

| Area  | Modifiche  |
|-------|----------|
| **Esperienza di gestione dell'editoria e dell'offerta** | Avrai un'esperienza utente migliorata con un'interfaccia intuitiva nel Centro per i partner. Per ulteriori dettagli, vedere Quali sono le differenze tra il Centro per i [partner e il portale per i partner cloud?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilità delle tue offerte sul mercato** | Nessuna modifica. Se l'offerta è attiva nel marketplace, continuerà a essere attiva e dopo il completamento della migrazione. |
| **Nuovi acquisti e distribuzioni** | Nessuna modifica. I tuoi clienti continueranno ad essere in grado di acquistare e distribuire le tue offerte senza interruzioni. |
| **Proventi** | Tutti gli acquisti e le distribuzioni che si verificano durante o dopo la migrazione continueranno a essere pagati all'utente come di consueto. |
|**Integrazioni di API con le API del [portale Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) esistenti** | Le API del portale Cloud Partner esistenti continueranno a essere supportate dopo la migrazione e le integrazioni esistenti continueranno a funzionare. Per altre informazioni, vedere Le API REST del [portale per i partner cloud saranno supportate dopo la migrazione?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Posso ancora accedere al portale Cloud Partner e gestire le mie offerte durante la migrazione?

Le offerte verranno migrate al Centro per i partner a partire dal 13 aprile per tutto il mese di maggio. Durante questo periodo di tempo potrai accedere al portale Cloud Partner come di consueto, ad eccezione dell'ora pianificata per la migrazione.
Durante la migrazione delle offerte, lo stato "Bloccato – si sposta al Centro per i partner", come illustrato nella schermata seguente. Questo periodo di migrazione deve essere inferiore a 24 ore. Durante questo periodo, non sarai in grado di apportare aggiornamenti alle tue offerte. Riceverai una notifica via email dopo aver completato la migrazione delle tue offerte.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Illustra lo stato delle offerte migrate.":::

Dopo la migrazione delle offerte, saranno in modalità di sola lettura **per un periodo di tempo limitato** nel portale Cloud Partner. Il loro stato mostrerà "Spostato al Centro per i partner" e includerà un link alla tua offerta nel Centro per i partner, come mostrato nelle schermate seguenti. Da questo punto, gestirai gli aggiornamenti a tutte le tue offerte o creerai nuove offerte esclusivamente tramite il Centro per i partner,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Illustra il messaggio fornito per le offerte migrate al Centro per i partner":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Illustra la pagina del portale Cloud Partner per un'offerta migrata.":::

## <a name="how-will-i-create-new-offers"></a>Come creerò nuove offerte?

Dal portale Cloud Partner, verrai indirizzato a creare nuove offerte nel Centro per i partner

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Illustra il menu per creare una nuova offerta nel portale Cloud Partner":::

Dopo aver selezionato una nuova offerta, verrà visualizzato un messaggio, ad esempio il seguente.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustra il messaggio ricevuto durante la creazione di una nuova offerta in CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Devo creare un nuovo account per gestire le offerte nel Centro per i partner?

No. Il tuo account sottostante verrà mantenuto e dovresti già gestirlo nel Centro per i partner. Ciò significa che se sei un partner esistente, puoi usare le credenziali dell'account Cloud Partner Portal esistente per accedere al Centro per i partner dopo la migrazione. Solo le offerte e l'esperienza di gestione associata si stanno spostando dal portale per i partner cloud al Centro per i partner. Ti chiediamo di non creare nuovi account poiché le tue offerte non saranno associate al nuovo account.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Viene visualizzato un messaggio nel portale Cloud Partner per attivare il mio account, cosa significa?

Abbiamo bisogno di alcuni dettagli da te per eseguire correttamente la migrazione del tuo account al Centro per i partner e consentirti di gestire le tue offerte nel Centro per i partner al termine della migrazione dell'offerta. Per ulteriori informazioni sull'attivazione dell'account, vedere [Migrazione dell'account dal portale Cloud Partner al Centro per](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)i partner.

I passaggi necessari per completare l'attivazione dell'account variano in base al ruolo dell'account.

| Ruolo account | Procedura di attivazione |
|--------------|----------------|
|Proprietario | Passare alla pagina [Profilo editore](https://cloudpartner.azure.com/#profile) nel portale Cloud Partner e quindi fare clic sul collegamento nel banner per eseguire l'attivazione. Verrai reindirizzato al Centro per i partner per completare l'attivazione dell'account. |
| Collaboratore | Solo un utente nell'account con un ruolo di proprietario può attivare l'account. Contattare i proprietari dell'account per completare l'attivazione dell'account. I proprietari dell'account devono essere elencati nel messaggio banner. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Non riesco ad accedere al mio account e ad aprire un ticket di supporto

Se non riesci ad accedere al tuo account, puoi aprire un ticket di [supporto.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Dove posso trovare la documentazione sulla nuova esperienza di pubblicazione del Centro per i partner?

Vai alla [documentazione](https://docs.microsoft.com/azure/marketplace/)del mercato commerciale . Espandere quindi **Portale Marketplace commerciale nel Centro**  > per i partner**Creare una nuova offerta** per visualizzare gli argomenti della Guida per la creazione di ogni tipo di offerta.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustra gli argomenti della Guida per il Centro per i partner":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quali sono le differenze tra il Centro per i partner e il portale per i partner cloud?

È possibile notare le differenze seguenti tra il portale Cloud Partner e il Centro per i partner.

### <a name="modular-publishing-capabilities"></a>Funzionalità di pubblicazione modulare

Il Centro per i partner offre un'opzione di pubblicazione modulare che consente di selezionare le modifiche che si desidera pubblicare anziché pubblicare sempre tutti gli aggiornamenti contemporaneamente. Ad esempio, la schermata seguente mostra che le uniche modifiche selezionate per la pubblicazione sono le modifiche apportate a **Proprietà** e **Inserzione offerta**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustra la pagina Anteprima e pubblicazione":::

Gli aggiornamenti che non pubblichi vengono salvati come bozze. Continua a utilizzare l'anteprima dell'offerta per verificare l'offerta prima di renderla diretta al pubblico.

### <a name="rich-text-format"></a>Formato RTF

Migliora la descrizione dell'offerta e del piano utilizzando l'Editor Rich Text nella pagina Inserzione offerta e inserzione del piano.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustra l'editor RTF":::

### <a name="enhanced-preview-options"></a>Opzioni di anteprima migliorate

Il Centro per i partner include una funzione di [confronto](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) con opzioni di filtro migliorate. Questo ti dà la possibilità di confrontare con l'anteprima e le versioni dal vivo dell'offerta.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustra la funzione di confronto":::

### <a name="branding-and-navigation-changes"></a>Modifiche al branding e alla navigazione

Noterai alcune modifiche di branding. Ad esempio, "SKU" sono marchiati come "Piani" nel Centro per i partner.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Illustra la panoramica del piano.":::

Inoltre, le informazioni utilizzate nelle pagine **Marketplace** o**S torefront Details** (Servizio di consulenza, power BI) del portale per i partner cloud vengono raccolte nella pagina **Dell'offerta** nel Centro per i partner.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Illustra la pagina dell'offerta.":::

Le informazioni utilizzate per fornire SKU in un'unica pagina del portale Cloud Partner possono ora essere raccolte in diverse pagine del Centro per i partner:

* Pagina di configurazione del piano
* Pagina di pianificazione dell'elenco
* Pagina Pianificazione della disponibilità
* Pagina di configurazione tecnica del piano, come illustrato in questa schermata.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Illustra la pagina Pianificazione della configurazione tecnica.":::

L'ID dell'offerta viene ora visualizzato nella barra di navigazione a sinistra dell'offerta.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Illustra il menu di navigazione a sinistra con l'ID dell'offerta.":::

### <a name="stop-selling-an-offer"></a>Smetti di vendere un'offerta

Puoi richiedere di interrompere la vendita di [un'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) sul marketplace direttamente dal portale del Centro per i partner. L'opzione è disponibile nella pagina **Panoramica dell'offerta.**

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Illustra la pagina Panoramica offerta con l'opzione di interruzione della vendita.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quali pagine del Centro per i partner corrispondono alle pagine utilizzate nel portale per i partner cloud?

Nella tabella seguente vengono illustrati i collegamenti corrispondenti tra i due portali.

| Pagina | Collegamento al portale per i partner cloud | Collegamento al Centro per i partner |
|------|---------------------------|---------------------|
| **Pagina Tutte le offerte** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Pagina Tutti gli autori** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profilo dell'editore** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Pagina Users (Utenti)** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Pagina Cronologia** | https://cloudpartner.azure.com/#history | La funzionalità Cronologia non è ancora supportata nel Centro per i partner. |
| **Dashboard di Insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Rapporto di pagamento** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Le API REST del portale Cloud Partner saranno supportate dopo la migrazione?

Le API del portale per i partner cloud sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte al Centro per i partner. L'integrazione introduce piccole modifiche. Esaminare le modifiche nella tabella seguente per assicurarsi che il codice continui a funzionare dopo la migrazione al Centro per i partner.

| **API** | **Descrizione modifica:** | **Impatto** |
| ------- | ---------------------- | ---------- |
| Pubblica Pubblicazione, GoLive, Annulla | Per le offerte migrate, l'intestazione della risposta avrà un formato diverso, ma continuerà a funzionare nello stesso modo, indicando un percorso relativo per recuperare lo stato dell'operazione. | Quando si inviauna una delle richieste POST corrispondenti per un'offerta, l'intestazione Location avrà uno dei due formati a seconda dello stato di migrazione dell'offerta:<ul><li>Offerte non migrate<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offerte migrate<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operazione GET | Per le offerte che in precedenza supportavano il campo 'notification-email' nella risposta, questo campo sarà deprecato e non tornerà più per le offerte migrate. | Per le offerte migrate, non invieremo più notifiche all'elenco dei messaggi di posta elettronica specificati nelle richieste. Al contrario, il servizio API si allineerà con il processo di posta elettronica di notifica nel Centro per i partner per l'invio di messaggi di posta elettronica. In particolare, le notifiche verranno inviate all'indirizzo e-mail impostato nella sezione Informazioni di contatto del venditore delle impostazioni dell'account nel Centro per i partner, per notificarti lo stato di avanzamento dell'operazione.<br><br>Esamina l'indirizzo email impostato nella sezione Informazioni di contatto del venditore nelle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/management) nel Centro per i partner per assicurarti che venga fornita l'email corretta per le notifiche.  |
| | | |
