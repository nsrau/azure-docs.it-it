---
title: Gestire IoT Central dal portale di Azure | Microsoft Docs
description: Gestire IoT Central dal portale di Azure.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 20b1c7500587324f6f7dbb5cc679a3603eff56bd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963828"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gestire IoT Central dal portale di Azure 
Oltre a creare e gestire le applicazioni IoT Central dal sito Web di IoT Central, è anche possibile gestire IoT Central dal portale di Azure. Questo articolo illustra tutte le potenzialità e come sfruttarle.

## <a name="create-iot-central-applications"></a>Creare applicazioni IoT Central
Per creare una nuova applicazione, passare al [portale di Azure](https://ms.portal.azure.com) e fare clic su "Crea una risorsa" nel menu di spostamento principale a sinistra. 

![Portale di gestione: menu di spostamento](media/howto-manage-iot-central-from-portal/image0.png)

Nella barra di ricerca digitare il termine "IoT Central".

![Portale di gestione: ricerca](media/howto-manage-iot-central-from-portal/image0a.png)

Fare clic sulla voce IoT Central Application nei risultati della ricerca.

![Portale di gestione: risultati della ricerca](media/howto-manage-iot-central-from-portal/image0b.png)

A questo punto, fare clic sul pulsante "Crea" per visualizzare il modulo da compilare.

![Portale di gestione: risorsa IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Compilare tutti i campi del modulo. Questo modulo è analogo a quello che si compila per creare applicazioni dal sito Web di IoT Central. Per altre informazioni su come compilare ogni campo, consultare la guida introduttiva [Creare l'applicazione IoT Central](quick-deploy-iot-central.md). 

![Portale di gestione: creare la risorsa IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Dopo aver compilato tutti i campi, fare clic sul pulsante "Crea".

## <a name="manage-existing-iot-central-applications"></a>Gestire le applicazioni IoT Central esistenti
Se si dispone già di un'applicazione Azure IoT Central è possibile eliminarla, spostarla in una sottoscrizione diversa o in un altro gruppo di risorse nel portale di Azure. Non è possibile visualizzare applicazioni di prova nel portale di Azure perché nessuna sottoscrizione supporta tali versioni di valutazione.

Per iniziare, fare clic su "Tutte le risorse" nel menu di spostamento principale a sinistra. Digitare il nome dell'applicazione nella casella di ricerca e individuarla nel proprio elenco di risorse. Quindi, fare clic sull'applicazione IoT Central che si desidera gestire.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image2.png)

Per passare all'applicazione, fare clic sull'URL dell'applicazione IoT Central.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image3.png)

Per spostare l'applicazione in un gruppo di risorse diverso, scegliere il collegamento **modifica** accanto al gruppo di risorse. Selezionare il gruppo di risorse nel quale si vuole eseguire la migrazione di questa applicazione nella finestra di dialogo visualizzata.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image4.png)

Per spostare l'applicazione in una sottoscrizione diversa, scegliere il collegamento **modifica** accanto alla sottoscrizione. Selezionare la sottoscrizione nella quale si vuole eseguire la migrazione di questa applicazione nella finestra di dialogo visualizzata.

![Portale di gestione: gestione delle risorse](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dal portale di Azure, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)