---
title: Fatture per la registrazione ad Azure Enterprise
description: Questo articolo illustra come gestire la fattura della registrazione EA di Azure.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 9fbc2ffcf1158f436c060817f8f25152f839c3e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037265"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Fatture per la registrazione ad Azure Enterprise

Questo articolo illustra come gestire la fattura del Contratto Enterprise (EA, Enterprise Agreement) di Azure. La fattura è una rappresentazione del conto. Esaminarla per verificarne la correttezza. È anche consigliabile acquisire familiarità con altre attività che potrebbero essere necessarie per la gestione della fattura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Modificare un numero di ordine di acquisto per una fattura per eccedenza

Azure Enterprise Portal genera automaticamente un numero di ordine d'acquisto predefinito, a meno che l'amministratore aziendale non ne imposti uno prima della data della fattura. L'amministratore aziendale può aggiornare il numero di ordine d'acquisto fino a sette giorni dopo la ricezione di un messaggio di posta elettronica automatico di notifica della fattura.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Per aggiornare il numero di ordine di acquisto dei servizi di Azure:

1. In Azure Enterprise Portal selezionare **Report** > **Usage Summary** (Riepilogo utilizzo).
1. Nell'angolo superiore destro selezionare **Edit PO Numbers** (Modifica numeri ordini di acquisto).
1. Selezionare il pulsante di opzione **Servizi di Azure**.
1. Scegliere un intervallo di date dal menu a discesa del campo **Periodo di fatturazione**.

   È possibile modificare un numero di ordine d'acquisto nei sette giorni successivi alla ricezione di una notifica di fatturazione, ma prima di pagare la fattura.
1. Immettere un nuovo numero di ordine d'acquisto nel campo **Numero di ordine di acquisto**.
1. Selezionare **Salva** per inviare la modifica.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Per aggiornare il numero di ordine d'acquisto di Azure Marketplace:

1. In Azure Enterprise Portal selezionare **Report** > **Usage Summary** (Riepilogo utilizzo).
1. Nell'angolo superiore destro selezionare **Edit PO Numbers** (Modifica numeri ordini di acquisto).
1. Selezionare il pulsante di opzione **Marketplace**.
1. Scegliere un intervallo di date dal menu a discesa del campo **Periodo di fatturazione**.

   È possibile modificare un numero di ordine d'acquisto nei sette giorni successivi alla ricezione di una notifica di fatturazione, ma prima di pagare la fattura.
1. Immettere un nuovo numero di ordine d'acquisto nel campo **Numero di ordine di acquisto**.
1. Selezionare **Salva** per inviare la modifica.

## <a name="cadence-of-azure-enterprise-billing"></a>Cadenza della fatturazione di Azure Enterprise

Microsoft addebita tutti gli acquisti di servizi di Microsoft Azure una volta all'anno in corrispondenza della data di entrata in vigore della registrazione. Per qualsiasi utilizzo in eccesso rispetto agli importi dell'impegno, Microsoft fattura in modo posticipato.

- I corrispettivi dell'impegno vengono suddivisi in base a una tariffa mensile e addebitati annualmente in anticipo.
- I corrispettivi dell'eccedenza vengono calcolati ogni mese e addebitati in via posticipata alla fine del periodo di fatturazione.

### <a name="billing-intervals"></a>Intervalli di fatturazione

L'intervallo di fatturazione dipende dalla modalità scelta per effettuare gli acquisti degli impegni. L'impegno annuale coincide con una delle date seguenti:

- Data di ricorrenza annuale della registrazione
- Data di validità della sottoscrizione di modifica di un anno.

La data di ricezione della fattura per eccedenza dipende dalla data di inizio e di impostazione della registrazione:

- **Registrazioni dirette con data di inizio precedente al 1° maggio 2018**:
  - Se si ha un Contratto Enterprise (EA) diretto, il periodo di fatturazione per i servizi di Azure è annuale e sono esclusi i servizi di Azure Marketplace. Il periodo di fatturazione è basato sulla data di ricorrenza annuale, ovvero la data in cui il contratto è diventato effettivo.
  - Se si supera il 150% della soglia di impegno monetario (MC) del Contratto Enterprise, si passerà automaticamente a un periodo di fatturazione trimestrale basato sulla data di ricorrenza. Si riceverà anche una fattura per eccedenza dei servizi Azure.
  - Se non si supera il 150% della soglia MC, la registrazione manterrà un periodo di fatturazione annuale. La fattura per eccedenza verrà ricevuta alla fine dell'anno di impegno.

- **Registrazioni dirette con data di inizio successiva al 1° maggio 2018**:
  - Le fatture relative all'utilizzo di Azure e agli addebiti fatturati separatamente hanno un periodo di fatturazione mensile.
  - Eventuali addebiti non coperti dall'impegno monetario sono dovuti come pagamenti per eccedenze.  

- **Registrazioni indirette con data di inizio precedente al 1° maggio 2018**:

  Se si è un cliente con Contratto Enterprise (EA) indiretto con data di inizio precedente al 1° maggio 2018, verrà impostato un periodo di fatturazione trimestrale. Il partner di canale (CP) emette direttamente la fattura.  

- **Registrazioni indirette con data di inizio successiva al 1° maggio 2018**:

  Il periodo di fatturazione è mensile.  

### <a name="increase-your-monetary-commitment"></a>Aumentare l'impegno monetario

È possibile aumentare l'impegno in qualsiasi momento. Verranno fatturati i mesi rimanenti del periodo di impegno annuale. Ad esempio, se si effettua la registrazione per una sottoscrizione di modifica di un anno e si aumenta l'impegno nel corso del sesto mese, verranno fatturati i restanti sei mesi del periodo di validità. Le quantità dell'impegno verranno quindi aggiornate per gli ultimi sei mesi del periodo di validità dell'impegno. Le nuove quantità verranno usate per determinare gli addebiti delle eventuali eccedenze.

### <a name="overage"></a>Eccedenza

In caso di eccedenza, vengono fatturati l'utilizzo o le prenotazioni che hanno superato l'impegno durante il periodo di fatturazione. Per visualizzare una suddivisione del calcolo delle quantità in eccedenza per le singole voci, fare riferimento al report di riepilogo dell'utilizzo oppure contattare il partner di canale.

Per ogni voce in fattura verrà visualizzato quanto segue:

- **Importo totale**: gli addebiti totali
- **Utilizzo dell'impegno**: la quantità dell'impegno usata per coprire gli addebiti
- **Importo netto**: gli addebiti che superano l'impegno

Le imposte applicabili vengono calcolate solo sull'importo netto in eccedenza rispetto all'impegno.

La fatturazione dell'eccedenza è automatizzata. La tempistica delle notifiche e delle fatture dipende dalla data di fine del periodo di fatturazione.

- La notifica dell'eccedenza viene in genere inviata sette giorni dopo la data di fine della fatturazione.
- Le fatture per eccedenze vengono inviate da sette a nove giorni dopo la notifica.
- È possibile esaminare gli addebiti e aggiornare i numeri di ordine d'acquisto generati dal sistema durante i sette giorni che intercorrono tra la notifica dell'eccedenza e la fatturazione.

### <a name="azure-marketplace"></a>Azure Marketplace

Dal periodo di fatturazione di aprile 2019, i clienti hanno iniziato a ricevere un'unica fattura per tutti gli addebiti di Azure e Azure Marketplace anziché due fatture distinte. Questa modifica non si applica ai clienti in Australia, Giappone o Singapore.

