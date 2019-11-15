---
title: Fatture per la registrazione EA di Azure
description: Questo articolo illustra come gestire la fattura della registrazione EA di Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 6bd70a58552a0217cff82cad10b11783aec64347
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888412"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Fatture per la registrazione EA di Azure

Questo articolo illustra come gestire la fattura della registrazione EA di Azure. La fattura è una rappresentazione della bolletta, quindi è opportuno esaminarla per verificarne la correttezza. È anche consigliabile acquisire familiarità con altre attività che potrebbero essere necessarie per la gestione della fattura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Modificare un numero di ordine di acquisto per una fattura per eccedenza

Azure EA Portal genera automaticamente un numero di ordine di acquisto predefinito, a meno che l'amministratore della registrazione EA non ne imposti uno prima della data della fattura. Tale amministratore può aggiornare il numero dell'ordine di acquisto anche fino a sette giorni dopo la ricezione di un messaggio di posta elettronica automatico di notifica della fattura.

È inoltre possibile immettere un numero di ordine di acquisto per un account o una sottoscrizione specifica. Per impostazione predefinita, nei report viene usato il numero di ordine di acquisto di livello più basso configurato nella gerarchia; pertanto, se non viene immesso alcun numero di ordine di acquisto della sottoscrizione, verrà usato il numero di ordine di acquisto dell'account e, se non viene immesso alcun ordine di acquisto dell'account, verrà usato l'ordine di acquisto del reparto.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Per aggiornare il numero di ordine di acquisto dei servizi di Azure:

1. In Azure EA Portal fare clic su **Report** e quindi su **Riepilogo utilizzo**.
1. Nell'angolo superiore destro selezionare **Edit PO Numbers** (Modifica numeri ordini di acquisto).
1. Selezionare il pulsante di opzione **Servizi di Azure**.
1. Scegliere un intervallo di date dal menu a discesa del campo **Periodo di fatturazione**. I numeri degli ordini di acquisto possono essere modificati anche fino a sette giorni dopo la notifica della fattura o fino al pagamento della fattura, a seconda di quale di questi due eventi si verifica per primo.
1. Immettere il nuovo numero di ordine di acquisto nel campo  **Numero di ordine di acquisto** .
1. Fare clic su  **Salva**  per inviare la modifica.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Per aggiornare il numero di ordine di acquisto del Marketplace:

1. In Azure EA Portal fare clic su **Report** e quindi su **Riepilogo utilizzo**.
1. Nell'angolo superiore destro selezionare **Edit PO Numbers** (Modifica numeri ordini di acquisto).
1. Selezionare il pulsante di opzione **Marketplace**.
1. Scegliere un intervallo di date dal menu a discesa del campo **Periodo di fatturazione**. I numeri degli ordini di acquisto possono essere modificati anche fino a sette giorni dopo la notifica della fattura o fino al pagamento della fattura, a seconda di quale di questi due eventi si verifica per primo.
1. Immettere il nuovo numero di ordine di acquisto nel campo  **Numero di ordine di acquisto** .
1. Fare clic su  **Salva**  per inviare la modifica.

## <a name="cadence-of-azure-ea-billing"></a>Frequenza della fatturazione EA di Azure

### <a name="billing-intervals"></a>Intervalli di fatturazione

Microsoft addebiterà una volta all'anno, in corrispondenza della data di entrata in vigore della registrazione, tutti gli acquisti dell'impegno riguardanti i servizi di Microsoft Azure e in via posticipata qualsiasi utilizzo in eccesso rispetto agli importi dell'impegno. I corrispettivi dell'impegno vengono suddivisi in base a una tariffa mensile e addebitati annualmente in anticipo. I corrispettivi dell'eccedenza vengono calcolati ogni mese e addebitati in via posticipata alla fine del periodo di fatturazione.

A seconda del modo in cui si sceglie di effettuare gli acquisti dell'impegno, l'impegno annuale sarà contemporaneo alla data di ricorrenza della registrazione o alla data di entrata in vigore della sottoscrizione di rettifica di un anno.

Si riceverà la fattura per eccedenza in base alla data di inizio e alla configurazione della registrazione.

**Registrazioni dirette con data di inizio precedente al 1° maggio 2018**: i clienti Enterprise Azure (EA) diretti sono configurati per il ciclo di fatturazione annuale per i servizi di Azure (esclusi i servizi del Marketplace). Il ciclo di fatturazione è basato sulla data di ricorrenza, ovvero sulla data in cui il contratto è entrato in vigore. Per ricevere la prima fattura per eccedenza per i servizi di Azure, è necessario superare il 150% della soglia dell'impegno monetario.  Una volta che l'utilizzo totale dei servizi supera il 150% di tale soglia, si passa automaticamente al ciclo di fatturazione trimestrale in base alla data di ricorrenza.  Se non si supera il 150% della soglia, per la registrazione continuerà a essere applicato il ciclo di fatturazione annuale e si riceverà la fattura per eccedenza alla fine dell'anno dell'impegno.

**Registrazioni dirette con data di inizio successiva al 1° maggio 2018**: le fatture relative all'utilizzo di Azure e agli addebiti fatturati separatamente per il cliente seguiranno un ciclo di fatturazione mensile.  Gli eventuali addebiti non coperti dall'impegno monetario di Azure saranno dovuti come pagamento dell'eccedenza.  

**Registrazioni indirette con data di inizio precedente al 1° maggio 2018**: i clienti Enterprise Azure (EA) indiretti sono configurati per il ciclo di fatturazione trimestrale.  Sarà il partner di canale a effettuare direttamente la fatturazione al cliente.  

**Registrazioni indirette con data di inizio successiva al 1° maggio 2018**: per qualsiasi contratto indiretto con data di inizio uguale o successiva al 1° maggio 2018 verrà applicata la fatturazione mensile.  

### <a name="increasing-commitment"></a>Aumento dell'impegno

È possibile aumentare l'impegno in qualsiasi momento e l'addebito avverrà per il numero di mesi rimanenti per il periodo di impegno dell'anno in questione. Ad esempio, se si effettua l'iscrizione per una sottoscrizione di rettifica di un anno e si aumenta l'impegno nel corso del sesto mese, verranno fatturati i restanti sei mesi di tale periodo di validità. Le quantità relative all'impegno verranno quindi aggiornate per gli ultimi sei mesi del periodo di impegno per la determinazione di eventuali addebiti per eccedenza.

### <a name="overage"></a>Eccedenza

In caso di eccedenza, viene effettuato l'addebito dell'utilizzo o delle prenotazioni che hanno superato l'impegno durante il periodo di fatturazione. Per visualizzare una suddivisione del calcolo delle quantità in eccedenza per i singoli elementi, fare riferimento al report di riepilogo dell'utilizzo oppure contattare il partner di canale.

