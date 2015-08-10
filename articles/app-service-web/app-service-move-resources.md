<properties
	pageTitle="Spostamento di risorse di app Web a un altro gruppo di risorse"
	description="Descrive gli scenari dove è possibile spostare le app Web e servizi app da un gruppo di risorse a un altro."
	services="app-service\web"
	documentationCenter=""
	authors="ZainRizvi"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="zarizvi"/>
	
# Configurazioni di spostamento supportate

È possibile spostare le risorse app Web di Azure (app Web e servizi app) usando l'[API di spostamento delle risorse ARM](../resource-group-move-resources.md).

App Web di Microsoft Azure supporta attualmente i seguenti scenari di spostamento:

* Spostamento dell'intero contenuto di un gruppo di risorse a un altro gruppo di risorse
	* Nota: il gruppo di risorse di destinazione non può contenere alcuna risorsa Microsoft.Web in questo scenario
* Spostamento di singole app Web a un gruppo di risorse diverse, mentre sono ancora ospitate nel relativo servizio app corrente (il servizio app rimane nel gruppo di risorse precedente)

<!---HONumber=July15_HO5-->