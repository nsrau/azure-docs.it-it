<properties
   pageTitle="Comprendere la fattura di Azure"
   description="Comprendere la fattura di Azure"
   services=""
   documentationCenter="Azure"
   authors="kareni"
   manager="jocho"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/06/2015"
   ms.author="kareni"/>


# Comprendere la fattura per Microsoft Azure

Gli addebiti per le sottoscrizioni di Microsoft Azure dipendono dal piano tariffario. Alcuni piani tariffari, ad esempio Visual Studio Ultimate con MSDN, includono crediti mensili che possono essere usati in qualsiasi servizio di Azure in base alle proprie esigenze.

Si noti che è possibile che fino a 24 ore di utilizzo latente dal periodo di fatturazione precedente vengano segnalati nel periodo di fatturazione corrente.

Per altre informazioni sul consumo, sull'utilizzo e sui piani tariffari, vedere la [pagina relativa alle opzioni di acquisto di Microsoft Azure](http://azure.microsoft.com/pricing/purchase-options/).

## Contenuto:

Questo argomento semplifica le operazioni seguenti durante la lettura della fattura.

-  Visualizzare o scaricare una fattura per Azure
-  Informazioni sul cliente
-  Comprendere il riepilogo della fattura
-  Comprendere gli addebiti attuali
-  Informazioni del piè di pagina
-  Comprendere le informazioni aggiuntive
-  Comprendere gli addebiti basati sui dati sull'utilizzo dettagliati
-  Analizzare i dati di utilizzo giornaliero

### Visualizzare o scaricare una fattura per Microsoft Azure:

