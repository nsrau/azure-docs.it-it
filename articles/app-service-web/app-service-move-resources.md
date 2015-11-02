<properties
	pageTitle="Spostamento di risorse di app Web a un altro gruppo di risorse"
	description="Descrive gli scenari dove è possibile spostare le app Web e servizi app da un gruppo di risorse a un altro."
	services="app-service"
	documentationCenter=""
	authors="ZainRizvi"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="zarizvi"/>
	
# Configurazioni di spostamento supportate

È possibile spostare le risorse app Web di Azure usando l'[API di spostamento delle risorse ARM](../resource-group-move-resources.md).

App Web di Microsoft Azure supporta attualmente i seguenti scenari di spostamento:

* Spostamento dell'intero contenuto di un gruppo di risorse (app Web, piani di servizio app e certificati) in un altro gruppo di risorse 
	* Nota: il gruppo di risorse di destinazione non può contenere alcuna risorsa Microsoft.Web in questo scenario
* Spostamento di singole app Web in un gruppo di risorse diverse, mentre sono ancora ospitate nel relativo piano di servizio app corrente (il piano di servizio app rimane nel gruppo di risorse precedente)

<!---HONumber=Oct15_HO4-->