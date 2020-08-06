---
title: Creare un'offerta SaaS, Azure Marketplace e Microsoft AppSource
description: Come creare un'offerta SaaS (software as a Service) per l'inserzione o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) tramite il programma Microsoft Commercial Marketplace nel centro per i partner Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2b12eb88f0297c44fabb62e40d315517f3d3b954
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800337"
---
# <a name="create-a-saas-offer"></a>Creare un'offerta SaaS

Per iniziare a creare offerte SaaS (software come un servizio) nel marketplace commerciale, assicurarsi prima di tutto di [creare un account per il Centro per i partner](./create-account.md) e aprire il [dashboard del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)con la scheda **Panoramica** selezionata.

> [!NOTE]
> Se si sta creando un'offerta SaaS transazionale, assicurarsi di implementare l'integrazione con le [API di evasione Saas](./pc-saas-fulfillment-apis.md).  L'integrazione con le API è l'unico modo per garantire il corretto funzionamento della transazione nel Marketplace. È anche necessario assicurarsi che l'app usi Azure AD autenticazione con Single Sign-on (SSO). Vedere [Azure ad e le offerte SaaS transazionali nel Marketplace commerciale](../azure-ad-saas.md).

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Software come servizio**.

   ![Immagine del menu di spostamento a sinistra.](./media/new-offer-saas.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

**Stato pubblicazione** mostra una rappresentazione visiva dei passaggi per pubblicare l'offerta e il tempo necessario per completare ogni passaggio. Le icone dei passaggi di pubblicazione incompleti vengono visualizzate in grigio.

Il menu **Panoramica offerta** contiene un elenco di collegamenti per l'esecuzione di operazioni su questa offerta. Questo elenco di operazioni cambia in base alle opzioni selezionate per l'offerta.  

- Se l'offerta è una bozza, Elimina bozza
- Se l'offerta è stata pubblicata, Interrompi vendita
- Se l'offerta è in anteprima, Passa allo stato Live
- Se la disconnessione del server di pubblicazione non è stata completata, Annulla la pubblicazione

## <a name="offer-setup"></a>Configurazione dell'offerta

Questa pagina richiede le informazioni seguenti.

- **Vendere tramite Microsoft?** (Sì/No)
  - **Sì, desidero vendere tramite Microsoft e avere transazioni host Microsoft personali**.
  - **No, preferisco presentare l'offerta solo tramite i marketplace ed elaborare le transazioni in modo indipendente**.

### <a name="sell-through-microsoft"></a>Vendita tramite Microsoft

La vendita tramite Microsoft assicura un'individuazione e un'acquisizione di clienti migliori, consente a Microsoft di ospitare le transazioni del marketplace per conto dell'editore e sfrutta le funzionalità commerciali disponibili a livello globale di Microsoft.

#### <a name="saas-offer-requirements"></a>Requisiti dell'offerta SaaS

Per presentare le offerte SaaS (software come un servizio) con il marketplace commerciale nel Centro per i partner, è necessario soddisfare i criteri seguenti:

- L'offerta deve usare [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione e la gestione delle identità.
- L'offerta deve usare [API di adempimento SaaS](pc-saas-fulfillment-api-v2.md) per l'integrazione con Azure Marketplace.

#### <a name="saas-pricing-and-billing-options"></a>Opzioni di fatturazione e prezzi dell'offerta SaaS

Con le soluzioni SaaS in esecuzione nella sottoscrizione di Azure dell'editore, i costi di licenza pagati dai clienti includono il costo dell'infrastruttura su cui viene distribuito il software. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di aggregare i costi di utilizzo dell'infrastruttura di Azure ai prezzi di licenza software.

Le offerte SaaS supportano la fatturazione mensile o annuale in base a una tariffa fissa, per utente o a costi di utilizzo usando il servizio di fatturazione a consumo. Il marketplace commerciale di Microsoft opera in base a un modello di agenzia, tramite il quale gli editori impostano i prezzi, Microsoft fattura ai clienti e quindi paga i ricavi all'editore, trattenendo una tariffa di agenzia.

Questo è un esempio di suddivisione dei costi e dei pagamenti per illustrare il modello di agenzia (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

|**Costo della licenza**|**$ 100 al mese**|
|:---|:---|
|Costo dell'utilizzo di Azure (D1/1-Core)|Costi addebitati direttamente all'editore, non al cliente|
|Importo addebitato da Microsoft al cliente|$ 100,00 al mese. L'editore deve calcolare i costi di infrastruttura sostenuti o trasferirli nei costi di licenza|

|**Microsoft addebita**|**$ 100 al mese**|
|:---|:---|
|Microsoft paga l'80% del costo della licenza <br>**Per le app SaaS idonee, Microsoft paga il 90% del costo di licenza*|$ 80,00 al mese <br>*$* 90,00 al mese*|

- In questo esempio Microsoft fattura $ 100,00 al cliente per la licenza software e paga $ 80,00 all'editore.

> [!NOTE]
> **Tariffa ridotta del servizio Marketplace** : per alcune offerte SaaS pubblicate sul Marketplace commerciale, Microsoft ridurrà la tariffa del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%. Per le offerte idonee, le offerte devono essere state designate da Microsoft come co-selling IP di Azure incentivate. L'idoneità deve essere soddisfatta almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per ricevere la tariffa del servizio Marketplace ridotta per il mese. La tariffa ridotta per il servizio Marketplace si applica anche alle macchine virtuali incentivate di Azure con co-selling IP di Azure, alle app gestite e a qualsiasi altra offerta IaaS transazionale qualificata resa disponibile tramite il Marketplace commerciale.

### <a name="list-through-microsoft"></a>Presentazione tramite Microsoft

È possibile promuovere la propria azienda con Microsoft creando una presentazione nel marketplace. La scelta di presentare l'offerta autonomamente e non con una transazione tramite Microsoft significa che Microsoft non partecipa direttamente alle transazioni di licenza software. Non viene addebitata alcuna tariffa per le transazioni e l'editore mantiene il 100% dei costi di licenza software raccolti dal cliente. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione di licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, analisi, fatturazione, pagamento e raccolta.

#### <a name="get-it-now-free"></a>Scarica ora (gratuito)

Elencare gratuitamente l'offerta ai clienti fornendo un indirizzo valido (a partire da *http* o *https*), in cui è possibile ottenere una versione di valutazione tramite [l'autenticazione con un clic usando Azure Active Directory (Azure ad)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials). Ad esempio: `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Free trial (listing) (Versione di valutazione gratuita - Presentazione)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un indirizzo valido (a partire da *http* o *https*), in cui è possibile ottenere una versione di valutazione tramite [l'autenticazione con un clic usando Azure Active Directory (Azure ad)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials). Ad esempio: `https://contoso.com/trial/saas-app`. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni sugli utenti tramite Azure AD per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Lead clienti](#customer-leads).

#### <a name="example-marketplace-offer-listing"></a>Presentazione di offerte di esempio nel marketplace

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource:

:::image type="content" source="media/example-appsource-saas.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo grande
2. Categorie
3. Settori
4. Indirizzo di supporto (collegamento)
5. Condizioni per l'utilizzo
6. Informativa sulla privacy
7. Nome offerta
8. Riepilogo
9. Descrizione
10. Screenshot/video
11. Documenti

<br>Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta nell'portale di Azure:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Viene illustrato il modo in cui questa offerta viene visualizzata nel portale di Azure.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Titolo
2. Descrizione
3. Collegamenti utili
4. Schermate

## <a name="enable-a-test-drive"></a>Abilitare un test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo più conversioni e la generazione di lead altamente qualificati. [Altre informazioni sui test drive](../what-is-test-drive.md).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Per altre informazioni, vedere [Test drive dell'offerta nel marketplace commerciale](test-drive.md).

### <a name="test-drive-resources"></a>Risorse per il test drive

- [Che cos'è un test drive?](../what-is-test-drive.md)
- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Panoramica](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (file con estensione pdf, assicurarsi che il blocco popup sia disattivato)

### <a name="customer-leads"></a>Lead clienti

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Risorse aggiuntive per la gestione dei lead
- [Domande frequenti sulla gestione dei lead](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [Errori comuni di configurazione del lead](../lead-management-for-cloud-marketplace.md#publishing-config-errors)
- [Pagina di panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina richiede di definire le categorie e i settori usati per raggruppare l'offerta nei marketplace, i contratti legali che supportano l'offerta e la versione dell'app.

### <a name="category"></a>Category

L'offerta verrà pubblicata in AppSource o in Azure Marketplace, a seconda delle funzionalità di transazione associate all'offerta e della selezione della categoria. Per informazioni dettagliate, vedere [confronto tra Microsoft AppSource e Azure Marketplace](../comparing-appsource-azure-marketplace.md) . Selezionare le categorie e le sottocategorie che meglio si allineano con l'offerta e con i destinatari desiderati. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie applicabili a ogni storefront nell' [elenco di offerte procedure](../gtm-offer-listing-best-practices.md)consigliate.

### <a name="industries"></a>Settori

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

La selezione del settore si applica solo alle offerte pubblicate in AppSource.

### <a name="app-version"></a>Versione dell'app

Questo campo è facoltativo e viene usato in AppSource Marketplace per identificare il numero di versione dell'offerta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il marketplace commerciale Microsoft

Microsoft fornisce un modello di contratto standard.

- **Usare il contratto standard per il Marketplace commerciale di Microsoft?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un modello di contratto standard per facilitare le transazioni nel marketplace. Anziché creare termini e condizioni personalizzati, gli editori del marketplace commerciale possono scegliere di offrire il proprio software ai sensi del contratto standard, che i clienti devono esaminare e accettare una sola volta. Il contratto standard è disponibile all'indirizzo https://go.microsoft.com/fwlink/?linkid=2041178.

È possibile scegliere di usare il contratto standard anziché fornire termini e condizioni personalizzati selezionando la casella di controllo "Usare il contratto standard per il Marketplace commerciale di Microsoft?".

![Casella di controllo per l'uso del contratto standard](./media/use-standard-contract.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il marketplace commerciale Microsoft, non è possibile usare termini e condizioni personalizzati. La scelta di uno scenario esclude automaticamente l'altro. È possibile offrire la propria soluzione ai sensi del contratto standard **o** di termini e condizioni personalizzati. Per modificare le condizioni del contratto standard, è possibile usare le modifiche contrattuali standard.

#### <a name="standard-contract-amendments"></a>Modifiche contrattuali standard

Le modifiche contrattuali standard consentono agli editori di selezionare le condizioni del contratto standard per semplificare e personalizzare le condizioni per il prodotto o l'azienda. I clienti devono esaminare solo le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori del marketplace commerciale sono disponibili due tipi di modifiche:

- Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Le modifiche universali vengono visualizzate a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa modifica prima di poter usare l'offerta.
- Modifiche personalizzate: si tratta di modifiche speciali apportate al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Le condizioni di modifica personalizzate verranno presentate solo ai clienti del tenant nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!NOTE]
> Questi due tipi di modifiche si integrano tra loro. I clienti cui sono destinate le modifiche personalizzate riceveranno anche la modifica universale al contratto standard durante l'acquisto.

**Condizioni di modifica universali del contratto standard per il marketplace commerciale di Microsoft**: immettere le condizioni di modifica universali in questa casella. È possibile specificare una sola modifica universale per ogni offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante l'individuazione e il flusso di acquisto.

**Condizioni di modifica personalizzate del contratto standard per il marketplace commerciale di Microsoft**: per iniziare, selezionare **Aggiungi condizione di modifica personalizzata**. È possibile specificare fino a 10 condizioni di modifica personalizzate per ogni offerta.

- **Condizioni di modifica personalizzate**: immettere le condizioni di modifica personalizzate in questa casella. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni verranno visualizzate solo ai clienti degli ID tenant specificati per queste condizioni personalizzate nel flusso di acquisto dell'offerta nel portale di Azure.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario specificare almeno un ID tenant. L'ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e il cliente può trovarlo passando a portal.azure.com > Azure Active Directory > Proprietà. Il valore dell'ID directory è l'ID tenant, ad esempio 50c464d3-4930-494c-963C-1e951d15360e. È anche possibile cercare l'ID tenant dell'organizzazione del cliente usando l'URL del nome di dominio in cui [si trova il Microsoft Azure e l'ID tenant di Office 365?](https://www.whatismytenantid.com)
- **Descrizione** (facoltativo): è possibile immettere una descrizione per l'ID tenant che consente di identificare il cliente cui è destinata la modifica.

#### <a name="terms-and-conditions"></a>Termini e condizioni

Se si vuole fornire termini e condizioni personalizzati, è possibile scegliere di immetterli nel campo Termini e condizioni. In questo campo è possibile immettere fino a 10.000 caratteri di testo. Se i termini e le condizioni richiedono una descrizione più lunga, immettere un unico collegamento URL in questo campo tramite il quale è possibile accedere ai termini e condizioni. Verrà visualizzato ai clienti come collegamento attivo.

I clienti sono tenuti ad accettare le condizioni prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione offerta

In questa pagina vengono visualizzate le lingue e i mercati in cui è disponibile l'offerta. Attualmente Inglese (Stati Uniti) è l'opzione corrispondente all'unica località disponibile. Inoltre, in questa pagina vengono visualizzati lo stato della presentazione specifica della lingua e la data/ora in cui è stata aggiunta l'offerta. Sarà necessario definire i dettagli per il marketplace (nome dell'offerta, descrizione, termini di ricerca e così via) per ogni lingua/mercato.

> [!NOTE]
> I contenuti per la presentazione dell'offerta, tra cui descrizione, documenti, screenshot, condizioni per l'utilizzo e informativa sulla privacy, non devono essere necessariamente in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un *URL di collegamento utile* per offrire contenuti in una lingua diversa da quella usata per la presentazione dell'offerta.

### <a name="offer-listings"></a>Presentazione delle offerte

Fornire i dettagli da visualizzare nel marketplace, incluse le descrizioni dell'offerta e gli asset di marketing.

- **Nome** (obbligatorio): il nome definito qui verrà visualizzato come titolo della presentazione dell'offerta nei marketplace scelti. Il nome viene immesso automaticamente in base al valore specificato in **Nuova offerta** in precedenza. Il nome può essere un marchio registrato. Non può contenere emoji (ad eccezione dei simboli di marchio e copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo** (obbligatorio): fornire una breve descrizione dell'offerta da usare nei risultati della ricerca delle presentazioni nel marketplace. In questo campo è possibile immettere fino a 100 caratteri di testo.
- **Descrizione** (obbligatoria): fornire una descrizione dell'offerta da visualizzare nella panoramica delle presentazioni nel marketplace. È consigliabile includere una proposta di valore, i vantaggi principali, qualsiasi categoria o settore associato, le opportunità di acquisto in-app, tutte le comunicazioni necessarie e un collegamento ad altre informazioni. In questo campo è possibile immettere fino a 3.000 caratteri di testo, incluso il markup. Per altri suggerimenti, vedere [Scrivere un'ottima descrizione dell'app](/windows/uwp/publish/write-a-great-app-description).
- **Parole chiave di ricerca**: immettere fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta nei marketplace.
- **Istruzioni sulle attività iniziali** (obbligatorio): spiegare come configurare e iniziare a usare l'app per i potenziali clienti.  Queste informazioni di avvio rapido possono contenere collegamenti a una documentazione online più dettagliata. In questo campo è possibile immettere fino a 3.000 caratteri di testo.

#### <a name="description"></a>Descrizione

Questo campo è obbligatorio.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Collegamenti

- **Informativa sulla privacy** (obbligatorio): collegamento all'informativa sulla privacy dell'organizzazione. L'editore è tenuto a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida
- **Materiale marketing del programma CSP** (facoltativo): fornire un collegamento ai materiali di marketing se si sceglie di estendere l'offerta al programma [Cloud Solution Provider (CSP)](../cloud-solution-providers.md). Il programma CSP estende l'offerta a una gamma più ampia di clienti idonei, permettendo ai partner CSP di aggregare, commercializzare e rivendere l'offerta. Questi rivenditori dovranno poter accedere ai materiali per il marketing dell'offerta. Per altre informazioni, vedere [Servizi Go-To-Market](https://partner.microsoft.com/reach-customers/gtm).
- **Collegamenti utili** (facoltativo): documenti online supplementari facoltativi sull'app o sui servizi correlati elencati impostando i campi **Titolo** e **URL**. Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi un URL**.

#### <a name="contact-information"></a>Informazioni contatto

- **Contatti**: per ogni contatto del cliente, completare i campi **Nome**, **Numero di telefono** e **Indirizzo di posta elettronica** di un dipendente (che *non* verranno visualizzati pubblicamente). È necessario anche immettere un valore in **URL supporto**  per il gruppo **Contatto di supporto** (che *verrà* visualizzato pubblicamente).

  - **Contatto di supporto** (obbligatorio): per domande di supporto generali.
  - **Contatto tecnico** (obbligatorio): per domande tecniche.
  - **Channel Manager Contact** (Contatto responsabile canale) (obbligatorio): per domande sui rivenditori correlate al programma CSP.

#### <a name="files-and-images"></a>File e immagini

- **Documenti** (obbligatorio): aggiungere documenti di marketing correlati per l'offerta, in formato PDF, di almeno uno e fino a tre documenti per ogni offerta.
- **Immagini** (obbligatorio): sono presenti più posizioni in cui le immagini del logo dell'offerta possono essere visualizzate in tutto il Marketplace. Fornire un file PNG per il logo di **grandi** dimensioni (compreso tra 216 x 216 e 350 x 350 pixel). Il centro per i partner lo utilizzerà per creare un logo di dimensioni **ridotte** (48 x 48 pixel) e un valore **medio** (90 x 90 pixel). Facoltativamente, è possibile sostituirli con immagini diverse. L'aggiunta di un logo **esteso** è facoltativa.

    Per l'uso in posizioni diverse dell'elenco sono necessarie tre dimensioni del logo; uno è facoltativo:

    - **Piccola** (48 x 48)
    - **Media** (90 x 90)
    - **Grande** (compreso tra 216 x 216 e 350 x 350)
    - **Wide** (255 x 115), *facoltativo*

    [!INCLUDE [Logo suggestions](./includes/graphics-suggestions.md)]

- **Screenshot** (obbligatorio): aggiungere un massimo di cinque screenshot che dimostrano l'offerta, ridimensionata a 1280 x 720 pixel. Tutte le immagini devono essere in formato PNG.
- **Video** (facoltativo): aggiungere collegamenti a video che mostrano l'offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. È necessario immettere anche un'immagine di anteprima del video, in formato PNG con dimensioni 1280 x 720 pixel. È possibile fornire un massimo di quattro video per ogni offerta.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

- [Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="preview-audience"></a>Gruppo di destinatari per l'anteprima

Questa pagina consente di definire un **gruppo di destinatari per l'anteprima** limitato cui rendere disponibile l'offerta prima di pubblicarla al più ampio pubblico del marketplace.

> [!IMPORTANT]
> Dopo aver controllato l'offerta in Anteprima, selezionare **Passa allo stato Live** in modo che l'offerta possa essere pubblicata ai destinatari pubblici del marketplace.

Aggiungere un solo indirizzo di posta elettronica dell'account AAD/account del servizio gestito per riga, insieme a una descrizione facoltativa.

Aggiungere fino a 10 indirizzi di posta elettronica manualmente oppure 20 se si carica un file CSV per l'account Microsoft (account del servizio gestito) esistente o per account Azure Active Directory per semplificare la convalida dell'offerta prima della pubblicazione. L'aggiunta di questi account consente di definire un gruppo di destinatari cui verrà consentito l'accesso in anteprima all'offerta prima che venga pubblicata nei marketplace. Se l'offerta è già stata pubblicata, è comunque possibile definire un gruppo di destinatari per l'anteprima che potrà testare qualsiasi modifica o aggiornamento all'offerta.

> [!NOTE]
> Il gruppo di destinatari per l'anteprima è diverso da un gruppo di destinatari privato. Un gruppo di destinatari per l'anteprima può accedere all'offerta _prima_ che l'offerta venga pubblicata nei marketplace. È anche possibile scegliere di creare un piano e renderlo disponibile solo per un gruppo di destinatari privato. Nella scheda **Elenco piani** è possibile definire un gruppo di destinatari privato selezionando la casella di controllo **Questo è un piano privato**. È quindi possibile definire un gruppo di destinatari privato di un massimo di 20.000 clienti usando gli ID tenant di Azure.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

La scheda **configurazione tecnica** definisce i dettagli tecnici usati da Marketplace per comunicare con il servizio SaaS. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla e gestirla.

>[!NOTE]
>È necessario implementare l'integrazione con le [API di evasione Saas](./pc-saas-fulfillment-api-v2.md) prima di configurare questi dettagli nei dettagli dell'offerta. È anche necessario creare una pagina di destinazione e l'app deve usare Azure AD autenticazione con Single Sign-on (SSO). Per ulteriori informazioni, vedere la pagina relativa alle [offerte SaaS Azure ad e transazionali nel Marketplace commerciale](../azure-ad-saas.md).

I diagrammi e le spiegazioni dettagliate che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione relativa alle [API](./pc-saas-fulfillment-api-v2.md).

- **URL della pagina di destinazione** (obbligatorio): definire l'URL del sito SaaS (ad esempio, `https://contoso.com/signup` ) che i clienti finali dovranno sbarcare dopo aver acquisito l'offerta dal Marketplace e aver attivato il processo di configurazione dalla sottoscrizione Saas appena creata.  Questo URL viene chiamato con il parametro del token di identificazione dell'acquisto del Marketplace che identifica in modo univoco l'acquisto SaaS del cliente finale specifico.  È necessario scambiare questo token per i dettagli della sottoscrizione SaaS corrispondenti usando l'API [Resolve](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) .  Questi dettagli e tutti gli altri che si desidera raccogliere devono essere utilizzati come parte di una pagina Web interattiva del cliente integrata nella propria esperienza per completare la registrazione del cliente finale e attivare l'acquisto.  In questa pagina l'utente deve iscriversi con l'autenticazione con un clic usando Azure Active Directory (Azure AD). <br> <br> Questo URL con il parametro del token di identificazione del Marketplace viene chiamato anche quando il cliente finale avvia un'esperienza SaaS gestita dall'interfaccia di amministrazione di portale di Azure o M365. È necessario gestire entrambi i flussi, quando il token viene fornito per la prima volta dopo l'acquisto per i nuovi clienti e quando viene fornito per i clienti esistenti che gestiscono il proprio SaaS. <br> <br> La pagina di destinazione configurata qui dovrebbe essere attiva ed eseguita 24/7. Questo è l'unico modo per ricevere notifiche sui nuovi acquisti delle offerte SaaS effettuate nel Marketplace o sulle richieste di configurazione di una sottoscrizione attiva di un'offerta.

- **Webhook di connessione** (obbligatorio): per tutti gli eventi asincroni che Microsoft deve inviare all'utente (ad esempio, la sottoscrizione Saas è stata annullata), è necessario specificare un URL del webhook di connessione. Questo URL verrà chiamato per notificare l'evento. <br> <br> Il webhook fornito dovrebbe essere attivo e in esecuzione 24/7 poiché questo è l'unico modo per ricevere una notifica sugli aggiornamenti delle sottoscrizioni SaaS dei clienti acquistate tramite il Marketplace.  Se non si dispone già di un sistema di Webhook, la configurazione più semplice consiste nel disporre di un'app per la logica endpoint HTTP che resterà in ascolto di tutti gli eventi da inviare e quindi di gestirli in modo appropriato (ad esempio, `https://prod-1westus.logic.azure.com:443/work` ). Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](../../logic-apps/logic-apps-http-endpoint.md).

- **Azure ad ID tenant** (obbligatorio): all'interno del portale di Azure, è necessario [creare un'app di Azure Active Directory (ad)](../../active-directory/develop/howto-create-service-principal-portal.md) in modo che sia possibile convalidare la connessione tra i due servizi sia dietro una comunicazione autenticata. Per trovare l' [ID tenant](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad ID app** (obbligatorio): è necessario anche l' [ID applicazione](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Per ottenere il valore, passare alla Azure Active Directory e selezionare **registrazioni app**, quindi cercare il numero di **ID dell'applicazione** elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

>[!NOTE]
>Il Azure AD ID app è associato all'ID editore nell'account del centro per i partner.  Assicurarsi che in tutte le offerte venga usato lo stesso ID applicazione.

>[!NOTE]
>Se il server di pubblicazione dispone di due o più account diversi nel centro per i partner, è necessario usare due o più ID app Azure AD diversi, ognuno per uno degli account. Ogni account partner nel centro per i partner deve usare un ID app univoco Azure AD per tutte le offerte SaaS pubblicate tramite questo account.

Prima di continuare, selezionare **Salva bozza**.

## <a name="plan-overview"></a>Panoramica del piano

Questa pagina consente di fornire diverse opzioni per i piani all'interno della stessa offerta. Questi piani (noti anche come SKU) possono differire in termini di versione, monetizzazione o livelli di servizio. Per vendere l'offerta nel marketplace, è necessario configurare almeno un piano.

Una volta creati i piani, verranno visualizzati i nomi, gli ID, i modelli di determinazione prezzi, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente e tutte le azioni disponibili.

Le **azioni** disponibili in **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

- Se lo stato del piano è **Bozza**, Elimina bozza
- Se lo stato del piano è **Attivo**, Interrompi vendita o Sincronizza destinatari privati

**Crea nuovo piano** (almeno un piano se si sceglie di vendere tramite Microsoft)

- **ID piano**: creare un ID piano univoco per ogni piano in questa offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto e nei modelli di Azure Resource Manager (se applicabile). Usare solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Questo ID non può essere modificato dopo aver selezionato Crea.
- **Nome piano**: i clienti visualizzano il nome quando decidono quale piano scegliere nell'offerta. Creare un nome di offerta univoco per ogni piano nell'offerta. Il nome del piano viene usato per distinguere i piani software che possono fare parte della stessa offerta (ad esempio, nome dell'offerta: Windows Server, piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Elenco piani

Questa pagina consente di definire il nome e la descrizione del piano. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Nome**: questo campo viene completato automaticamente in base alla voce **Nuovo piano** per l'anteprima e apparirà come titolo in "Piano software" per l'offerta visualizzato nel marketplace.
- **Descrizione**: questa descrizione offre l'opportunità di spiegare che cosa rende unico questo piano software e qualsiasi differenza rispetto ad altri piani software all'interno dell'offerta. Può contenere un massimo di 500 caratteri.

Prima di continuare, selezionare **Salva bozza**.

#### <a name="pricing-and-availability"></a>Prezzi e disponibilità

Questa pagina consente di configurare i mercati in cui sarà disponibile questo piano, il modello di monetizzazione, il prezzo e il periodo di fatturazione desiderati. È anche possibile indicare se rendere il piano visibile a tutti o solo a specifici clienti (gruppo di destinatari privato).

#### <a name="markets-optional"></a>Mercati (facoltativo)

Ogni piano deve essere disponibile in almeno un mercato. Selezionare **Modifica mercati** e selezionare la casella di controllo relativa alla località di qualsiasi mercato in cui si vuole rendere disponibile questo piano. Questa pagina include una casella di ricerca e un'opzione per la selezione di paesi o aree geografiche in cui Microsoft versa l'IVA e l'imposta sui beni durevoli per conto dell'editore.

Se i prezzi per il piano sono già stati impostati in dollari statunitensi (USD) e si aggiunge la località di un altro mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Controllare il prezzo di ogni mercato prima della pubblicazione. Visualizzare i prezzi tramite il collegamento "Export prices (xlsx)" (Esporta prezzi - XLSX) dopo aver salvato le modifiche.

Prima di continuare, selezionare **Salva**.

#### <a name="pricing"></a>Prezzi

##### <a name="pricing-model"></a>Modello di prezzi

**Tariffa fissa**: consente di accedere all'offerta con un unico prezzo forfettario mensile o annuale. Questo approccio è a volte noto come prezzo basato sul sito. Con questo modello di determinazione prezzi è possibile definire piani a consumo che usano l'API del servizio di analisi del marketplace per addebitare i costi ai clienti in base a unità non standard.  Per altre informazioni sulla fatturazione a consumo, vedere [Fatturazione a consumo con il servizio di analisi del marketplace](./saas-metered-billing.md).  Usare questa opzione anche se il comportamento di utilizzo si trova in picchi per il servizio SaaS.  Non è consigliabile che il cliente Commuti spesso i piani su base giornaliera o oraria.

**Per utente**: consente l'accesso all'offerta con il prezzo basato sul numero di utenti che accedono all'offerta o che occupano postazioni. Questo modello basato sull'utente permette di impostare il numero minimo e massimo di utenti consentiti in base al prezzo. In questo modo, è possibile configurare prezzi diversi in base al numero di utenti configurando più piani.  Questi campi sono facoltativi. Se lasciati non selezionati, il numero di utenti verrà interpretato in modo da non avere limiti (minimo 1 e fino a quanti ne può supportare il sistema). Questi campi possono essere modificati nell'ambito di un aggiornamento del piano.

Dopo la pubblicazione, non è possibile modificare la scelta del modello di determinazione prezzi per la fatturazione. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione prezzi.

##### <a name="user-limits"></a>Limiti di utenti

Se applicabile, selezionare e impostare i limiti minimo e massimo per gli utenti.

##### <a name="billing-term-and-price"></a>Periodo di fatturazione e prezzi

Selezionare i valori desiderati in **Periodo** e **Prezzo** in base ai quali i clienti devono pagare per l'offerta. È necessario specificare almeno un prezzo mensile o annuale oppure entrambe le opzioni possono essere rese disponibili ai clienti.

I prezzi impostati in USD (USD = dollaro statunitense) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e controllando il prezzo in ogni mercato. Per impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. L'editore è tenuto a convalidare questi prezzi e ha la proprietà delle impostazioni.
*\*È necessario salvare prima di tutto le modifiche ai prezzi per consentire l'esportazione dei dati relativi ai prezzi.*

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune restrizioni relative alle modifiche consentite dopo la pubblicazione di un piano:

- Una volta pubblicato un piano, il modello di determinazione prezzi non può essere modificato.
- Una volta pubblicato un periodo di fatturazione per un piano, non è possibile rimuoverlo in un secondo momento.
- Una volta pubblicato il prezzo per un mercato nel piano, non è possibile modificarlo in un secondo momento.

#### <a name="free-trial"></a>Versione di valutazione gratuita

Le offerte SaaS tramite il Marketplace commerciale ti permettono di offrire una versione di valutazione gratuita di un mese per la vendita tramite Microsoft. Le versioni di valutazione gratuite sono supportate per tutti i modelli e i periodi di fatturazione ad eccezione dei piani a consumo. Questa opzione consente ai clienti di ridurre gli ostacoli all'accesso tramite un mese gratuito.  Se si sceglie di abilitare una versione di valutazione gratuita per i piani all'interno dell'offerta, il cliente non sarà in grado di eseguire la conversione a una sottoscrizione a pagamento prima della fine del periodo iniziale di un mese.  Durante questo periodo, i clienti che acquistano l'offerta possono provare tutti i piani supportati con la versione di valutazione gratuita abilitata e convertirli tra loro.  La conversione a una sottoscrizione a pagamento viene eseguita automaticamente alla fine del periodo.

>[!NOTE]
>Se il cliente sceglie di eseguire la conversione a un piano senza versioni di valutazione gratuite, la conversione viene eseguita, ma la versione di valutazione gratuita andrà persa immediatamente. Inoltre, quando un cliente inizia a pagare per un piano, non può più ottenere una versione di valutazione gratuita per la stessa sottoscrizione, anche se esegue la conversione a uno SKU che supporta versioni di valutazione gratuite.

Qui è possibile configurare una versione di valutazione gratuita per ogni piano all'interno dell'offerta. Selezionare la casella di controllo per consentire una versione di valutazione di un mese.

![Casella di controllo per la versione di valutazione gratuita di un mese](./media/free-trial-enable.png)

>[!NOTE]
>Quando l'offerta transazionale è stata pubblicata con una versione di valutazione gratuita, non può essere disabilitata per questo piano. Verificare che questa impostazione sia corretta per la prima pubblicazione, in modo da evitare di dover ricreare il piano.

Per ottenere informazioni sulle sottoscrizioni dei clienti che attualmente partecipano a una versione di valutazione gratuita, usare la nuova proprietà API `isFreeTrial`, che viene contrassegnata come true o false. Per altre informazioni, vedere l'[API di ottenimento delle sottoscrizioni SaaS](pc-saas-fulfillment-api-v2.md#get-subscription).

>[!NOTE]
>Le versioni di valutazione gratuite non sono supportate per i piani che sfruttano il servizio di analisi del marketplace.

#### <a name="plan-visibility"></a>Plan Visibility (Visibilità del piano)

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando gli ID tenant di Azure.

##### <a name="privacy"></a>Privacy

Selezionare **Questo è un piano privato** per rendere il piano privato e visibile solo a un gruppo limitato di destinatari di propria scelta. Una volta pubblicato il piano come privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Dopo che un piano è stato pubblicato come visibile a tutti, deve restare visibile a tutti gli utenti e non può essere riconfigurato come piano privato.

##### <a name="restricted-audience-tenant-ids"></a>**Gruppo di destinatari con limitazioni (ID tenant)**

Assegnare i destinatari che avranno accesso a questo piano privato. L'accesso viene assegnato usando gli ID tenant con l'opzione per includere una descrizione di ogni ID tenant assegnato. È possibile aggiungere un massimo di 10 ID tenant oppure 20.000 ID tenant dei clienti se si importa un file di foglio di calcolo CSV.

Un tenant è una rappresentazione di un'organizzazione, con un ID rappresentato come GUID (identificatore univoco globale, un numero intero a 128 bit usato per identificare le risorse). Si tratta di un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft, ad esempio durante l'iscrizione ad Azure, Microsoft Intune o Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD. Per controllare il tenant, accedere al portale di Azure con l'account che si vuole usare per gestire l'applicazione. Se si dispone già di un tenant, verrà effettuata automaticamente la connessione e si vedrà il nome del tenant direttamente sotto il nome dell'account. Se si passa il puntatore sul nome dell'account nella parte superiore destra del portale di Azure, verranno visualizzati il nome utente, l'indirizzo di posta elettronica, un ID di directory/tenant (GUID) e il dominio. Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID. È anche possibile cercare l'ID tenant dell'organizzazione usando un URL del nome di dominio all'indirizzo [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Benché le offerte SaaS usino ID tenant per definire un gruppo di destinatari privato, altri tipi di offerta possono usare gli ID sottoscrizione di Azure (anch'essi rappresentati come GUID).

> [!NOTE]
> Un gruppo di destinatari privato (o limitato) è diverso da un gruppo di destinatari per l'anteprima. Nella pagina **[Anteprima](#preview-audience)** è possibile definire un gruppo di destinatari per l'anteprima. Un gruppo di destinatari per l'anteprima può accedere all'offerta *prima* che l'offerta venga pubblicata nel marketplace. Mentre la designazione di un gruppo di destinatari privato si applica solo a un piano specifico, il gruppo di destinatari per l'anteprima può visualizzare tutti i piani (privati o meno), ma solo nel periodo di anteprima limitato durante il test e la convalida del piano.

Prima di continuare, selezionare **Salva bozza**.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Elenco di esempio di piani all'interno di un'offerta nel marketplace

:::image type="content" source="media/marketplace-plan.png" alt-text="Elenco del piano di Marketplace di esempio con note.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Nome piano
2. Descrizione piano

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Destinatari rivenditori CSP

La scelta di rendere disponibile l'offerta nel programma CSP consente ai Cloud Solution Provider di vendere il prodotto come parte di una soluzione in bundle ai clienti. Per altre informazioni, vedere [Cloud Solution Provider](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Pubblica

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **Rivedi e pubblica** nell'angolo superiore destro del portale.

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
  - **Non avviata**: la sezione è stata lasciata inalterata e deve essere completata.
  - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario fornire altre informazioni. È necessario tornare alla sezione e aggiornarla.
  - **Completa**: la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completo prima di poter inviare l'offerta.
- Fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Tornare al centro per i partner e selezionare **Go-Live** per pubblicare l'offerta sul pubblico (o se un'offerta privata, per i destinatari privati).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