Durante la transizione alla fattura unica, si riceverà una fattura parziale per Azure Marketplace. Questa fattura separata finale conterrà gli addebiti per Azure Marketplace precedenti alla data di aggiornamento della fatturazione. Gli addebiti per Azure Marketplace successivi a questa data verranno inclusi nella fattura di Azure. Dopo il periodo di transizione, tutti gli addebiti per Azure e Azure Marketplace saranno inclusi nella fattura unica.  

## <a name="adjust-billing-frequency"></a>Modificare la frequenza di fatturazione

La frequenza di fatturazione di un cliente è annuale, trimestrale o mensile. Il ciclo di fatturazione viene determinato quando il cliente firma il contratto. La fatturazione mensile è l'intervallo di fatturazione più breve.

- Per modificare un periodo di fatturazione da annuale a trimestrale per le registrazioni dirette, è necessaria l'**approvazione** di un amministratore aziendale. Per le registrazioni indirette è necessaria l'approvazione di un amministratore partner. La modifica diventa effettiva alla fine del trimestre di fatturazione corrente.
- Per cambiare un periodo di fatturazione da annuale o trimestrale a mensile, è necessaria una **modifica** del contratto.  Qualsiasi modifica al periodo di fatturazione della registrazione esistente richiede l'approvazione di un amministratore aziendale o della persona identificata come "contatto fatturazione".
- **Inviare** l'approvazione al [supporto di Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selezionare la categoria di problema: **Fatturazione**.

La modifica diventa effettiva alla fine del trimestre di fatturazione corrente.

Se viene firmata una modifica M503, è possibile cambiare la frequenza di fatturazione di qualsiasi contratto in mensile, qualunque sia la frequenza originale. 

## <a name="credits-and-adjustments"></a>Crediti e rettifiche

