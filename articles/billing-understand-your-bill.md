---
title: Informazioni sulla fattura | Microsoft Docs
description: Informazioni su come leggere e comprendere l'utilizzo e la fattura per la sottoscrizione di Azure
services: ''
documentationcenter: ''
author: erihur
manager: stevenpo
editor: ''
tags: billing

ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: erihur;genli

---
# Comprendere la fattura per Microsoft Azure
> [!NOTE]
> Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
> 
> 

Gli addebiti per le sottoscrizioni di Microsoft Azure dipendono dal piano tariffario. Alcuni piani tariffari, ad esempio le sottoscrizioni di Visual Studio Enterprise (MPN), includono crediti mensili che possono essere usati in qualsiasi servizio di Azure in base alle proprie esigenze.

Si noti che fino a 24 ore di utilizzo non attivo del periodo di fatturazione precedente possono essere inserite nel periodo di fatturazione corrente.

Per altre informazioni sul consumo e sui piani tariffari, vedere la [pagina relativa alle opzioni di acquisto di Microsoft Azure](https://azure.microsoft.com/pricing/purchase-options/).

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### Visualizzare o scaricare una fattura per Microsoft Azure:
1. Accedere al [Centro account](https://account.windowsazure.com/subscriptions) usando l'account Microsoft o l'ID dell'account aziendale.
2. Fare clic sulla sottoscrizione per cui si vogliono visualizzare i dettagli e l'utilizzo.
3. Fare clic su **Cronologia di fatturazione**.
   
    ![Riepilogo - cronologia fatturazione -1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)
4. La sezione **Cronologia di fatturazione** include un elenco dei rendiconti dei periodi di fatturazione precedenti, oltre al periodo attuale non fatturato. Il rendiconto per il periodo attuale è una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati alla data corrente. È possibile che la fattura mensile non corrisponda a questa stima.
   
    ![Riepilogo - cronologia fatturazione 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)
5. Fare clic su **Visualizza estratto conto corrente** per visualizzare una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati alla data corrente. È possibile che la fattura mensile non corrisponda a questa stima.
   
    ![Riepilogo - cronologia fatturazione 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)
   
    ![Riepilogo - cronologia fatturazione 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)
6. Fare clic su **Scarica fattura** per visualizzare una copia della fattura precedente.
   
    ![Riepilogo - cronologia fatturazione 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)

> [!NOTE]
> Gli addebiti elencati negli estratti conto per i clienti internazionali sono solo a scopo di valutazione, perché le banche hanno costi diversi per i tassi di conversione.
> 
> 

Di seguito sono fornite alcune istruzioni di esempio per due offerte differenti disponibili in Microsoft Azure.

| Tipo di offerta | Description | Scaricare |
|:--- |:--- |:--- |
| Pagamento in base al consumo |Pagamento mensile in arretrato |[File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf) |
| Offerta dell'impegno |Spesa detratta dall'impegno prepagato |[File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf) |

## Informazioni sull'account
La sezione relativa alle informazioni sull'account identifica le informazioni pertinenti relative all'utilizzo e al profilo dell'utente.

![intestazione](./media/billing-understand-your-bill/Header.png)

| Termine | Descrizione |
| --- | --- |
| N. fattura |Identificatore univoco della fattura per scopi di registrazione. |
| Ciclo di fatturazione |Intervallo di tempo durante il quale è avvenuto l'utilizzo. |
| Data fattura |Data in cui è stata generata la fattura. |
| Metodo di pagamento |Tipo di pagamento usato per l'account, ovvero addebito con fattura o carta di credito. |
| Indirizzo di fatturazione |Indirizzo per i pagamenti per Microsoft Azure. |
| Offerta di sottoscrizione |Tipo di offerta di sottoscrizione che è stato acquistato, ossia con pagamento in base al consumo, BizSpark Plus, Azure Pass e così via. |
| Indirizzo di posta elettronica proprietario account |Indirizzo di posta elettronica dell'account associato all'account Microsoft Azure. |

## Comprendere il riepilogo della fattura
La sezione relativa al **riepilogo della fattura** riassume le transazioni avvenute dall'ultima fattura e gli addebiti per l'utilizzo attuale.

![riepilogo fattura](./media/billing-understand-your-bill/InvoiceSummary.png)

Nella sezione della fattura relativa a saldo precedente, pagamenti e saldo in sospeso sono riepilogate le transazioni avvenute dall'ultima fattura.

| Termine | Description |
| --- | --- |
| Saldo precedente |Importo totale dovuto nell'ultima fattura. |
| Pagamenti |Pagamenti totali applicati all'ultima fattura. |
| Saldo in sospeso (dal ciclo di fatturazione precedente) |Eventuali rettifiche della fattura (crediti o saldi) applicate all'account dopo l'ultima fattura. |

## Comprendere gli addebiti attuali
La sezione Costi attuali della fattura contiene informazioni dettagliate sui costi mensili. I collegamenti sono organizzati nelle sottosezioni seguenti.

| Termine | Description |
| --- | --- |
| Addebiti per utilizzo |Gli addebiti per l'utilizzo corrispondono agli addebiti mensili totali per una sottoscrizione. Questi costi vengono addebitati in modo posticipato per l'utilizzo del mese precedente. |
| Sconti |Gli eventuali sconti applicati all'attuale fattura sono indicati in corrispondenza di questa voce. |
| Rettifiche |Le rettifiche varie sono crediti di diverso tipo o addebiti in sospeso applicati alla fattura corrente. Ad esempio, se è stata attivata l'offerta Visual Studio Enterprise con MSDN, in questa voce verrà visualizzato un credito mensile. Se la sottoscrizione viene annullata, i costi di utilizzo mensili in eccesso rispetto al credito mensile incluso nell'offerta sono visualizzati a partire dal periodo di fatturazione corrente fino alla data di annullamento della sottoscrizione. |

## Informazioni del piè di pagina
![piè di pagina](./media/billing-understand-your-bill/footerinformation.png)

## Comprendere le informazioni aggiuntive
La pagina delle informazioni aggiuntive contiene riferimenti ad altre risorse utili per comprendere la fattura e alcuni collegamenti per visualizzare i dati sull'utilizzo e altri dati correlati alla fattura.

![informazioni aggiuntive](./media/billing-understand-your-bill/AdditionalInformation.png)

### Uso dettagliato
Un collegamento all'interno della descrizione inclusa nella sezione dei **dati sull'utilizzo dettagliati** permette di passare al Centro account in cui è possibile visualizzare i dati sull'utilizzo dettagliati per questa sottoscrizione. Sono ora disponibili due versioni disponibili per il download: **versione 1 CSV**, che contiene i campi di utilizzo e la convenzione di denominazione precedente e **versione 2 CSV**, che contiene i nomi descrittivi del cliente per ognuna delle categorie e altri campi che consentiranno di comprendere quali servizi di Microsoft Azure si utilizzano. Si noti che nella versione 1 del file CSV non sono presenti dettagli di Azure Resource Manager. Le informazioni di Azure Resource Manager sono reperibili nella versione 2 del file con estensione csv.

### Informazioni aggiuntive e risorse utili
Questa sezione contiene collegamenti a semplici domande relative alle dimensioni delle istanze di calcolo e agli addebiti di SQL DB, nonché collegamenti utili per rispondere ad altre domande.

| Termine | Descrizione |
| --- | --- |
| Venduto a |Prepopolato con l'indirizzo del profilo associato all'account. |
| Istruzioni per il pagamento |Questa sezione contiene le istruzioni di pagamento relative a dove inviare assegni, assegni urgenti o bonifici se è stato scelto il metodo di pagamento con fattura. |

## Comprendere gli addebiti basati sull'uso dettagliato
Come parte dell'impegno volto a consentire ai clienti di gestire facilmente l'utilizzo di Azure, è stato migliorato il file di utilizzo di download che segnala l'uso e i costi dei servizi di Azure. Il collegamento al download prevede due versioni del file dei dati di utilizzo:

* La **versione 1** usa il formato preesistente.
* La **versione 2** include informazioni aggiuntive e nomi di colonna aggiornati nella sezione Utilizzo giornaliero.

I costi di utilizzo sono i costi **mensili** totali di una sottoscrizione dedotti di eventuali crediti o sconti. Questi costi vengono addebitati in modo posticipato per l'utilizzo del mese precedente. Nella sezione superiore del file vengono visualizzati i dettagli sui servizi fatturati durante il ciclo di fatturazione del mese precedente. La tabella precedente elenca i nomi delle colonne per ognuno dei file di versione CSV.

| Versione 1 | Versione 2 | Description |
|:--- |:--- | --- |
| Periodo di fatturazione |Periodo di fatturazione |Indica il periodo di fatturazione in cui la risorsa è stata consumata. |
| Nome |Categoria misuratore |Identifica il servizio di primo livello a cui si riferisce l'utilizzo. |
| Tipo |Sottocategoria misuratore |In questa colonna un servizio di Azure può essere definito ulteriormente in base al tipo, che può influire sulla tariffa. |
| Risorsa |Nome misuratore |Identifica l'unità di misura per la risorsa consumata. |
| Region |Area misuratore |Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. |
| SKU |SKU |Indica l'identificatore di sistema univoco per ogni risorsa di Azure. |
| Unità |Unità |Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia. |
| Consumato |Quantità consumata |Indica la quantità di risorsa consumata durante il periodo di fatturazione. |
| Incluso |Quantità inclusa |Contiene la quantità di risorsa inclusa gratuitamente nel periodo di fatturazione corrente. |
| Fatturabile |Quantità in eccesso |Se la quantità consumata supera quella inclusa, la differenza viene visualizzata in questa colonna. Questa quantità viene fatturata. Per le offerte di pagamento in base al consumo senza alcuna quantità inclusa, questo totale corrisponde a quello della quantità consumata. |
| Entro l'impegno |Entro l'impegno |Contiene i costi della risorsa detratti dall'importo dell'impegno associato all'offerta di 6 o 12 mesi. I costi della risorsa vengono detratti dall'importo dell'impegno in ordine cronologico. |
| Valuta |Valuta |Identifica la valuta per il periodo di fatturazione corrente. |
| Eccedenza |Eccedenza |Contiene i costi della risorsa che eccedono l'importo dell'impegno associato all'offerta di 6 o 12 mesi. |
| Tariffa dell'impegno |Tariffa dell'impegno |Contiene la tariffa dell'impegno basata sull'importo totale dell'impegno associato all'offerta di 6 o 12 mesi. |
| Tariffa |Tariffa |Indica la tariffa addebitata per ogni unità fatturabile. |
| Valore |Valore |Indica il risultato della moltiplicazione della colonna Fatturabile per la colonna Tariffa. Se la quantità consumata non supera quella inclusa, in questa colonna non viene visualizzato alcun importo. |

## Analizzare i dati sull'uso giornaliero
In base all'utilizzo, potrebbero essere presenti migliaia di righe relative ai dati sull'utilizzo giornaliero. Se si desidera analizzare i dati, fare clic su **Scarica utilizzo** e scegliere una versione di file variabile delimitato da virgole (CSV) per visualizzare i dati di utilizzo giornaliero per il periodo di fatturazione appropriato. È possibile scaricare un file CSV di esempio per ciascuna versione di seguito come riferimento.

| Nome | Scaricare |
|:---:|:---:|
|  Versione 1 CSV utilizzo dettagliato |[File di esempio](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx) |
|  Versione 2 CSV utilizzo dettagliato |[File di esempio](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx) |

![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)

Nel file CSV le voci sono suddivise per mostrare un elenco che indica la quantità consumata di ogni risorsa nel periodo di fatturazione corrente.

![csv snapshot](./media/billing-understand-your-bill/csvsnapshotportal.png)

Le colonne seguenti indicano i dettagli che influiscono sulle tariffe all'inizio del periodo di fatturazione:

| Versione 1 | Versione 2 | Descrizione |
|:--- |:--- | --- |
| Data utilizzo |Data utilizzo |Data in cui la risorsa è stata generata. |
| Nome |Categoria misuratore |Identifica il servizio di primo livello a cui si riferisce l'utilizzo. |
| GUID risorsa |ID misuratore |ID della metrica fatturata. Viene utilizzato come identificatore per la fatturazione dei costi. |
| Tipo |Sottocategoria misuratore |In questa colonna un servizio di Azure può essere definito ulteriormente in base al tipo, che può influire sulla tariffa. |
| Risorsa |Nome misuratore |Identifica l'unità di misura per la risorsa consumata. |
| Region |Area misuratore |Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. |
| Unità |Unità |Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia. |
| Consumato |Quantità consumata |Contiene la quantità di risorsa consumata per il giorno corrente. |
| Area secondaria |Percorso della risorsa |Identifica il datacenter in cui la risorsa è in esecuzione. |
| Service |Servizio utilizzato |Questa colonna è usata per tenere traccia del singolo servizio della piattaforma Azure che potrebbe non essere identificato in modo specifico nella colonna Nome. La colonna Servizio indica il servizio specifico a cui si riferisce l'utilizzo. |
| N/D |Gruppo di risorse |***Aggiunta nuova colonna.*** Il gruppo di risorse in cui la risorsa distribuita è in esecuzione. Vedere [Panoramica di Azure Resource Manager](resource-group-overview.md) |
| Componente |ID istanza |Identificatore per la risorsa in esecuzione. L'identificatore contiene il nome specificato per la risorsa al momento della creazione. |
| N/D |Tag |***Aggiunta nuova colonna.*** Nuovi tipi di risorse in Azure consentono di aggiungere tag alle risorse. Vedere [Organize your Azure resources with tags](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizzare le risorse di Azure con i tag) |
| Informazioni aggiuntive |Informazioni aggiuntive |Metadati aggiuntivi correlati al servizio. |
| Informazioni servizio 1 |Informazioni servizio 1 |Questa colonna fornisce il nome del progetto a cui il servizio appartiene nella sottoscrizione. |
| Informazioni servizio 2 |Informazioni servizio 2 |Si tratta di un campo legacy che acquisisce i metadati specifici del servizio facoltativo. |

Oltre ad alcuni nuovi campi e alle modifiche dei nomi nella Versione 2 csv, per i dati nei campi di seguito sarà applicata la formattazione standardizzata:

* **ID istanza**: il campo ID istanza rappresenta l’ID specificato dall’utente per il servizio di provisioning. Attualmente, esistono due formati in cui è rappresentata l'ID istanza: il nome della risorsa o l'ID di risorsa completo. I servizi Microsoft Azure sono in fase di transizione alla rappresentazione dell'ID istanza in un formato di ID risorsa completo standardizzato: ***/subscriptions/<ID sottoscrizione>/resourcegroups/<nome gruppo di risorse>/providers/<nome provider>/<nome risorsa>***. Nel momento in cui i servizi passano al nuovo formato sarà possibile osservare il campo ID istanza che passa dal semplice nome della risorsa a ID risorsa. L'ID risorsa è il formato usato dall'[API di Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790567.aspx) per identificare le risorse in una sottoscrizione.

![instanceid](./media/billing-understand-your-bill/instanceid.png)

* **Informazioni aggiuntive**: la colonna Informazioni aggiuntive del file CSV dei dati di utilizzo specifica i metadati specifici del servizio. Ad esempio, un tipo di immagine per una macchina virtuale. Attualmente, un servizio genera metadati specifici del servizio in più colonne, nei campi Informazioni aggiuntive, Informazioni servizio 1 e Informazioni servizio 2. I servizi di Microsoft Azure verranno standardizzati e i metadati specifici del servizio verranno generati solo nella colonna Informazioni aggiuntive. Vedere lo snapshot del formato standardizzato di seguito:

![additionalinfo\_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

* **Tag**: questa colonna contiene i tag delle risorse specificati dall'utente. I tag possono essere utilizzati per raggruppare i record di fatturazione. Ad esempio, è possibile utilizzare i tag per distribuire i costi per reparto mediante il servizio. Ulteriori informazioni sull’[uso dei tag per organizzare le risorse di Azure ](resource-group-using-tags.md). Di seguito sono indicati i servizi che supportano la creazione di tag:
  
  * Macchine virtuali
  * Archiviazione e
  * Servizi di rete di cui viene eseguito il provisioning mediante l’[API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![tags](./media/billing-understand-your-bill/tags.png)

## Passaggi successivi
* [Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure](billing-set-up-alerts.md)
* [Gestire i metodi di pagamento](billing-how-to-change-credit-card.md)
* [Informazioni sugli addebiti di Azure Marketplace](billing-understand-your-azure-marketplace-charges.md)
* [Domande frequenti su fatturazione e sottoscrizione di Azure](billing-subscription-faq.md)

> [!NOTE]
> Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
> 
> 

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->

<!---HONumber=AcomDC_0914_2016-->