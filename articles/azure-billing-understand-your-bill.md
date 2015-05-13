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
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2015"
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

    ![Riepilogo - cronologia fatturazione -1](./media/azure-billing-understand-your-bill/ContentViewaBillforMA1.png)


4. La sezione **Cronologia di fatturazione** include un elenco dei rendiconti per i sei periodi di fatturazione più recenti, oltre al periodo attuale non fatturato. Il rendiconto per il periodo attuale è una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati alla data corrente. È possibile che la fattura mensile non corrisponda a questa stima.

    ![Riepilogo - cronologia fatturazione 2](./media/azure-billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Fare clic su **Visualizza estratto conto corrente** per visualizzare una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati alla data corrente. È possibile che la fattura mensile non corrisponda a questa stima.

    ![Riepilogo - cronologia fatturazione 3](./media/azure-billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Riepilogo - cronologia fatturazione 4](./media/azure-billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Fare clic su **Scarica fattura** per visualizzare una copia della fattura precedente.

    ![Riepilogo - cronologia fatturazione 5](./media/azure-billing-understand-your-bill/ContentViewaBillforMA5.png)



**Di seguito sono riportate alcune fatture di esempio delle offerte Visual Studio Ultimate con MSDN e Pagamento in base al consumo.**

*Eventuali crediti mensili non usati non possono essere inclusi nei mesi successivi.

 **NOME DELL'OFFERTA** | **TIPO DI OFFERTA** | **INCLUDE SERVIZI GRATUITI** | **DOWNLOAD** |
 :--------- |:-------- | :----------------: | :-------|
Azure MSDN-Visual Studio Ultimate| Programma di vantaggi | Sì\* | [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_MSDN_Sample.pdf)
Pagamento in base al consumo | Consumo | No| [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_PAYG_Sample.pdf)
Utilizzo dettagliato - CSV | N/D | N/D | [File di esempio](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_Detailed_Usage_v1_csv.xlsx)


## Intestazione - Informazioni sul cliente

La sezione relativa alle informazioni sul cliente identifica le informazioni pertinenti relative all'utilizzo e al profilo dell'utente specifico. ![intestazione](./media/azure-billing-understand-your-bill/Header.png)

### N. fattura
Identificatore univoco della fattura per scopi di registrazione.

### Ciclo di fatturazione
Intervallo di tempo durante il quale è avvenuto l'utilizzo.

### Data fattura
Data in cui è stata generata la fattura.

### Metodo di pagamento
Tipo di pagamento usato per l'account \(fattura o carta di credito\).

### Indirizzo di fatturazione
Indirizzo per i pagamenti per Microsoft Azure.

### Indirizzo di posta elettronica proprietario account
Indirizzo di posta elettronica dell'account associato all'account Microsoft Azure.



## Comprendere il riepilogo della fattura
La sezione relativa al riepilogo della fattura riassume le transazioni avvenute dall'ultima fattura e gli addebiti sull'attuale utilizzo.

![riepilogo fattura](./media/azure-billing-understand-your-bill/InvoiceSummary.png)

Nella sezione della fattura relativa a saldi, pagamenti e crediti sono riepilogate le transazioni dall'ultima fattura.

### Saldo precedente
Il saldo precedente è l'importo totale dovuto nell'ultima fattura.

### Pagamenti
I pagamenti sono i pagamenti totali applicati all'ultima fattura.

### Saldo in sospeso \(dal ciclo di fatturazione precedente\)
Eventuali rettifiche della fattura \(crediti o saldi\) applicate all'account dopo l'ultima fattura.


## Comprendere gli addebiti attuali
La sezione Costi attuali della fattura contiene informazioni dettagliate sui costi mensili. I collegamenti sono organizzati nelle sottosezioni seguenti.

### Costi di utilizzo
Gli addebiti per l'utilizzo corrispondono agli addebiti mensili totali per una sottoscrizione. Questi costi vengono addebitati in modo posticipato per l'utilizzo del mese precedente.

### Sconti
Gli eventuali sconti sull'utilizzo del servizio applicati all'attuale fattura sono indicati in corrispondenza di questa voce.

### Rettifiche
Le rettifiche varie sono crediti di diverso tipo o addebiti in sospeso applicati alla fattura corrente. Ad esempio, se è stata attivata l'offerta Visual Studio Ultimate con MSDN, in questa voce verrà visualizzato un credito mensile. Se la sottoscrizione viene annullata, i costi di utilizzo mensili in eccesso rispetto al credito mensile incluso nell'offerta sono visualizzati a partire dal periodo di fatturazione corrente fino alla data di annullamento della sottoscrizione.

## Informazioni del piè di pagina
![piè di pagina](./media/azure-billing-understand-your-bill/footerinformation.png)

## Comprendere le informazioni aggiuntive
La pagina delle informazioni aggiuntive contiene riferimenti ad altre risorse utili per comprendere la fattura e alcuni collegamenti per visualizzare i dati sull'utilizzo e altri dati correlati alla fattura.

![informazioni aggiuntive](./media/azure-billing-understand-your-bill/AdditionalInformation.png)

### Dati sull'utilizzo dettagliati
Un collegamento all'interno della descrizione inclusa nella sezione dei dati sull'utilizzo dettagliati permette di passare al portale di utilizzo e fatturazione di Azure in cui è possibile visualizzare i dati sull'utilizzo dettagliati per questa sottoscrizione.

### Informazioni aggiuntive e risorse utili
Questa sezione contiene collegamenti a semplici domande relative alle dimensioni delle istanze di calcolo e agli addebiti di SQL DB, nonché collegamenti utili per rispondere ad altre domande.

### Venduto a
Prepopolato con l'indirizzo del profilo associato all'account.

### Istruzioni di pagamento
Questa sezione contiene le istruzioni di pagamento relative a dove inviare assegni, assegni urgenti o bonifici se è stato scelto il metodo di pagamento con fattura.

## Comprendere gli addebiti basati sui dati sull'utilizzo dettagliati

I costi di utilizzo sono i costi mensili totali di una sottoscrizione dedotti di eventuali crediti o sconti. Questi costi vengono addebitati in modo posticipato per l'utilizzo del mese precedente. Quando si visualizza un elenco di costi di utilizzo nella fattura, nelle colonne seguenti sono indicati i dettagli relativi alle voci fatturate. È possibile scaricare un [file di esempio](https://understandingyourbill.blob.core.windows.net/appendices/UnderstandDetailedUsageCharges.xlsx) che mostra i valori per ogni colonna.

### Nome servizio
Identifica il servizio di primo livello a cui si riferisce l'utilizzo.

### Tipo di servizio
In questa colonna un servizio di Azure può essere definito ulteriormente in base al tipo, che può influire sulla tariffa.

### Risorsa
Identifica l'unità di misura per la risorsa consumata.

### Region
Aree in cui i dati sull'utilizzo si applicano a e riguardano prevalentemente Servizi cloud, Macchine virtuali e i trasferimenti di dati \(esclusa la rete CDN\), in quanto queste tariffe possono variare a seconda dell'area. L'area per CDN è mappata alla posizione del data center da cui è stato gestito il traffico.

### Consumato
Indica la quantità di risorsa consumata durante il periodo di fatturazione.

### Incluso
Indica la quantità inclusa ogni mese nell'offerta.

### Fatturabile
Se la quantità consumata supera quella inclusa, la differenza viene visualizzata in questa colonna. Questa quantità viene fatturata. Per le offerte di pagamento in base al consumo senza alcuna quantità inclusa, questo totale corrisponde a quello della quantità consumata.

### Tariffa
Indica la tariffa addebitata per ogni unità fatturabile.

### Valore
Indica il risultato della moltiplicazione della colonna Fatturabile per la colonna Tariffa. Se la quantità consumata non supera quella inclusa, in questa colonna non viene visualizzato alcun importo.

## Analizzare i dati di utilizzo giornaliero
In base all'utilizzo, potrebbero essere presenti migliaia di righe relative ai dati sull'utilizzo giornaliero. Per analizzare i dati, è possibile fare clic su Scarica utilizzo per esportare i dati relativi all'utilizzo giornaliero per il periodo di fatturazione attuale in un file CSV, che può essere quindi visualizzato in Microsoft Office Excel e altri programmi. È possibile scaricare un file CSV di esempio come riferimento.


![Riepilogo Pagamento a consumo](./media/azure-billing-understand-your-bill/AnalyzeDailyUsageData1.png)

Nel file CSV le voci sono suddivise per mostrare un elenco che indica la quantità consumata di ogni risorsa nel periodo di fatturazione corrente.

![csv snapshot](./media/azure-billing-understand-your-bill/csvsnapshotportal.png)

Le colonne seguenti indicano i dettagli che influiscono sulle tariffe all'inizio del periodo di fatturazione:

**NOME COLONNA** | **DESCRIZIONE** |
:---------------| :----------------|
Periodo di fatturazione | Indica il periodo di fatturazione in cui la risorsa è stata consumata.
Nome | Identifica il nome del servizio usato.
Tipo | In alcuni casi, in questa colonna un servizio di Azure può essere definito ulteriormente in base al tipo, che può influire sulla tariffa.
Risorsa | Identifica il tipo di risorsa consumata. Trasferimento di dati, ore di calcolo e transazioni di archiviazione sono ad esempio tipi di risorse.
Region | Identifica la posizione del data center per determinati servizi il cui prezzo dipende dalla posizione stessa, ad esempio i trasferimenti di dati.
SKU | Indica l'identificatore di sistema univoco per ogni risorsa di Azure.
Unità | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia.
Consumato | Contiene la quantità di risorsa consumata per il giorno corrente.
Incluso | Contiene la quantità di risorsa inclusa gratuitamente nel periodo di fatturazione corrente.
Fatturabile | Contiene la quantità di risorsa fatturabile nel periodo di fatturazione corrente.
Entro l'impegno | Contiene i costi della risorsa detratti dall'importo dell'impegno associato all'offerta di 6 o 12 mesi. Si noti che i costi della risorsa vengono detratti dall'importo dell'impegno in ordine cronologico.
Valuta | Identifica la valuta per il periodo di fatturazione corrente.
Eccedenza | Contiene i costi della risorsa che eccedono l'importo dell'impegno associato all'offerta di 6 o 12 mesi.
Tariffa dell'impegno | Contiene la tariffa dell'impegno basata sull'importo totale dell'impegno associato all'offerta di 6 o 12 mesi.
Tariffa | Contiene la tariffa del pagamento in base al consumo per la risorsa.
Valore | Moltiplica la colonna Tariffa per la colonna Fatturabile per calcolare il costo esteso.

Fare riferimento alle descrizioni dettagliate delle colonne sopra nella sezione **Addebiti per l'utilizzo** per informazioni complete su queste colonne. Le colonne seguenti includono informazioni aggiuntive che potrebbero risultare utili. A seconda della risorsa, alcune delle colonne potrebbero essere vuote.

### Data utilizzo
Data in cui è avvenuto l'utilizzo.

### GUID risorse
GUID della metrica fatturata.

### Area secondaria
Identifica la posizione specifica del servizio emesso, ovvero la posizione del data center.

### Servizio
Questa colonna è usata per tenere traccia del singolo servizio della piattaforma Azure che potrebbe non essere identificato in modo specifico nella colonna Nome. La colonna Servizio indica il servizio specifico a cui si riferisce l'utilizzo.

### Componente
Se applicabile, questa colonna identifica ulteriormente la risorsa consumata. Per i servizi di calcolo ospitati, ad esempio, questa colonna indica le dimensioni della macchina virtuale, se non sono già specificate in Risorse.

### Informazioni servizio 1
Questa colonna fornisce il nome del progetto a cui il servizio appartiene nella sottoscrizione.

### Informazioni servizio 2
Questa colonna contiene informazioni specifiche sui servizi di terze parti.

### Informazioni aggiuntive
Questa colonna mostra dati aggiuntivi per alcuni servizi.

Per informazioni più dettagliate, vedere i file relativi a informazioni aggiuntive seguenti. Questi file sono aggiornati su base mensile, pubblicati il giorno 25 di ogni mese e disponibili a partire da maggio 2015. Di seguito è riportato l'URL del file di base per i campi di download del servizio:

>>  https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_v1_MMYYYY.xlsx


Per trovare la versione più aggiornata, immettere l'anno e il mese. Se si cerca un file di maggio 2015, immettere **052015** nella sezione _**"MMYYYY"**_ nell'URL precedente. Questo foglio di calcolo fornisce un elenco di tutte le combinazioni possibili dei campi correlati al servizio nel **report relativo al download di utilizzo**.


<!--Image references-->

<!--HONumber=52-->