È possibile visualizzare tutti i crediti o le rettifiche applicate alla registrazione nella sezione **Report** di [Azure Enterprise Portal](https://ea.azure.com).

Per visualizzare i crediti:

1. In [Azure Enterprise Portal](https://ea.azure.com) selezionare la sezione **Report**.
1. Selezionare **Usage Summary** (Riepilogo utilizzo).
1. Nell'angolo superiore destro cambiare visualizzazione passando da **M** a **C**.
1. Estendere il campo di rettifica nella tabella Impegno del servizio di Azure.
1. Verranno visualizzati i crediti applicati alla registrazione e una breve descrizione. Ad esempio: Credito per contratto di servizio.

## <a name="request-an-invoice-copy"></a>Richiedere una copia della fattura

Per richiedere una copia della fattura, contattare il proprio partner.

## <a name="overage-offset"></a>Compensazione dell'eccedenza

Per applicare l'impegno monetario alle eccedenze, è necessario soddisfare i criteri seguenti:

- Sono stati sostenuti addebiti per eccedenze che non sono stati pagati e che rientrano entro un anno dalla data di fine del servizio fatturato.
- L'importo dell'impegno monetario disponibile copre il numero totale di addebiti sostenuti, incluse tutte le fatture di Azure precedenti non pagate.
- Il periodo di fatturazione che si vuole completare deve essere completamente chiuso. La fatturazione viene chiusa completamente dopo il quinto giorno di ogni mese.
- Il periodo di fatturazione che si vuole compensare deve essere completamente chiuso.
- Lo Sconto per impegno in Azure è basato sul nuovo impegno effettivo meno gli eventuali fondi pianificati per il consumo precedente. Questo requisito si applica solo agli addebiti per eccedenza contratti. Poiché è valido solo per i servizi che utilizzano l'impegno monetario, non si applica agli addebiti per Azure Marketplace. Gli addebiti per Azure Marketplace vengono fatturati separatamente.

Per completare una compensazione dell'eccedenza, l'utente o il team degli account può aprire una richiesta di supporto. È necessaria un'approvazione tramite posta elettronica da parte dell'amministratore aziendale o del contatto fatturazione.

## <a name="view-price-sheet-information"></a>Visualizzare le informazioni dell'elenco prezzi

Gli amministratori aziendali possono visualizzare il listino prezzi associato alla registrazione per i servizi di Azure.

Per visualizzare il listino prezzi corrente:

1. In Azure Enterprise Portal selezionare **Report** e quindi **Elenco prezzi**.
2. Visualizzare l'elenco prezzi o selezionare **Download**.

![Esempio che mostra le informazioni dell'elenco prezzi](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Per scaricare un elenco prezzi precedente:

1. In Azure Enterprise Portal selezionare **Report** e quindi **Scarica utilizzo**.
2. Scaricare l'elenco prezzi.

![Esempio che mostra dove scaricare un elenco prezzi precedente](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Alcuni motivi delle differenze nei prezzi:

- I prezzi potrebbero essere cambiati tra la registrazione precedente e la nuova registrazione. Le variazioni di prezzo possono essere dovute al fatto che i prezzi sono contrattuali per una registrazione specifica dalla data di inizio alla data di fine di un contratto.
- Quando si passa a una nuova registrazione, i prezzi vengono modificati in base al nuovo contratto. I prezzi sono determinati dall'elenco prezzi che potrebbe contenere prezzi più alti nella nuova registrazione.
- In caso di prolungamento di una registrazione, anche i prezzi cambiano. I prezzi cambiano in base alle tariffe con pagamento in base al consumo.

## <a name="request-detailed-usage-information"></a>Richiedere informazioni dettagliate sull'utilizzo

Gli amministratori aziendali possono visualizzare un riepilogo dei dati di utilizzo, l'impegno monetario utilizzato e gli addebiti associati all'utilizzo aggiuntivo in Azure Enterprise Portal. Gli addebiti vengono presentati a livello di riepilogo in tutti gli account e le sottoscrizioni.

Per visualizzare i dettagli dell'utilizzo in account specifici, scaricare il report relativo ai dettagli sull'utilizzo scegliendo **Report** > **Scarica utilizzo**.

> [!NOTE]
> Il report relativo ai dettagli dell'utilizzo non include le imposte applicabili. Potrebbero verificarsi fino a otto ore di latenza tra l'ora dell'utilizzo e il momento in cui viene visualizzato nel report.

Per le registrazioni indirette, il partner deve abilitare la funzione di markup per poter visualizzare le informazioni relative ai costi.

## <a name="reports"></a>Report

Gli amministratori aziendali possono visualizzare un riepilogo dei dati di utilizzo, l'impegno monetario utilizzato e gli addebiti associati all'utilizzo aggiuntivo in Azure Enterprise Portal. Gli addebiti vengono presentati a livello di riepilogo in tutti gli account e le sottoscrizioni.

### <a name="azure-enterprise-reports"></a>Record di Azure Enterprise

- Riepilogo e grafici dell'utilizzo
- Report sull'utilizzo dei servizi
- Report sui saldi e sugli addebiti
- Report relativo ai dettagli dell'utilizzo
- Report sugli addebiti per Azure Marketplace
- Elenco prezzi
- Download dei report avanzati
- Formattazione dei report CSV

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Per visualizzare grafici e report di riepilogo sull'utilizzo:

1. Passare ad Azure Enterprise Portal.
1. Selezionare **Report** nel riquadro di sinistra.
1. Selezionare la scheda **Usage Summary** (Riepilogo utilizzo).
1. Scegliere il periodo di impegno dal menu degli intervalli di date in alto a sinistra.
1. Per visualizzare ulteriori dettagli, selezionare un periodo o un mese nel grafico.
1. In questa scheda è possibile:
   - Visualizzare un grafico dell'utilizzo mensile con il dettaglio su utilizzo, maggiorazioni di prezzo dei servizi, addebiti fatturati separatamente e addebiti per Azure Marketplace.
   - Applicare un filtro in base a reparti, account e sottoscrizioni sotto il grafico.
   - Passare dal dettaglio **Charge-by-Services** (Addebito per servizi) al dettaglio **Charge-by-Hierarchy** (Addebito per gerarchia) e viceversa.
   - Visualizzare i dettagli dei servizi di Azure, gli addebiti fatturati separatamente e gli addebiti per Azure Marketplace.

## <a name="service-usage-report"></a>Report sull'utilizzo dei servizi

La pagina del report sull'utilizzo dei servizi consente agli amministratori aziendali di visualizzare un riepilogo dei dati di utilizzo di un servizio. L'utilizzo è visualizzato come riepilogo per tutti gli account e le sottoscrizioni. Per visualizzare i dettagli di utilizzo, è possibile filtrare il report in base ad account o sottoscrizioni.

> [!NOTE]
> Potrebbero verificarsi fino a cinque giorni di latenza tra la data dell'utilizzo e il momento in cui viene visualizzato nel report.

### <a name="to-view-the-report"></a>Per visualizzare il report:

1. Accedere ad Azure Enterprise Portal.
1. Selezionare **Report** nel riquadro di spostamento di sinistra.
1. Selezionare la scheda **Usage Summary** (Riepilogo utilizzo).
1. Selezionare l'intervallo di date.
1. Scegliere gli account o le sottoscrizioni da visualizzare.
1. Facoltativamente, è possibile:
   - Cambiare visualizzazione da **Charge by Services** (Addebito per servizi) a **Charge by Hierarchy** (Addebito per gerarchia) e viceversa per visualizzare dettagli diversi.
   - Visualizzare i dettagli Nome servizio, Unità di misura, Unità consumate, Effective Rate (Tariffa effettiva) e Costo esteso.

## <a name="download-csv-reports"></a>Scaricare i report CSV

Gli amministratori aziendali usano la pagina di download dei report mensili per scaricare diversi report come file CSV. I report scaricabili includono:

- Report sui saldi e sugli addebiti
- Report relativo ai dettagli dell'utilizzo
- Report sugli addebiti per Azure Marketplace
- Elenco prezzi

### <a name="to-download-reports"></a>Per scaricare i report:

1. In Azure Enterprise Portal selezionare **Report**.
1. Sulla barra multifunzione superiore selezionare **Usage Download** (Download utilizzo).
1. Selezionare l'opzione di **download** accanto al report del mese appropriato.

### <a name="csv-report-formatting-issues"></a>Problemi di formattazione dei report CSV

I clienti che visualizzano i report CSV di Azure Enterprise Portal in euro potrebbero riscontrare problemi di formattazione per le virgole e i punti.

Ad esempio, potrebbe venire visualizzato quanto segue:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Ore | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Dovrebbe essere visualizzato:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Ore | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Questo problema di formattazione si verifica a causa delle impostazioni predefinite nella funzionalità di importazione di Excel. Excel importa tutti i campi come testo 'Generale' e presuppone che un numero abbia un separatore basato sullo standard matematico. Ad esempio: "1,000.00".

Se una valuta europea usa il punto (.) come separatore delle migliaia e la virgola (,) come separatore decimale, la valuta non viene visualizzata correttamente. Ad esempio: "1.000,00". I risultati dell'importazione possono variare a seconda dell'impostazione della lingua.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Per importare il file CSV senza problemi di formattazione:

1. In Microsoft Excel scegliere **File** > **Apri**.
   Verrà visualizzata l'Importazione guidata testo.
1. In **Tipo dati originali** scegliere **Delimitato**.  L'impostazione predefinita è **Larghezza fissa**.
1. Selezionare **Avanti**.
1. In Delimitatori selezionare la casella di controllo per **Virgola**. Deselezionare **TAB** se l'opzione è selezionata.
1. Selezionare **Avanti**.
1. Scorrere fino alle colonne **ResourceRate** ed **ExtendedCost**.
1. Selezionare la colonna **ResourceRate**. La colonna viene evidenziata in nero.
1. Nella sezione **Formato dati per colonna** selezionare **Testo** invece di **Generale**. L'intestazione di colonna cambierà da **Generale** a **Testo**.
1. Ripetere i passaggi 8 e 9 per la colonna **ExtendedCost** e quindi selezionare **Fine**.

> [!TIP]
> Se è stata impostata l'apertura automatica dei file CSV in Excel, è necessario usare invece la funzione **Apri** in Excel. In Excel scegliere **File** > **Apri**.

## <a name="balance-and-charge-report"></a>Report sui saldi e sugli addebiti

Il report sui saldi e sugli addebiti offre un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace, rettifiche e addebiti per eccedenza.

Tutte le righe della tabella di riepilogo Impegno del servizio di Azure rimangono statiche per ogni mese. I dettagli per tutti gli acquisti e le rettifiche sono disponibili nelle sezioni **New Purchase Details** (Dettagli nuovo acquisto) e **Adjustment Details** (Dettagli rettifica).

### <a name="download-the-balance-and-charge-report"></a>Scaricare il report sui saldi e sugli addebiti

1. Accedere ad Azure Enterprise Portal come amministratore aziendale.
1. Selezionare **Report** nel riquadro di sinistra.
1. Selezionare la scheda **Report Download** (Download report).
1. Selezionare il mese appropriato nella colonna **Balance and Charge** (Saldo e addebito) e fare clic per scaricare il report.

## <a name="usage-detail-report"></a>Report relativo ai dettagli dell'utilizzo

Il report relativo ai dettagli dell'utilizzo offre un riepilogo mensile sull'utilizzo dei servizi e delle quantità di una registrazione. Include informazioni sui nomi dei contatori, sui tipi di contatore e sulle quantità utilizzate.

### <a name="download-the-usage-detail-report"></a>Scaricare il report relativo ai dettagli dell'utilizzo

1. Accedere ad Azure Enterprise Portal come amministratore aziendale.
1. Selezionare **Report** nel riquadro di spostamento di sinistra.
1. Selezionare la scheda **Scarica utilizzo**.
1. Selezionare il mese appropriato nella colonna **Dettagli utilizzo** e fare clic per scaricare il report.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Addebiti per Azure Marketplace nei report di Azure Enterprise Portal

Esistono due tipi di addebiti per Azure Marketplace:

- **Basato sull'utilizzo:** prodotti misurati in unità transazionali.  Ad esempio, per le macchine virtuali viene effettuato un addebito orario, mentre per le ricerche nell'API Bing viene effettuato un addebito in base al numero delle ricerche.
- **Tariffa mensile:** fatturazione mensile in base all'utilizzo o all'accesso.

Per altre informazioni sugli addebiti per Azure Marketplace, vedere [Domande frequenti su Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

Per visualizzare i diversi addebiti in Azure Enterprise Portal:

- **Report di riepilogo dell'utilizzo**: Visualizza gli addebiti per Azure Marketplace basati sull'utilizzo **e** con tariffa mensile.
- **Report sugli addebiti per Marketplace**: Visualizza **solo** gli addebiti per Azure Marketplace basati sull'utilizzo.  Le tariffe una tantum non vengono visualizzate.

> [!NOTE]
> Azure Marketplace non è disponibile per le registrazioni MPSA.

## <a name="advanced-report-download"></a>Download dei report avanzati

Per la creazione di report relativi a intervalli di date o account specifici, è possibile usare la funzionalità di download dei report avanzati. A partire dal 30 agosto 2016, il formato del file di output è il formato CSV per contenere set di record più grandi.

1. In Azure Enterprise Portal selezionare **Advanced Report Download** (Download dei report avanzati).
1. Selezionare **Appropriate Date Range** (Intervallo di date appropriato).
1. Selezionare **Appropriate Accounts** (Account appropriati).
1. Selezionare **Request Usage Data** (Richiedi dati utilizzo).
1. Fare clic sul pulsante **Aggiorna** finché lo stato del report non viene aggiornato in **Download**.
1. Scaricare il report.

## <a name="reporting-for-non-enterprise-administrators"></a>Creazione di report per chi non è amministratore dell'organizzazione

Gli amministratori aziendali possono concedere agli amministratori di reparto e ai proprietari di account le autorizzazioni per la visualizzazione degli addebiti in una registrazione. I proprietari degli account con accesso possono scaricare report CSV specifici dell'account e delle sottoscrizioni. Possono anche visualizzare graficamente le informazioni nella sezione dei report di Azure Enterprise Portal.

### <a name="to-enable-access"></a>Per abilitare l'accesso:

 1. Accedere ad Azure Enterprise Portal come amministratore aziendale.
 1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
 1. Selezionare la scheda **Registrazione**.
 1. Nella sezione **Enrollment Detail** (Dettagli registrazione) selezionare l'icona della matita accanto a **DA View Charges** (Visualizza addebiti Amministratore reparto) o **AO View Charges** (Visualizza addebiti Proprietario account).
 1. Selezionare **Enabled**.
 1. Selezionare **Salva**.

### <a name="to-view-reports"></a>Per visualizzare i report:

1. Accedere ad Azure Enterprise Portal come amministratore di reparto o proprietario di account.
1. Selezionare **Report** nel riquadro di spostamento di sinistra.
1. Fare clic sulla scheda **Usage Summary** (Riepilogo utilizzo) per visualizzare graficamente le informazioni sull'account e sulla sottoscrizione.
1. Selezionare **Usage Download** (Download utilizzo) per visualizzare i report CSV.

Gli amministratori di reparto e i proprietari di account non possono visualizzare gli addebiti quando usano la funzione **Advanced Report Download** (Download report avanzati). I costi vengono visualizzati come $ 0.

Le autorizzazioni dei proprietari di account per la visualizzazione degli addebiti si estendono ai proprietari di account e a tutti gli utenti che hanno le autorizzazioni per le sottoscrizioni associate. Per i clienti indiretti le funzionalità relative ai costi devono essere abilitate dal partner di canale.

## <a name="move-usage-data-to-another-enrollment"></a>Spostare i dati di utilizzo in un'altra registrazione

I dati di utilizzo vengono spostati solo quando un trasferimento viene retrodatato. Sono disponibili due opzioni per spostare i dati di utilizzo da una registrazione a un'altra:

- Trasferimenti di account da una registrazione a un'altra
- Trasferimenti della registrazione da una registrazione a un'altra

Per entrambe le opzioni, è necessario inviare una [richiesta di supporto](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) al team di supporto EA per assistenza. 

## <a name="azure-ea-pricing-overview"></a>Panoramica sui prezzi EA di Azure

Questa sezione include informazioni dettagliate sul modo in cui viene calcolato l'utilizzo. La sezione contiene le risposte a molte domande frequenti sugli addebiti per i diversi servizi di Azure di un Contratto Enterprise in cui i calcoli sono più complessi.

### <a name="price-protection"></a>Protezione dei prezzi

Ai clienti o partner di canale vengono garantiti prezzi corrispondenti o inferiori a quelli indicati nell'Elenco prezzi cliente (CPS) o il prezzo applicato alla data di validità dell'acquisto di Azure. Questo prezzo è chiamato prezzo di base. Per i servizi resi disponibili dopo l'acquisto di Azure, viene applicato il prezzo effettivo con lo sconto applicabile al momento in cui il servizio è stato reso disponibile per la prima volta. Questa protezione dei prezzi si applica per la durata del periodo di impegno, che può essere di uno o tre anni a seconda del Contratto Enterprise.

### <a name="price-changes"></a>Modifiche ai prezzi

Microsoft può decidere di non applicare il prezzo corrente del Contratto Enterprise per i singoli servizi di Azure durante il periodo di durata di una registrazione. Durante il periodo di validità del prezzo più basso, le tariffe ridotte verranno estese ai clienti attuali. Se queste tariffe aumentano in seguito, il prezzo della registrazione per un servizio non aumenterà oltre il limite di protezione dei prezzi, come dichiarato in precedenza. È possibile tuttavia che la tariffa aumenti rispetto al precedente prezzo ridotto. In entrambi i casi, Microsoft informerà i clienti e i partner di canale indiretti delle imminenti variazioni di prezzo inviando un messaggio di posta elettronica agli amministratori aziendali e agli amministratori partner associati alla registrazione.

### <a name="current-effective-pricing"></a>Prezzi attualmente in vigore

Il cliente e i partner di canale possono visualizzare i prezzi correnti per una registrazione accedendo ad [Azure Enterprise Portal](https://ea.azure.com/) e passando alla pagina dell'elenco prezzi della registrazione. Se si acquista Azure indirettamente tramite uno dei partner di canale Microsoft, sarà necessario richiedere gli aggiornamenti dei prezzi al partner di canale a meno che non sia stata abilitata la visualizzazione degli aumenti dei prezzi per la registrazione.

### <a name="introduction-of-new-azure-services"></a>Introduzione ai nuovi servizi di Azure

Azure viene migliorato costantemente e vengono aggiunti periodicamente nuovi servizi con prezzi distinti rispetto ai servizi esistenti. Alcuni servizi in anteprima sono disponibili automaticamente, mentre altri richiedono un'azione esplicita del cliente nel [portale Account Azure](https://account.windowsazure.com/PreviewFeatures).

Alcuni servizi vengono offerti con i prezzi promozionali in vigore al momento in cui sono stati presentati per la prima volta che possono essere aumentati successivamente.

Man mano che i servizi passano dall'anteprima alla disponibilità generale (GA), i prezzi possono aumentare a seguito dell'applicazione dei contratti di servizio per l'affidabilità e le prestazioni complete. Questo aumento non è limitato dalla normale protezione dei prezzi di base. L'utilizzo di questi servizi viene addebitato in base alla tariffa aumentata. Per evitare addebiti correlati a questi nuovi servizi, è necessario rifiutarne esplicitamente l'utilizzo.

### <a name="introduction-of-regional-pricing"></a>Introduzione ai prezzi a livello di area

Oltre all'introduzione di nuovi servizi, i servizi vengono anche cambiati periodicamente passando da un'unica base globale a un modello per un'area specifica man mano che ne viene esteso il supporto a livello locale.

Quando viene introdotta per la prima volta la regionalizzazione di un servizio, la protezione dei prezzi di base viene applicata per le nuove aree. Tuttavia, se le aree aggiuntive per il servizio vengono introdotte successivamente, vengono considerate come nuovi servizi e offerte con tariffe separate indipendenti dalla protezione dei prezzi di base.

### <a name="enterprise-devtest"></a>Sviluppo/test Enterprise

Gli amministratori aziendali possono consentire ai proprietari di account di creare sottoscrizioni basate sull'offerta Sviluppo/test Enterprise. Il proprietario dell'account deve configurare le sottoscrizioni Sviluppo/test Enterprise necessarie per i sottoscrittori sottostanti. Questa configurazione consente ai sottoscrittori di Visual Studio attivi di eseguire carichi di lavoro di sviluppo e test in Azure alle tariffe speciali di sviluppo/test Enterprise. Per altre informazioni, vedere [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Calcoli di utilizzo del Contratto Enterprise

Per informazioni su prezzi pubblici di base, unità di misura, domande frequenti e linee guida per la creazione di report sull'utilizzo per ogni singolo servizio, vedere [Servizi di Azure](https://azure.microsoft.com/services/) e [Prezzi di Azure](https://azure.microsoft.com/pricing/). Altre informazioni sui calcoli del Contratto Enterprise sono disponibili nelle sezioni seguenti.

### <a name="enterprise-agreement-units-of-measure"></a>Unità di misura del Contratto Enterprise

Le unità di misura per i contratti Enterprise sono spesso diverse da quelle di altri programmi Microsoft, ad esempio il Contratto di Sottoscrizione Microsoft Online. Questa differenza indica che, per diversi servizi, l'unità di misura viene aggregata per offrire prezzi normalizzati. L'unità di misura nella visualizzazione Usage Summary (Riepilogo utilizzo) di Azure Enterprise Portal è sempre la misura Enterprise. Un elenco completo delle unità di misura e conversioni correnti per ogni servizio è disponibile nel file di Excel [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Regole di arrotondamento

Azure Enterprise Portal applica la logica di arrotondamento gaussiano o del banchiere dello standard IEEE. Questa logica arrotonda i numeri alla cifra pari più vicina per i valori non interi. La logica di arrotondamento per eccesso più comune arrotonda sempre le cifre non intere alla successiva cifra più alta. Questo metodo di Azure Enterprise Portal effettivamente fornisce una somma totale più precisa per il gruppo rispetto alla logica standard di Excel.

Ovvero, quando la prima cifra eliminata è il 5 e non sono presenti cifre che seguono oppure le cifre che seguono sono zeri, viene eseguito l'arrotondamento alla cifra pari più vicina. Ad esempio, 2,315 e 2,325 arrotondati al centesimo più vicino diventano 2,32.

Come riferimento, la tabella seguente mostra le formule di Excel che è possibile usare per modellare le regole di Azure Enterprise Portal per l'arrotondamento e la conversione:

| Scenario | Formula della logica del banchiere |
| --- | --- |
| Arrotondamento dell'utilizzo | =MROUND({_origine_}, 0,0002) |
| Arrotondamento dei prezzi (due decimali) | =MROUND({_origine_}, 0,02) |
| Arrotondamento dei prezzi (zero decimali) | =MROUND({_origine_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversione tra il report relativo ai dettagli dell'utilizzo e la pagina di riepilogo dell'utilizzo

Il report dei dati di utilizzo scaricati include l'utilizzo delle risorse non elaborato fino a sei posizioni decimali. Tuttavia, i dati di utilizzo visualizzati in Azure Enterprise Portal sono arrotondati a quattro posizioni decimali per le unità di impegno e troncati a zero decimali per le unità di eccedenza. I dati di utilizzo non elaborati vengono arrotondati a quattro cifre prima della conversione nelle unità usate in Azure Enterprise Portal. Le unità Enterprise convertite vengono quindi arrotondate di nuovo a quattro cifre. Le ore effettive utilizzate prima della conversione sono visibili solo nel report sull'utilizzo scaricato e non in Azure Enterprise Portal.

Ad esempio: Si supponga che il report relativo ai dettagli dell'utilizzo indichi 694,533404 ore di SQL Server effettive. Queste unità vengono convertite in 6,94533404 di 100 ore di calcolo e quindi arrotondate a 6,9453 e visualizzate in Azure Enterprise Portal.

- Per determinare l'importo di fatturazione totale, le unità visualizzate vengono moltiplicate per il prezzo unitario dell'impegno e il risultato viene troncato a due decimali. Per lo yen giapponese (JPY) e il won coreano (KRW), l'importo totale viene arrotondato a zero decimali.
- Per l'eccedenza, le unità di fatturazione vengono troncate a sei cifre e quindi moltiplicate per il prezzo unitario di eccedenza per determinare l'importo di fatturazione totale.
- Per la fatturazione del provider di servizi gestiti (MSP), tutto l'utilizzo associato a un reparto contrassegnato come MSP viene troncato a zero decimali dopo la conversione nell'unità di misura del Contratto Enterprise. Di conseguenza, la somma di questo utilizzo potrebbe essere inferiore alla somma totale di tutti i dati di utilizzo visualizzati in Azure Enterprise Portal, a seconda che il provider di servizi gestiti rientri o meno nel saldo dell'impegno monetario o sia in eccedenza.

### <a name="graduated-pricing"></a>Prezzi progressivi

I prezzi del Contratto Enterprise attualmente non includono i prezzi progressivi in cui gli addebiti per unità diminuiscono man mano che l'utilizzo aumenta. Quando si passa da un Contratto di Sottoscrizione Microsoft Online con prezzi progressivi a un Contratto Enterprise, i prezzi sono proporzionali al livello di base del servizio meno eventuali rettifiche applicabili per gli sconti del Contratto Enterprise.

### <a name="partial-hour-billing"></a>Fatturazione di ore parziali

Tutto l'utilizzo viene fatturato in base ai minuti convertiti in ore parziali e non in base a incrementi di ore intere. Le tariffe Enterprise orarie elencate vengono applicate alle ore totali più le ore parziali.

### <a name="average-daily-consumption"></a>Utilizzo giornaliero medio

Per alcuni servizi i prezzi sono su base mensile, mentre l'utilizzo è indicato su base giornaliera. In questi casi, l'utilizzo viene calcolato una volta al giorno, suddiviso per 31 e sommato per il numero di giorni incluso nel mese di fatturazione. Di conseguenza, le tariffe non sono mai più alte di quanto previsto per un mese e sono leggermente inferiori per i mesi con meno di 31 giorni.

### <a name="compute-hours-conversion"></a>Conversione delle ore di calcolo

Prima del 28 gennaio 2016, l'utilizzo di macchine virtuali A0, A2, A3 e A4 Standard e Basic e dei servizi cloud veniva calcolato come minuti del contatore della macchina virtuale A1. Le macchine virtuali A0 venivano conteggiate come frazioni dei minuti della macchina virtuale A1, mentre le macchine virtuali A2, A3 e A4 venivano convertite in multipli. Poiché questi criteri hanno causato confusione per i clienti, è stata implementata una modifica che assegna l'utilizzo per minuto ai contatori A0, A2, A3 e A4 dedicati.

La nuova misurazione è diventata effettiva tra il 27 e il 28 gennaio 2016. Nel file CSV scaricato che mostra l'utilizzo durante questo periodo di transizione sono riportati:

- Utilizzo calcolato con il contatore A1
- Utilizzo calcolato con il nuovo contatore dedicato che corrisponde alla dimensione della distribuzione.

| **Dimensione della distribuzione** | **Utilizzo calcolato come multiplo di A1 fino al 26 gennaio 2016** | **Utilizzo calcolato con un contatore dedicato a partire dal 27 gennaio 2016** |
| --- | --- | --- |
| A0 | 0,25 dell'ora A1 | 1 ora A0 |
| A2 | 2 ore A1 | 1 ora A2 |
| A3 | 4 ore A1 | 1 ora A3 |
| A4. | 8 ore A1 | 1 ora A4 |

### <a name="regions"></a>Regioni

Per i servizi in cui la zona e l'area influiscono sui prezzi, vedere la tabella seguente per una mappa delle zone e delle aree geografiche:

| Area geografica | Aree di trasferimento dati | Aree CDN |
| --- | --- | --- |
| Zona 1 | Europa settentrionale <br> Europa occidentale <br> Stati Uniti occidentali <br> Stati Uniti orientali <br> Stati Uniti centro-settentrionali <br> Stati Uniti centro-meridionali <br> Stati Uniti orientali 2 <br> Stati Uniti centrali | America del Nord <br> Europa |
| Zona 2 | Asia Pacifico orientale <br> Asia Pacifico sud-orientale <br> Giappone orientale <br> Giappone occidentale <br> Australia orientale <br> Australia sud-orientale | Asia Pacifico <br> Giappone <br> America Latina <br> Medio Oriente/Africa <br> Australia orientale <br> Australia sud-orientale |
| Zona 3 | Brasile meridionale |   |

Non sono previsti addebiti per i dati in uscita tra i servizi ospitati all'interno dello stesso data center. Ad esempio, Office 365 e Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Impegno monetario e utilizzo non fatturato

L'impegno monetario di Azure è un importo pagato in anticipo per i servizi di Azure. L'impegno monetario viene utilizzato man mano che si usano i servizi. I servizi proprietari di Azure vengono fatturati in base all'impegno monetario. Tuttavia, alcuni addebiti vengono fatturati separatamente, mentre i servizi di Azure Marketplace non utilizzano l'impegno monetario.

### <a name="charges-billed-separately"></a>Addebiti fatturati separatamente

Alcuni prodotti e servizi forniti da origini di terze parti non utilizzano l'impegno monetario di Azure. Questi elementi vengono fatturati separatamente come parte della fattura per eccedenza del periodo di fatturazione standard.

Tutti gli addebiti per Azure e Azure Marketplace sono stati riuniti in un'unica fattura allineata al periodo di fatturazione della registrazione. La fattura unica non si applica ai clienti in Australia, Giappone o Singapore.

La fattura unica riporta innanzitutto l'utilizzo di Azure, seguito da eventuali addebiti per Azure Marketplace. Per i clienti in Australia, Giappone o Singapore, gli addebiti per Azure Marketplace sono inclusi in una fattura separata.

Se alla fine del periodo di fatturazione standard non risulta alcun utilizzo in eccedenza, gli addebiti calcolati separatamente verranno fatturati separatamente. Questo processo si applica ai clienti in Australia, Giappone e Singapore.

I servizi seguenti vengono fatturati separatamente:

- Canonical
- Citrix XenApp Essentials
- Utente registrato Citrix XenDesktop
- Openlogic
- Utente registrato XenApp Essentials con Diritti di accesso remoto
- Ubuntu Advantage
- Visual Studio Enterprise (mensile)
- Visual Studio Enterprise (annuale)
- Visual Studio Professional (mensile)
- Visual Studio Professional (annuale)

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace per i clienti con Contratto Enterprise

Per i clienti diretti, gli addebiti per Azure Marketplace sono visibili in Azure Enterprise Portal. Tutti gli acquisti e l'utilizzo di Azure Marketplace vengono fatturati al di fuori dell'impegno monetario con cadenza trimestrale o mensile e in modo posticipato.

Per i clienti indiretti, le sottoscrizioni di Azure Marketplace sono disponibili nella pagina **Gestisci sottoscrizioni** di Azure Enterprise Portal, ma i prezzi sono nascosti. I clienti devono contattare il proprio Licensing Solutions Provider (LSP) per informazioni sugli addebiti per Azure Marketplace.

I nuovi acquisti periodici in Azure Marketplace mensili o annuali vengono fatturati a prezzo intero durante il periodo di acquisto degli elementi di Azure Marketplace. Per questi elementi verrà eseguito il rinnovo automatico nel periodo seguente nello stesso giorno dell'acquisto originario.

Gli addebiti periodici mensili esistenti continueranno a essere rinnovati il primo giorno di ogni mese di calendario. Gli addebiti annuali verranno rinnovati alla ricorrenza annuale della data di acquisto.

Alcuni servizi di rivenditori di terze parti disponibili in Azure Marketplace ora utilizzano il saldo dell'impegno monetario del Contratto Enterprise (EA). In precedenza questi servizi venivano addebitati al di fuori dell'impegno monetario EA e venivano fatturati separatamente. L'impegno monetario EA per questi servizi in Azure Marketplace semplifica la gestione degli acquisti e dei pagamenti dei clienti. Per un elenco completo dei servizi che ora utilizzano l'impegno monetario, vedere l'[aggiornamento del 6 marzo 2018 nel sito Web di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

I Licensing Solutions Provider (LSP) possono scaricare un listino prezzi di Azure Marketplace dalla pagina dell'elenco prezzi in Azure Enterprise Portal. Selezionare il collegamento per il **listino prezzi di Marketplace** in alto a destra. Il listino prezzi di Azure Marketplace elenca tutti i servizi disponibili e i relativi prezzi.

Per scaricare il listino prezzi:

1. In Azure Enterprise Portal scegliere **Report** > **Elenco prezzi**.
1. Nell'angolo superiore destro trovare il collegamento per il listino prezzi di Azure Marketplace sotto il proprio nome utente.
1. Fare clic con il pulsante destro del mouse sul collegamento e selezionare **Salva oggetto con nome**.
1. Nella finestra **Salva** modificare il titolo del documento in `AzureMarketplacePricelist.zip` in modo che il file XLSX venga cambiato in file ZIP.
1. Al termine del download, si avrà a disposizione un file ZIP con i listini prezzi specifici del paese.
1. Gli LSP devono fare riferimento al file del singolo paese per individuare i prezzi specifici del paese in questione. I Licensing Solutions Provider (LSP) possono usare la scheda **Notifiche** per visualizzare gli SKU nuovi o ritirati.
1. Le variazioni di prezzo si verificano raramente. I Licensing Solutions Provider (LSP) ricevono 30 giorni prima tramite posta elettronica le notifiche relative agli aumenti dei prezzi e alle modifiche del tasso di cambio.
1. I Licensing Solutions Provider (LSP) ricevono una fattura per registrazione, per ISV, per trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Abilitazione degli acquisti in Azure Marketplace

Gli amministratori aziendali hanno la possibilità di disabilitare o abilitare gli acquisti in Azure Marketplace per tutte le sottoscrizioni di Azure incluse nella registrazione. Se l'amministratore aziendale disabilita gli acquisti e sono presenti sottoscrizioni di Azure che hanno già sottoscrizioni di Azure Marketplace, queste sottoscrizioni di Azure Marketplace non verranno annullate o non saranno interessate.

Sebbene i clienti possano convertire le sottoscrizioni di Azure dirette al Contratto Enterprise di Azure associandole alla relativa registrazione in Azure Enterprise Portal, questa azione non converte automaticamente le sottoscrizioni figlio.

Per abilitare gli acquisti in Azure Marketplace:

1. Accedere ad Azure Enterprise Portal come amministratore aziendale.
1. Passare a **Gestisci**.
1. In **Dettagli registrazione** selezionare l'icona matita accanto alla voce **Azure Marketplace**.
1. Selezionare o deselezionare **Abilitato/Disabilitato** o **Solo SKU di tipo Gratuito/BYOL** in base alle esigenze.
1. Selezionare **Salva**.

> [!NOTE]
> L'opzione BYOL (Bring Your Own License) e Gratuito limita l'acquisto e l'acquisizione degli SKU di Azure Marketplace ai soli SKU di tipo BYOL e Gratuito.

Altre informazioni sugli [addebiti per Azure Marketplace sono disponibili nei report di Azure Enterprise Portal](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Servizi fatturati su base oraria per il Contratto Enterprise di Azure

I servizi seguenti vengono fatturati su base oraria secondo il Contratto Enterprise anziché in base alla tariffa mensile di MOSP:

- Application Delivery Network
- Web application firewall

### <a name="azure-remoteapp"></a>Azure RemoteApp

Se si ha un Contratto Enterprise, l'addebito per Azure RemoteApp è basato sul livello di prezzo del Contratto Enterprise. Non sono previsti addebiti aggiuntivi. Il prezzo standard include 40 ore iniziali. Il prezzo illimitato include 80 ore iniziali. RemoteApp interrompe il calcolo dell'utilizzo oltre le 80 ore.

## <a name="azure-marketplace-faq"></a>Domande frequenti su Azure Marketplace

Questa sezione descrive in che modo l'impegno monetario di Azure potrebbe essere applicato ad alcuni servizi di rivenditori di terze parti in Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Che cosa è cambiato nei servizi di Azure Marketplace e nell'impegno monetario del Contratto Enterprise?

A partire dal 1° marzo 2018 alcuni servizi di rivenditori di terze parti utilizzano l'impegno monetario (MC) del Contratto Enterprise. Ad eccezione delle istanze di macchine virtuali riservate (RI) di Azure, i servizi venivano conteggiati in precedenza al di fuori dell'impegno monetario del Contratto Enterprise e fatturati separatamente.

L'uso dell'impegno monetario (MC) è stato esteso ad alcuni servizi di Azure Marketplace pubblicati di terze parti che vengano acquistati con maggiore frequenza. L'impegno monetario del Contratto Enterprise per questi servizi in Azure Marketplace semplifica la gestione degli acquisti e dei pagamenti.

### <a name="why-did-we-make-this-change"></a>Perché è stata apportata questa modifica?

I clienti cercano sempre altri modi per utilizzare il pagamento anticipato dell'impegno monetario. Questa modifica è stata richiesta spesso dai clienti e interessa una gran parte dei clienti di Azure Marketplace.

### <a name="how-do-you-benefit"></a>Quali sono i vantaggi?

L'esperienza di fatturazione è più semplice ed è possibile utilizzare l'impegno monetario del Contratto Enterprise. Poiché questi servizi sono inclusi nell'impegno monetario prepagato, l'impegno monetario del Contratto Enterprise acquisisce maggior valore.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Quali servizi di Azure Marketplace utilizzano l'impegno monetario del Contratto Enterprise e come viene informato il cliente?

Quando si acquista un servizio che utilizza l'impegno monetario, Azure Marketplace visualizza una dichiarazione di non responsabilità. Sono supportati alcuni servizi pubblicati da Red Hat, SUSE, Autodesk e Oracle. Attualmente, i servizi con denominazione simile pubblicati da altre parti non vengono detratti dall'impegno monetario. Alla fine di queste domande frequenti è disponibile un elenco completo.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Cosa accade se si esaurisce l'impegno monetario del Contratto Enterprise?

Se è stato utilizzato tutto l'impegno monetario e si è in condizione di eccedenza, gli addebiti relativi a questi servizi saranno inclusi nella successiva fattura per eccedenza insieme a eventuali altri servizi a consumo. Prima della modifica del 1° marzo 2018, questi addebiti venivano fatturati con gli altri servizi di Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Perché l'utilizzo dell'impegno monetario del Contratto Enterprise non viene abilitato per tutti i servizi di Azure Marketplace?

Stiamo lavorando spesso per offrire la miglior esperienza cliente correlata all'impegno monetario del Contratto Enterprise. Questa modifica riguarderà un numero elevato di clienti e una parte significativa della spesa totale in Azure Marketplace. In futuro potrebbero venire aggiunti altri servizi.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Quali saranno le differenze per la registrazione e i partner indiretti?

Non ci sono variazioni per i partner o i clienti con registrazione indiretta. Questi servizi sono soggetti alle stesse possibilità di ricarico dei partner a cui sono sottoposti altri servizi a consumo. L'unico cambiamento è costituito dall'inserimento degli addebiti in una fattura diversa e dal pagamento degli addebiti attraverso l'impegno monetario del Contratto Enterprise del cliente.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>È disponibile un elenco dei servizi di Azure Marketplace che utilizzano l'impegno monetario del Contratto Enterprise?

Alcune offerte specifiche di Azure Marketplace possono usare i fondi dell'impegno monetario. Per un elenco completo dei prodotti che partecipano al programma, vedere i [servizi di terze parti che usano l'impegno monetario](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).

## <a name="power-bi-reporting"></a>Report di Power BI

La creazione di report di Power BI è disponibile per i partner EA diretti e per i clienti indiretti che hanno accesso alla visualizzazione delle informazioni di fatturazione.

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro è disponibile per i clienti con Contratto Enterprise. Con Power BI Pro è possibile generare e condividere report per gestire i dati dei costi in modo efficace. Sono disponibili anche funzionalità di collaborazione e aggiornamento dati aggiuntive. Power BI Pro offre limiti più elevati per la capacità dati e il flusso dei dati

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Per accedere a Informazioni dettagliate sul consumo di Microsoft Azure:

1. Passare a [Informazioni dettagliate sul consumo di Microsoft Azure](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Selezionare **Scarica adesso**.
1. Specificare un numero di registrazione e il numero di mesi, quindi selezionare **Avanti**.
1. Specificare la chiave di accesso API per connettersi. La chiave per la registrazione è disponibile in [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Selezionare **Accedi** per avviare automaticamente il processo di importazione.
1. Al termine, nel riquadro di spostamento vengono visualizzati un nuovo dashboard, un nuovo report e un nuovo modello. Selezionare il dashboard per visualizzare i dati importati.

> [!TIP]
>
> - Per altre informazioni su come generare la chiave API per la registrazione, vedere il file della Guida sui report API in [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Per altre informazioni sulla connessione di Power BI al consumo di Azure, vedere [Informazioni dettagliate sul consumo di Microsoft Azure](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Per accedere al pacchetto di contenuto EA Power BI legacy:

1. Passare al [sito Web Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Accedere con un account aziendale o dell'istituto di istruzione valido.

   L'account aziendale o dell'istituto di istruzione può essere uguale o diverso da quello usato per accedere alla registrazione tramite Azure Enterprise Portal.
1. Nel dashboard dei servizi selezionare **Microsoft Azure Enterprise** e fare clic su **Connetti**.
1. Nella schermata di **connessione ad Azure Enterprise** specificare quanto segue:
    - URL dell'ambiente Azure: [https://ea.azure.com](https://ea.azure.com/)
    - Numero di mesi: da 1 a 36
    - Numero di registrazione: il numero della registrazione
1. Selezionare **Avanti**.
1. Nella casella **Chiave di autenticazione** immettere la chiave API.

    È possibile ottenere la chiave API in Azure Enterprise Portal nella scheda **Scarica utilizzo**. Selezionare **API Access Key** (Chiave accesso API) e quindi incollare la chiave nella casella **Account Key** (Chiave account).
1. Il caricamento dei dati in Power BI richiede dai 5 ai 30 minuti, a seconda delle dimensioni dei set di dati.

## <a name="reports-faq"></a>Domande frequenti sui report

In questa sezione vengono trattate le domande comuni sui report.

### <a name="why-is-my-cost-showing-as-0"></a>Perché il costo viene visualizzato come $ 0?

Per i clienti con **registrazione diretta**, gli amministratori aziendali possono concedere ai proprietari di account e agli amministratori di reparto l'accesso alle informazioni su costi e prezzi nei report sull'utilizzo. A tale scopo, seguire questa procedura:

1. In Azure Enterprise Portal selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Selezionare la matita blu accanto alla visualizzazione degli addebiti per gli amministratori di reparto.
1. Selezionare **Abilitato** e salvare.
1. Selezionare la matita blu accanto alla visualizzazione degli addebiti per i proprietari di account.
1. Selezionare **Abilitato** e salvare.

> [!NOTE]
> Se si è un proprietario di account o un amministratore di reparto, contattare l'amministratore aziendale per abilitare la funzionalità relativa ai prezzi.

Per i clienti con **registrazione indiretta**, contattare il partner per verificare che abbia abilitato la funzionalità relativa ai prezzi. Questa operazione può essere eseguita solo dal partner. Dopo aver abilitato la funzionalità, è possibile visualizzare il costo e i prezzi per la registrazione come amministratore aziendale.

I partner che vogliono abilitare la funzionalità di visualizzazione degli addebiti per un proprietario di account e un amministratore di reparto, possono seguire la procedura descritta in **registrazione diretta**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Perché nel report relativo ai dettagli dell'utilizzo non sono presenti informazioni sugli SKU?

Il report relativo ai dettagli dell'utilizzo non contiene informazioni sugli SKU. Il report, tuttavia, contiene informazioni sull'utilizzo ed è possibile scaricare il report dell'elenco prezzi per ottenere le informazioni sullo SKU.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Perché l'importo totale in Azure Marketplace non corrisponde ai report di riepilogo e dei dettagli dell'utilizzo?

Il report degli addebiti di Azure Marketplace mostra solo gli addebiti basati sull'utilizzo. Le tariffe una tantum non vengono visualizzate. Vedere la pagina di riepilogo dell'utilizzo per gli addebiti basati sull'utilizzo e per le tariffe una tantum più aggiornati.

### <a name="why-is-there-no-information-on-my-api-report"></a>Perché il report API non contiene informazioni?

Le chiavi API scadono ogni sei mesi. Se si verifica un problema, un amministratore aziendale deve generare una nuova chiave API. Ricordarsi di seguire la procedura descritta nelle domande frequenti sul report API.

### <a name="why-isnt-my-power-bi-report-working"></a>Perché il report di Power BI non funziona?

Per problemi relativi a Power BI, registrare un ticket presso il [team di supporto di Power BI](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Perché i tag delle risorse non vengono visualizzati nei report?

I tag delle risorse vengono gestiti nel portale di Azure. È possibile contattare il team della sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com). Seguire la procedura descritta nell'articolo [Come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Perché la tariffa per le risorse cambia ogni giorno?

La tariffa per le risorse visualizzata nel report dei dettagli di utilizzo è un valore calcolato. Rappresenta la tariffa mensile media addebitata per il servizio. La tariffa per le risorse viene calcolata usando la media dell'impegno mensile e degli addebiti per eccedenza mensili per un'unità di servizio. La parte di utilizzo addebitata a fronte dell'impegno e delle tariffe di eccedenza cambia nel giorno di chiusura del mese. Di conseguenza, anche la tariffa per le risorse cambia nel corso del mese. La tariffa si blocca il quinto giorno successivo alla fine del mese.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossario dei processi per il calcolo della tariffa per le risorse

- **Unità RAW totali:** quantità utilizzata nel report dei dettagli di utilizzo.
- **Risorsa MOCP per unità:** il sistema di utilizzo upstream calcola gli utilizzi relativi a ogni servizio in unità diverse. (Preimpostato)
- **Consumo unitario:** unità di misura di Azure Enterprise. (Preimpostato)
- **Prezzo:** prezzo unitario indicato in Azure Enterprise Portal.
- **Costo totale:** costo totale indicato nel report dei dettagli di utilizzo o utilizzo dell'impegno più l'eccedenza indicati in Azure Enterprise Portal.

### <a name="charges-calculations"></a>Calcoli degli addebiti

- **Conversione in risorse MOCP utilizzate** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Conversione in unità utilizzate** = `Consumed MOCP Resources / Consumption per Unit`
- **Calcolo del costo totale** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Logica di calcolo dell'utilizzo

**Tariffa per le risorse** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

La tariffa per le risorse viene calcolata in base agli addebiti. Potrebbe non corrispondere al prezzo unitario effettivo indicato nell'elenco prezzi.

Il report dei dati di utilizzo scaricati include l'utilizzo delle risorse non elaborato fino a sei posizioni decimali. Questi dati vengono usati per i calcoli degli addebiti per eccedenza. Tuttavia, i dati di utilizzo visualizzati in Azure Enterprise Portal sono arrotondati a quattro posizioni decimali per le unità di impegno e troncati a zero decimali per le unità di eccedenza. In Azure Enterprise Portal l'utilizzo in eccedenza viene addebitato solo per le unità complete. È possibile notare una notevole differenza tra il prezzo unitario e la tariffa per le risorse per l'utilizzo addebitato come eccedenza o in mesi misti.

## <a name="next-steps"></a>Passaggi successivi

- I file di Excel seguenti offrono informazioni dettagliate sui servizi di Azure e vengono aggiornati il giorno 6 e 20 di ogni mese:

   | Titolo | Descrizione | Nome file |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Elenca tutti i servizi attivi e include: <br>  <ul><li>categoria del servizio</li>   <li>nome descrittivo del servizio</li>   <li>nome dell'impegno e numero di parte</li> <li>nome del consumo e numero di parte</li>   <li>unità di misura</li>   <li>fattori di conversione tra utilizzo indicato nel report e utilizzo visualizzato in Azure Enterprise Portal</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Questo foglio di calcolo fornisce un elenco di tutte le possibili combinazioni dei campi relativi ai servizi nel report di download dell'utilizzo. | Service\_Download\_Fields.xlsx |

- Per informazioni su come comprendere la fattura e gli addebiti, vedere [Informazioni sulla fattura del contratto Enterprise di Azure](../understand/review-enterprise-agreement-bill.md).
- Per iniziare a usare Azure Enterprise Portal, vedere [Introduzione ad Azure Enterprise Portal](ea-portal-get-started.md).
