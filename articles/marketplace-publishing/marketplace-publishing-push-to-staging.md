<properties
   pageTitle="Preparazione e test dell’offerta per la distribuzione in Azure Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate fornitura di contenuti di marketing, configurazione dei prezzi e test dell'offerta prima della distribuzione in Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="04/26/2016"
   ms.author="hascipio"/>

# Completare la creazione dell’offerta con contenuti di marketing
In questo passaggio del processo di pubblicazione, sarà necessario fornire alcuni contenuti di marketing e informazioni dettagliate sull'offerta e/o sugli SKU in Azure Marketplace. Ad esempio, si fornirà una descrizione del prodotto, i logo aziendali, piani di prezzo, dettagli dei piani e altre informazioni necessarie per portare l'offerta e/o SKU in gestione temporanea. Queste informazioni vengono utilizzate come contenuto di marketing nel portale di Azure. Si inizierà il processo nel [portale di pubblicazione][link-pubportal].

## Passaggio 1: Fornire contenuti di marketing di Marketplace
**Inglese è il valore predefinito e la sola lingua supportata.** Assicurarsi che tutte le informazioni contenute nei campi siano in inglese. Tutte le informazioni possono essere modificate in qualsiasi momento fino al passaggio alla gestione temporanea.

  1. Passare al portale di pubblicazione, [https://publish.windowsazure.com](https://publish.windowsazure.com).
  2. Nel menu a sinistra fare clic sulla scheda **Marketing**.
  3. Nel pannello principale, fare clic sul pulsante **English (US)**.

  > [AZURE.IMPORTANT] Tutti i campi devono essere completati, incluse le immagini, per poter passare alla gestione temporanea.

### Dettagli
1. Immettere nella scheda **Dettagli** il titolo dell'offerta composto da 50 caratteri al massimo, il riepilogo dell'offerta di 100 caratteri al massimo, il riepilogo lungo dell'offerta di 256 caratteri al massimo, la descrizione dell'offerta di 1300 caratteri al massimo e il logo.
2. Immettere nella scheda **Piani** il titolo dello SKU di 50 caratteri al massimo, il riepilogo dello SKU di 100 caratteri al massimo e la descrizione dello SKU di 2000 caratteri al massimo.
3. Non immettere testi duplicati nell'offerta e nella descrizione dello SKU.
4. Non immettere testi duplicati nel titolo dello SKU e nel riepilogo lungo dell'offerta.
5. Non immettere testi duplicati nel titolo dello SKU e nel riepilogo dell'offerta.
6. Caricare le immagini delle specifiche richieste indicate nel portale di pubblicazione in formato PNG, una per ogni dimensione.
7. Assicurarsi che il logo sia conforme alle linee guida per il logo di Azure Marketplace riportate di seguito.

  ![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Linee guida per il Logo di Azure Marketplace**

Ogni logo caricato nel portale di pubblicazione deve rispettare le seguenti linee guida:

- La progettazione di Azure ha una tavolozza dei colori semplice. Mantenere un numero basso di colori primari e secondari nel logo.
- Il logo non deve essere posizionato su uno sfondo bianco. È consigliabile usare colori primari semplici o sfondi trasparenti.
- Non usare uno sfondo sfumato sul logo.
- Evitare di inserire testo, anche il nome del marchio o della società, sul logo.
- L'aspetto del logo deve essere semplice e senza sfumature.
- Il logo non può essere allungato.
- Il formato del logo piccolo deve essere di 40 X 40 pixel
- Il formato del logo medio deve essere 90 X 90 pixel
- Il formato del logo grande deve essere di 115 X 115 pixel
- Il formato del logo largo deve essere di 255 X 115 pixel
- Il formato del logo alto deve essere di 815 X 290 pixel

  ![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Indicazioni aggiuntive per il banner del logo alto:**

- Il logo alto è facoltativo. È possibile scegliere di non caricare un logo alto.
- Il nome visualizzato dell'autore, il titolo dello SKU, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati automaticamente all'interno del logo alto quando l'offerta viene pubblicata. Pertanto, non è necessario immetterli mentre si progetta il logo alto.
- Dato che il nome visualizzato dell'autore, il titolo dello SKU e il riepilogo lungo dell'offerta vengono visualizzati in bianco, è consigliabile evitare di usare lo sfondo bianco o chiaro per l'icona alta.
- Lasciare spazio per il testo sopra l'icona alta. Lo spazio per il testo è di 415 x 100, con uno scostamento verso sinistra di 370 pixel.

  ![disegno](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### Collegamenti
Nella scheda dei **Collegamenti** sulla barra a sinistra immettere tutti i collegamenti con informazioni utili per i clienti. Immettere un nome e un URL per ogni collegamento.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### Immagini di esempio (facoltative)
> [AZURE.NOTE] L’inclusione di un'immagine di esempio è un passaggio facoltativo. È possibile completare gli altri contenuti di marketing per soddisfare i requisiti per il push nella gestione temporanea.

Nella scheda **Immagini di esempio** nel menu a sinistra caricare una nuova immagine facendo clic su **Carica una nuova immagine**. Se si dispone di un'immagine esistente e si desidera sostituirla, fare clic su **Sostituisci immagine**.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### Note legali
Nella scheda **Note legali** specificare un collegamento alle politiche/condizioni per l'utilizzo. Immettere o incollare le condizioni per l'utilizzo nella casella grande **Condizioni per l'utilizzo**. Il limite di caratteri per le condizioni per l'utilizzo è 1.000.000 caratteri.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

> [AZURE.NOTE] Per le macchine virtuali, non è possibile modificare gli elementi seguenti quando lo SKU è in gestione temporanea o attivo: **Identificatore offerta**, **Identificatore autore** e **Identificatore SKU**.

## Passaggio 2: Impostare i prezzi
### Modelli di prezzi
|Modello di prezzi |Descrizione |
|---------------|------------------------------------------|
|Base| Tariffa mensile fissa al momento dell'acquisto, ad esempio $10/mese.|
|Consumo (noto anche come utilizzo, contatore) | Pagamento in base all'utilizzo, che viene definito dall’editore dell'offerta. L’eccedenza non può essere definita per postazione, per utente e così via, poiché non esiste alcun concetto di frazione di un utente o funzionalità per una ripartizione. L'utilizzo è segnalato dal partner su base oraria. Il cliente paga alla fine del ciclo di fatturazione mensile anziché in anticipo come per i piani mensili. |
|Versione di prova gratuita | Il cliente può utilizzare il servizio gratuitamente per un periodo limitato e quindi pagare le tariffe normali in seguito. |
|Livello gratuito | Il piano è sempre gratuito. |
| Migrazione (nota come conversione o aggiornamento/downgrade) del piano | Concetto di un utente che esegue lo spostamento dal piano corrente a un altro piano accettabile; definito dal partner. |

**Modelli di prezzi disponibili per tipo di offerta**

> [AZURE.IMPORTANT] La disponibilità di determinati modelli di prezzi variano in base al tipo di offerta. Vedere la tabella riportata di seguito.

| | Solo Base | Solo Consumo | Base + Consumo |
|---|---|---|---|
| Immagine di macchina virtuale | No | Sì | No|
| Servizio per gli sviluppatori | Sì | Sì | Sì |
| Servizio dati | Sì | No | No |

### 2\.1. Impostare i prezzi della VM
> [AZURE.NOTE] BYOL è supportato solo per le macchine virtuali.

1.	Nella scheda **Prezzi** vengono visualizzati tutti i mercati supportati. Selezionare quello appropriato per visualizzare i campi per l'impostazione dei prezzi.
2.	Il collegamento fornito nel portale di pubblicazione mostrerà alcune informazioni sui prezzi quale forma di aiuto per determinare i prezzi degli SKU.
3.	Se l’SKU è BYOL, selezionare la casella di controllo per disponibilità SKU (BYOL) concesso in licenza esternamente.
4.	Se l’SKU è orario, immettere il prezzo del software. SKU senza prezzi non sono disponibili per l'acquisto o l’utilizzo.

  > [AZURE.NOTE] Se si dispone sia di BYOL che di SKU orari, verificare che entrambi i requisiti siano soddisfatti: casella di controllo BYOL e valori del prezzo per orario.

5.	Verrà aperta una procedura guidata relativa ai prezzi. Eseguire tutti i passaggi per completare l'impostazione dei prezzi, inclusi quelli per gli altri paesi se si è scelto di consentire gli acquisti al di fuori del mercato specificato.
6.	Alcuni paesi sono paesi di rimessa ISV. Per vendere in un paese di rimessa ISV, è necessario essere in grado di addebitare e tassare gli SKU, nonché di calcolare e pagare le imposte all'autorità del paese. Microsoft non è in condizione di fornire supporto legale o fiscale. Vedere la sezione "Vendere a paesi dell'offerta" nell'Introduzione di questo documento per ulteriori informazioni su "Vendere a paesi".

  > [AZURE.NOTE] Per le macchine virtuali, non è possibile modificare gli elementi seguenti quando uno SKU è attivo, poiché influisce sulla fatturazione dei clienti esistenti: **Modifica dei prezzi**, **Modifica del modello di fatturazione** e **Rimozione delle aree di fatturazione**.

### 2\.2. Impostare i prezzi del servizio di sviluppatore
I piani possono essere qualsiasi combinazione di Base + Consumo, dove base è il prezzo mensile e l'eccedenza è il prezzo da pagare in base all’utilizzo. (Vedere di seguito per ulteriori dettagli).

**Esempio:** offerta di servizio per sviluppatori di Contoso

| Pianificazione | Prezzo | Include | Percorso di migrazione |
|-------|------|-------|-------|
|Gratuito|$0/mese|Funzionalità di base|È possibile eseguire la migrazione a qualsiasi altro piano|
|Bronze|$10/mese|Funzionalità di base e una quota pari a 1.000 della funzionalità X.|È possibile eseguire la migrazione ai piani Bronze Plus, Silver e Gold|
|Bronze Plus|Periodo di prova gratuita: $0/mese + $0/contatore01 |Funzionalità di base e una quota di 10.000 funzionalità X. Una volta che viene utilizzata la quota della funzionalità X, il cliente può pagare per ogni utilizzo tramite contatore01.|È possibile eseguire la migrazione ai piani Silver Plus e Gold|
|Bronze Plus| Periodo di pagamento (noto anche come Versione di prova gratuita scaduta): $10/mese + $0,05/contatore01|Funzionalità di base e una quota di 10.000 funzionalità X. Una volta che viene utilizzata la quota della funzionalità X, il cliente può pagare per ogni utilizzo tramite contatore01.|È possibile eseguire la migrazione ai piani Silver Plus e Gold|
|Silver|$0,15/contatore01|Il cliente può pagare per uso tramite contatore01, per la funzionalità X.|È possibile eseguire la migrazione ai piani Bronze e Gold|
|Silver Plus|$20/mese + $ 0,15/contatore01 + $ 0,01/contatore02|La funzionalità di base e una quota di 10.000 funzionalità X e 100 funzionalità Y. Una volta che viene utilizzata la quota della funzionalità X, il cliente può pagare per ogni utilizzo tramite contatore01. Una volta che viene utilizzata la quota della funzionalità Y, il cliente può pagare per ogni utilizzo tramite contatore02|È possibile eseguire la migrazione ai piani Bronze Plus e Gold|
|Gold|$1,000/mese|Quota di 10.000 funzionalità X, 1.000 funzionalità Y e un numero illimitato di funzionalità Z.|È possibile eseguire la migrazione a tutti i piani, ad eccezione di quello gratuito|

## Passaggio 3: Fornire informazioni di supporto
I dettagli di contatto vengono utilizzati per comunicazioni interne solo tra partner e Microsoft. L’URL del supporto sarà disponibile ai clienti finali.

1.	Passare all'intestazione **Supporto** sul lato sinistro del portale di pubblicazione.
2.	Immettere le informazioni nella sezione **Contatto tecnico**.
3.	Immettere le informazioni nella sezione **Supporto tecnico**. Se si è scelto di fornire supporto solo tramite posta elettronica, immettere un numero di telefono fittizio. Al suo posto verrà utilizzato l'indirizzo di posta elettronica specificato.
4.	Immettere l'URL del supporto.

## Passaggio 4: Scegliere le categorie di Azure Marketplace
La scheda **Categorie** fornisce una matrice delle selezioni. L'offerta può essere associata a tali opzioni ed è possibile selezionare fino a cinque categorie.

## Visualizzazione del marketing
Di seguito è riportata una visualizzazione dettagliata di come vengono usate le informazioni di marketing dell'offerta nel [sito Web di Azure Marketplace](https://azure.microsoft.com/marketplace/) e nel [portale di Azure](https://portal.azure.com).

### Sito Web di Azure Marketplace
![disegno](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![disegno](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Elenco di offerte sul sito Web del marketplace di Azure*

![disegno](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Dettagli di descrizione dell’offerta nel sito Web del marketplace di Azure*

![disegno](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Descrizione dei dettagli dei prezzi dell’offerta nel sito Web del marketplace di Azure*

### Portale di Azure
![disegno](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Elenco di offerte nel portale di Azure*

![disegno](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Descrizione dei dettagli dell'offerta nel portale di Azure*

## Passaggi successivi
Ora che il contenuto del Marketplace è caricato, è possibile passare al test dell'offerta nella gestione temporanea. Tuttavia, è necessario selezionare il tipo di offerta appropriato dall'elenco riportato di seguito, poiché i passaggi variano in base al tipo di offerta.

||Immagine di macchina virtuale |Servizio per gli sviluppatori | Servizio dati | Modello di soluzione |
|----|----|----|----|----|
| **Passaggio 3. Push dell'offerta nella gestione temporanea** | [Testare l'offerta VM nello staging](marketplace-publishing-vm-image-test-in-staging.md) | Anteprima privata | [Test dell'offerta del servizio dati nello staging](marketplace-publishing-data-service-test-in-staging.md) | [Test del modello di soluzione nello staging](marketplace-publishing-solution-template-test-in-staging.md) |

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0504_2016-->