<properties 
	pageTitle="Usare i set di dati di esempio in Machine Learning Studio | Microsoft Azure" 
	description="Descrizione dei set di dati usati nei modelli di esempio inclusi in ML Studio. È possibile usare questi set di dati di esempio per gli esperimenti." 
	keywords="data sets,datasets,ml studio,sample data sets"
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Usare i set di dati di esempio in Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Quando si crea una nuova area di lavoro in Azure Machine Learning, per impostazione predefinita è inclusa una serie di set di dati e di esperimenti di esempio. Molti di questi set di dati di esempio vengono usati dai modelli di esempio nella [raccolta di modelli di Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/models/), altri sono inclusi come esempi dei diversi tipi di dati usati in Machine Learning.

Alcuni di questi set di dati sono disponibili nell'archivio BLOB di Azure. Per questi set di dati è disponibile un collegamento diretto nelle tabelle seguenti. Questi set di dati possono essere usati negli esperimenti con il modulo [Reader][reader]. Per vedere un esempio della modalità di accesso a un set di dati, visualizzare le proprietà del modulo [Reader][reader] nell'esperimento di esempio associato al set di dati.

I restanti set di dati di esempio sono elencati in **Set di dati salvati** nella tavolozza del modulo a sinistra dell'area di disegno dell'esperimento quando si apre o si crea un nuovo esperimento in ML Studio. Per usare uno qualsiasi di questi set di dati in un esperimento personalizzato, trascinarlo sull'area di disegno dell'esperimento.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Set di dati di esempio associati ai modelli nella Raccolta di Machine Learning

I set di dati seguenti sono usati nei modelli di esempio disponibili nella [raccolta di modelli di Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/models/). Per altre informazioni sul modello e l'esperimento associato, seguire il collegamento disponibile nella tabella per vedere i dettagli del modello.

<table>

<tr>
  <th align=left>Modello di esempio</th>
  <th align=left>Esperimento associato</th>
  <th align=left>Nome del set di dati</th>
  <th align=left>Descrizione del set di dati</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">Color quantization using K-Means clustering</a></td>
  <td valign=top>Esperimento di esempio - Color Based Image Compression using K-Means Clustering - Development</td>
  <td valign=top>Bill Gates RGB Image</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">Credit risk prediction</a></td>
  <td valign=top>Esperimento di esempio - German Credit - Development</td>
  <td valign=top>German Credit Card UCI dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">CRM task</a></td>
  <td rowspan=4 valign=top>Esperimenti di esempio - CRM - Development</td>
  <td valign=top>CRM Dataset Shared</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM Appetency Labels Shared</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>CRM Churn Labels Shared</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM Upselling Labels Shared</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">Finding similar companies</a></td>
  <td valign=top>Esperimento di esempio - S &amp; P 500 Company Clustering - Development</td>
  <td valign=top>Wikipedia SP 500 Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">Flight delay prediction</a></td>
  <td rowspan=3 valign=top>Esperimento di esempio - Flight Delay Prediction - Development</td>
  <td valign=top>Airport Codes Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Flight Delays Data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Weather Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">Network intrusion detection</a></td>
  <td valign=top>Esperimento di esempio - Network Intrusion Detection - Development</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">Prediction of the number of bike rentals</a></td>
  <td valign=top>Esperimento di esempio - Demand Forecasting of Bikes</td>
  <td valign=top>Bike Rental UCI dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">Prediction of student performance</a></td>
  <td valign=top>Esperimento di esempio - Student Performance - Development</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">Sentiment analysis</a></td>
  <td valign=top>Esperimento di esempio - Sentiment Classification - Development</td>
  <td valign=top>Book Reviews from Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## Set di dati usati negli esperimenti di esempio di Machine Learning Studio

I set di dati seguenti sono usati dagli esperimenti di esempio inclusi in [Machine Learning Studio](https://studio.azureml.net/Home).

Questi set di dati sono elencati in **Set di dati salvati** nella tavolozza del modulo a sinistra dell'area di disegno dell'esperimento quando si apre o si crea un nuovo esperimento in ML Studio. Per usare uno qualsiasi di questi set di dati in un esperimento personalizzato, trascinarlo sull'area di disegno dell'esperimento.

È possibile trovare tutti gli esperimenti di esempio elencati di seguito nella scheda **Samples** della pagina **Experiments** in ML Studio. Per creare una copia di un esperimento di esempio che è possibile modificare, aprire l'esperimento in ML Studio e fare clic su **Save As**.


<table>

<tr>
  <th align=left>Nome del set di dati</th>
  <th align=left>Esperimento di esempio</th>
  <th align=left>Descrizione del set di dati</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Breast Cancer Features
  <td valign=top>Esperimento di esempio - Breast Cancer - Development</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Breast Cancer Info</td>
  <td valign=top><i>uguale</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>Esperimento di esempio - Direct Marketing - Development - Training</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB Movie Titles</td>
  <td valign=top>Esperimento di esempio - Movie Recommender - Development</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Movie Tweets</td>
  <td valign=top>Esperimento di esempio - Ranking of Movie Tweets According to their Future Popularity - Development</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>Esperimento di esempio - News Categorization - Development</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>uguale</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top><i>uguale</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Vari set di dati di esempio in Machine Learning Studio

I set di dati seguenti sono inclusi in [Machine Learning Studio](https://studio.azureml.net/Home) per fornire esempi dei diversi tipi di dati usati in Machine Learning. Sono disponibili in **Saved Datasets** nella tavolozza del modulo a sinistra dell'area di disegno dell'esperimento quando si apre o si crea un nuovo esperimento in ML Studio. Per usare uno qualsiasi di questi set di dati in un esperimento personalizzato, trascinarlo sull'area di disegno dell'esperimento.

<table>
<tr>
  <th align=left>Nome del set di dati</th>
  <th align=left>Descrizione</th>
</tr>

<tr>
  <td valign=top>Adult Census Income Binary Classification dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>Automobile price data (Raw)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>Blood donation data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>Breast cancer data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Energy Efficiency Regression data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>Flight on-time performance (Raw)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Forest fires data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>Iris two class data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>MPG data for various automobiles</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Pima Indians Diabetes Binary Classification dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Restaurant customer data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Restaurant feature data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Restaurant ratings</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Steel Annealing multi-class dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Telescope data</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->