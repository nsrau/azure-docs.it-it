<properties
   pageTitle="Preparazione e test dell’offerta per la distribuzione in Azure Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate fornitura di contenuti di marketing, configurazione dei prezzi e test dell'offerta prima della distribuzione in Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Completare la creazione dell’offerta con contenuti di marketing
In questo passaggio del processo di pubblicazione, è necessario fornire alcuni contenuti di marketing e dettagli dell'offerta e/o SKU di Azure Marketplace, ad esempio descrizione del prodotto, i logo aziendali, le tariffe, i dettagli degli impianti e altre informazioni necessarie per effettuare il push dell'offerta e/o dell’SKU alla gestione temporanea. Queste informazioni vengono utilizzate come contenuto di marketing nel nostro portale di Azure. Si inizierà il processo nel [portale pubblicazione][link-pubportal].

## Passaggio 1. Fornire contenuti di marketing di Marketplace
**La lingua inglese è quella predefinita nonché l'unica supportata;** assicurarsi che tutte le informazioni fornite nei campi siano in inglese. Tutte le informazioni possono essere modificate in qualsiasi momento fino al passaggio alla gestione temporanea.

  1. Passare al portale di pubblicazione, [https://publish.windowsazure.com](https://publish.windowsazure.com)
  2. Nel menu a sinistra fare clic sulla scheda **Marketing**.
  3. Nel pannello principale, fare clic sul pulsante **English (US)**.

### Dettagli
1. Immettere il riepilogo dell'offerta, un riepilogo più approfondito e la descrizione dell'offerta.
2.	Caricare immagini delle specifiche richieste (menzionate nel portale di pubblicazione) in formato PNG, una per ogni dimensione.

  ![disegno](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *Titolo, descrizione e logo dell’offerta*

### Collegamenti
Nella scheda dei collegamenti sulla barra a sinistra immettere tutti i collegamenti con informazioni utili per i clienti. Immettere un nome e un URL per ogni collegamento.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Immagini di esempio (facoltative)
> [AZURE.NOTE]L’inclusione di un'immagine di esempio è un passaggio facoltativo. È possibile completare gli altri contenuti di marketing per soddisfare i requisiti per il push nella gestione temporanea.

Nella scheda **Immagini di esempio** nel menu a sinistra caricare una nuova immagine facendo clic su **Carica una nuova immagine**. Se si dispone di un'immagine esistente e si desidera sostituirla, fare clic su **Sostituisci immagine**.

![disegno](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### Piani
![disegno](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Note legali
Nella scheda Note legali specificare un collegamento alle politiche/condizioni per l'utilizzo. Immettere o incollare le condizioni per l'utilizzo nella casella grande Condizioni per l'utilizzo.

> [AZURE.IMPORTANT]Tutti i campi devono essere completati, incluse le immagini, per poter passare alla gestione temporanea.


## Passaggio 2: Impostare i prezzi
### Modelli di prezzi
|Modello di prezzi |Descrizione |
|---------------|------------------------------------------|
|Base| Tariffa mensile fissa al momento dell'acquisto, ad esempio $10/mese|
|Consumo (noto anche come Utilizzo, Contatore) | Pagamento in base all'utilizzo, che viene definito dall’editore dell'offerta. L’Eccedenza non può essere definita per postazione, per utente e così via, poiché non esiste alcun concetto di frazione di un utente o funzionalità per una ripartizione. L'utilizzo è segnalato dal Partner su base oraria. Il cliente paga alla fine del ciclo di fatturazione mensile anziché in anticipo come per i piani mensili. |
|Versione di valutazione gratuita | Il cliente può utilizzare il servizio gratuitamente per un periodo limitato e quindi pagare le tariffe normali in seguito |
|Livello gratuito | Il piano è sempre gratuito |
| Migrazione (nota come conversione o aggiornamento/downgrade) del piano | Concetto di un utente che esegue lo spostamento dal piano corrente a un altro piano accettabile; definito dal partner |

**Modelli di prezzi disponibili per tipo di offerta**

> [AZURE.IMPORTANT]La disponibilità di determinati modelli di prezzi variano in base al tipo di offerta. Vedere la tabella riportata di seguito.

| | Solo Base | Solo Consumo | Base + Consumo |
|---|---|---|---|
| Immagine di macchina virtuale | No | Sì | No|
| Servizio per gli sviluppatori | Sì | Sì | Sì |
| Servizio dati | Sì | No | No |

### 2\.1. Impostare i prezzi della VM
> [AZURE.NOTE]BYOL è supportato solo per le macchine virtuali.

1.	Nella scheda **Prezzi** vengono visualizzati tutti i mercati supportati. Selezionare quello appropriato per visualizzare i campi per l'impostazione dei prezzi.
2.	Il collegamento fornito nel portale di pubblicazione mostrerà alcune informazioni sui prezzi quale forma di aiuto per determinare i prezzi degli SKU.
3.	Se l’SKU è BYOL, selezionare la casella di controllo per 'Disponibilità SKU (BYOL) concesso in licenza esternamente.
4.	Se l’SKU è orario, immettere il prezzo del software. SKU senza prezzi non sono disponibili per l'acquisto o l’utilizzo.

  >[AZURE.NOTE]Se si dispone sia di BYOL che di SKU orari, verificare che entrambi i requisiti siano soddisfatti: casella di controllo BYOL e valori del prezzo per Orario.

5.	Verrà avviata una procedura guidata per l'immissione dei prezzi. Eseguire tutti i passaggi per completare l'impostazione dei prezzi, inclusi quelli per gli altri paesi se si è scelto di consentire gli acquisti al di fuori del mercato specificato.
6.	Alcuni paesi sono paesi di rimessa ISV. Per vendere in un paese di rimessa ISV, è necessario essere in grado di addebitare e tassare gli SKU, nonché di calcolare e pagare le imposte all'autorità del paese. Microsoft non è in condizione di fornire supporto legale o fiscale. Vedere la sezione "Vendere a paesi dell'offerta" nell'Introduzione di questo documento per ulteriori informazioni su "Vendere a paesi".

### 2\.2. Impostare i prezzi del servizio di sviluppatore
I piani possono essere qualsiasi combinazione di Base + Consumo, dove BASE è il prezzo mensile e l'Eccedenza è il prezzo da pagare in base all’utilizzo (vedere di seguito per ulteriori dettagli)

**Esempio:** offerta di servizio per sviluppatori di Contoso

| Pianificazione | Prezzo | Include | Percorso di migrazione |
|-------|------|-------|-------|
|Free|$0/mese|Funzionalità di base|È possibile eseguire la migrazione a qualsiasi altro piano|
|Bronze|$10/mese|Funzionalità di base e una quota pari a 1.000 della funzionalità X|È possibile eseguire la migrazione ai piani Bronze Plus, Silver e Gold|
|Bronze Plus|Periodo di valutazione gratuita: $0/mese + $0/contatore01 |Funzionalità di base e una quota di 10.000 funzionalità X. Una volta che viene utilizzata la quota della funzionalità X, il cliente può pagare per ogni utilizzo tramite contatore01.|È possibile eseguire la migrazione ai piani Silver Plus e Gold|
|Bronze Plus| Periodo di pagamento (noto anche come Versione di valutazione gratuita scaduta): $10/mese + $0,05/contatore01|Funzionalità di base e una quota di 10.000 funzionalità X. Una volta che viene utilizzata la quota della funzionalità X, il cliente può pagare per ogni utilizzo tramite contatore01.|È possibile eseguire la migrazione ai piani Silver Plus e Gold|
|Silver|$0,15/contatore01|Il cliente può pagare per uso tramite contatore01, per la funzionalità X|È possibile eseguire la migrazione ai piani Bronze e Gold|
|Silver Plus|$20/mese + $ 0,15/contatore01 + $ 0,01/contatore02|Funzionalità di base e una quota di 10.000 funzionalità X e 100 funzionalità Y. Una volta che viene utilizzata la quota della funzionalità X, il cliente può pagare per ogni utilizzo tramite contatore01. Una volta che viene utilizzata la quota della funzionalità Y, il cliente può pagare per ogni utilizzo tramite contatore02|È possibile eseguire la migrazione ai piani Bronze Plus e Gold|
|Gold|$1000/mese|Quota di 10.000 funzionalità X, 1.000 funzionalità Y e un numero illimitato di funzionalità Z|È possibile eseguire la migrazione a tutti i piani, ad eccezione di quello gratuito|

## Passaggio 3: Fornire informazioni di supporto
Alcune di queste informazioni verranno completate durante il passaggio di certificazione. È possibile aggiungere informazioni o modificarle come indicato nei passaggi seguenti. I dettagli di contatto vengono utilizzati per comunicazioni interne solo tra partner e Microsoft. L’URL del supporto sarà disponibile ai clienti finali.

1.	Passare all'intestazione Supporto sul lato sinistro del portale di pubblicazione.
2.	Immettere le informazioni nella sezione Contatto tecnico.
3.	Immettere le informazioni nella sezione Supporto tecnico. Se si è scelto di fornire supporto solo tramite posta elettronica, immettere un numero di telefono fittizio. Al suo posto verrà utilizzato l'indirizzo di posta elettronica specificato.
4.	Immettere l'URL del supporto

## Passaggio 4: Scegliere le categorie di Azure Marketplace
Nella scheda **Categorie** è disponibile una gamma di opzioni. L'offerta può essere associata a tali opzioni ed è possibile selezionare fino a cinque (5) categorie.

## Visualizzazione del marketing
Di seguito è riportata la visualizzazione dettagliata di come i dettagli di marketing dell’offerta del portale di pubblicazione vengono utilizzati nel [sito Web di Azure Marketplace](http://azure.microsoft.com/marketplace) e nella [raccolta del marketplace del portale di anteprima di Azure](https://ms.portal.azure.com).

### Nel sito Web del marketplace Azure.com
![disegno](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![disegno](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Elenco di offerte sul sito Web del marketplace Azure.com*

![disegno](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Dettagli di descrizione dell’offerta nel sito Web del marketplace Azure.com*

![disegno](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Descrizione dei dettagli dei prezzi dell’offerta nel sito Web del marketplace Azure.com*

### Nella raccolta del marketplace nel portale di anteprima di Azure
![disegno](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Elenco di offerte nella raccolta del marketplace del portale di anteprima di Azure*

![disegno](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

![disegno](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Descrizione dei dettagli dell’offerta nella raccolta del marketplace del portale di anteprima di Azure*

## Passaggi successivi
Ora che il contenuto del Marketplace è caricato, è possibile passare al **Passaggio 3: Test dell'offerta nella gestione temporanea**. Tuttavia, è necessario selezionare il tipo di offerta appropriato dall'elenco riportato di seguito, poiché i passaggi variano in base al tipo di offerta.

|| Immagine di macchina virtuale | Servizio per sviluppatori | Servizio dati | Modello di soluzione |
|----|----|----|----|----|
| **Passaggio 3. Eseguire il push dell'offerta in gestione temporanea** | [Testare l'offerta della VM in gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md) | Testare l'offerta del servizio per sviluppatori in gestione temporanea | Testare l'offerta del servizio dati in gestione temporanea | [Testare il modello di soluzione in gestione temporanea](marketplace-publishing-solution-template-test-in-staging.md)

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!----HONumber=Oct15_HO3-->