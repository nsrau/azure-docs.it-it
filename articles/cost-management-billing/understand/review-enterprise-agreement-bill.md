---
title: Esaminare la fattura del Contratto Enterprise di Azure
description: Informazioni su come leggere e comprendere l'uso e la fattura per i contratti Enterprise di Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5340d6124ae4fdc669a30458b9e58cf4c86d6746
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684661"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Informazioni sulla fattura del contratto Enterprise di Azure

I clienti di Azure con un Contratto Enterprise ricevono una fattura quando superano il credito dell'organizzazione o usano i servizi che non sono coperti dal credito.

Il credito della propria organizzazione include l'impegno monetario. L'impegno monetario è l'importo anticipato pagato dall'organizzazione per l'uso dei servizi di Azure. È possibile aggiungere fondi di impegno monetario al Contratto Enterprise contattando l'account manager o il rivenditore Microsoft.

Questa esercitazione si applica solo ai clienti di Azure con un Contratto Enterprise di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Controllare gli addebiti fatturati
> * Controllare gli addebiti per l'eccedenza del servizio
> * Controllare la fattura del Marketplace

## <a name="prerequisites"></a>Prerequisiti

Per rivedere e verificare gli addebiti sulla propria fattura, bisogna essere l'amministratore di Enterprise. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](../manage/understand-ea-roles.md). Se non si sa chi è l'amministratore di Enterprise della propria organizzazione, [contattare l'assistenza](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Controllare gli addebiti fatturati per la maggior parte dei clienti

Questa sezione non si applica ai clienti di Azure in Australia, Giappone o Singapore.

Si riceve una fattura di Azure quando si verifica uno degli eventi seguenti durante il periodo di fatturazione:

- **Eccedenza del servizio**: gli addebiti per l'utilizzo della propria organizzazione superano il saldo del credito.
- **Addebiti fatturati separatamente**: i servizi usati dall'organizzazione non sono coperti dal credito. Si riceve una fattura per i servizi seguenti nonostante il saldo del credito:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utente registrato
    - Openlogic
    - Utente registrato XenApp Essentials con Diritti di accesso remoto
    - Ubuntu Advantage
    - Visual Studio Enterprise (mensile)
    - Visual Studio Enterprise (annuale)
    - Visual Studio Professional (mensile)
    - Visual Studio Professional (annuale)
- **Addebiti per Azure Marketplace**: gli acquisti e l'utilizzo di Azure Marketplace non sono coperti dal credito della propria organizzazione. Quindi, si riceve una fattura per gli addebiti del Marketplace nonostante il saldo del credito. In Enterprise Portal un amministratore dell'organizzazione può abilitare e disabilitare gli acquisti nel Marketplace.

La fattura visualizzerà prima gli addebiti per l'utilizzo di Azure con i costi associati, seguiti da eventuali addebiti per il Marketplace. Nel caso di un saldo a credito, questo viene applicato all'utilizzo di Azure e la fattura visualizzerà l'utilizzo di Azure e l'utilizzo del Marketplace senza costi rimanenti.

Confrontare l'importo totale combinato mostrato in Enterprise Portal in **Report** > **Riepilogo utilizzo** con la fattura di Azure. Gli importi in **Riepilogo utilizzo** non includono l'imposta.

Accedere ad [Azure EA Portal](https://ea.azure.com). Selezionare quindi **Report**. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  

![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

L'importo combinato di **Utilizzo totale** e **Azure Marketplace** deve corrispondere all'**Importo totale esteso** della fattura. Per ottenere altre informazioni sugli addebiti, vedere **Scarica utilizzo**.  

![Screenshot che mostra la scheda Scarica utilizzo](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Controllare gli addebiti fatturati per altri clienti

Questa sezione si applica solo ai clienti di Azure in Australia, Giappone o Singapore.

Si ricevono una o più fatture di Azure quando si verifica uno degli eventi seguenti:

- **Eccedenza del servizio**: gli addebiti per l'utilizzo della propria organizzazione superano il saldo del credito.
- **Addebiti fatturati separatamente**: i servizi usati dall'organizzazione non sono coperti dal credito. Si riceve una fattura per i servizi seguenti:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utente registrato
    - Openlogic
    - Utente registrato XenApp Essentials con Diritti di accesso remoto
    - Ubuntu Advantage
    - Visual Studio Enterprise (mensile)
    - Visual Studio Enterprise (annuale)
    - Visual Studio Professional (mensile)
    - Visual Studio Professional (annuale)
- **Addebiti per Azure Marketplace**: gli acquisti e l'utilizzo di Azure Marketplace non sono coperti dal credito della propria organizzazione e vengono fatturati separatamente. In Enterprise Portal un amministratore dell'organizzazione può abilitare e disabilitare gli acquisti nel Marketplace.

Se durante il periodo di fatturazione vengono fatturati separatamente l'eccedenza di servizio e gli addebiti, si riceve un'unica fattura che include entrambi i tipi di addebiti. Gli addebiti di Marketplace vengono sempre fatturati separatamente.

## <a name="review-service-overage-charges-for-other-customers"></a>Controllare gli addebiti per l'eccedenza del servizio per altri clienti

Questa sezione si applica solo se si risiede in Australia, Giappone o Singapore.

Confrontare l'importo totale dell'utilizzo in Enterprise Portal in **Report** > **Riepilogo utilizzo** con la fattura di eccedenza del servizio. La fattura di eccedenza del servizio include l'uso che supera il credito della propria organizzazione e/o i servizi che non sono coperti dal credito. Gli importi nel **Riepilogo uso** non includono l'imposta.

Accedere ad [Azure EA Portal](https://ea.azure.com) e quindi selezionare **Report**. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  

![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

L'importo dell'**Uso totale** deve corrispondere all'**Importo totale** esteso nella fattura di eccedenza del servizio. Per ottenere altre informazioni sui propri addebiti, visitare **Scarica uso** > **Download del report avanzato**. Questo report non include le imposte o gli addebiti per le prenotazioni o gli addebiti del marketplace.  

![Screenshot che mostra il Download del report avanzato nella scheda Scarica uso.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

La tabella seguente elenca i termini e le descrizioni mostrate nella fattura e sul **Riepilogo uso** nel portale Enterprise:

|Termine di fatturazione|Termine Riepilogo uso|Descrizione|
|---|---|---|
|Importo totale esteso|Uso totale|L'addebito di uso totale al lordo delle imposte per il periodo specifico prima che venga applicato il credito.|
|Uso dell'impegno|Uso dell'impegno|Il credito applicato durante tale periodo specifico.|
|Vendita totale|Eccedenza totale|L'addebito di uso totale che supera l'importo del credito. Questa quantità non include l'imposta.|
|Importo imposta|Non applicabile|L'imposta che si applica all'importo di vendita totale per il periodo specifico.|
|Importo totale|Non applicabile|L'importo dovuto per la fattura dopo che viene applicato il credito e viene aggiunta l'imposta.|

### <a name="review-marketplace-invoice"></a>Controllare la fattura del Marketplace

Questa sezione si applica solo se si risiede in Australia, Giappone o Singapore.

Confrontare il totale di Azure Marketplace in **Report** > **Riepilogo uso** nel portale Enterprise con la fattura di marketplace. La fattura di marketplace è solo per gli acquisti e l'uso di Azure Marketplace. Gli importi indicati in **Riepilogo utilizzo** includono già un'imposta, determinata dall'editore.

Accedere a [Enterprise Portal](https://ea.azure.com) e quindi selezionare **Report**. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  

![Screenshot che mostra l'opzione M + C nel Riepilogo uso.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Il totale **Azure Marketplace** deve corrispondere alla **Vendita totale** nella fattura di marketplace. Per ottenere altre informazioni sui propri addebiti basati sull'uso, visitare **Scarica uso**. Sotto **Addebiti Marketplace**, selezionare **Download**. Il prezzo del Marketplace include un'imposta determinata dall'editore. I clienti non riceveranno una fattura separata dall'editore per l'imposta sulla transazione.

![Screenshot che mostra l'opzione di download sotto gli addebiti di Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Visualizzare le informazioni dell'elenco prezzi

Gli amministratori aziendali possono visualizzare il listino prezzi associato alla registrazione per i servizi di Azure.

Per visualizzare il listino prezzi corrente:

1. In Azure Enterprise Portal selezionare **Report** e quindi **Elenco prezzi**.
2. Visualizzare l'elenco prezzi o selezionare **Download**.

![Esempio che mostra le informazioni dell'elenco prezzi](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Per scaricare un elenco prezzi precedente:

1. In Azure Enterprise Portal selezionare **Report** e quindi **Scarica utilizzo**.
2. Scaricare l'elenco prezzi.

![Esempio che mostra dove scaricare un elenco prezzi precedente](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

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

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Controllare gli addebiti fatturati
> * Controllare gli addebiti per l'eccedenza del servizio
> * Controllare la fattura del Marketplace

Passare all'articolo successivo per altre informazioni sull'uso di Azure EA Portal.

> [!div class="nextstepaction"]
> [Introduzione ad Azure EA Portal](../manage/ea-portal-get-started.md)
