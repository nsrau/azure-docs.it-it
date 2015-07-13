<properties 
	pageTitle="App Machine Learning: Servizio di rilevamento delle anomalie | Microsoft Azure" 
	description="API di rilevamento delle anomalie è un esempio compilato con Microsoft Azure Machine Learning che consente di rilevare anomalie nei dati della serie temporale con i valori numerici disposti in modo uniforme nel tempo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="05/05/2015" 
	ms.author="luisca"/>


# Servizio di rilevamento delle anomalie Machine Learning#

##Panoramica

API di rilevamento delle anomalie è un esempio compilato con Azure Machine Learning che consente di rilevare anomalie nei dati della serie temporale con i valori numerici disposti in modo uniforme nel tempo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Questo servizio di rilevamento delle anomalie è in grado di rilevare i diversi tipi di anomalie seguenti nei dati della serie temporale:

1. Tendenze positive e negative: quando, ad esempio, viene monitorato l'utilizzo della memoria di elaborazione, una tendenza verso l'alto è indicativa di una perdita di memoria,

2. Aumento dell’intervallo dinamico di valori: ad esempio, quando vengono monitorate le eccezioni generate da un servizio, ogni aumento dell'intervallo dinamico dei valori può indicare l'instabilità dell'integrità del servizio e

3. Picchi e flessioni: ad esempio, durante il monitoraggio il numero di errori di accesso a un servizio o il numero di transazioni in un sito di e-commerce, i picchi o le flessioni possono indicare un comportamento anomalo.


Questi rilevatori tengono traccia e riportano le modifiche in corso e future ai valori. Non richiedono la sintonizzazione della soglia ad hoc e i punteggi possono essere utilizzati per controllare la frequenza falsa positiva. Il rilevamento delle anomalie API è utile in diversi scenari come il monitoraggio del servizio tenendo traccia degli indicatori KPI nel tempo, l'utilizzo della metrica come numero di ricerca, numeri di clic, contatori di prestazioni come memoria, CPU, letture dei file ecc. nel tempo.

##Definizione dell'API

Il servizio offre un’API basata su REST in HTTPS che può essere usata in modi diversi, ad esempio un’applicazione Web o mobile, R, Python, Excel, ecc. È disponibile un’[applicazione Web di Azure](http://anomalydetection-aml.azurewebsites.net/) che consente di eseguire il servizio Web di rilevamento delle anomalie sui dati e di visualizzare i risultati.

È inoltre possibile inviare i dati della serie temporale al servizio tramite una chiamata API REST che esegue una combinazione dei tre tipi di anomalie descritti in precedenza. Il servizio viene eseguito sulla piattaforma AzureML Machine Learning che consente di ridimensionare le esigenze aziendali senza problemi e fornisce i contratti di servizio pari al 99,95%.

Nella figura seguente viene illustrato un esempio di anomalie rilevate nelle serie temporali utilizzando il framework di cui sopra. La serie temporale dispone di due modifiche di livello distinte e di 3 picchi. I punti rossi mostrano il momento in cui è stata rilevata la modifica del livello, mentre le frecce rosse verso l'alto indicano i picchi rilevati.


![][1]

##Input

L'API accetta 2 parametri di input

1. "data" rappresenta l'input della serie temporale nel formato: t1=v1;t2=v2;... 

 
	Dati di esempio:
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "params" impostato su "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3" che controlla la sensibilità dei rilevatori del picco. I valori più alti intercettano i picchi superiori e viceversa.

	URL di esempio con parametri di input citato in precedenza:

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###Output###

L'API esegue questi rilevatori sui dati della serie temporale e restituisce i punteggi delle anomalie in ogni punto nel tempo. Questo output può essere analizzato utilizzando un parser semplice, come illustrato in [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/). Viene fornito il codice di esempio che illustra come connettersi all'API e analizzare l'output. Gli avvisi generati possono essere visualizzati su un dashboard e/o passati a degli esperti in grado di intraprendere azioni correttive.

Esempio di output per il precedente input di esempio:

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

che è una rappresentazione della tabella riportata di seguito:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Time</th>
   <th align="left" valign="middle">Dati</th>
   <th align="left" valign="middle">Tspike</th>
   <th align="left" valign="middle">Zspike</th>
   <th align="left" valign="middle">Valori martingale</th>
   <th align="left" valign="middle">Indicatore di avviso</th>
   <th align="left" valign="middle">Valori martingale (2)</th>
   <th align="left" valign="middle">Indicatore di avviso (2)</th>
   </tr>
<tr>
   <td valign="middle">9/21/2014 11:05</td>
   <td valign="middle">3</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.687952591</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.687952591</td>
   <td valign="middle">0</td>
   </tr>
<tr>
<td valign="middle">9/21/2014 11:10</td>
   <td valign="middle">9.09</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.070304977</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.884548154</td>
   <td valign="middle">0</td>
    </tr>
<tr>
<td valign="middle">9/21/2014 11:15</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.051862374</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.1738002814</td>
   <td valign="middle">0</td>
   </tr>
   </table>

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=July15_HO1-->