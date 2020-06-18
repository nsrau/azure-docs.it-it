---
title: Creare una nuova offerta SaaS per il marketplace commerciale Microsoft
description: Come creare una nuova offerta SaaS (software come un servizio) per la presentazione o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) usando il programma del marketplace commerciale Microsoft nel Centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 3c922675619fc877d9d97a43b69a15f5ca4f393e
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849111"
---
# <a name="create-a-new-saas-offer-in-the-commercial-marketplace"></a>Creare una nuova offerta SaaS nel marketplace commerciale

Per iniziare a creare offerte SaaS (software come un servizio) nel marketplace commerciale, assicurarsi prima di tutto di [creare un account per il Centro per i partner](./create-account.md) e aprire il [dashboard del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)con la scheda **Panoramica** selezionata.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Software come servizio**.

   ![Immagine del menu di spostamento a sinistra.](./media/new-offer-saas.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta nel marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

**Stato pubblicazione** mostra una rappresentazione visiva dei passaggi per pubblicare l'offerta e il tempo necessario per completare ogni passaggio. Le icone dei passaggi di pubblicazione incompleti vengono visualizzate in grigio.

Il menu **Panoramica offerta** contiene un elenco di collegamenti per l'esecuzione di operazioni su questa offerta. Questo elenco di operazioni cambia in base alle opzioni selezionate per l'offerta.  

- Se l'offerta è una bozza, Elimina bozza
- Se l'offerta è stata pubblicata, Interrompi vendita
- Se l'offerta è in anteprima, Passa allo stato Live
- Se non è stata completata la disconnessione dell'editore, Annulla pubblicazione

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
- L'offerta deve usare [API di adempimento SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per l'integrazione con Azure Marketplace.

#### <a name="saas-pricing-and-billing-options"></a>Opzioni di fatturazione e prezzi dell'offerta SaaS

Con le soluzioni SaaS in esecuzione nella sottoscrizione di Azure dell'editore, i costi di licenza pagati dai clienti includono il costo dell'infrastruttura su cui viene distribuito il software. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di aggregare i costi di utilizzo dell'infrastruttura di Azure ai prezzi di licenza software. 

Le offerte SaaS supportano la fatturazione mensile o annuale in base a una tariffa fissa, per utente o a costi di utilizzo usando il servizio di fatturazione a consumo. Il marketplace commerciale di Microsoft opera in base a un modello di agenzia, tramite il quale gli editori impostano i prezzi, Microsoft fattura ai clienti e quindi paga i ricavi all'editore, trattenendo una tariffa di agenzia.

Questo è un esempio di suddivisione dei costi e dei pagamenti per descrivere il modello di agenzia:

|**Costo della licenza**|**$ 100 al mese**|
|:---|:---|
|Costo dell'utilizzo di Azure (D1/1-Core)|Costi addebitati direttamente all'editore, non al cliente|
|Importo addebitato da Microsoft al cliente|$ 100,00 al mese. L'editore deve calcolare i costi di infrastruttura sostenuti o trasferirli nei costi di licenza|

|**Microsoft addebita**|**$ 100 al mese**|
|:---|:---|
|Microsoft paga l'80% del costo della licenza <br>**Per le app SaaS idonee, Microsoft paga il 90% del costo di licenza*|$ 80,00 al mese <br>*$* 90,00 al mese*|

- In questo esempio Microsoft fattura $ 100,00 al cliente per la licenza software e paga $ 80,00 all'editore.
- I partner idonei per la **riduzione della tariffa del servizio marketplace** visualizzeranno una tariffa di transazione ridotta per le offerte SaaS da maggio 2019 a giugno 2020. In questo scenario Microsoft fattura $ 100,00 per la licenza software e paga $ 90,00 all'editore.

> [!NOTE]
> **Tariffa ridotta del servizio marketplace**: per alcune offerte SaaS pubblicate nel marketplace commerciale, Microsoft ridurrà la tariffa del servizio marketplace dal 20% (indicata nel contratto Microsoft con l'editore) al 10%. Perché l'offerta sia idonea, almeno una delle offerte deve essere stata indicata da Microsoft come pronta per il co-selling della proprietà intellettuale o come soluzione di co-selling della proprietà intellettuale con priorità. L'idoneità deve essere soddisfatta per almeno cinque (5) giorni lavorativi prima della fine di ogni mese del calendario per poter usufruire di questa riduzione della tariffa per il servizio marketplace per il mese.  La riduzione della tariffa del servizio marketplace non si applica alle macchine virtuali, alle app gestite o ad altri prodotti resi disponibili tramite il marketplace commerciale. La riduzione della tariffa del servizio marketplace è disponibile solo per le offerte idonee per i costi di licenza raccolti da Microsoft tra il 1° maggio 2019 e il 30 giugno 2020. Al termine di questo periodo di tempo, la tariffa del servizio marketplace tornerà all'importo normale.

### <a name="list-through-microsoft"></a>Presentazione tramite Microsoft

È possibile promuovere la propria azienda con Microsoft creando una presentazione nel marketplace. La scelta di presentare l'offerta autonomamente e non con una transazione tramite Microsoft significa che Microsoft non partecipa direttamente alle transazioni di licenza software. Non viene addebitata alcuna tariffa per le transazioni e l'editore mantiene il 100% dei costi di licenza software raccolti dal cliente. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione di licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, analisi, fatturazione, pagamento e raccolta.

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>Scarica ora (gratuito)

È possibile presentare gratuitamente l'offerta ai clienti fornendo un indirizzo valido (che inizi con *http* o *https*) dal quale possono ottenere una versione di valutazione con l'[autenticazione con un clic usando Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials). Ad esempio: `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Free trial (listing) (Versione di valutazione gratuita - Presentazione)

È possibile presentare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita specificando un indirizzo valido (che inizi con *http* o *https*) dal quale possono ottenere una versione di valutazione con l'[autenticazione con un clic usando Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials). Ad esempio: `https://contoso.com/trial/saas-app`. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni sugli utenti tramite Azure AD per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Lead clienti](#customer-leads).

#### <a name="example-marketplace-offer-listing"></a>Presentazione di offerte di esempio nel marketplace

![Presentazione di offerte di esempio nel marketplace con note](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Abilitare un test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo più conversioni e la generazione di lead altamente qualificati. [Altre informazioni sui test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Per altre informazioni, vedere [Test drive dell'offerta nel marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

### <a name="test-drive-resources"></a>Risorse per il test drive

- [Procedure consigliate di marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Panoramica](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (file PDF, assicurarsi che il blocco popup sia disattivato)

### <a name="customer-leads"></a>Lead clienti

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Risorse aggiuntive per la gestione dei lead
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Pagina di panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina richiede di definire le categorie e i settori usati per raggruppare l'offerta nei marketplace, i contratti legali che supportano l'offerta e la versione dell'app.

### <a name="category"></a>Category

Selezionare almeno una e fino a tre categorie per raggruppare l'offerta nelle aree di ricerca del marketplace appropriate. Descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="industries"></a>Settori

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

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

**Condizioni di modifica universali del contratto standard per il marketplace commerciale di Microsoft**: immettere le condizioni di modifica universali in questa casella. È possibile specificare una sola modifica universale per ogni offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

**Condizioni di modifica personalizzate del contratto standard per il marketplace commerciale di Microsoft**: per iniziare, selezionare **Aggiungi condizione di modifica personalizzata**. È possibile specificare fino a 10 condizioni di modifica personalizzate per ogni offerta.

- **Condizioni di modifica personalizzate**: immettere le condizioni di modifica personalizzate in questa casella. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni verranno visualizzate solo ai clienti degli ID tenant specificati per queste condizioni personalizzate nel flusso di acquisto dell'offerta nel portale di Azure.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario specificare almeno un ID tenant. L'ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e il cliente può trovarlo passando a portal.azure.com > Azure Active Directory > Proprietà. Il valore dell'ID directory è l'ID tenant, ad esempio 50c464d3-4930-494c-963C-1e951d15360e. È anche possibile cercare l'ID tenant dell'organizzazione del cliente usandone l'URL del nome di dominio in [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com).
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
- **Descrizione** (obbligatoria): fornire una descrizione dell'offerta da visualizzare nella panoramica delle presentazioni nel marketplace. È consigliabile includere una proposta di valore, i vantaggi principali, qualsiasi categoria o settore associato, le opportunità di acquisto in-app, tutte le comunicazioni necessarie e un collegamento ad altre informazioni. In questo campo è possibile immettere fino a 3.000 caratteri di testo, incluso il markup. Per altri suggerimenti, vedere [Scrivere un'ottima descrizione dell'app](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Parole chiave di ricerca**: immettere fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta nei marketplace.
- **Istruzioni sulle attività iniziali** (obbligatorio): spiegare come configurare e iniziare a usare l'app per i potenziali clienti.  Queste informazioni di avvio rapido possono contenere collegamenti a una documentazione online più dettagliata. In questo campo è possibile immettere fino a 3.000 caratteri di testo.

#### <a name="description"></a>Descrizione

Questo campo è obbligatorio.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Collegamenti

- **Informativa sulla privacy** (obbligatorio): collegamento all'informativa sulla privacy dell'organizzazione. L'editore è tenuto a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida
- **Materiale marketing del programma CSP** (facoltativo): fornire un collegamento ai materiali di marketing se si sceglie di estendere l'offerta al programma [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers). Il programma CSP estende l'offerta a una gamma più ampia di clienti idonei, permettendo ai partner CSP di aggregare, commercializzare e rivendere l'offerta. Questi rivenditori dovranno poter accedere ai materiali per il marketing dell'offerta. Per altre informazioni, vedere [Servizi Go-To-Market](https://partner.microsoft.com/reach-customers/gtm).
- **Collegamenti utili** (facoltativo): documenti online supplementari facoltativi sull'app o sui servizi correlati elencati impostando i campi **Titolo** e **URL**. Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi un URL**.

#### <a name="contact-information"></a>Informazioni contatto

- **Contatti**: per ogni contatto del cliente, completare i campi **Nome**, **Numero di telefono** e **Indirizzo di posta elettronica** di un dipendente (che *non* verranno visualizzati pubblicamente). È necessario anche immettere un valore in **URL supporto**  per il gruppo **Contatto di supporto** (che *verrà* visualizzato pubblicamente).

    - **Contatto di supporto** (obbligatorio): per domande di supporto generali.
    - **Contatto tecnico** (obbligatorio): per domande tecniche.
    - **Channel Manager Contact** (Contatto responsabile canale) (obbligatorio): per domande sui rivenditori correlate al programma CSP.

#### <a name="files-and-images"></a>File e immagini

- **Documenti** (obbligatorio): aggiungere documenti di marketing correlati per l'offerta, in formato PDF, fornendo almeno uno (1) e fino a tre (3) documenti per ogni offerta.
- **Immagini** (facoltativo): le immagini del logo dell'offerta possono apparire in più posizioni nei marketplace e devono essere in formato PNG con le dimensioni in pixel seguenti:

    - **Piccola** (48 x 48, obbligatoria)
    - **Media** (90 x 90, obbligatoria)
    - **Grande** (216 x 216, obbligatoria)
    - **Molto grande** (255 x 115)
    - **Hero** (815 x 290)

- **Screenshot** (obbligatorio): aggiungere screenshot che illustrano l'offerta. È possibile aggiungere un massimo di cinque (5) screenshot con dimensioni 1280 x 720 pixel. Tutte le immagini devono essere in formato PNG.
- **Video** (facoltativo): aggiungere collegamenti a video che mostrano l'offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. È necessario immettere anche un'immagine di anteprima del video, in formato PNG con dimensioni 1280 x 720 pixel. È possibile fornire un massimo di quattro video per ogni offerta.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

- [Procedure consigliate per la presentazione di offerte nel marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

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

Questa pagina definisce i dettagli tecnici (percorso URL, webhook, ID tenant e ID app) usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla. I diagrammi che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione per le [API di adempimento SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL pagina di destinazione** (obbligatorio): definire l'URL cui verranno indirizzati i clienti dopo aver acquistato l'offerta dal marketplace. Questo URL sarà l'endpoint che riceve un token quando un cliente viene indirizzato nella pagina. Questo token può essere scambiato per dettagli di provisioning usando la risoluzione nelle API di adempimento. Questi e tutti gli altri dettagli raccolti possono essere usati come parte di una pagina Web interattiva per il cliente integrata nell'esperienza per completare la registrazione e attivare l'acquisto.

- **Webhook connessione** (obbligatorio): è necessario fornire un webhook di connessione per tutti gli eventi asincroni che Microsoft deve inviare all'editore per conto del cliente, ad esempio riguardo all'invalidità della sottoscrizione SaaS. Se non è disponibile un sistema di webhook, la configurazione più semplice consiste in un'app per la logica con endpoint HTTP che resta in ascolto degli eventi inseriti e li gestisce in modo appropriato, ad esempio https:\//prod-1westus.logic.azure.com:443/work. Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenant di Azure AD** (obbligatorio): all'interno del portale di Azure è necessario [creare un'app Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in modo che sia possibile verificare che la connessione tra i due servizi sia protetta da una comunicazione autenticata. Per trovare l'[ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), passare ad Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato, ad esempio 50c464d3-4930-494c-963C-1e951d15360e.

- **ID applicazione Azure Active Directory** (obbligatorio): sono necessari anche l'[ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e una chiave di autenticazione. Per ottenere questi valori, passare ad Azure Active Directory e selezionare **Registrazioni app**, quindi cercare il numero **ID applicazione** elencato, ad esempio 50c464d3-4930-494c-963C-1e951d15360e. Per trovare la chiave di autenticazione, passare a **Impostazioni** e selezionare **Chiavi**. Sarà necessario specificare una descrizione e una durata, quindi verrà fornito un valore numerico.

>[!Note]
>Poiché l'ID applicazione di Azure è associato all'ID editore, assicurarsi che venga usato lo stesso ID applicazione in tutte le offerte.

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

**Tariffa fissa**: consente di accedere all'offerta con un unico prezzo forfettario mensile o annuale. Questo approccio è a volte noto come prezzo basato sul sito. Con questo modello di determinazione prezzi è possibile definire piani a consumo che usano l'API del servizio di analisi del marketplace per addebitare i costi ai clienti in base a unità non standard.  Per altre informazioni sulla fatturazione a consumo, vedere [Fatturazione a consumo con il servizio di analisi del marketplace](./saas-metered-billing.md).

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

Le offerte SaaS tramite il marketplace commerciale consentono di offrire una versione di valutazione gratuita di un mese quando si sceglie la vendita tramite Microsoft. Le versioni di valutazione gratuite sono supportate per tutti i modelli e i periodi di fatturazione ad eccezione dei piani a consumo. Questa opzione consente ai clienti di ridurre gli ostacoli all'accesso tramite un mese gratuito.  Se si sceglie di abilitare una versione di valutazione gratuita per i piani all'interno dell'offerta, il cliente non sarà in grado di eseguire la conversione a una sottoscrizione a pagamento prima della fine del periodo iniziale di un mese.  Durante questo periodo, i clienti che acquistano l'offerta possono provare tutti i piani supportati con la versione di valutazione gratuita abilitata e convertirli tra loro.  La conversione a una sottoscrizione a pagamento viene eseguita automaticamente alla fine del periodo.

>[!NOTE]
>Se il cliente sceglie di eseguire la conversione a un piano senza versioni di valutazione gratuite, la conversione viene eseguita, ma la versione di valutazione gratuita andrà persa immediatamente. Inoltre, quando un cliente inizia a pagare per un piano, non può più ottenere una versione di valutazione gratuita per la stessa sottoscrizione, anche se esegue la conversione a uno SKU che supporta versioni di valutazione gratuite.

Qui è possibile configurare una versione di valutazione gratuita per ogni piano all'interno dell'offerta. Selezionare la casella di controllo per consentire una versione di valutazione di un mese.

![Casella di controllo per la versione di valutazione gratuita di un mese](./media/free-trial-enable.png)

>[!NOTE]
>Quando l'offerta transazionale è stata pubblicata con una versione di valutazione gratuita, non può essere disabilitata per questo piano. Verificare che questa impostazione sia corretta per la prima pubblicazione, in modo da evitare di dover ricreare il piano.

Per ottenere informazioni sulle sottoscrizioni dei clienti che attualmente partecipano a una versione di valutazione gratuita, usare la nuova proprietà API `isFreeTrial`, che viene contrassegnata come true o false. Per altre informazioni, vedere l'[API di ottenimento delle sottoscrizioni SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

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

![Presentazione di un piano di esempio nel marketplace con note](./media/marketplace-plan.svg)

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
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica che indica che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. È necessario tornare al Centro per i partner e selezionare **Passa allo stato Live** per l'offerta per pubblicare l'offerta al pubblico (o, se è un'offerta privata, a destinatari privati).

## <a name="next-step"></a>Passaggio successivo

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
