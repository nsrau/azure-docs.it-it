---
title: Creare un'offerta di app Power BI in Microsoft AppSource
description: Informazioni su come creare e pubblicare un'offerta per un'app Power BI in Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: fa8c08f5d6f49b12306ea1f4bb82c07747622f48
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011941"
---
# <a name="create-a-power-bi-app-offer"></a>Creare un'offerta di app Power BI

Questo articolo descrive come creare e pubblicare un'offerta per un'app Power BI in Microsoft [AppSource](https://appsource.microsoft.com/).

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **App del servizio Power BI**.

   ![Immagine del menu di spostamento a sinistra.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

> [!IMPORTANT]
> Se l'opzione **App del servizio Power BI** non è visualizzata o abilitata, l'account non è autorizzato a creare questo tipo di offerta. Verificare di aver soddisfatto tutti i [requisiti](create-power-bi-app-overview.md) per questo tipo di offerta, tra cui la registrazione per un account sviluppatore.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta nel marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

Questa pagina mostra una rappresentazione visiva dei passaggi per pubblicare l'offerta (completa o imminente) e il tempo necessario per completare ogni passaggio.

Sono inclusi i collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è una bozza di [eliminazione](update-existing-offer.md#delete-a-draft-offer),
- Se l'offerta è Live- [stop selling the offer](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima- [Go-Live](publishing-status.md#publisher-approval))
- Se il server di pubblicazione non è stato completato, [annullare la pubblicazione](update-existing-offer.md#cancel-publishing))

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="customer-leads"></a>Lead clienti

Quando si pubblica l'offerta nel marketplace con il Centro per i partner, è necessario connetterla al sistema Customer Relationship Management (CRM). In questo modo, è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime interesse o usa il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i sistemi CRM seguenti:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se il sistema CRM non è incluso nell'elenco, usare [Tabella di Azure](commercial-marketplace-lead-management-instructions-azure-table.md) o [Endpoint HTTPS](commercial-marketplace-lead-management-instructions-https.md) per archiviare i dati dei lead clienti. Esportare quindi i dati nel sistema CRM.

2. Quando si pubblica l'offerta nel Centro per i partner, connettere l'offerta alla destinazione dei lead.
3. Verificare che la connessione alla destinazione dei lead sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'offerta in anteprima prima della pubblicazione, è anche possibile testare la connessione dei lead provando ad acquistare personalmente l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione dei lead resti aggiornata in modo da non perdere alcun lead.

Di seguito sono elencate alcune risorse aggiuntive per la gestione dei lead:

- [Panoramica della gestione dei lead](commercial-marketplace-get-customer-leads.md)
- [Domande frequenti sulla gestione dei lead](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [Errori comuni di configurazione del lead](../lead-management-for-cloud-marketplace.md#publishing-config-errors)
- PDF [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assicurarsi che il blocco dei popup sia disattivato)

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Categoria

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](../gtm-offer-listing-best-practices.md)consigliate.

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Note legali

#### <a name="terms-and-conditions"></a>Termini e condizioni

Per specificare termini e condizioni personalizzati, immettere fino a 10.000 caratteri nella casella **Termini e condizioni**. Se i termini e le condizioni richiedono una descrizione più lunga, immettere un unico collegamento Web alla pagina in cui possono essere consultati. Verrà visualizzato ai clienti come collegamento attivo.

I clienti devono accettare queste condizioni prima di poter provare l'offerta.

Prima di passare alla sezione successiva, Presentazione dell'offerta, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

È possibile definire i dettagli dell'offerta visualizzati nel marketplace. Sono inclusi il nome dell'offerta, la descrizione, le immagini e così via.

### <a name="language"></a>Lingua

Selezionare la lingua in cui verrà presentata l'offerta. Attualmente, **Inglese (Stati Uniti)** è l'unica opzione disponibile.

Definire i dettagli del marketplace per ogni lingua/mercato, ad esempio il nome dell'offerta, la descrizione e le immagini. Selezionare il nome della lingua e del mercato in cui fornire queste informazioni.

> [!NOTE]
> I dettagli dell'offerta non devono essere necessariamente in inglese se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un collegamento utile per offrire contenuti in una lingua diversa da quella usata nella presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prodotti
3. Categorie
4. Settori
5. Indirizzo di supporto (collegamento)
6. Condizioni per l'utilizzo
7. Informativa sulla privacy
8. Nome offerta
9. Riepilogo
10. Descrizione
11. Screenshot/video

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo viene precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. È possibile modificarlo in seguito.

Il nome:

- Può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright).
- Non può avere una lunghezza superiore a 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Specificare una breve descrizione dell'offerta. Questa può essere lunga 100 caratteri e viene usata nei risultati della ricerca nel marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Immettere fino a tre parole chiave di ricerca facoltative per aiutare i clienti a trovare l'offerta nel marketplace. Per ottenere risultati ottimali, usare queste parole chiave anche nella descrizione.

### <a name="helpprivacy-web-addresses"></a>Indirizzi Web della Guida/informativa sulla privacy

Fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla Guida

Immettere l'indirizzo Web in cui i clienti possono ottenere altre informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. L'editore è tenuto a verificare che l'offerta sia conforme alle leggi e normative sulla privacy. È anche responsabile della pubblicazione di un'informativa sulla privacy valida sul proprio sito Web.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto di supporto** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti. Sono disponibili per Microsoft e possono essere fornite ai partner CSP (Cloud Solution Provider).

- Contatto supporto tecnico (obbligatorio): per domande di supporto generiche.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto programma CSP (facoltativo): per domande destinate al rivenditore relative al programma CSP.

Nella sezione **Contatto di supporto** specificare l'indirizzo Web per **Sito Web supporto** in cui i partner possono trovare supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire almeno uno e fino a tre documenti di marketing correlati in formato PDF. Ad esempio, white paper, brochure, elenchi di controllo o presentazioni.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfuocate verranno rifiutate.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio `https://upload.xboxlive.com` usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire file PNG del logo dell'offerta in due dimensioni in pixel:
- **Piccola** (48 x 48)
- **Grande** (216 x 216)

Entrambi i logo sono obbligatori e vengono usati in posizioni diverse nella presentazione nel marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere almeno uno e fino a cinque screenshot che mostrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG.

#### <a name="videos-optional"></a>Video (facoltativi)

Aggiungere fino a cinque video che descrivono l'offerta. Immettere il nome del video, l'indirizzo Web e l'immagine PNG dell'anteprima del video con dimensioni pari a 1280 x 720 pixel.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

Per altre informazioni sulla creazione di presentazioni di offerte, vedere [Procedure consigliate per la presentazione di offerte](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Configurazione tecnica

Promuovere l'app nel servizio Power BI all'ambiente di produzione e fornire il collegamento al programma di installazione dell'app Power BI che consente ai clienti di installare l'app. Per altre informazioni, vedere [Pubblicare app con dashboard e report in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Contenuto supplementare

Fornire informazioni aggiuntive sull'offerta per semplificarne la convalida. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="validation-assets"></a>Risorse di convalida

È possibile aggiungere istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Queste includono le impostazioni di configurazione, gli account, i parametri o altre informazioni tipiche che è possibile usare per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono usate solo per scopi di convalida.

## <a name="review-and-publish"></a>Rivedi e pubblica

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, è possibile inviare l'offerta per la revisione e la pubblicazione.

Nell'angolo in alto a destra del portale selezionare **Rivedi e pubblica**.

Nella pagina di revisione è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta. Non è possibile eseguire la pubblicazione fino a quando tutte le sezioni dell'offerta non vengono contrassegnate come complete.
  - **Non avviata**: la sezione non è stata avviata e deve essere completata.
  - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario inserire altre informazioni. Per indicazioni, vedere le sezioni precedenti di questo documento.
  - **Completa**: la sezione contiene tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere complete prima di poter inviare l'offerta.
- Fornire al team di certificazione istruzioni di test per assicurarsi che l'app venga testata correttamente. Specificare anche eventuali note supplementari utili per comprendere l'offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Si riceverà un messaggio di posta elettronica per far sapere all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Per pubblicare l'offerta al pubblico, passare a centro per i partner e selezionare **Go-Live**.
