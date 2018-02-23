---
title: Preparare e testare l'offerta per la distribuzione in Azure Marketplace | Documentazione Microsoft
description: Istruzioni dettagliate fornitura di contenuti di marketing, configurazione dei prezzi e test dell'offerta prima della distribuzione in Azure Marketplace.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Completare la creazione dell’offerta con contenuti di marketing
In questo passaggio del processo di pubblicazione, sarà necessario fornire alcuni contenuti di marketing e informazioni dettagliate sull'offerta e/o sugli SKU in Azure Marketplace. Ad esempio, si fornirà una descrizione del prodotto, i logo aziendali, piani di prezzo, dettagli dei piani e altre informazioni necessarie per portare l'offerta e/o SKU in gestione temporanea. Queste informazioni vengono utilizzate come contenuto di marketing nel portale di Azure. Si inizierà il processo nel [portale di pubblicazione][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Passaggio 1: Fornire contenuti di marketing di Marketplace
**Inglese è il valore predefinito e la sola lingua supportata.** Assicurarsi che tutte le informazioni contenute nei campi siano in inglese. Tutte le informazioni possono essere modificate in qualsiasi momento fino al passaggio alla gestione temporanea.

1. Passare al portale di pubblicazione, [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. Nel menu a sinistra fare clic sulla scheda **Marketing** .
3. Nel pannello principale, fare clic sul pulsante **English (US)** .
   
   > [!IMPORTANT]
   > Tutti i campi devono essere completati, incluse le immagini, per poter passare alla gestione temporanea.
   > 
   > 

### <a name="details-and-plans"></a>Dettagli e piani
1. Immettere nella scheda **Dettagli** il titolo dell'offerta composto da 50 caratteri al massimo, il riepilogo dell'offerta di 100 caratteri al massimo, il riepilogo lungo dell'offerta di 256 caratteri al massimo, la descrizione dell'offerta di 1300 caratteri al massimo e il logo.
2. Immettere il titolo del piano di 50 caratteri al massimo, il riepilogo del piano di 100 caratteri al massimo e la descrizione del piano di 2000 caratteri al massimo nella scheda **Piani** .
   
   > [!NOTE]
   > Per formattare il riepilogo, il riepilogo lungo e la descrizione dell'offerta e dei piani è possibile usare i seguenti tag HTML. I tag HTML consentiti sono h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], strong, em, b, i.
   > 
   > 
3. Non immettere testi duplicati nell'offerta e nella descrizione del piano.
4. Non immettere testi duplicati nel titolo del piano e nel riepilogo lungo dell'offerta.
5. Non immettere testi duplicati nel titolo del piano e nel riepilogo dell'offerta.
6. Non immettere titoli di piano identici per un'offerta con più piani.
7. Caricare le immagini delle specifiche richieste indicate nel portale di pubblicazione in formato PNG, una per ogni dimensione.
8. Assicurarsi che il logo sia conforme alle linee guida per il logo di Azure Marketplace riportate di seguito.
   
   ![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Linee guida per il Logo di Azure Marketplace**

Ogni logo caricato nel portale di pubblicazione deve rispettare le seguenti linee guida:

* La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
* I colori del tema del portale di Azure sono il bianco e il nero. Pertanto evitare di usare questi colori per lo sfondo dei logo. Usare un colore che faccia risaltare i logo nel portale di Azure. Si consiglia di usare colori primari semplici. **Se si usa lo sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.**
* Non usare uno sfondo sfumato sul logo.
* Evitare di inserire testo, anche il nome del marchio o della società, sul logo. L'aspetto del logo deve essere semplice e senza sfumature.
* Il logo non può essere allungato.
* Il formato del logo piccolo deve essere di 40 X 40 pixel
* Il formato del logo medio deve essere 90 X 90 pixel
* Il formato del logo grande deve essere di 115 X 115 pixel
* Il formato del logo largo deve essere di 255 X 115 pixel
* Il formato del logo alto deve essere di 815 X 290 pixel

> [!NOTE]
> Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. Tuttavia, una volta caricata, l'icona del logo alto non può essere eliminata dal portale di pubblicazione. A quel punto il partner deve seguire le istruzioni di Azure Marketplace per le icone del logo alto.
> 
> 

  ![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Indicazioni aggiuntive per l'icona del logo alto (facoltativo)**

* Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. **Tuttavia, una volta caricata, l'icona del logo alto non può essere eliminata dal portale di pubblicazione. A quel punto il partner deve seguire le istruzioni di Azure Marketplace per le icone del logo alto o l'offerta non verrà approvata per la produzione.**
* Il nome visualizzato dell'autore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il testo bianco. Di conseguenza non è consigliabile usare un colore chiaro come sfondo dell'icona del logo alto. Lo sfondo nero, bianco o trasparente non è ammesso per le icone del logo alto.
* Il nome visualizzato dell'autore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati a livello di codice all'interno del logo alto quando l'offerta viene elencata. Pertanto non è necessario inserire testo mentre si progetta il logo alto. Lasciare uno spazio vuoto a destra, perché il testo (nome visualizzato dell'editore, titolo del piano e riepilogo lungo dell'offerta) viene automaticamente incluso a livello di codice. Lo spazio vuoto per il testo deve essere 415 x 100 a destra e viene spostato con un offset di 370 px da sinistra.
  
  ![disegno](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Collegamenti
Nella scheda dei **Collegamenti** sulla barra a sinistra immettere tutti i collegamenti con informazioni utili per i clienti. Immettere un nome e un URL per ogni collegamento.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Immagini di esempio (facoltative)
> [!NOTE]
> L’inclusione di un'immagine di esempio è un passaggio facoltativo.
> Anche se è possibile caricare più immagini di esempio nel portale di pubblicazione, solo un'immagine (selezionata in modo casuale dal sistema) viene visualizzata nel portale di Azure. Per questo motivo si consiglia di caricare al massimo un'immagine di esempio.
> 
> 

Nella scheda **Immagini di esempio** nel menu a sinistra caricare una nuova immagine facendo clic su **Upload a new image** (Carica una nuova immagine). Se si dispone di un'immagine esistente e si desidera sostituirla, fare clic su **Sostituisci immagine**.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Note legali
Nella scheda **Note legali** specificare un collegamento alle politiche/condizioni per l'utilizzo. Immettere o incollare le condizioni per l'utilizzo nella casella grande **Condizioni per l'utilizzo** . Il limite di caratteri per le condizioni per l'utilizzo è 1.000.000 caratteri.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Nota:** per le offerte per macchina virtuale, una volta che un'offerta o uno SKU è passato alla gestione temporanea nel portale di Azure, non è possibile modificare i campi indicati di seguito:

* **Offer Identifier (Identificatore offerte):** [Portale di pubblicazione -> Macchine virtuali -> selezionare l'offerta -> scheda Immagini VM -> Offer Identifier (Identificatore offerte)]
* **SKU Identifier (Identificatore SKU):** [Portale di pubblicazione -> Macchine virtuali -> selezionare l'offerta -> scheda SKU -> Add a SKU (Aggiungi uno SKU)]
* **Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione):** [Portale di pubblicazione -&gt; Macchine virtuali -&gt; scheda Procedura dettagliata -&gt; Tell Us About Your Company (Informazioni sull'azienda), disponibile nel Passaggio 2 "Registrazione dell'azienda" -&gt; Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione) -&gt;Spazio dei nomi]

Per le offerte per macchina virtuale, una volta che l'offerta o lo SKU è elencato in Azure Marketplace, non è possibile modificare i campi indicati di seguito:

* **Offer Identifier (Identificatore offerte):** [Portale di pubblicazione -> Macchine virtuali -> selezionare l'offerta -> Immagini VM -> Offer Identifier (Identificatore offerte)]
* **SKU Identifier (Identificatore SKU):** [Portale di pubblicazione -&gt; Macchine virtuali -&gt; selezionare l'offerta -&gt; scheda SKU -&gt; Add a SKU (Aggiungi uno SKU)]
* **Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione):** [Portale di pubblicazione -&gt; Macchine virtuali -&gt; scheda Procedura dettagliata -&gt; Tell Us About Your Company (Informazioni sull'azienda), disponibile nel Passaggio 2 "Registrazione -&gt; Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione) -&gt; Spazio dei nomi]
* **Porte:** [Portale di pubblicazione -&gt; Macchine virtuali -&gt; selezionare l'offerta -&gt; scheda Immagini VM -&gt; Open Ports (Porte aperte)]
* **Modifica dei prezzi degli SKU elencati**
* **Modifica del modello di fatturazione degli SKU elencati**
* **Rimozione delle aree di fatturazione degli SKU elencati**
* **Modifica del numero di dischi dati degli SKU elencati**

## <a name="step-2-set-your-prices"></a>Passaggio 2: Impostare i prezzi
### <a name="pricing-models"></a>Modelli di prezzi
| Modello di prezzi | DESCRIZIONE |
| --- | --- |
| Base |Tariffa mensile fissa al momento dell'acquisto, ad esempio $10/mese. |
| Consumo (noto come uso o misuratore) |Pagamento in base all'utilizzo, che viene definito dall’editore dell'offerta. L’eccedenza non può essere definita per postazione, per utente e così via, poiché non esiste alcun concetto di frazione di un utente o funzionalità per una ripartizione. L'utilizzo è segnalato dal partner su base oraria. Il cliente paga alla fine del ciclo di fatturazione mensile anziché in anticipo come per i piani mensili. |
| Versione di prova gratuita |Il cliente può utilizzare il servizio gratuitamente per un periodo limitato e quindi pagare le tariffe normali in seguito. |
| Livello gratuito |Il piano è sempre gratuito. |
| Migrazione (nota come conversione o aggiornamento/downgrade) del piano |Concetto di un utente che esegue lo spostamento dal piano corrente a un altro piano accettabile; definito dal partner. |

**Modelli di prezzi disponibili per tipo di offerta**

> [!IMPORTANT]
> La disponibilità di determinati modelli di prezzi variano in base al tipo di offerta. Vedere la tabella riportata di seguito.
> 
> 

|  | Solo Base | Solo Consumo | Base + Consumo |
| --- | --- | --- | --- |
| Immagine di macchina virtuale |No  |Sì |No  |
| Servizio per gli sviluppatori |Sì |Sì |Sì |

### <a name="21-set-your-vm-prices"></a>2.1. Impostare i prezzi della VM
Attualmente per le macchine virtuali sono disponibili i **tre tipi di modelli di fatturazione**

* **Oraria:** i clienti ricevono un addebito su base oraria secondo le tariffe stabilite dagli editori in base alle dimensioni della macchina virtuale. In caso di **fatturazione oraria** degli SKU, il prezzo totale sarà la somma del costo del software addebitato dall'editore più il costo dell'infrastruttura addebitato da Microsoft. Questo costo totale sarà visualizzato al cliente come prezzo orario e mensile quando prende in considerazione l'acquisto (vedere la schermata riportata di seguito). **L'autore riceve l'80% del costo del software addebitato.** Di conseguenza è necessario assicurarsi di eseguire il calcolo relativo prima di impostare i prezzi per gli SKU.
  
    ![disegno](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Versione di valutazione gratuita:** si tratta di un'altra versione del modello orario. In questo caso al cliente non viene addebitato il costo del software per i primi 30 giorni (gratuiti) dopo la distribuzione della VM. Dopo 30 giorni i clienti ricevono un addebito su base oraria secondo le tariffe stabilite dagli autori nel modello orario.
* **Bring Your Own License (BYOL):** gli autori gestiscono le licenze del software eseguito sulla macchina virtuale.

**Importante:** se l'offerta o lo SKU è elencato in Azure Marketplace, non è possibile modificare i campi indicati di seguito.

* **Modifica dei prezzi degli SKU elencati**
* **Modifica del modello di fatturazione degli SKU elencati**
* **Rimozione delle aree di fatturazione degli SKU elencati**
* **Modifica del numero di dischi dati degli SKU elencati**
* **Offer Identifier (Identificatore offerte):** [Portale di pubblicazione -&gt; Macchine virtuali -&gt; selezionare l'offerta -&gt; Immagini VM -&gt; Offer Identifier (Identificatore offerte)]
* **SKU Identifier (Identificatore SKU):** [Portale di pubblicazione -&gt; Macchine virtuali -&gt; selezionare l'offerta -&gt; scheda SKU -&gt; Add a SKU (Aggiungi uno SKU)]
* **Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione):** [Portale di pubblicazione -> Macchine virtuali -> scheda Procedura dettagliata -> Tell Us About Your Company (Informazioni sull'azienda), disponibile nel Passaggio 2 "Registrazione -> Publisher Namespace (Spazio dei nomi dell'entità di pubblicazione) -> Spazio dei nomi]
* **Porte:** [Portale di pubblicazione -> Macchine virtuali -> selezionare l'offerta -> scheda Immagini VM -> Open Ports (Porte aperte)]

### <a name="sell-to-countries-of-the-sku"></a>Paesi in cui vendere lo SKU
I paesi in cui rendere disponibile lo SKU vanno considerati con attenzione. Alcuni paesi sono classificati come "a rimessa Microsoft" e altri come "a rimessa ISV".

* Nei paesi “a rimessa Microsoft” è Microsoft a riscuotere le imposte dai clienti e a versare (rimessa) le imposte al governo.
* Nei paesi “a rimessa ISV” sono i partner a essere responsabili della riscossione delle imposte dai clienti e del versamento delle imposte al governo. Se si sceglie di vendere in paesi "a rimessa ISV", è necessario avere la capacità di calcolare e versare le imposte in tali paesi.

> [!NOTE]
> Lo SKU non sarà disponibile nei vari paesi se non si impostano i relativi prezzi nel [portale di pubblicazione](https://publish.windowsazure.com). Di seguito sono riportate informazioni di guida per la definizione dei prezzi degli SKU con i modelli orario e BYOL.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 Come configurare il modello di prezzi orario per uno SKU
Per configurare il modello di prezzi orario per uno SKU, seguire i passaggi riportati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SKU** .
4. Assicurarsi che lo SKU sia contrassegnato come "Modello di fatturazione oraria". In caso contrario fare clic sul pulsante di **modifica** per cambiare il modello di fatturazione. Verrà visualizzata una finestra. Deselezionare la casella di controllo "Billing and licensing is done externally from Azure (aka Bring Your Own License)" ("La fatturazione e le licenze sono gestite all'esterno di Azure (Bring Your Own License)") e salvare le modifiche.
5. Se si desidera abilitare la versione di valutazione gratuita per 30 giorni prima della distribuzione dello SKU, selezionare l'opzione "Un mese" per la domanda "È disponibile una versione di valutazione gratuita?" In caso contrario, selezionare l'opzione "Versione di valutazione non disponibile". Ora eseguire i passaggi indicati di seguito.
6. Dal menu sul lato sinistro fare clic sulla scheda **PREZZI** .
7. Selezionare l'area di base.
   
   ![disegno](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Impostare i prezzi per tutte le memorie centrali. **È necessario fornire il prezzo per tutte le memorie centrali di uno SKU anche se lo SKU non lo supporta.**
   
    ![disegno](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Impostare i prezzi per le altre aree manualmente o usare la procedura guidata di impostazione automatica dei prezzi per impostare i prezzi delle altre aree con riferimento all'area di base. Per usare la procedura guidata di impostazione automatica dei prezzi, fare clic sul pulsante **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES** (IMPOSTA AUTOMATICAMENTE I PREZZI PER GLI ALTRI MERCATI IN BASE A QUELLI DEGLI STATI UNITI). **Nota:** l'etichetta del pulsante può essere diversa a seconda dell'area selezionata. Poiché sono stati selezionati gli Stati Uniti in fase di creazione del documento, nella schermata riportata di seguito l'etichetta del pulsante è "Auto price other markets based on prices in United States" ("Imposta automaticamente i prezzi per gli altri mercati in base a quelli degli Stati Uniti").
   
   ![disegno](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Verrà aperta la procedura guidata di impostazione automatica dei prezzi. La prima pagina consente di selezionare il mercato di base. Eseguire la selezione e passare alla pagina successiva facendo clic sul pulsante "->".
    
    ![disegno](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. La pagina 2 mostra le opzioni per la selezione delle memorie centrali e dei piani. Selezionare i piani desiderati e fare clic sul pulsante “->”. Fare clic sul pulsante **Toggle All** (Attiva/Disattiva tutto) per selezionare tutti i **piani di servizio** e i **misuratori** oppure selezionare manualmente le caselle di controllo. **È necessario fornire il prezzo per tutte le memorie centrali di uno SKU anche se lo SKU non lo supporta.** Assicurarsi pertanto che siano selezionate tutte le dimensioni di memoria centrale.
    
    ![disegno](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. La pagina 3 mostra i mercati/aree. Fare clic sul pulsante **Toggle All** (Attiva/Disattiva tutto) per selezionare tutte le aree oppure selezionare manualmente le caselle per l'area. Fare clic sul pulsante "->" per passare alla pagina successiva. **Nota:** i paesi in cui Microsoft riscuote e versa le imposte sono contrassegnati da un simbolo che raffigura una casa. Per altre informazioni, consultare il sezione "Paesi in cui vendere lo SKU" di questa pagina.
    
    ![disegno](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. La pagina 4 mostra i tassi di cambio. Fare clic sul pulsante Fine per completare i passaggi.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 Come configurare il modello di prezzi BYOL per uno SKU
Per configurare il modello di prezzi BYOL per uno SKU, seguire i passaggi riportati di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **MACCHINE VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SKU** .
4. Assicurarsi che lo SKU sia contrassegnato come "Bring Your Own License". In caso contrario fare clic sul pulsante MODIFICA per cambiare il modello di fatturazione. Verrà visualizzata una finestra. Selezionare la casella di controllo "Billing and licensing is done externally from Azure (aka Bring Your Own License)" ("La fatturazione e le licenze sono gestite all'esterno di Azure (ovvero Bring Your Own License)") e salvare le modifiche.
   
   ![disegno](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. Dal menu sul lato sinistro fare clic sulla scheda **PRICING** (PREZZI).
6. Selezionare l'area di base e rendere disponibile lo SKU nell'area selezionando la casella di controllo dello SKU nella sezione EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY (DISPONIBILITÀ DELLO SKU CON LICENZA ESTERNA (BYOL)) (vedere la schermata riportata di seguito).
   
   ![disegno](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Rendere lo SKU disponibile nelle altre aree manualmente o usare la procedura guidata di impostazione automatica dei prezzi per questo scopo. Vedere i punti da 9 a 13 che spiegano come usare la procedura guidata nella sezione **"2.1.1 Come configurare il modello di prezzi orario per uno SKU"** di questa pagina.

### <a name="22-set-your-developer-service-prices"></a>2.2. Impostare i prezzi del servizio di sviluppatore
I piani possono essere qualsiasi combinazione di Base + Consumo, dove base è il prezzo mensile e l'eccedenza è il prezzo da pagare in base all’utilizzo. (Vedere di seguito per ulteriori dettagli).

**Esempio:** offerta di servizio per sviluppatori di Contoso

| Pianificazione | Prezzo | Include | Percorso di migrazione |
| --- | --- | --- | --- |
| Gratuito |$0/mese |Funzionalità di base |È possibile eseguire la migrazione a qualsiasi altro piano |
| Bronze |$10/mese |Funzionalità di base e una quota pari a 1.000 della funzionalità X. |È possibile eseguire la migrazione ai piani Bronze Plus, Silver e Gold |
| Bronze Plus |Periodo di prova gratuita: $0/mese + $0/contatore01 |Funzionalità di base e una quota di 10.000 funzionalità X. Una volta che viene usata la quota della funzionalità X, il cliente può pagare per ogni uso tramite misuratore01. |È possibile eseguire la migrazione ai piani Silver Plus e Gold |
| Bronze Plus |Periodo di pagamento (noto come versione di prova gratuita scaduta): $10/mese + $0,05/misuratore01 |Funzionalità di base e una quota di 10.000 funzionalità X. Una volta che viene usata la quota della funzionalità X, il cliente può pagare per ogni uso tramite misuratore01. |È possibile eseguire la migrazione ai piani Silver Plus e Gold |
| Silver |$0,15/contatore01 |Il cliente può pagare per uso tramite contatore01, per la funzionalità X. |È possibile eseguire la migrazione ai piani Bronze e Gold |
| Silver Plus |$20/mese + $ 0,15/contatore01 + $ 0,01/contatore02 |Funzionalità di base e una quota di 10.000 funzionalità X e 100 funzionalità Y. Una volta che viene usata la quota della funzionalità X, il cliente può pagare per ogni uso tramite misuratore01.  Una volta che viene utilizzata la quota della funzionalità Y, il cliente può pagare per ogni utilizzo tramite contatore02 |È possibile eseguire la migrazione ai piani Bronze Plus e Gold |
| Gold |$1,000/mese |Quota di 10.000 funzionalità X, 1.000 funzionalità Y e un numero illimitato di funzionalità Z. |È possibile eseguire la migrazione a tutti i piani, ad eccezione di quello gratuito |

## <a name="step-3-provide-support-information"></a>Passaggio 3: Fornire informazioni di supporto
I dettagli di contatto vengono utilizzati per comunicazioni interne solo tra partner e Microsoft. L’URL del supporto sarà disponibile ai clienti finali.

1. Passare all'intestazione **Supporto** sul lato sinistro del portale di pubblicazione.
2. Immettere le informazioni nella sezione **Contatto tecnico**.
3. Immettere le informazioni nella sezione **Supporto tecnico**. Se si è scelto di fornire supporto solo tramite posta elettronica, immettere un numero di telefono fittizio. Al suo posto verrà utilizzato l'indirizzo di posta elettronica specificato.
4. Immettere l'URL del supporto.

## <a name="step-4-choose-azure-marketplace-categories"></a>Passaggio 4: Scegliere le categorie di Azure Marketplace
La scheda **Categorie** fornisce una matrice delle selezioni. L'offerta può essere associata a tali opzioni ed è possibile selezionare fino a cinque categorie.

## <a name="how-your-marketing-will-appear"></a>Visualizzazione del marketing
Di seguito è riportata una visualizzazione dettagliata di come vengono usate le informazioni di marketing dell'offerta nel [sito Web di Azure Marketplace](https://azure.microsoft.com/marketplace/) e nel [portale di Azure](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Sito Web di Azure Marketplace
![disegno](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![disegno](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Elenco di offerte sul sito Web del marketplace di Azure*

![disegno](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Dettagli di descrizione dell’offerta nel sito Web del marketplace di Azure*

![disegno](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Descrizione dei dettagli dei prezzi dell’offerta nel sito Web del marketplace di Azure*

### <a name="azure-portal"></a>Portale di Azure
![disegno](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Elenco di offerte nel portale di Azure*

![disegno](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Descrizione dei dettagli dell'offerta nel portale di Azure*

## <a name="next-steps"></a>Passaggi successivi
Ora che il contenuto del Marketplace è caricato, è possibile passare al test dell'offerta nella gestione temporanea. Tuttavia, è necessario selezionare il tipo di offerta appropriato dall'elenco riportato di seguito, poiché i passaggi variano in base al tipo di offerta.

* [Testare l'offerta VM nello staging](marketplace-publishing-vm-image-test-in-staging.md)
* [Test dell'offerta di modello di soluzione nello staging](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Vedere anche 
* [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
