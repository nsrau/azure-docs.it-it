---
title: Analizzare i costi di Azure con l'app Power BI
description: Questo articolo illustra come installare e usare l'app Power BI Gestione costi di Azure.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 7282ef9269c1168214fe57d3faa5488e531af8da
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131123"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analizzare i costi con l'app Power BI Gestione costi di Azure per contratti Enterprise (EA)

Questo articolo illustra come installare e usare l'app Power BI Gestione costi di Azure. L'app consente di analizzare e gestire i costi di Azure in Power BI. È possibile usare l'app per monitorare i costi e le tendenze di utilizzo e per identificare le opzioni di ottimizzazione dei costi per ridurre le spese.

è possibile usarla così com'è oppure modificarla personalizzando i filtri, le viste e le visualizzazioni predefiniti in base alle esigenze. È quindi possibile usarla per unire dati aggiuntivi per creare report personalizzati in modo da ottenere una visione olistica dei costi aziendali complessivi.

Attualmente l'app Power BI Gestione costi di Azure supporta solo i clienti con [contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

> [!NOTE]
> Le app modello Power BI non supportano il download del file PBIX.

## <a name="prerequisites"></a>Prerequisiti

- Per installare e usare l'app, è necessaria una [licenza di Power BI Pro](/power-bi/service-self-service-signup-for-power-bi).
- Per connettersi ai dati, è necessario usare un account di [amministratore dell'organizzazione](../manage/understand-ea-roles.md). Il ruolo Amministratore dell'organizzazione (sola lettura) è supportato.

## <a name="installation-steps"></a>Procedura di installazione

Per installare l'app:

1. Aprire l'[app Power BI Gestione costi di Azure](https://aka.ms/costmgmt/ACMApp).
2. Nella pagina di AppSource per Power BI selezionare **Scarica adesso** .
3. Selezionare **Continua** per accettare le condizioni per l'utilizzo e l'informativa sulla privacy.
4. Nella finestra **Install this Power BI app** (Installa questa app di Power BI) selezionare **Installa** .
5. Se necessario, creare un'area di lavoro e selezionare **Continua** .
6. Al termine dell'installazione, viene visualizzata una notifica che informa che la nuova app è pronta.
7. Selezionare **Vai all'app** .
8. Nella finestra **Operazioni iniziali con la nuova app** selezionare **Connetti** in **Connettere i dati** .  
  ![Operazioni iniziali con la nuova app - Connetti](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. Nella finestra di dialogo visualizzata immettere il numero del contratto Enterprise per **BillingProfileIdOrEnrollmentNumber** . Specificare il numero di mesi di dati da ottenere. In **Ambito** lasciare il valore predefinito **Numero di registrazione** e quindi selezionare **Avanti** .  
  ![Immettere le informazioni sulla registrazione del contratto Enterprise](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. La finestra di dialogo successiva consente di connettersi ad Azure e ottenere i dati necessari per le raccomandazioni sulle istanze riservate. *Lasciare invariati i valori predefiniti configurati* , quindi selezionare **Accedi** .  
  ![Screenshot che illustra la finestra di dialogo Connetti all'app Gestione costi di Azure con i valori predefiniti.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. Il passaggio finale dell'installazione consiste nella connessione alla registrazione del contratto Enterprise e richiede un account [amministratore dell'organizzazione](../manage/understand-ea-roles.md). Selezionare **Accedi** per eseguire l'autenticazione con la registrazione EA. Questo passaggio avvia anche un'azione di aggiornamento dei dati in Power BI.  
  ![Connessione alla registrazione EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > Il completamento del processo di aggiornamento dei dati potrebbe richiedere tempo. La quantità di tempo dipende dal numero di mesi specificati e dalla quantità di dati da sincronizzare.
12. Per controllare lo stato dell'aggiornamento dei dati, selezionare la scheda **Set di dati** nell'area di lavoro. Osservare il timestamp della voce Aggiornato. Se l'aggiornamento è ancora in corso, un indicatore ne mostra lo stato.  
  ![Aggiornamento dei dati](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Al termine dell'aggiornamento dei dati, selezionare l'app Gestione costi di Azure per visualizzare i report già creati.

## <a name="reports-available-with-the-app"></a>Report disponibili con l'app

Nell'app sono disponibili i report descritti di seguito.

**Attività iniziali** : offre collegamenti utili alla documentazione e collegamenti per fornire feedback.

**Panoramica account** : mostra un riepilogo mensile di informazioni, tra cui:

- Addebiti rispetto ai crediti
- Nuovi acquisti
- Addebiti per Azure Marketplace
- Eccedenze e addebiti totali

**Usage by Subscriptions and Resource Groups** (Utilizzo in base a sottoscrizioni e gruppi di risorse): offre una panoramica dei costi nel tempo, con grafici che mostrano i costi per sottoscrizione e gruppo di risorse.

**Usage by Services** (Utilizzo in base ai servizi): offre una panoramica dell'utilizzo nel tempo per categoria del contatore. È possibile tenere traccia dei dati di utilizzo ed esaminare in profondità eventuali anomalie per comprendere i picchi o i cali nell'utilizzo.

**Top 5 Usage drivers** (Prime 5 categorie di utilizzo): mostra un riepilogo dei costi filtrato in base alle prime 5 categorie del contatore e al nome contatore corrispondente.

**Windows Server AHB Usage** (Utilizzo Vantaggio Azure Hybrid di Windows Server): mostra il numero di macchine virtuali in cui è abilitato Vantaggio Azure Hybrid, oltre al numero di core/vCPU usate dalle macchine virtuali.

![Report completo su Vantaggio Azure Hybrid](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

Il report identifica anche le VM Windows in cui Vantaggio Azure Hybrid è **abilitato** ma le vCPU sono _meno di_ 8. Mostra inoltre le VM in cui Vantaggio Azure Hybrid **non è abilitato** e le vCPU sono 8 _o più_ . Queste informazioni consentono di sfruttare completamente Vantaggio Azure Hybrid. Applicare il vantaggio alle macchine virtuali più dispendiose per ottimizzare i potenziali risparmi.

![Vantaggio Azure Hybrid - meno di 8 vCPU e vCPU non abilitate](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**RI Chargeback** (Chargeback istanza riservata): aiuta a identificare dove e in quale misura il vantaggio di un'istanza riservata viene applicato per area, sottoscrizione, gruppo di risorse o risorsa. A questo scopo il report usa dati di utilizzo ammortizzati.

È possibile applicare un filtro a _chargetype_ per visualizzare i dati di sottoutilizzo delle istanze riservate.

Per altre informazioni sui dati ammortizzati, vedere [Ottenere informazioni sui costi di prenotazione e l'utilizzo dei contratti Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

**RI Savings** (Risparmi istanze riservate): mostra i risparmi accumulati dalle prenotazioni per sottoscrizione, gruppo di risorse e livello di risorsa. Vengono visualizzati questi dati:

- Costi con acquisti di prenotazioni
- Costo su richiesta stimato in caso di utilizzo senza prenotazione
- Risparmi sui costi accumulati dalla prenotazione

 Il report sottrae gli eventuali sprechi delle prenotazioni sottoutilizzate dal risparmio totale. Non dovrebbe esserci alcuno spreco senza una prenotazione.

È possibile usare i dati di utilizzo ammortizzati per elaborare i dati.

<a name="shared-recommendation"></a>
**VM RI Coverage (shared recommendation)** (Copertura istanze riservate VM - raccomandazione condivisa): il report è suddiviso tra l'utilizzo della VM su richiesta e l'utilizzo della VM per istanza riservata nel periodo selezionato. Fornisce raccomandazioni per gli acquisti di istanze riservate di VM in un ambito condiviso.

Per usare il report, selezionare il filtro di drill-down.

![Report sulla copertura istanze riservate VM - selezione del filtro di drill-down](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Selezionare l'area che si vuole analizzare. Selezionare quindi il gruppo di flessibilità delle dimensioni istanza e così via.

Per ogni livello di drill-down, al report vengono applicati i filtri seguenti:

- I dati di copertura a destra rappresentano il filtro che mostra la quantità di utilizzo addebitata usando la tariffa su richiesta rispetto all'utilizzo coperto dalla prenotazione.
- Sono filtrate anche le raccomandazioni.

La tabella delle raccomandazioni fornisce raccomandazioni per l'acquisto di prenotazioni, in base alle dimensioni delle VM usate.

I valori _Normalized Size_ (Dimensioni normalizzate) e _Recommended Quantity Normalized_ (Quantità consigliata normalizzata) consentono di normalizzare l'acquisto alle dimensioni più piccole per un gruppo di flessibilità delle dimensioni istanza. Le informazioni sono utili se si prevede di acquistare solo una prenotazione per tutte le dimensioni nel gruppo di flessibilità delle dimensioni istanza.

![Raccomandazioni sulle istanze riservate](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**VM RI Coverage (single recommendation)** (Copertura istanze riservate VM - singola raccomandazione): il report è suddiviso tra l'utilizzo della VM su richiesta e l'utilizzo della VM per istanza riservata nel periodo selezionato. Fornisce raccomandazioni per gli acquisti di istanze riservate di VM nell'ambito di una sottoscrizione.

Per informazioni su come usare il report, vedere la sezione [VM RI Coverage (shared recommendation)](#shared-recommendation).

**RI purchases** (Acquisti di istanze riservate): mostra gli acquisti di istanze riservate nel periodo specificato.

**Price sheet** (Elenco prezzi): mostra un elenco dettagliato di prezzi specifici per un account di fatturazione o una registrazione EA.

## <a name="troubleshoot-problems"></a>Risolvere i problemi

Se si verificano problemi con l'app Power BI, le informazioni seguenti potrebbero rivelarsi utile per la risoluzione.

### <a name="error-processing-the-data-in-the-dataset"></a>Si è verificato un errore durante l'elaborazione dei dati nel set di dati

È possibile che venga visualizzato un messaggio di errore simile a quello seguente:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Verrà visualizzato un nome di tabella invece di `<TableName>`.

#### <a name="cause"></a>Causa

Il valore di **Ambito** predefinito `Enrollment Number` è stato modificato nella connessione a Gestione costi.

#### <a name="solution"></a>Soluzione

Riconnettersi a Gestione costi e impostare il valore di **Ambito** su `Enrollment Number`. Non immettere il numero di registrazione dell'organizzazione. Digitare invece `Enrollment Number` esattamente come mostrato nell'immagine seguente.

![Immettere le informazioni sulla registrazione del contratto Enterprise](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>Errore di BudgetAmount

È possibile che venga visualizzato un messaggio di errore simile a quello seguente:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Causa

Questo errore è dovuto a un bug nei metadati sottostanti. Il problema si verifica perché non è disponibile alcun budget in **Gestione costi > Budget** nel portale di Azure. La correzione del bug sta per essere distribuita in Power BI Desktop e nel servizio Power BI. 

#### <a name="solution"></a>Soluzione

- Finché il bug non viene corretto, è possibile aggirare il problema aggiungendo un budget di test nel portale di Azure al livello di account di fatturazione/registrazione EA. Il budget di test sblocca la connessione con Power BI. Per altre informazioni sulla creazione di un budget, vedere [Esercitazione: Creare e gestire budget di Azure](tutorial-acm-create-budgets.md).


### <a name="invalid-credentials-for-azureblob-error"></a>Credenziali non valide per l'errore di AzureBlob

È possibile che venga visualizzato un messaggio di errore simile a quello seguente:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Causa

Questo errore si verifica se si cambia il metodo di autenticazione per la connessione BLOB AutoFitComboMeter.

#### <a name="solution"></a>Soluzione

1. Connettersi ai dati.
1. Dopo aver immesso la registrazione EA e il numero di mesi, assicurarsi di lasciare il valore predefinito **Anonimo** per il metodo di autenticazione e **Nessuno** per l'impostazione del livello di privacy.  
  ![Screenshot che illustra la finestra di dialogo Connetti all'app Gestione costi di Azure con i valori Anonimo e Nessuno.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. Nella pagina successiva impostare **OAuth2** come metodo di autenticazione e **Nessuno** per il livello di privacy. Quindi, accedere per eseguire l'autenticazione con la registrazione. Questo passaggio avvia anche un aggiornamento dei dati in Power BI.


## <a name="data-reference"></a>Riferimenti ai dati

Le informazioni seguenti riepilogano i dati disponibili tramite l'app. Sono disponibili anche collegamenti ad API che forniscono informazioni approfondite per i campi dati e i valori.

| **Riferimento a tabella** | **Descrizione** |
| --- | --- |
| **AutoFitComboMeter** | Dati inclusi nell'app per normalizzare la raccomandazione e l'utilizzo delle istanze riservate alla dimensione minima del gruppo di famiglie di istanze. |
| [**Balance summary**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) (Riepilogo saldo) | Riepilogo del saldo per i contratti Enterprise. |
| [**Budgets**](/rest/api/consumption/budgets/get#definitions) | Dettagli del budget per visualizzare i costi o l'utilizzo effettivi rispetto a destinazioni di budget esistenti. |
| [**Pricesheets**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) (Elenchi prezzi) | Tariffe del contatore applicabili per il profilo di fatturazione o la registrazione EA specificati. |
| [**RI charges**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) (Addebiti istanze riservate) | Addebiti associati alle istanze riservate negli ultimi 24 mesi. |
| [**RI recommendations (shared)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) (Raccomandazioni sulle istanze riservate - condivise) | Raccomandazioni per l'acquisto di istanze riservate in base a tutte le tendenze di utilizzo delle sottoscrizioni per gli ultimi 7, 30 o 60 giorni. |
| [**RI recommendations (single)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) (Raccomandazioni sulle istanze riservate - singola) | Raccomandazioni per l'acquisto di istanze riservate in base a tutte le tendenze di utilizzo della singola sottoscrizione per gli ultimi 7, 30 o 60 giorni. |
| [**RI usage details**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) (Dettagli di utilizzo istanze riservate) | Dettagli relativi all'utilizzo delle istanze riservate esistenti nell'ultimo mese. |
| [**RI usage summary**](/rest/api/consumption/reservationssummaries/list) (Riepilogo utilizzo istanze riservate) | Percentuale di utilizzo giornaliero della prenotazione di Azure. |
| [**Usage details**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) (Dettagli di utilizzo) | Ripartizione delle quantità utilizzate e degli addebiti stimati per il profilo di fatturazione specificato nella registrazione EA. |
| [**Usage details amortized**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) (Dettagli di utilizzo ammortizzati) | Ripartizione delle quantità utilizzate e degli addebiti ammortizzati stimati per il profilo di fatturazione specificato nella registrazione EA. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla configurazione dei dati, il processo di aggiornamento, la condivisione di report e ulteriori opzioni di personalizzazione dei report, vedere gli articoli seguenti:

- [Configurare l'aggiornamento pianificato](/power-bi/refresh-scheduled-refresh)
- [Condividere i dashboard e i report di Power BI con colleghi e altri utenti](/power-bi/service-share-dashboards)
- [Sottoscrivere per se stessi e altri utenti report e dashboard nel servizio Power BI](/power-bi/service-report-subscribe)
- [Scaricare un report dal servizio Power BI in Power BI Desktop](/power-bi/service-export-to-pbix)
- [Salvare un report nel servizio Power BI e in Power BI Desktop](/power-bi/service-report-save)
- [Creare un report nel servizio Power BI importando un set di dati](/power-bi/service-report-create-new)