Nel [portale di utilizzo e fatturazione](https://account.windowsazure.com/subscriptions) è possibile visualizzare le fatture correnti e scaricare le fatture precedenti.

Per visualizzare o scaricare una fattura:

1. Accedere al [portale di utilizzo e fatturazione](https://account.windowsazure.com/subscriptions) usando l'ID dell'account Microsoft o l'ID dell'account aziendale o dell'istituto di istruzione.

2. Fare clic sulla sottoscrizione per cui si vogliono visualizzare i dettagli e l'utilizzo.

3. Fare clic su **Cronologia di fatturazione**

    ![Riepilogo - cronologia fatturazione -1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)


4. La sezione **Cronologia di fatturazione** include un elenco dei rendiconti per i sei periodi di fatturazione più recenti, oltre al periodo attuale non fatturato. Il rendiconto per il periodo attuale è una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati alla data corrente. È possibile che la fattura mensile non corrisponda a questa stima.

    ![Riepilogo - cronologia fatturazione 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Fare clic su **Visualizza estratto conto corrente** per visualizzare una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati alla data corrente. È possibile che la fattura mensile non corrisponda a questa stima.

    ![Riepilogo - cronologia fatturazione 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Riepilogo - cronologia fatturazione 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Fare clic su **Scarica fattura** per visualizzare una copia della fattura precedente.

    ![Riepilogo - cronologia fatturazione 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)



**Di seguito sono fornite alcune istruzioni di esempio per due differenti offerte disponibili in Microsoft Azure.**

 **TIPO DI OFFERTA** | **DESCRIZIONE** | **DOWNLOAD** |
 :--------- |:-------- | :-------|
Pagamento in base al consumo | Pagamento mensile in arretrato | [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
Offerta dell'impegno | Spesa detratta dall'impegno prepagato | [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)



## Intestazione - Informazioni sul cliente

La sezione relativa alle informazioni sul cliente identifica le informazioni pertinenti relative all'utilizzo e al profilo dell'utente specifico. ![intestazione](./media/billing-understand-your-bill/Header.png)

### N. fattura
Identificatore univoco della fattura per scopi di registrazione.

### Ciclo di fatturazione
Intervallo di tempo durante il quale è avvenuto l'utilizzo.

### Data fattura
Data in cui è stata generata la fattura.

### Metodo di pagamento
Tipo di pagamento usato per l'account (fattura o carta di credito).

### Indirizzo di fatturazione
Indirizzo per i pagamenti per Microsoft Azure.

### Offerta di sottoscrizione
Tipo di offerta di sottoscrizione che è stato acquistato (vale a dire pagamento in base al consumo, MSDN Vidual Studio Ultimate, ecc.)

### Indirizzo di posta elettronica proprietario account
Indirizzo di posta elettronica dell'account associato all'account Microsoft Azure.



## Comprendere il riepilogo della fattura
La sezione relativa al riepilogo della fattura riassume le transazioni avvenute dall'ultima fattura e gli addebiti sull'attuale utilizzo.

![riepilogo fattura](./media/billing-understand-your-bill/InvoiceSummary.png)

Nella sezione della fattura relativa a saldi, pagamenti e crediti sono riepilogate le transazioni dall'ultima fattura.

### Saldo precedente
Il saldo precedente è l'importo totale dovuto nell'ultima fattura.

### Pagamenti
I pagamenti sono i pagamenti totali applicati all'ultima fattura.

### Saldo in sospeso (dal ciclo di fatturazione precedente)
Eventuali rettifiche della fattura (crediti o saldi) applicate all'account dopo l'ultima fattura.


## Comprendere gli addebiti attuali
La sezione Costi attuali della fattura contiene informazioni dettagliate sui costi mensili. I collegamenti sono organizzati nelle sottosezioni seguenti.

### Costi di utilizzo
Gli addebiti per l'utilizzo corrispondono agli addebiti mensili totali per una sottoscrizione. Questi costi vengono addebitati in modo posticipato per l'utilizzo del mese precedente.

### Sconti
Gli eventuali sconti sull'utilizzo del servizio applicati all'attuale fattura sono indicati in corrispondenza di questa voce.

### Rettifiche
Le rettifiche varie sono crediti di diverso tipo o addebiti in sospeso applicati alla fattura corrente. Ad esempio, se è stata attivata l'offerta Visual Studio Ultimate con MSDN, in questa voce verrà visualizzato un credito mensile. Se la sottoscrizione viene annullata, i costi di utilizzo mensili in eccesso rispetto al credito mensile incluso nell'offerta sono visualizzati a partire dal periodo di fatturazione corrente fino alla data di annullamento della sottoscrizione.

## Informazioni del piè di pagina
![piè di pagina](./media/billing-understand-your-bill/footerinformation.png)

## Comprendere le informazioni aggiuntive
La pagina delle informazioni aggiuntive contiene riferimenti ad altre risorse utili per comprendere la fattura e alcuni collegamenti per visualizzare i dati sull'utilizzo e altri dati correlati alla fattura.

![informazioni aggiuntive](./media/billing-understand-your-bill/AdditionalInformation.png)

### Dati sull'utilizzo dettagliati
Un collegamento all'interno della descrizione inclusa nella sezione dei dati sull'utilizzo dettagliati permette di passare al portale di utilizzo e fatturazione di Azure in cui è possibile visualizzare i dati sull'utilizzo dettagliati per questa sottoscrizione. Sono ora disponibili due versioni disponibili per il download: **versione 1 CSV**, che contiene i campi di utilizzo e la convenzione di denominazione precedente e **versione 2 CSV**, che contiene i nomi descrittivi del cliente per ognuna delle categorie e altri campi che consentiranno di comprendere quali servizi di Microsoft Azure si utilizzano.

### Informazioni aggiuntive e risorse utili
Questa sezione contiene collegamenti a semplici domande relative alle dimensioni delle istanze di calcolo e agli addebiti di SQL DB, nonché collegamenti utili per rispondere ad altre domande.

### Venduto a
Prepopolato con l'indirizzo del profilo associato all'account.

### Istruzioni di pagamento
Questa sezione contiene le istruzioni di pagamento relative a dove inviare assegni, assegni urgenti o bonifici se è stato scelto il metodo di pagamento con fattura.

## Comprendere gli addebiti basati sui dati sull'utilizzo dettagliati

Come parte dell'impegno volto a consentire ai clienti di gestire facilmente l'utilizzo di Azure, è stato migliorato il file di utilizzo di download che segnala l'uso e i costi dei servizi di Azure. Il collegamento per il download contiene due versioni del file di utilizzo: **versione 1**, che utilizza il formato preesistente; **versione 2**, che include informazioni aggiuntive e nomi di colonna aggiornati nella sezione dedicata all’utilizzo giornaliero.

I costi di utilizzo sono i costi **mensili** totali di una sottoscrizione dedotti di eventuali crediti o sconti. Questi costi vengono addebitati in modo posticipato per l'utilizzo del mese precedente. Nella sezione superiore del file di vengono visualizzati i dettagli sui servizi per cui viene fornita la fattura durante il ciclo di fatturazione del mese precedente. Nella tabella seguente sono elencati i nomi delle colonne per ognuno dei file di versione CSV.

**Versione 1** | **Versione 2** | **Descrizione**|
:---------------| :---------------- | --------|
Periodo di fatturazione | Periodo di fatturazione | Indica il periodo di fatturazione in cui la risorsa è stata consumata.
Nome | Categoria misuratore | Identifica il servizio di primo livello a cui si riferisce l'utilizzo.
Tipo | Sottocategoria misuratore | In questa colonna un servizio di Azure può essere definito ulteriormente in base al tipo, che può influire sulla tariffa.
Risorsa | Nome misuratore | Identifica l'unità di misura per la risorsa consumata.
Region | Area misuratore | Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa.
SKU | SKU | Indica l'identificatore di sistema univoco per ogni risorsa di Azure.
Unità | Unità | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia.
Consumato | Quantità consumata | Indica la quantità di risorsa consumata durante il periodo di fatturazione.
Incluso | Quantità inclusa | Contiene la quantità di risorsa inclusa gratuitamente nel periodo di fatturazione corrente.
Fatturabile | Quantità in eccesso | Se la quantità consumata supera quella inclusa, la differenza viene visualizzata in questa colonna. Questa quantità viene fatturata. Per le offerte di pagamento in base al consumo senza alcuna quantità inclusa, questo totale corrisponde a quello della quantità consumata.
Entro l'impegno | Entro l'impegno | Contiene i costi della risorsa detratti dall'importo dell'impegno associato all'offerta di 6 o 12 mesi. Si noti che i costi della risorsa vengono detratti dall'importo dell'impegno in ordine cronologico.
Valuta | Valuta | Identifica la valuta per il periodo di fatturazione corrente.
Eccedenza | Eccedenza | Contiene i costi della risorsa che eccedono l'importo dell'impegno associato all'offerta di 6 o 12 mesi.
Tariffa dell'impegno | Tariffa dell'impegno | Contiene la tariffa dell'impegno basata sull'importo totale dell'impegno associato all'offerta di 6 o 12 mesi.
Tariffa | Tariffa | Indica la tariffa addebitata per ogni unità fatturabile.
Valore | Valore | Indica il risultato della moltiplicazione della colonna Fatturabile per la colonna Tariffa. Se la quantità consumata non supera quella inclusa, in questa colonna non viene visualizzato alcun importo.

## Analizzare i dati di utilizzo giornaliero
In base all'utilizzo, potrebbero essere presenti migliaia di righe relative ai dati sull'utilizzo giornaliero. Se si desidera analizzare i dati, fare clic su **Scarica utilizzo** e scegliere una versione di file variabile delimitato da virgole (CSV) per visualizzare i dati di utilizzo giornaliero per il periodo di fatturazione appropriato. È possibile scaricare un file CSV di esempio per ciascuna versione di seguito come riferimento.

 NOME | DOWNLOAD |
 :----------:| :-------: |
 Versione 1 CSV utilizzo dettagliato| [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Micorosft_Azure_Detailed_Usage_v1.csv)
 Versione 2 CSV utilizzo dettagliato | [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Micorosft_Azure_Detailed_Usage_v2.csv)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



Nel file CSV le voci sono suddivise per mostrare un elenco che indica la quantità consumata di ogni risorsa nel periodo di fatturazione corrente.

![csv snapshot](./media/billing-understand-your-bill/csvsnapshotportal.png)

Le colonne seguenti indicano i dettagli che influiscono sulle tariffe all'inizio del periodo di fatturazione:

**Versione 1** | **Versione 2** | **Descrizione** |
:---------------| :----------------| -----|
Data utilizzo | Data utilizzo | Data in cui la risorsa è stata generata.
Nome | Categoria misuratore | Identifica il servizio di primo livello a cui si riferisce l'utilizzo.
GUID risorsa | ID misuratore | ID della metrica fatturata. Viene utilizzato come identificatore per la fatturazione dei costi.
Tipo | Sottocategoria misuratore | In questa colonna un servizio di Azure può essere definito ulteriormente in base al tipo, che può influire sulla tariffa.
Risorsa | Nome misuratore | Identifica l'unità di misura per la risorsa consumata.
Region | Area misuratore | Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa.
Unità | Unità | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia.
Consumato | Quantità consumata | Contiene la quantità di risorsa consumata per il giorno corrente.
Area secondaria | Percorso della risorsa | Identifica il datacenter in cui la risorsa è in esecuzione.
Servizio | Servizio utilizzato | Questa colonna è usata per tenere traccia del singolo servizio della piattaforma Azure che potrebbe non essere identificato in modo specifico nella colonna Nome. La colonna Servizio indica il servizio specifico a cui si riferisce l'utilizzo.
N/D | Gruppo di risorse | _**Aggiunta nuova colonna.**_ Il gruppo di risorse in cui la risorsa distribuita è in esecuzione. Fare riferimento a http://azure.microsoft.com/documentation/articles/resource-group-overview/
Componente | ID istanza | Identificatore per la risorsa in esecuzione. L'identificatore contiene il nome specificato per la risorsa al momento della creazione.
N/D | Tag | _**Aggiunta nuova colonna.**_ Nuovi tipi di risorse in Azure consentono di aggiungere tag alle risorse. Fare riferimento a http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/
Informazioni aggiuntive | Informazioni aggiuntive | Metadati aggiuntivi correlati al servizio.
Informazioni servizio 1 | Informazioni servizio 1 | Questa colonna fornisce il nome del progetto a cui il servizio appartiene nella sottoscrizione.
Informazioni servizio 2 | Informazioni servizio 2 | Si tratta di un campo legacy che acquisisce i metadati specifici del servizio facoltativo.

Oltre ad alcuni nuovi campi e alle modifiche dei nomi nella Versione 2 csv, per i dati nei campi di seguito sarà applicata la formattazione standardizzata:

- **ID istanza**: il campo ID istanza rappresenta l’ID specificato dall’utente per il servizio di provisioning. Attualmente, esistono due formati in cui è rappresentata l'ID istanza: il nome della risorsa o l'ID di risorsa completo. Servizi di Microsoft Azure sono in fase di transizione per rappresentare l'ID istanza in un formato standardizzato completo di ID risorsa _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. Nel momento in cui i servizi passano al nuovo formato sarà possibile osservare il campo ID istanza che passa dal semplice nome della risorsa a ID risorsa. L'ID risorsa è il formato utilizzato dall’[API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx) per identificare le risorse in un una sottoscrizione.

![instanceid](./media/billing-understand-your-bill/instanceid.png)

- **Informazioni aggiuntive**: nella colonna Informazioni aggiuntive del CSV vengono specificati i metadati specifici del servizio. Ad esempio, un tipo di immagine per una macchina virtuale. Attualmente, un servizio genera metadati specifici del servizio in più colonne: campi Informazioni aggiuntive, Informazioni 1 servizio e Informazioni 2 servizio. I servizi di Microsoft Azure eseguiranno la standardizzazione della generazione dei metadati specifici del servizio solo nella colonna Informazioni aggiuntive. Vedere lo snapshot del formato standardizzato di seguito:

![additionalinfo\_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **Tag**: questa colonna contiene i tag delle risorse specificati dall'utente. I tag possono essere utilizzati per raggruppare i record di fatturazione. Ad esempio, è possibile utilizzare i tag per distribuire i costi per reparto mediante il servizio. Ulteriori informazioni sull’[uso dei tag per organizzare le risorse di Azure ](/resource-group-using-tags.md/). Di seguito sono indicati i servizi che supportano la creazione di tag:
    - Macchine virtuali
    - Archiviazione e
    - Servizi di rete di cui viene eseguito il provisioning mediante l’[API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![tags](./media/billing-understand-your-bill/tags.png)


Per informazioni più dettagliate su ciascun servizio, vedere le seguenti fonti aggiuntive. Questi file sono aggiornati su base mensile, pubblicati il 25 di ogni mese e disponibili a partire da **maggio 2015** per la Versione 1 CSV, e da **giugno 2015** per la Versione 2 CSV. Di seguito è riportato l'URL del file di base per i campi di download del servizio:

>>  https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_v1_MMYYYY.xlsx https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_v2_MMYYYY.xlsx



Per trovare la versione più aggiornata, immettere l'anno e il mese. Se si cerca un file di maggio 2015, immettere **052015** nella sezione _**"MMYYYY"**_ nell'URL precedente. Questo foglio di calcolo fornisce un elenco di tutte le combinazioni possibili dei campi correlati al servizio nel **report relativo al download di utilizzo**.

## Altre risorse
Passare alla sezione **Gestire account, sottoscrizioni e ruoli amministrativi** di [Gestione dei servizi](https://msdn.microsoft.com/library/azure/dn578292.aspx) per alcuni collegamenti molto utili:

- [Gestire il metodo di pagamento](https://msdn.microsoft.com/library/azure/dn736054.aspx)

- [Modificare le informazioni di pagamento per una carta di credito esistente](https://msdn.microsoft.com/library/azure/dn736053.aspx)

- [Aggiungere una nuova carta di credito come un metodo di pagamento](https://msdn.microsoft.com/library/azure/dn736057.aspx)

- [Modificare la carta di credito per l'account di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn736050.aspx)

- [Operazioni da eseguire se la sottoscrizione di Azure viene disabilitata](https://msdn.microsoft.com/library/azure/dn736049.aspx)



<!--Image references-->

<!---HONumber=July15_HO5-->