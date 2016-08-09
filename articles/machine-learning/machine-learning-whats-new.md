<properties
	pageTitle="Novità di Machine Learning | Microsoft Azure"
	description="Nuove funzionalità disponibili in Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="v-donglo"/>

# Novità in Azure Machine Learning

A luglio 2016 gli aggiornamenti di Microsoft Azure Machine Learning (ML) forniscono le funzionalità seguenti:

* I servizi Web vengono ora gestiti come risorse di Azure gestite tramite le interfacce di [Azure Resource Manager](../resource-group-overview.md) consentendo i miglioramenti seguenti:
	* Sono disponibili nuove [API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) per distribuire e gestire i servizi Web basati su Resource Manager.
	* Il nuovo portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/) consente di gestire tutti gli aspetti del servizio Web da un'unica posizione.
* Incorpora un nuovo modello di distribuzione dei servizi Web basato sulla sottoscrizione, con più aree usando API basate su Resource Manager che si avvalgono del provider di risorse di Resource Manager per i servizi Web.
* Introduce nuovi [piani tariffari](https://azure.microsoft.com/pricing/details/machine-learning/) e capacità di gestione dei piani avvalendosi del nuovo provider di risorse di Resource Manager per la fatturazione.
	* È ora possibile [distribuire il servizio Web in più aree](machine-learning-how-to-deploy-to-multiple-regions.md) senza dover creare una nuova sottoscrizione in ogni area.
* Fornisce le [statistiche di uso](machine-learning-manage-new-webservice.md) del servizio Web.
* Semplifica i test della funzione del servizio di richiesta/risposta di Azure ML.
* Fornisce una nuova pagina di test del servizio esecuzione batch (BES) con la cronologia di invio di processi e dati di esempio.

Inoltre, Machine Learning Studio è stato aggiornato per consentire di distribuire il nuovo modello di servizio Web o continuare a distribuire il modello di servizio Web classico.

<!---HONumber=AcomDC_0727_2016-->