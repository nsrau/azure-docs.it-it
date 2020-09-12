---
title: Piani e prezzi per le offerte del Marketplace commerciale
description: Informazioni sui piani per le offerte di Microsoft Commercial Marketplace nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: ef860d57556e17d432988fd0fd43f861a31fde6c
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380923"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Piani e prezzi per le offerte del Marketplace commerciale

Un piano definisce l'ambito e i limiti di un'offerta e i prezzi associati quando applicabile. Ad esempio, a seconda del tipo di offerta, è possibile selezionare i mercati regionali e scegliere se un piano è visibile al pubblico o solo a un pubblico privato. Alcuni tipi di offerte supportano le sottoscrizioni ricorrenti, alcune supportano i prezzi basati sull'utilizzo e altri consentono a un cliente di acquistare l'offerta con una licenza acquistata direttamente dall'editore. Questo ti offre la flessibilità necessaria per offrire ai tuoi clienti opzioni tecniche e di prezzo diverse, se applicabile.

È possibile creare fino a 100 piani per ogni offerta che supporta i piani e fino a 45 di tali piani possono essere [privati](#plan-visibility). È possibile creare piani a pagamento solo per le offerte di macchine virtuali di Azure, le offerte per applicazioni Azure (applicazioni gestite) e le offerte SaaS (Software as a Service). Quando si sceglie di vendere una di queste offerte tramite Microsoft (offerte transazionali), è necessario creare almeno un piano. È possibile creare piani per alcuni degli altri tipi di offerta, ma i piani per tali tipi di offerte non includono opzioni di prezzo.

> [!TIP]
> Un'offerta transazionale è una delle quali Microsoft semplifica lo scambio di denaro per una licenza software per conto dell'editore.

## <a name="plans-by-offer-type"></a>Piani per tipo di offerta

Nella tabella seguente vengono illustrate le opzioni del piano per ogni tipo di offerta. Nelle sezioni seguenti vengono illustrate altre informazioni su queste opzioni. 

| Tipo di offerta | Piani con opzioni di prezzo | Piani senza opzioni di prezzo | Opzione audience privata |
| ------------ | ------------- | ------------- | ------------- |
| Applicazione gestita di Azure | &#10004; | | &#10004; |
| Modello di soluzione di Azure | | &#10004; | &#10004; |
| Contenitore di Azure | | &#10004; (BYOL) | |
| Modulo IoT Edge |  | &#10004; |  |
| Servizi gestiti |  | &#10004; (BYOL) | &#10004; |
| Software come un servizio | &#10004; |  | &#10004; |
| Macchina virtuale di Azure | &#10004; |  | &#10004; |
|||||

I piani non sono supportati per i tipi di offerta seguenti:
-  Servizio di consulenza
-  Dynamics 365 Business Central
-  & PowerApps di Dynamics 365 Customer Engagement
-  Dynamics 365 for Operations
-  App Power BI

## <a name="plan-information"></a>Informazioni sul piano

Ogni tipo di offerta richiede informazioni diverse quando si crea un nuovo piano. È possibile trovare collegamenti ad articoli specifici dell'offerta nella [Guida alla pubblicazione in base al tipo di offerta](publisher-guide-by-offer-type.md). Una volta creato un nuovo piano nella pagina **panoramica piano** , vengono visualizzate diverse schede, ad esempio un **elenco di piani** o **prezzi e disponibilità** per configurare dettagli diversi per il piano. Ogni scheda indicherà lo stato incompleto o completo durante l'utilizzo di questi campi.

![Viene illustrata la pagina di elenco dei piani nel centro per i partner. I campi nome piano, Riepilogo piano e descrizione piano sono evidenziati.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Per un nuovo piano, è necessario completare alcuni dettagli comuni:

-  **ID piano**: creare un ID univoco per ogni piano nell'offerta. Usare un massimo di 50 caratteri: solo lettere minuscole, caratteri alfanumerici, trattini e caratteri di sottolineatura. Questo ID sarà visibile ai clienti nell'URL del prodotto e nei modelli di Azure Resource Manager (se applicabile). Non è possibile modificare questo ID dopo la pubblicazione dell'offerta.
-  **Nome del piano**: (callout 1 nell'immagine precedente). Creare un nome univoco per ogni piano nell'offerta. Usare un massimo di 50 caratteri. Il nome del piano viene usato per distinguere i piani software che possono far parte della stessa offerta, ad esempio il nome dell'offerta, ovvero il piano standard e il piano Enterprise. Il nome verrà visualizzato dai clienti quando si decide quale piano selezionare nell'offerta.
-  **Riepilogo del piano**: (callout 2 nell'immagine precedente). Questo riepilogo viene visualizzato nei risultati della ricerca di Azure Marketplace e può contenere un massimo di 100 caratteri.
   > [!NOTE]
   > Questo campo non si applica alle offerte SaaS.
-  **Descrizione piano**: (callout 3 nell'immagine precedente). Aggiungere una descrizione del piano che spieghi cosa rende questo piano univoco da altri piani per l'offerta. Usare un massimo di 500 caratteri. Questo contenuto verrà visualizzato ai clienti nelle pagine di presentazione dell'offerta durante la ricerca e selezionare un piano per l'offerta.

Il nome e la descrizione del piano vengono visualizzati nella pagina di inserzione dell'offerta negli archivi online del Marketplace commerciale. La schermata seguente mostra tre piani per un elenco di offerte SaaS in Azure Marketplace.

![Viene illustrata una pagina di presentazione dell'offerta nel centro per i partner. Vengono visualizzati tre piani.](./media/commercial-marketplace-plans/offer-listing-page.png)

Una volta creati i piani, nella pagina **panoramica piano** viene visualizzato un elenco di nome, ID, altri dettagli, stato di pubblicazione corrente ed eventuali azioni disponibili. Le azioni disponibili variano a seconda dello stato del piano e possono includere:

-  Se lo stato del piano è **Bozza**, Elimina bozza.
-  Se lo stato del piano è **Attiva**, Interrompi vendita o Sincronizza destinatari privati.

Lo screenshot seguente mostra due offerte bozza.

![Viene illustrata la pagina panoramica piano nel centro per i partner. Vengono visualizzati due piani.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Il Marketplace commerciale opera su un modello di agenzia, in base al quale gli editori impostano i prezzi, i clienti di Microsoft fatturazione e i ricavi per gli editori, mantenendo al tempo stesso la tariffa dell'Agenzia. Si definiscono i mercati, la visibilità e i prezzi dell'offerta (quando applicabile) nella scheda **prezzi e disponibilità** o **disponibilità** .

-  **Mercati**: ogni piano deve essere disponibile in almeno un mercato.  Si ha la possibilità di selezionare solo i paesi "fiscali rimittenti", in cui Microsoft esegue le vendite e usa le imposte per conto dell'utente.
-  **Prezzi**: i modelli di prezzi si applicano solo ai piani per le offerte di applicazioni gestite di Azure, Saas e macchine virtuali di Azure. Tutti i piani per la stessa offerta devono utilizzare lo stesso modello di determinazione prezzi.  
-  **Visibilità del piano**: a seconda del tipo di offerta, è possibile definire un pubblico privato o nascondere l'offerta o il piano da Azure Marketplace. Questa operazione viene illustrata più dettagliatamente nella [visibilità del piano](#plan-visibility) più avanti in questo articolo.

> [!TIP]
> Si consiglia di creare piani più adatti ai modelli di utilizzo della base dei clienti di destinazione. In questo modo si riducono gli utenti da piani di cambio frequente in base alle modifiche all'utilizzo. Per un esempio di un'offerta SaaS con tre piani di fatturazione a consumo, vedere l' [offerta di esempio](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Modelli di determinazione dei prezzi

È necessario associare un modello di determinazione dei prezzi a ogni piano per i tipi di offerta seguenti. Ognuno di questi tipi di offerta ha modelli tariffari disponibili diversi:

-  **Applicazione gestita di Azure**: tariffa fissa (mensile) e prezzi basati sull'utilizzo (dimensioni del servizio di misurazione).
-  **Software come servizio**: tariffa fissa (mensile o annuale), per utente e prezzi basati sull'utilizzo (dimensioni del servizio di misurazione). 
-  **Macchina virtuale di Azure**: Bring your own License (BYOL) e prezzi basati sull'utilizzo. Per un modello di determinazione prezzi basato sull'utilizzo, è possibile addebitare per core, dimensione core o per mercato e dimensioni core. Un modello di licenza BYOL non consente addebiti aggiuntivi basati sull'utilizzo.   (Le offerte della macchina virtuale BYOL non richiedono un modello di determinazione dei prezzi).

Tutti i piani per la stessa offerta devono utilizzare lo stesso modello di determinazione prezzi. Un'offerta SaaS, ad esempio, non può avere un piano di tariffa fissa e un altro piano per ogni utente. Per informazioni dettagliate, vedere la documentazione specifica dell'offerta.

Se i prezzi per il piano sono già stati impostati in dollari statunitensi (USD) e si aggiunge la località di un altro mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Dopo aver salvato le modifiche, viene visualizzato un collegamento ai **prezzi di esportazione (xlsx)** che può essere usato per rivedere e modificare il prezzo di ogni mercato prima della pubblicazione.

> [!IMPORTANT]
> Dopo la pubblicazione dell'offerta, non è possibile modificare la scelta del modello di determinazione dei prezzi.

Le offerte SaaS flat e le offerte di applicazioni gestite supportano la fatturazione a consumo tramite il servizio di misurazione del Marketplace. Si tratta di un modello di fatturazione basato sull'utilizzo che consente di definire unità non standard, ad esempio la larghezza di banda o i messaggi di posta elettronica, che i clienti pagheranno su base di consumo. Per altre informazioni sulla fatturazione a consumo per [applicazioni gestite](./partner-center-portal/azure-app-metered-billing.md) e [app Saas](./partner-center-portal/saas-metered-billing.md), vedere la documentazione correlata.

## <a name="custom-prices"></a>Prezzi personalizzati

Per impostare prezzi personalizzati in un singolo mercato, esportare, modificare e quindi importare il foglio di calcolo dei prezzi. L'utente è responsabile della convalida dei prezzi e del possesso di queste impostazioni.

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune restrizioni relative alle modifiche consentite dopo la pubblicazione di un piano:

-  Dopo la pubblicazione di un piano, il modello di determinazione dei prezzi non può essere modificato.
-  Dopo la pubblicazione di un termine di fatturazione per un piano, non sarà possibile rimuoverlo in un secondo momento.
-  In seguito alla pubblicazione, non sarà più possibile modificare il prezzo per un mercato del piano.

I prezzi impostati in Stati Uniti dollari (USD) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e esaminando il prezzo di ogni mercato selezionato.

1.  Salvare le modifiche apportate ai prezzi per consentire l'esportazione dei dati relativi ai prezzi. Nella parte inferiore della scheda relativa a **prezzi e disponibilità** selezionare **Salva bozza**.
1.  In **prezzi**selezionare il collegamento **Esporta dati sui prezzi** .
1.  Aprire il file exportedPrice.xlsx in Microsoft Excel.
1.  Nel foglio di calcolo, apportare gli aggiornamenti desiderati alle informazioni sui prezzi e quindi salvare il. File CSV.
    > [!NOTE]
    > Per poter aggiornare il file, potrebbe essere necessario abilitare la modifica in Excel.
1.  Nella scheda **prezzi e disponibilità** , in **prezzi**, selezionare il collegamento **Importa dati prezzi** .
1.  Nella finestra di dialogo visualizzata selezionare **Sì**.
1.  Selezionare il file exportedPrice.xlsx aggiornato e quindi selezionare **Apri**.
















### <a name="plan-visibility"></a>Plan Visibility (Visibilità del piano)

È possibile rendere disponibili pubblicamente i piani per alcuni tipi di offerte o disponibili solo per un pubblico specifico (privato). Le offerte con piani privati verranno pubblicate nel portale di Azure. Scopri di più sui piani privati in [offerte private in Microsoft Commercial Marketplace](private-offers.md).

Il pubblico privato viene definito usando gli ID tenant di Azure o gli ID sottoscrizione di Azure, a seconda del tipo di offerta. È possibile immettere fino a 10 ID manualmente o importare fino a 10, 00 ID sottoscrizione o 20.000 ID tenant (come applicabile) con un file con estensione CSV. È anche possibile definire i destinatari privati per i servizi di consulenza e le offerte di Dynamics 365 che non hanno piani.

Dopo la pubblicazione dell'offerta con un piano privato, è possibile aggiornare i destinatari o scegliere di rendere il piano disponibile a tutti. Dopo che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti e non può essere configurato nuovamente come piano privato.

> [!NOTE]
> Un pubblico privato è diverso da un pubblico di anteprima. Nella pagina di **disponibilità** per alcuni tipi di offerta è possibile definire un pubblico che può visualizzare in anteprima l'offerta prima che l'offerta venga pubblicata nel Marketplace commerciale. Sebbene la designazione dei destinatari privati venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno), ma solo durante il periodo di anteprima limitato durante il test e la convalida del piano.

È anche possibile scegliere di nascondere il piano interamente da Azure Marketplace, se l'offerta verrà distribuita come parte di un modello di soluzione o di applicazione gestita.

## <a name="free-trials"></a>Versioni di valutazione gratuite

È possibile abilitare una versione di valutazione gratuita sui piani per le offerte SaaS e le macchine virtuali di Azure sottoposte a transazione.

> [!NOTE]
> Questa sezione illustra le versioni di valutazione gratuite dei piani a pagamento per le offerte vendute tramite Microsoft. Questo è diverso rispetto agli elenchi di valutazione gratuiti dei partner che scelgono di elaborare le transazioni in modo indipendente. È possibile creare un elenco di valutazione gratuita (non associato a un piano) per i tipi di offerta seguenti:
> -  Macchina virtuale di Azure 
> -  SaaS 
> -  Dynamics 365 Business Central
> -  Dynamics 365 per Customer Engagement & PowerApps
> -  Dynamics 365 for Operations
> 
> Per ulteriori informazioni sulle opzioni di elenco, vedere [determinare l'opzione di pubblicazione](determine-your-listing-type.md).

Le versioni di valutazione gratuite sono supportate per tutti i modelli di fatturazione eccetto i piani a consumo. I piani SaaS consentono versioni di valutazione gratuite di 1 mese. I piani di macchine virtuali di Azure consentono versioni di valutazione gratuite di 1, 3 o 6 mesi.

Quando un cliente seleziona una versione di valutazione gratuita, raccoglie le informazioni di fatturazione, ma non inizia la fatturazione del cliente fino a quando la versione di valutazione non viene convertita in una sottoscrizione a pagamento. Le versioni di valutazione gratuite vengono convertite automaticamente in una sottoscrizione a pagamento al termine della versione di valutazione, a meno che il cliente non cancelli la sottoscrizione prima della scadenza del periodo di valutazione.

Durante il periodo di valutazione, i clienti possono valutare i piani supportati all'interno della stessa offerta con una versione di valutazione gratuita abilitata. Se passano a una versione di valutazione diversa all'interno della stessa offerta, il periodo di valutazione non viene riavviato. Se, ad esempio, un cliente usa una versione di valutazione gratuita per 15 giorni e passa a una versione di valutazione gratuita diversa per la stessa offerta, il nuovo periodo di valutazione verrà usato per 15 giorni. La versione di valutazione gratuita che il cliente stava valutando al termine del periodo di valutazione è quella che viene convertita automaticamente in una sottoscrizione a pagamento.

Quando un cliente seleziona una versione di valutazione gratuita per un piano, non può eseguire la conversione a una sottoscrizione a pagamento per quel piano fino al termine del periodo di valutazione. Se un cliente sceglie di eseguire la conversione in un piano diverso all'interno dell'offerta che non dispone di una versione di valutazione gratuita, la conversione verrà eseguita, ma la versione di valutazione gratuita terminerà immediatamente e i dati andranno perduti.

> [!NOTE]
> Quando un cliente inizia a pagare per un piano, non può ottenere una versione di valutazione gratuita sulla stessa offerta, anche se passa a un altro piano che supporta le versioni di valutazione gratuite.

Per ottenere informazioni sulle sottoscrizioni dei clienti che attualmente partecipano a una versione di valutazione gratuita, usare la nuova proprietà API `isFreeTrial`, che viene contrassegnata come true o false. Per altre informazioni, vedere l'[API di ottenimento delle sottoscrizioni SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Passaggi successivi

-  Per informazioni su come aggiungere o aggiornare piani in un'offerta esistente, vedere [aggiornare un'offerta esistente nel Marketplace commerciale](./partner-center-portal/update-existing-offer.md).
-  Per ulteriori informazioni sulle opzioni di transazione e sui modelli tariffari associati, vedere la pagina relativa alle [funzionalità Transact del Marketplace commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).