Per ogni elemento presente nella fattura, verranno visualizzati gli addebiti totali (importo totale), l'importo dell'impegno usato per coprire gli addebiti (utilizzo dell'impegno) e l'importo degli addebiti in eccedenza dell'impegno (importo netto).  Le imposte applicabili vengono calcolate solo sull'importo netto in eccedenza dell'impegno.

La fatturazione dell'eccedenza è automatizzata.  La tempistica delle notifiche e delle fatture dipende dalla data di fine del periodo di fatturazione del cliente.  La notifica dell'eccedenza in genere viene inviata sette giorni dopo la data di fine fatturazione del cliente, durante i quali egli può accedere al portale, esaminare gli addebiti e aggiornare gli ordini di acquisto generati dal sistema (l'aggiornamento può anche essere eseguito in qualsiasi momento prima del rilascio dell'eccedenza).  Le fatture per eccedenza verranno inviate 7-9 giorni dopo.

### <a name="azure-marketplace"></a>Azure Marketplace

A partire dal ciclo di fatturazione di aprile 2019, i clienti inizieranno a ricevere un'unica fattura per Azure, in quanto tutti gli addebiti relativi ad Azure e Azure Marketplace sono stati combinati e non vengono più inclusi in due fatture distinte. Questa modifica non riguarda i clienti in Australia, Giappone o Singapore. Durante la transizione alla fattura consolidata, si riceverà una fattura parziale per il Marketplace. Questa fattura separata finale mostrerà gli addebiti per il Marketplace precedenti alla data dell'aggiornamento della fatturazione. Gli addebiti per il Marketplace successivi a tale data verranno inclusi nella fattura relativa ad Azure. Dopo il periodo di transizione, tutti gli addebiti per Azure e il Marketplace verranno visualizzati nella fattura consolidata.  

### <a name="purchase-order-numbers"></a>Numeri degli ordini di acquisto

Il valore predefinito dell'ordine di acquisto è un numero di ordine di acquisto generato dal sistema. Gli utenti possono aggiornare tale numero in Enterprise Portal accedendo come amministratore dell'organizzazione e passando alla sezione Report. Nell'angolo superiore destro della finestra è presente una casella per il numero di ordine di acquisto che consentirà all'amministratore dell'organizzazione di modificare tale numero facendo clic sull'icona a forma di matita.

## <a name="adjust-billing-frequency"></a>Modificare la frequenza di fatturazione

La frequenza di fatturazione di un cliente è annuale, trimestrale o mensile. Il ciclo di fatturazione viene determinato quando il cliente firma il contratto. La fatturazione mensile è l'intervallo di fatturazione più breve.

Per modificare un ciclo di fatturazione da annuale a trimestrale per le registrazioni dirette, è necessaria l'approvazione dell'amministratore dell'organizzazione. Per le registrazioni indirette è necessaria l'approvazione di un amministratore partner. La modifica diventa effettiva alla fine del trimestre di fatturazione corrente.

Per cambiare un ciclo di fatturazione annuale o trimestrale in mensile, è necessaria una modifica al contratto.  Qualsiasi modifica al ciclo di fatturazione della registrazione esistente richiede l'approvazione di un amministratore dell'organizzazione o della persona identificata come _contatto per la fatturazione_ nel contratto. È possibile inviare l'approvazione al [Supporto per il portale di Azure EA](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) e quindi selezionare la categoria di problema **Fatturazione**.  La modifica diventa effettiva alla fine del trimestre di fatturazione corrente.

Se viene firmata una modifica M503, è possibile cambiare la frequenza di fatturazione di qualsiasi contratto in mensile, qualunque sia la frequenza originale. 

## <a name="credits-and-adjustments"></a>Crediti e rettifiche

Tutti i crediti o le rettifiche applicate alla registrazione sono visibili all'indirizzo [https://ea.azure.com](https://ea.azure.com) nella sezione **Report**.

Per visualizzare i crediti, seguire questa procedura:

1. Selezionare la sezione **Report**.
1. Fare clic su **Riepilogo utilizzo**.
1. Nell'angolo superiore destro cambiare visualizzazione passando da _M_ a _C_.
1. Estendere il campo di rettifica nella tabella Impegno del servizio di Azure.
1. In questa riga saranno visualizzati i crediti applicati alla registrazione e una breve descrizione, ad esempio: Credito per contratto di servizio.

## <a name="request-an-invoice-copy"></a>Richiedere una copia della fattura

Per richiedere una copia della fattura, contattare il proprio partner.

## <a name="overage-offset-by-customers"></a>Richiesta di compensazione dell'eccedenza da parte dei clienti

Se il cliente ha eccedenze che vuole usare con l'impegno monetario, deve soddisfare i criteri seguenti:

- Il cliente deve aver contratto addebiti per eccedenza che non sono stati pagati e che rientrano entro un anno dalla data di fine del servizio fatturato.
- L'importo dell'impegno monetario disponibile deve coprire il numero totale di addebiti contratti, incluse tutte le fatture passate di Azure non pagate.
- Il periodo di fatturazione di cui si richiede il completamento deve essere completamente chiuso. La fatturazione viene chiusa completamente dopo il quinto giorno di ogni mese.
- Il periodo di fatturazione di cui si richiede la compensazione deve essere completamente chiuso.
- Lo sconto di ACD è basato sul nuovo impegno effettivo meno gli eventuali fondi pianificati per il consumo precedente. Questo requisito si applica solo agli addebiti per eccedenza contratti. Funziona solo per i servizi che utilizzano l'impegno monetario, pertanto non è possibile coprire gli addebiti del Marketplace. Gli addebiti per il Marketplace vengono fatturati separatamente.
- Se un cliente vuole completare una compensazione dell'eccedenza, può aprire una richiesta di supporto. La richiesta può essere aperta anche dal team degli account. Per completare il processo, è necessaria l'approvazione tramite posta elettronica dell'amministratore EA del cliente o del contatto per la fatturazione.

## <a name="view-price-sheet-information"></a>Visualizzare le informazioni dell'elenco prezzi

Gli amministratori dell'organizzazione possono visualizzare l'elenco prezzi associato alla propria registrazione per i servizi di Azure.

Per visualizzare l'elenco prezzi corrente:

1. In Azure EA Portal fare clic su **Report** e quindi su **Elenco prezzi**.
2. Visualizzare l'elenco prezzi oppure fare clic su **Scarica**.

![Esempio che mostra le informazioni dell'elenco prezzi](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Per scaricare un elenco prezzi precedente:

1. In Azure EA Portal fare clic su **Report** e quindi su **Scarica utilizzo**.
2. Scaricare l'elenco prezzi.

![Esempio che mostra dove scaricare un elenco prezzi precedente](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Se si nota una discrepanza nei prezzi, il motivo può essere uno dei seguenti:

I prezzi potrebbero essere cambiati tra la registrazione precedente e la nuova registrazione. Può succedere che i prezzi vengano modificati perché sono contrattuali per la registrazione specifica dalla data di inizio alla data di fine di un contratto. Quando una registrazione viene trasferita in una nuova registrazione, segue i prezzi di un nuovo contratto. I prezzi sono definiti dall'elenco prezzi del cliente, pertanto potrebbero essere più elevati nella nuova registrazione.

In caso di prolungamento di una registrazione, anche i prezzi cambiano. I prezzi cambiano in base alle tariffe con pagamento in base al consumo.

## <a name="request-detailed-usage-information"></a>Richiedere informazioni dettagliate sull'utilizzo

In Azure EA Portal gli amministratori dell'organizzazione possono visualizzare un riepilogo dei dati sull'utilizzo, l'impegno monetario utilizzato e gli addebiti associati all'utilizzo aggiuntivo. Gli addebiti vengono presentati a livello di riepilogo in tutti gli account e le sottoscrizioni.

Per visualizzare informazioni dettagliate sull'utilizzo in account specifici, è possibile scaricare il report relativo ai dettagli sull'utilizzo passando a **Report** > **Scarica utilizzo**. Il report non include imposte applicabili. Potrebbero verificarsi fino a otto ore di latenza tra l'ora di utilizzo e il momento in cui viene visualizzato nel report.

Per le registrazioni indirette, il partner deve abilitare la funzione di markup per poter visualizzare le informazioni relative ai costi.

## <a name="reports"></a>Report

Gli amministratori dell'organizzazione possono visualizzare un riepilogo dei dati di utilizzo, l'impegno monetario utilizzato e gli addebiti associati all'utilizzo aggiuntivo in Enterprise Portal. Gli addebiti verranno presentati a livello di riepilogo per tutti gli account e le sottoscrizioni.

**Report EA**

- Riepilogo e grafici dell'utilizzo
- Report sull'utilizzo dei servizi
- Report sui saldi e sugli addebiti
- Report relativo ai dettagli dell'utilizzo
- Report sugli addebiti del Marketplace
- Elenco prezzi
- Download dei report avanzati
- Formattazione dei report CSV

**Per visualizzare i report e i grafici di riepilogo dell'utilizzo**

1. Nel riquadro di spostamento sinistro di Azure EA Portal fare clic su **Report** e visualizzare la scheda **Riepilogo utilizzo**.
1. Scegliere il periodo di impegno desiderato dal menu a discesa con gli intervalli di date in alto a sinistra.
1. Selezionare il periodo o il mese desiderato sul grafico per visualizzare altri dettagli.
1. Visualizzare il grafico dell'utilizzo mese dopo mese con una suddivisione tra utilizzo effettivo, sovraddebito del servizio, addebiti fatturati separatamente e addebiti del Marketplace.
1. Per il mese selezionato, filtrare in base a reparti, account e sottoscrizioni al di sotto del grafico.
1. Alternare le suddivisioni Charge by Services (Addebito per servizi) e Charge by Hierarchy (Addebito per gerarchia).
1. Visualizzare i servizi di Azure, gli addebiti fatturati separatamente e gli addebiti di Azure Marketplace in dettaglio.

## <a name="service-usage-report"></a>Report sull'utilizzo dei servizi

La pagina del report sull'utilizzo dei servizi consente agli amministratori dell'organizzazione di visualizzare un riepilogo dei dati di utilizzo di un servizio. Sebbene l'utilizzo venga presentato a livello di riepilogo per tutti gli account e le sottoscrizioni, è anche possibile filtrare il report in base agli account o alle sottoscrizioni per visualizzare l'utilizzo dettagliato.

Tenere presente che si potrebbe riscontrare una latenza fino a un massimo di cinque giorni tra la data di utilizzo effettivo e il momento in cui l'utilizzo si riflette nel report.

### <a name="to-view-the-report"></a>Per visualizzare il report:

1. Accedere a Enterprise Portal.
1. Nel riquadro di spostamento sinistro fare clic su **Report**.
1. Fare clic sulla scheda **Riepilogo utilizzo**.
1. Fare clic sull'intervallo di date desiderato.
1. Scegliere gli account o le sottoscrizioni da visualizzare.
1. Cambiare visualizzazione da Charge by Services (Addebito per servizi) o Charge by Hierarchy (Addebito per gerarchia) per visualizzare suddivisioni diverse.
1. Visualizzare i dettagli, tra cui il nome del servizio, l'unità di misura, le unità consumate, la tariffa effettiva e il costo esteso.

## <a name="download-csv-reports"></a>Scaricare i report CSV

La pagina di download dei report mensili consente agli amministratori dell'organizzazione di scaricare diversi report come file con estensione csv, inclusi un report sui saldi e sugli addebiti, un report relativo ai dettagli dell'utilizzo, un report sugli addebiti del Marketplace e un elenco prezzi.

## <a name="to-download-reports"></a>Per scaricare i report:

1. In Azure EA Portal fare clic su **Report**.
1. Sulla barra multifunzione superiore selezionare **Usage Download** (Download utilizzo).
1. Selezionare l'opzione di **download** accanto al report del mese appropriato.



## <a name="csv-report-formatting"></a>Formattazione dei report CSV

I clienti che visualizzano i report CSV di Azure EA Portal in euro potrebbero riscontrare problemi di formattazione per le virgole e i punti.

Ad esempio, potrebbe venire visualizzato quanto segue:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Ore | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Dovrebbe essere visualizzato:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Ore | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Causa radice

Excel importa tutti i campi come testo "Generale" e presuppone che un numero sia separato secondo lo standard matematico "1,000.00".  Pertanto, per una valuta europea che usa il punto (.) come separatore delle migliaia e la virgola come separatore decimale (,), "1.000,00" non verrà visualizzato correttamente. Questa situazione può variare a seconda dell'impostazione della lingua.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Quando si importa il file CSV, seguire questa procedura:

1.    Aprire Excel e scegliere File > Apri.
1.    Verrà visualizzata l'Importazione guidata testo.
1.    In Tipo dati originali selezionare _Delimitato_.  L'impostazione predefinita è _Larghezza fissa_.
1.    Fare clic su **Avanti**.
1.    In Delimitatori selezionare la casella di controllo per Virgola. L'impostazione predefinita è "Tabulazione" (deselezionare la relativa casella di controllo).
1.    Fare clic su **Avanti**.
1.    Scorrere fino alle colonne "ResourceRate" ed "ExtendedCost".
1.    Selezionare la colonna "ResourceRate" (verrà visualizzata evidenziata in nero).
9.    Nella sezione Formato dati per colonna selezionare "Testo" anziché "Generale".  Si noterà che l'intestazione della colonna passa da "Standard" a "Testo".
10. Ripetere i passaggi 8 e 9 per la colonna "ExtendedCost". Selezionare **Fine**.

Se Excel è stato associato all'apertura automatica dei file \*.csv, è invece necessario usare la funzione "Apri" di Excel. Aprire Excel e scegliere File > Apri.

## <a name="balance-and-charge-report"></a>Report sui saldi e sugli addebiti

Il report sui saldi e sugli addebiti offre un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace, rettifiche e addebiti per eccedenza. Tutte le righe nella tabella di riepilogo Impegno del servizio di Azure rimarranno statiche mese dopo mese, mentre i dettagli relativi a tutti gli acquisti e le rettifiche verranno visualizzati nelle sezioni New Purchase Details (Dettagli nuovi acquisti) e Dettagli rettifica.

### <a name="download-the-balance-and-charge-report"></a>Scaricare il report sui saldi e sugli addebiti

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
1. Nel riquadro di spostamento sinistro fare clic su **Report**.
1. Fare clic sulla scheda **Report Download** (Download report).
1. Selezionare il mese appropriato nella colonna _Balance and Charge_ (Saldo e addebito) e fare clic per scaricare il report.

## <a name="usage-detail-report"></a>Report relativo ai dettagli dell'utilizzo

Il report relativo ai dettagli dell'utilizzo offre un riepilogo mensile dei servizi e delle quantità utilizzati da una registrazione, incluse informazioni sui nomi dei contatori, sui tipi di contatori e sulle quantità utilizzate.

### <a name="download-the-usage-detail-report"></a>Scaricare il report relativo ai dettagli dell'utilizzo

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
1. Nel riquadro di spostamento sinistro fare clic su **Report**.
1. Fare clic sulla scheda **Scarica utilizzo**.
1. Selezionare il mese appropriato nella colonna _Dettagli di utilizzo_ e fare clic per scaricare il report.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Addebiti del Marketplace nei report di Azure EA Portal

Altre informazioni sugli addebiti del Marketplace sono disponibili [qui](https://azure.microsoft.com/marketplace/faq/).

Esistono due tipi di addebiti del Marketplace:

- **Basato sull'utilizzo:** prodotti misurati in unità transazionali.  Ad esempio, per le macchine virtuali viene effettuato un addebito orario, mentre per le ricerche nell'API Bing viene effettuato un addebito in base al numero delle ricerche.
- **Tariffa mensile:** viene effettuato l'addebito mensile in base all'utilizzo/accesso.

Per visualizzare i diversi addebiti in Enterprise Portal:

1. **Report di riepilogo dell'utilizzo:** mostra gli addebiti del Marketplace basati sull'utilizzo **e** quelli con tariffa mensile.
1. **Report sugli addebiti del Marketplace:** mostra **solo** gli addebiti del Marketplace basati sull'utilizzo.  Le tariffe una tantum non vengono mostrate.

Tenere presente che Azure Marketplace non è disponibile per le registrazioni MPSA.

## <a name="advanced-report-download"></a>Download dei report avanzati

Per la creazione di report relativi a intervalli di date o ad account specifici, è possibile usare la funzionalità di download dei report avanzati. A partire dal 30 agosto 2016, il formato del file di output passa da xlsx a csv per contenere set di record più grandi.

1. Selezionare **Advanced Report Download** (Download report avanzati).
1. Selezionare **Appropriate Date Range** (Intervallo di date appropriato).
1. Selezionare **Appropriate Accounts** (Account appropriati).
1. Selezionare **Request Usage Data** (Richiedi dati utilizzo).
1. Fare clic sul pulsante **Aggiorna** finché lo stato del report non si imposta su "Download".
1. Scaricare il report.

## <a name="reporting-for-non-enterprise-administrators"></a>Creazione di report per chi non è amministratore dell'organizzazione

Gli amministratori dell'organizzazione possono abilitare l'accesso per la visualizzazione dei costi per gli amministratori di reparto e i proprietari di account per una registrazione. Una volta abilitato, il proprietario di un account può scaricare i report CSV specifici dell'account e delle sottoscrizioni in questione, nonché visualizzare graficamente le informazioni nella sezione dei report di Enterprise Portal.

### <a name="to-enable-access"></a>Per abilitare l'accesso:

 1. Accedere come amministratore dell'organizzazione.
 1. Nel riquadro di spostamento sinistro fare clic su **Gestisci**.
 1. Fare clic sulla scheda **Registrazione**.
 1. Nella sezione Dettagli della registrazione selezionare l'icona a forma di matita accanto a:
    - DA View Charges (Visualizzazione addebiti per amministratori di reparto)
    - AO View Charges (Visualizzazione addebiti per proprietari di account)
 1. Selezionare **Enabled**.
 1. Fare clic su **Save**.

### <a name="to-view-reports"></a>Per visualizzare i report:

1. Accedere ad Azure EA Portal come amministratore del reparto o proprietario dell'account.
1. Nel riquadro di spostamento sinistro fare clic su **Report**.
1. Fare clic sulla scheda **Riepilogo utilizzo** per visualizzare graficamente le informazioni relative all'account e alla sottoscrizione.
1. Fare clic su **Usage Download** (Download utilizzo) per visualizzare i report CSV.

Gli amministratori di reparto e i proprietari di account non possono visualizzare gli addebiti quando usano la funzione _Advanced Report Download_ (Download report avanzati). I costi vengono visualizzati come $ 0.

La visualizzazione addebiti per proprietari di account si estende ai proprietari degli account e a tutti gli utenti che dispongono di autorizzazioni per le sottoscrizioni associate. Se si è un cliente indiretto, le funzionalità relative ai costi devono essere abilitate dal partner di canale.

## <a name="move-usage-data-to-another-enrollment"></a>Spostare i dati di utilizzo in un'altra registrazione

I dati di utilizzo vengono spostati solo quando un trasferimento viene retrodatato. Sono disponibili due opzioni per spostare i dati di utilizzo da una registrazione a un'altra:

- Trasferimenti di account da una registrazione a un'altra
- Trasferimento di registrazione da una registrazione a un'altra

Per entrambe le opzioni, è necessario inviare una [richiesta di supporto](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) al team di supporto EA per assistenza. 

## <a name="azure-ea-pricing-overview"></a>Panoramica sui prezzi EA di Azure

Questo documento fornisce informazioni dettagliate sul modo in cui viene calcolato l'utilizzo e risponde a molte delle domande più frequenti sui prezzi dei vari servizi di Azure nei programmi Enterprise in cui i calcoli sono più complessi.

### <a name="price-protection"></a>Protezione dei prezzi

Per i clienti e i partner di canale (nel caso dei canali indiretti Microsoft) viene garantita l'applicazione di prezzi pari o inferiori a quelli indicati nell'Elenco Prezzi Cliente (CPS) o al prezzo in vigore alla data di inizio validità dell'acquisto di Azure. Questo prezzo viene indicato come prezzo di base. Per i servizi introdotti dopo il suddetto acquisto, si tratta del prezzo in vigore al livello di sconto applicabile quando il servizio è stato introdotto per la prima volta. Questa protezione dei prezzi si applica per l'intera durata del periodo di impegno, che può essere di uno o tre anni a seconda del programma Enterprise.

### <a name="price-changes"></a>Modifiche ai prezzi

Microsoft può decidere di non applicare il prezzo corrente del programma Enterprise per i singoli servizi di Azure durante il periodo di durata di una registrazione.  Tali tariffe ridotte verranno estese ai clienti attuali mentre è in vigore il prezzo più basso.  Se queste tariffe vengono aumentate successivamente, il prezzo della registrazione per un servizio non potrà superare il tetto di protezione dei prezzi del cliente come specificato sopra, ma potrà aumentare rispetto al precedente prezzo ribassato.  In entrambi i casi, Microsoft informerà i clienti e i partner di canale indiretti delle imminenti variazioni di prezzo inviando un messaggio di posta elettronica agli amministratori dell'organizzazione e agli amministratori partner associati alla registrazione.

### <a name="current-effective-pricing"></a>Prezzi attualmente in vigore

Il cliente e i partner di canale possono visualizzare i prezzi correnti per una registrazione accedendo ad [Azure Enterprise Portal](https://ea.azure.com/) e passando alla pagina dell'elenco prezzi relativo alla registrazione in questione.  Se si acquista Azure indirettamente tramite uno dei partner di canale Microsoft, sarà necessario richiedere gli aggiornamenti relativi ai prezzi al partner di canale se quest'ultimo non ha abilitato la visualizzazione degli aumenti dei prezzi per la registrazione.

### <a name="introduction-of-new-azure-services"></a>Introduzione ai nuovi servizi di Azure

Azure viene migliorato costantemente e vengono aggiunti periodicamente nuovi servizi con prezzi distinti rispetto ai servizi esistenti.  Alcuni servizi in anteprima sono disponibili automaticamente, mentre altri richiedono un'azione esplicita del cliente tramite [Azure Account Portal](https://account.windowsazure.com/PreviewFeatures). Tenere inoltre presente che, man mano che i servizi passano dalla fase di anteprima alla disponibilità a livello generale, i prezzi possono aumentare a seguito dell'applicazione dei contratti di servizio per l'affidabilità e le prestazioni complete. Alcuni servizi, infine, vengono forniti con prezzi promozionali in vigore al momento della prima introduzione che possono aumentare in futuro. Gli aumenti che possono verificarsi nel passaggio dalla fase di anteprima o di applicazione dei prezzi promozionali alla disponibilità a livello generale non sono soggetti alla normale protezione dei prezzi di base e verranno applicati per l'utilizzo di questi servizi in futuro. I clienti possono evitare gli addebiti correlati a tali servizi scegliendo di non usare il nuovo servizio.

### <a name="introduction-of-regional-pricing"></a>Introduzione ai prezzi a livello di area

Se da un lato vengono introdotti nuovi servizi, dall'altro i servizi cambiano periodicamente passando da un'unica base globale a un modello più a livello di area, man mano che si estende il supporto per tali servizi nelle varie aree geografiche. Quando viene introdotta per la prima volta la regionalizzazione di un servizio, per le nuove aree viene applicata la protezione dei prezzi rispetto al precedente prezzo globale in vigore per la registrazione. Le aree aggiuntive introdotte in un secondo momento per lo stesso servizio vengono tuttavia considerate come nuovi servizi, offerti con tariffe separate indipendenti dalla protezione dei prezzi di base.

### <a name="enterprise-msdn-devtest"></a>Sviluppo/test MSDN con contratto Enterprise

Gli amministratori dell'organizzazione possono consentire ai proprietari di account di creare sottoscrizioni basate sull'offerta Sviluppo/test MSDN EA. Per un corretto funzionamento, è necessario che il proprietario dell'account configuri le sottoscrizioni relative a tale offerta necessarie per i sottoscrittori MSDN sottostanti. In questo modo i sottoscrittori MSDN attivi possono eseguire carichi di lavoro di sviluppo e test in Azure a tariffe speciali per le attività di sviluppo e test. Per altre informazioni, vedere [Sviluppo/test MSDN EA](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Linee guida per il calcolo dell'utilizzo dei programmi Enterprise

Per informazioni di base pubbliche su prezzi, unità di misura, domande frequenti e linee guida per la creazione di report relativi all'utilizzo per i singoli servizi, fare riferimento a [Servizi di Azure](https://azure.microsoft.com/services/) e [Prezzi di Azure](https://azure.microsoft.com/pricing/). Inoltre, per il calcolo dell'utilizzo dei servizi, è necessario attenersi alle linee guida seguenti dei programmi Enterprise.

### <a name="enterprise-program-units-of-measure"></a>Unità di misura dei programmi Enterprise

Le unità di misura per i programmi Enterprise sono spesso diverse da quelle visualizzate negli altri programmi Microsoft, ad esempio nel programma Contratto di Sottoscrizione Microsoft Online. Ciò significa che, per diversi servizi, l'unità di misura viene aggregata per fornire i prezzi normalizzati. L'unità di misura mostrata nella visualizzazione Riepilogo utilizzo di Enterprise Portal è sempre la misura Enterprise. Un elenco completo di unità di misura e conversioni correnti per ogni servizio è disponibile nel documento [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Regole di arrotondamento

Per l'arrotondamento effettuato all'interno di Enterprise Portal viene usata la logica di arrotondamento gaussiano o del banchiere dello standard IEEE. Pertanto, i valori con cifre non intere vengono arrotondati alla cifra pari più vicina e quindi le funzioni di arrotondamento per eccesso più comuni arrotondano sempre le cifre non intere per eccesso alla successiva cifra più alta. Questo metodo effettivamente fornisce una somma totale più precisa per il gruppo rispetto alla logica standard di Excel.

Quando la prima cifra eliminata è il 5 e non sono presenti cifre che seguono oppure le cifre che seguono sono zeri, diventa pari la cifra precedente, ovvero si arrotonda alla cifra pari più vicina. Ad esempio, 2,315 e 2,325 corrispondono entrambi a 2,32 se arrotondati al centesimo più vicino.

Come riferimento, quando si usa Excel per modellare le regole di arrotondamento e conversione applicate all'interno di Enterprise Portal, è necessario usare le formule MROUND come illustrato di seguito.

| Scenario | Formula della logica del banchiere |
| --- | --- |
| Arrotondamento dell'utilizzo | =MROUND({_origine_}, 0,0002) |
| Arrotondamento dei prezzi (due decimali) | =MROUND({_origine_}, 0,02) |
| Arrotondamento dei prezzi (zero decimali) | =MROUND({_origine_}, 2) |

**Tabella** **2** **- Conversione delle ore del servizio cloud e della macchina virtuale**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Conversione tra il file ottenuto tramite Scarica utilizzo e la visualizzazione del portale Riepilogo utilizzo

I dati di utilizzo base delle risorse vengono riportati con un massimo di sei posizioni decimali, come si può notare nel report con i dati ottenuti tramite Scarica utilizzo. Azure Enterprise Portal tuttavia arrotonda l'utilizzo a quattro posizioni decimali per le unità di impegno e tronca a zero decimali per le unità di eccedenza. L'utilizzo base viene prima arrotondato a quattro cifre prima della conversione all'unità di misura Enterprise e le unità Enterprise risultanti vengono di nuovo arrotondate a quattro cifre. Le effettive ore utilizzate prima della conversione vengono mostrate solo nel report ottenuto tramite Scarica utilizzo e non all'interno dell'interfaccia utente vera e propria.

Si supponga, ad esempio, che nel report che si ottiene tramite Scarica utilizzo vengano riportate 694,533404 ore SQL Server effettive. Tali unità vengono quindi convertite a 6,94533404 unità di 100 ore di calcolo e infine arrotondate a 6,9453 per la visualizzazione in Enterprise Portal.

Queste unità vengono quindi semplicemente moltiplicate per il prezzo unitario dell'impegno e il risultato viene troncato a due decimali per determinare l'importo di fatturazione totale. Per lo yen giapponese (JPY) e il won coreano (KRW), l'importo totale viene arrotondato a zero decimali.

Per questo stesso esempio in eccedenza, le unità fatturabili vengono troncate a sei e quindi moltiplicate per il prezzo unitario di eccedenza per determinare l'importo di fatturazione totale. Per la fatturazione del provider di servizi gestiti (MSP), tutto l'utilizzo associato a un reparto contrassegnato come MSP viene troncato a zero decimali dopo la conversione all'unità di misura EA, come avviene per la creazione di report per l'eccedenza. Di conseguenza, la somma di questo utilizzo potrebbe essere inferiore alla somma totale di tutti i dati di utilizzo riportati nell'interfaccia utente, a seconda che l'MSP sia ancora entro il saldo dell'impegno monetario o sia già in eccedenza.

### <a name="graduated-pricing"></a>Prezzi progressivi

I prezzi del programma Enterprise attualmente non includono i prezzi progressivi, ovvero i prezzi per unità che diminuiscono man mano che l'utilizzo aumenta. Quando si passa da un programma Contratto di Sottoscrizione Microsoft Online con prezzi progressivi a un programma Enterprise, i prezzi vengono impostati in modo proporzionale al livello di base per il servizio in questione, dopo la rettifica secondo gli eventuali sconti applicabili per il programma Enterprise.

### <a name="partial-hour-billing"></a>Fatturazione di ore parziali

Tutto l'utilizzo viene addebitato in base ai minuti convertiti in ore parziali anziché come incrementi di ore intere.  Le tariffe Enterprise elencate come orarie vengono applicate semplicemente alle ore totali, incluse le eventuali ore parziali.

### <a name="average-daily-consumption"></a>Utilizzo giornaliero medio

Quando a un servizio viene applicato un prezzo su base mensile ma l'utilizzo viene riportato su base giornaliera, tale utilizzo viene valutato una volta al giorno, diviso per 31 e sommato per il numero di giorni inclusi nel mese di fatturazione. In questo modo, si ottengono tariffe che non sono mai più alte del previsto in alcun mese e che sono leggermente inferiori per i mesi con meno di 31 giorni.

### <a name="compute-hours-conversion"></a>Conversione delle ore di calcolo

In precedenza, per l'utilizzo di macchine virtuali A0, A2, A3 e A4 Basic e Standard e per i servizi cloud il costo veniva calcolato sotto forma di frazioni (per A0) o multipli (per A2, A3 e A4) dei minuti del contatore delle macchine virtuali A1. Visto che questo modello ha causato confusione per i clienti, è in corso l'implementazione di una modifica in modo da assegnare un utilizzo al minuto a contatori dedicati per A0, A2, A3 e A4.

La nuova misurazione diventerà effettiva tra il 27 gennaio e il 28 gennaio 2016. Nel download in formato CSV per la distribuzione durante questo periodo di transizione, si noteranno due voci: una per l'utilizzo calcolato con il contatore A1 e un'altra per l'utilizzo calcolato con il nuovo contatore dedicato che corrisponde alla dimensione della distribuzione.

| **Dimensione della distribuzione** | **Utilizzo calcolato come multiplo di A1 fino al 26 gennaio 2016** | **Utilizzo calcolato con un contatore dedicato a partire dal 27 gennaio 2016** |
| --- | --- | --- |
| A0 | 0,25 dell'ora A1 | 1 ora A0 |
| A2 | 2 ore A1 | 1 ora A2 |
| A3 | 4 ore A1 | 1 ora A3 |
| A4. | 8 ore A1 | 1 ora A4 |

### <a name="regions"></a>Regioni

Per i servizi in cui la zona e l'area influiscono sui prezzi, nella tabella seguente viene illustrata la corrispondenza per le aree geografiche e le aree:

| Area geografica | Aree di trasferimento dati | Aree CDN |
| --- | --- | --- |
| Zona 1 | Europa settentrionale <br> Europa occidentale <br> Stati Uniti occidentali <br> Stati Uniti orientali <br> Stati Uniti centro-settentrionali <br> Stati Uniti centro-meridionali <br> Stati Uniti orientali 2 <br> Stati Uniti centrali | America del Nord <br> Europa |
| Zona 2 | Asia Pacifico orientale <br> Asia Pacifico sud-orientale <br> Giappone orientale <br> Giappone occidentale <br> Australia orientale <br> Australia sud-orientale | Asia/Pacifico <br> Giappone <br> America Latina <br> Medio Oriente/Africa <br> Australia orientale <br> Australia sud-orientale |
| Zona 3 | Brasile meridionale |   |

**Tabella** **4** **- Aree di trasferimento dati**

Non sono previsti addebiti per i dati in uscita tra i servizi (ad esempio, O365 e Azure) ospitati all'interno dello stesso data center.

### <a name="monetary-commitment-and-unbilled-usage"></a>Impegno monetario e utilizzo non fatturato

L'impegno monetario di Azure è un importo pagato in anticipo per i servizi di Azure. L'impegno monetario viene utilizzato man mano che si usano i servizi. I servizi proprietari di Azure usano l'impegno monetario. Esistono tuttavia eccezioni per gli addebiti fatturati separatamente e i servizi del Marketplace.

### <a name="charges-billed-separately"></a>Addebiti fatturati separatamente

Alcuni prodotti e servizi forniti da origini di terze parti non utilizzano l'impegno monetario di Azure. Questi elementi verranno anzi fatturati separatamente come parte della fattura per eccedenza del ciclo di fatturazione standard.

Tutti gli addebiti per Azure e il Marketplace sono stati combinati in un'unica fattura allineata al ciclo di fatturazione della registrazione. Ciò non riguarda i clienti in Australia, Giappone o Singapore.

La fattura consolidata indicherà innanzitutto l'utilizzo di Azure, seguito da eventuali addebiti per il Marketplace. I clienti in Australia, Giappone o Singapore continueranno a visualizzare gli addebiti per il Marketplace in una fattura separata.

Se alla fine del ciclo di fatturazione standard non risulta alcun utilizzo in eccedenza, gli addebiti fatturati separatamente verranno fatturati a parte (si applica ai clienti in Australia, Giappone e Singapore).

**I servizi fatturati separatamente includono:**

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

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace per i clienti EA

Per i clienti diretti, gli addebiti del Marketplace sono visibili in Azure EA Portal. Gli acquisti e l'utilizzo del Marketplace verranno addebitati al di fuori dell'impegno monetario e fatturati su base trimestrale o mensile e in via posticipata.

I clienti indiretti possono trovare le proprie sottoscrizioni di Azure Marketplace nella pagina "Gestisci sottoscrizioni" di Azure EA Portal, ma i prezzi saranno nascosti. I clienti devono contattare il proprio LSP per informazioni sugli addebiti di Marketplace.

Per i nuovi acquisti del Marketplace con ricorrenza mensile o annuale verrà addebitato l'intero importo nel periodo in cui gli elementi del Marketplace sono stati acquistati. Per questi elementi verrà eseguito il rinnovo automatico nel periodo seguente nello stesso giorno dell'acquisto originario.

Ciò non influirà sui costi ricorrenti attuali del Marketplace. Gli addebiti con ricorrenza mensile continueranno a essere rinnovati il primo giorno di ogni mese di calendario, mentre gli addebiti annuali verranno rinnovati nel giorno in cui ricorre la data di acquisto.

Alcuni servizi di rivenditori di terze parti disponibili in Azure Marketplace ora utilizzano il saldo dell'impegno monetario del Contratto Enterprise (EA). In precedenza questi servizi venivano addebitati al di fuori dell'impegno monetario EA e venivano fatturati separatamente. L'impegno monetario EA per questi servizi nel Marketplace contribuisce alla semplificazione della gestione degli acquisti e dei pagamenti dei clienti. Per un elenco completo dei servizi che ora utilizzano l'impegno monetario, vedere il [sito Web Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Partner

Gli LSP possono scaricare un listino prezzi specifico del Marketplace passando all'elenco prezzi in Azure EA Portal e facendo clic sul collegamento **Marketplace price list** (Listino prezzi del Marketplace) nell'angolo superiore destro. Nel listino prezzi del Marketplace si rifletteranno tutti i servizi disponibili e i relativi prezzi.

**Per scaricare il listino prezzi, seguire questa procedura:**

1. Passare a Report > Elenco prezzi.
1. Nell'angolo superiore destro trovare il collegamento al listino prezzi di Azure Marketplace sotto il proprio nome utente.
1. Fare clic con il pulsante destro del mouse sul collegamento e scegliere **Salva oggetto con nome**.
1. Nella finestra Salva modificare il titolo del documento specificando _AzureMarketplacePricelist.zip_; di conseguenza, il tipo di file cambierà da XLSX a ZIP.
1. Al termine del download, si disporrà di un file ZIP con i listini prezzi specifici del paese.
1. Gli LSP devono fare riferimento al file del singolo paese per individuare i prezzi specifici del paese in questione. Gli LSP devono usare la scheda "Notifiche" per visualizzare gli SKU nuovi per il Marketplace, nonché gli SKU rimossi.
1. Le variazioni di prezzo saranno poco frequenti, ma quando si verificano, gli LSP verranno informati degli aumenti dei prezzi e delle modifiche del tasso di cambio 30 giorni prima tramite posta elettronica.
1. L'LSP riceverà una fattura per registrazione, per ISV, per trimestre.

### <a name="enabling-marketplace-purchases"></a>Abilitazione degli acquisti nel Marketplace

Gli amministratori dell'organizzazione hanno la possibilità di "disabilitare" o "abilitare" gli acquisti nel Marketplace per tutte le sottoscrizioni di Azure nella registrazione in questione. Se l'amministratore dell'organizzazione disabilita gli acquisti e sono presenti sottoscrizioni di Azure che hanno già sottoscrizioni del Marketplace, tali sottoscrizioni del Marketplace non verranno annullate o non saranno interessate.

Sebbene i clienti possano convertire a EA le sottoscrizioni di Azure dirette associandole alla loro registrazione in Azure EA Portal, questa azione non converte automaticamente le sottoscrizioni figlio del Marketplace.

**Per abilitare gli acquisti nel Marketplace:**

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
1. Passare a _Gestisci_.
1. In _Dettagli registrazione_ fare clic sull'icona a forma di matita accanto alla voce _Azure Marketplace_.
1. Selezionare/deselezionare _Abilitato/Disabilitato_ o _Solo SKU di tipo Gratuito/BYOL\*_ secondo i casi.
1. Fare clic su **Save**.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Addebiti del Marketplace nei report di Azure EA Portal

Altre informazioni sugli addebiti del Marketplace sono disponibili [qui](https://azure.microsoft.com/marketplace/faq/).

Esistono due tipi di addebiti del Marketplace:

- **Basato sull'utilizzo**: i prodotti vengono misurati in unità transazionali.  Ad esempio, per le macchine virtuali viene effettuato un addebito orario, mentre per le ricerche nell'API Bing viene effettuato un addebito in base al numero delle ricerche.
- **Non-Usage based** (Non basato sull'utilizzo): addebito una tantum o tariffa mensile ricorrente indipendente dall'utilizzo.

Sia gli addebiti basati sull'utilizzo che quelli non basati sull'utilizzo verranno acquisiti nel report sugli addebiti del Marketplace.

Tenere presente che Azure Marketplace non è disponibile per le registrazioni MPSA.

\*L'opzione relativa ai tipi BYOL (Bring Your Own License) e Gratuito limita l'acquisto e l'acquisizione degli SKU di Azure Marketplace ai soli SKU di tipo BYOL e Gratuito.

### <a name="services-billed-hourly-for-ea"></a>Servizi con addebito orario per EA

Ad Application Delivery Network e web application firewall viene applicato un addebito orario per EA, anziché la tariffa mensile applicata nel programma di Microsoft Online Services.

### <a name="remote-app"></a>App remota

I clienti EA pagano per l'app remota in base al loro livello di prezzo EA e non ricevono altri addebiti. Il prezzo di vendita standard include 40 ore iniziali, mentre il prezzo di vendita senza limiti copre 80 ore iniziali. L'app remota smette di calcolare l'utilizzo oltre le 80 ore.

## <a name="azure-marketplace-faq"></a>Domande frequenti su Azure Marketplace

In questa sezione relativa alle domande frequenti vengono illustrati gli aggiornamenti riguardanti l'applicabilità dell'impegno monetario ad alcuni servizi pubblicati di terze parti in Azure Marketplace.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>Quali sono i cambiamenti in corso per i servizi del Marketplace e l'impegno monetario di Azure?

A parte le istanze di macchina virtuale riservate di Azure, i clienti ricevono una fattura separata per tutti i servizi che acquistano da Azure Marketplace. È in corso l'espansione dell'uso dell'impegno monetario di Azure, in modo da includere ora alcuni dei servizi di Azure Marketplace pubblicati di terze parti che vengono acquistati con maggiore frequenza dai clienti Microsoft.

### <a name="why-are-we-making-this-change"></a>Perché è in corso questa modifica?

I clienti cercano continuamente altri modi per sfruttare il pagamento anticipato che hanno effettuato sotto forma di impegno monetario di Azure.  Espandendo l'impegno monetario a tali servizi, rispondiamo a una richiesta avanzata frequentemente dai clienti e favoriamo una grossa fetta dei nostri clienti di Azure Marketplace.

### <a name="what-is-the-customer-benefit"></a>Qual è il vantaggio per i clienti?

I clienti ottengono un'esperienza di fatturazione più semplice e possono essere certi di spendere il loro impegno monetario di Azure.  L'aggiunta di questo vantaggio all'impegno monetario prepagato e alle istanze riservate che usano tale impegno conferisce ancora più valore all'impegno monetario di Azure.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Quali servizi verranno detratti dall'impegno monetario di Azure e come verrà informato il cliente?

Durante l'esperienza di acquisto in Azure Marketplace, i servizi che useranno l'impegno monetario verranno distinti con una dichiarazione di non responsabilità. Gli editori attualmente supportati includono alcuni servizi pubblicati da Red Hat, SUSE, Autodesk e Oracle. I servizi con convenzioni di denominazione simili ma pubblicati da altre parti non identificate come spiegato sopra non verranno detratti dall'impegno monetario. Alla fine di queste domande frequenti è disponibile un elenco completo.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>Cosa accade se il cliente esaurisce l'impegno monetario?

Per i clienti che hanno utilizzato tutto il loro impegno monetario e ora sono in condizione di eccedenza, gli addebiti relativi a tali servizi verranno visualizzati nella successiva fattura per eccedenza insieme a qualsiasi altro servizio a consumo.  Si tratta di una novità, perché in precedenza questi addebiti sarebbero stati fatturati in una fattura a parte con altre offerte di Azure Marketplace.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Perché l'impegno monetario di Azure non viene abilitato per tutti gli acquisti nel Marketplace?

Stiamo lavorando spesso per offrire un'esperienza cliente ottimale relativamente all'impegno monetario di Azure. Questa modifica riguarderà un numero elevato di clienti e una parte significativa della spesa totale in Azure Marketplace. In futuro potrebbero venire aggiunti altri servizi.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Quali saranno le differenze per la registrazione e i partner indiretti?

Non ci sono variazioni per i partner o i clienti con registrazione indiretta. Questi servizi sono soggetti alle stesse possibilità di ricarico dei partner a cui sono sottoposti altri servizi a consumo. L'unico cambiamento sarà rappresentato dalla fattura in cui compaiono e dal pagamento degli addebiti dall'impegno monetario.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Elenco dei servizi che verranno detratti dall'impegno monetario di Azure

Alcune offerte specifiche di Azure Marketplace possono usare i fondi dell'impegno monetario. Per un elenco completo dei prodotti che partecipano al programma, vedere la pagina relativa all'[impegno monetario di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).

## <a name="additional-information"></a>Informazioni aggiuntive

Per informazioni più dettagliate, vedere queste altre fonti di informazioni. Tali file vengono aggiornati due volte al mese, il 6 e il 20 di ogni mese. Di seguito sono riportati i dettagli relativi a ciascun file:

| Titolo appendice | DESCRIZIONE | Convenzione di denominazione URL |
| --- | --- | --- |
| [**Friendly Service Names**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Fornisce un elenco di tutti i servizi attivi con la categoria e il nome descrittivo del servizio, il nome dell'impegno e il numero di parte, il nome dell'utilizzo e il numero di parte, le unità di misura e i fattori di conversione tra l'utilizzo riportato e l'utilizzo di Enterprise Portal visualizzato. | Friendly\_Service\_Names.xlsx |
| [**Service Download Fields**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Questo foglio di calcolo fornisce un elenco di tutte le possibili combinazioni dei campi relativi ai servizi nel report di download dell'utilizzo. | Service\_Download\_Fields.xlsx |

**Tabella** **5** **- Altre fonti di informazioni**

## <a name="power-bi-reporting"></a>Report di Power BI

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro è ora disponibile per i clienti EA. Con Power BI Pro è possibile generare e condividere report per gestire i dati dei costi in modo efficace, grazie a funzionalità di collaborazione e aggiornamento dati aggiuntive. Power BI Pro offre limiti più elevati per la capacità dati e il flusso dei dati e presto per i clienti Azure Enterprise saranno disponibili nuove e interessanti funzionalità per la gestione dei costi.

Gli attuali utenti del piano gratuito di Power BI che usano il pacchetto di contenuto Informazioni dettagliate sul consumo di Microsoft Azure sono idonei per ottenere una versione di valutazione gratuita di 60 giorni di Power BI Pro. Se si vuole continuare a usare Power BI Pro dopo il periodo di valutazione gratuita, è necessario aggiungere una licenza.

Per iscriversi per la versione di valutazione gratuita, passare all'icona a forma di ingranaggio e selezionare **Gestisci archivio personale**. Selezionare quindi **Prova gratuitamente la versione Pro** a destra. Per altre informazioni sulla versione di valutazione gratuita di Power BI Pro, vedere [Iscrizione self-service per Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial).

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Termini della versione di valutazione di Power BI Pro per Microsoft Azure EA

- **Utilizzo generico**: l'offerta per la valutazione del pacchetto di contenuto di Power BI Pro esteso per "Microsoft Azure Enterprise" ("Offerta") è disponibile per gli attuali utenti idonei durante il periodo di validità dell'Offerta; essi avranno così la possibilità di accedere ad alcune informazioni dettagliate correlate al loro utilizzo di Microsoft Azure mediante l'uso di uno specifico pacchetto di contenuto di Power BI.
- **Idoneità**: gli utenti con un Contratto Enterprise (EA) possono aderire all'Offerta se hanno una funzione correlata alla gestione della fatturazione, dei servizi oppure dei servizi e/o dei costi di Microsoft Azure nell'ambito della loro organizzazione.
- **Esclusioni**:
  - gli utenti che già partecipano alla valutazione di Power BI Pro esteso continueranno a essere idonei per l'offerta preesistente e non possono aderire all'offerta relativa alla valutazione di Power BI Pro per Azure EA.
  - Gli utenti che aderiscono all'offerta possono solo usare Power BI Pro con il pacchetto di contenuto per Microsoft Azure Enterprise. Qualsiasi altro uso di Power BI Pro è vietato.
  - Periodo di validità: l'Offerta inizierà il 1° giugno 2017 e terminerà il 31 maggio 2018.  L'adesione può avere luogo in qualsiasi momento nell'arco dei 12 mesi, anche se l'offerta terminerà il 31 maggio 2018 per tutti gli utenti, indipendentemente dal momento in cui è stata accettata.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Per accedere al pacchetto di contenuto Informazioni dettagliate sul consumo di Microsoft Azure:

1. Passare a [Informazioni dettagliate sul consumo di Microsoft Azure](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Fare clic su **Scarica adesso**.
1. Specificare il numero di registrazione e il numero di mesi. Fare clic su **Avanti**.
1. Specificare la chiave di accesso API per connettersi. La chiave relativa alla propria registrazione è disponibile in [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Fare clic su **Sign In**.
1. Il processo di importazione inizierà automaticamente. Al termine, nel riquadro di spostamento verranno visualizzati un nuovo dashboard, un nuovo report e un nuovo modello. Fare clic sul dashboard per visualizzare i dati importati.

Per altre informazioni su come generare la chiave API per la propria registrazione, vedere il file della Guida sui report API in [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Per altre informazioni sul nuovo pacchetto di contenuto, scaricare il documento [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Per accedere al pacchetto di contenuto EA Power BI legacy:

 1. Passare al [sito Web Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Accedere con un account aziendale o dell'istituto di istruzione valido.
    - L'account aziendale o dell'istituto di istruzione può essere uguale o diverso da quello usato per accedere alla registrazione tramite Azure EA Portal.
 1. Nel dashboard dei servizi selezionare **Microsoft Azure Enterprise** e fare clic su **Connetti**.
 1. Nella schermata di connessione ad Azure Enterprise scegliere:
    - Azure Environment URL (URL dell'ambiente di Azure): [https://ea.azure.com](https://ea.azure.com/).
    - Numero di mesi: scegliere un valore compreso tra 1 e 36.
    - Numero di registrazione: immettere il numero di registrazione.
    - Fare clic su **Avanti**.
 1. Nella casella Chiave di autenticazione immettere la chiave API. È possibile ottenere tale chiave qui in Azure Enterprise Portal, nella scheda "Scarica utilizzo" sopra descritta, facendo clic su "API Access Key" (Chiave di accesso API).
    - Copiare e incollare la chiave nella casella per "Chiave account".
 1. Il caricamento dei dati in Power BI richiederà all'incirca da 5 a 30 minuti, a seconda delle dimensioni dei set di dati.

La creazione di report di Power BI è disponibile per i partner EA diretti e per i clienti indiretti che hanno accesso alla visualizzazione delle informazioni di fatturazione.

## <a name="report-faq"></a>Domande frequenti sui report

In questa sezione dell'articolo sono contenute le risposte alle domande comuni sull'interpretazione dei report.

### <a name="why-is-my-cost-showing-as-0"></a>Perché il costo viene visualizzato come $ 0?

**Registrazione diretta** Se si è un proprietario di account o un amministratore di reparto, contattare l'amministratore EA per abilitare la funzionalità relativa ai prezzi:

1. Nel riquadro di spostamento sinistro fare clic su **Gestisci**.
1. Fare clic sulla matita blu accanto a DA (department administrator) view charges (Visualizzazione addebiti per amministratori di reparto).
1. Selezionare **Abilitato** e salvare.
1. Fare clic sulla matita blu accanto ad AO (account owner) view charges (Visualizzazione addebiti per proprietari di account).
1. Selezionare **Abilitato** e salvare.

Questa azione darà ai proprietari di account e agli amministratori di reparto l'accesso alle informazioni relative ai costi/prezzi nei report sull'utilizzo.

**Registrazione indiretta** Assicurarsi che il partner abbia abilitato la funzionalità relativa ai prezzi. L'abilitazione può essere effettuata solo dal partner e, dopo l'attivazione della funzionalità, sarà possibile visualizzare i costi e i prezzi per la propria registrazione come un amministratore EA.

Se si vuole abilitare la funzionalità di visualizzazione degli addebiti per i proprietari di account e l'amministratore del reparto, seguire la procedura illustrata in precedenza in **Registrazione diretta**.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Nel report relativo ai dettagli dell'utilizzo non sono presenti informazioni sugli SKU

Il report relativo ai dettagli dell'utilizzo non contiene informazioni sugli SKU, tuttavia nel report sarà possibile visualizzare le informazioni sul servizio utilizzato. È quindi possibile scaricare il report dell'elenco prezzi per ottenere le informazioni relative agli SKU.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>L'importo totale nel Marketplace non corrisponde nel report di riepilogo dell'utilizzo e nel report CSV

Il report sugli addebiti del Marketplace mostra solo gli addebiti del Marketplace basati sull'utilizzo. Le tariffe una tantum non vengono mostrate. È possibile fare riferimento alla pagina di riepilogo dell'utilizzo per avere i dati di utilizzo più aggiornati sia per gli addebiti basati sull'utilizzo sia per le tariffe una tantum.

### <a name="there-is-no-information-on-my-api-report"></a>Il report API non contiene informazioni

Le chiavi API scadono ogni sei mesi. In caso di problemi, generare una nuova chiave API. È inoltre importante chiedere all'amministratore EA di generare le nuove chiavi API e seguire i passaggi descritti nelle Domande frequenti sui report API.

### <a name="my-power-bi-report-isnt-working"></a>Il report di Power BI non funziona

In caso di problemi relativi a Power BI, registrare un ticket tecnico presso il team di Power BI all'indirizzo [https://support.powerbi.com](https://support.powerbi.com), in modo da poter ricevere assistenza da tale team.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>I tag delle risorse non vengono visualizzati nei report

I tag delle risorse vengono gestiti nel portale di Azure. È possibile contattare il team responsabile della sottoscrizione di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). Per informazioni su come inviare una richiesta di supporto, seguire la procedura a cui si accede tramite [questo collegamento](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Perché la tariffa per le risorse cambia ogni giorno?

La tariffa per le risorse nel report di utilizzo dettagliato è un valore calcolato e rappresenta la tariffa mensile media addebitata per un servizio. Questa tariffa viene calcolata usando la media dell'impegno mensile e degli addebiti di eccedenza mensili per un'unità di servizio. La parte di utilizzo addebitata a fronte dell'impegno e delle tariffe di eccedenza cambierà nel giorno di chiusura del mese. Di conseguenza, anche la tariffa per le risorse cambierà nel corso del mese. La tariffa si blocca il quinto giorno successivo alla fine del mese.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossario dei processi per il calcolo della tariffa per le risorse

**Unità RAW totali:** quantità utilizza all'interno del report di utilizzo dettagliato.
**Risorsa MOCP per unità:** il sistema di utilizzo upstream calcola gli utilizzi relativi a ogni servizio in unità diverse. (Preimpostato) **Utilizzo per unità:** unità di misura EA. (Preimpostato) **Prezzo:** prezzo unitario da Azure EA Portal.
**Costo totale:** costo esteso derivante dal report di utilizzo dettagliato o utilizzo dell'impegno + eccedenza da Azure EA Portal.


### <a name="charges-calculation"></a>Calcolo degli addebiti

**Conversione a Risorsa MOCP per unità** = ROUND(Unità RAW totali * Risorsa MOCP per unità,4) **Conversione a unità** = Unità dopo la conversione a Risorsa MOCP per unità / Utilizzo per unità **Costo totale** = Unità * Prezzo

### <a name="download-usage-calculation-logic"></a>Logica di calcolo per Scarica utilizzo

**Tariffa per le risorse** = Costo totale /(Unità RAW totali / Risorsa MOCP per unità)

La tariffa per le risorse viene calcolata in base agli addebiti e spesso non corrisponde all'effettivo prezzo unitario nell'elenco prezzi.

Per i calcoli degli addebiti di eccedenza, i dati di utilizzo delle risorse non elaborati vengono riportati con un massimo di sei posizioni decimali, come si può notare nel report con i dati ottenuti tramite Scarica utilizzo. Azure EA Portal tuttavia arrotonda l'utilizzo a quattro posizioni decimali per le unità di impegno e tronca a zero decimali per le unità di eccedenza. Ciò significa che in Azure EA Portal, per tutto l'utilizzo addebitato come eccedenza, vengono addebitate solo le unità intere. Si noterà una notevole differenza tra il prezzo unitario e la tariffa per le risorse per l'utilizzo addebitato come eccedenza o in mesi misti.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come comprendere la fattura e gli addebiti, vedere [Informazioni sulla fattura del contratto Enterprise di Azure](billing-understand-your-bill-ea.md).
- Per iniziare a usare Azure EA Portal, vedere [Introduzione ad Azure EA Portal](billing-ea-portal-get-started.md).
