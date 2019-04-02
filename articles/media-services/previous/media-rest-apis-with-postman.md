---
title: Configurare Postman per le chiamate API REST di Servizi multimediali di Azure
description: Informazioni su come configurare Postman per le chiamate API REST di Servizi multimediali.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 5d0b5a57f3fe587a06a102c958b17dbf2a73225c
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805143"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurare Postman per le chiamate API REST di Servizi multimediali  

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche [materiale sussidiario di migrazione dalla v2 alla v3](../latest/migrate-from-v2-to-v3.md)

In questa esercitazione viene illustrato come configurare **Postman** in modo da usarlo per chiamare le API REST di Servizi multimediali di Azure (AMS). L'esercitazione illustra come importare i file di ambiente e raccolta in **Postman**. La raccolta contiene definizioni raggruppate delle richieste HTTP che chiamano le API REST di Servizi multimediali di Azure (AMS). Il file di ambiente contiene le variabili usate dalla raccolta.

Questo ambiente e raccolta viene usato negli articoli che illustrano come eseguire varie attività con le API REST di Servizi multimediali di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Installare il client REST di [Postman](https://www.getpostman.com/) per eseguire le API REST mostrate in alcune delle esercitazioni REST di AMS. 

    Si sta usando **Postman** ma si può usare qualsiasi strumento REST. Altre alternative possibili: **Visual Studio Code** con il plug-in REST o **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurare l'ambiente 

1. Creare un file .json che contenga le variabili di ambiente usate nelle esercitazioni di AMS. Denominare il file (ad esempio, **AzureMediaServices.postman_environment.json**). Aprire il file e incollare il codice che definisce l'ambiente di Postman da [questo elenco di codici](postman-environment.md). 
2. Aprire **Postman**.
3. A destra dello schermo, selezionare l'opzione **Manage environment** (Gestisci ambiente).

    ![Caricare un file](./media/media-services-rest-upload-files/postman-create-env.png)
4. Dalla finestra di dialogo **Manage environment** (Gestisci ambiente), fare clic su **Import** (Importa).
5. Sfogliare e selezionare il file **AzureMediaServices.postman_environment.json**.
6. Viene aggiunto l'ambiente **AzureMedia**.
7. Chiudere la finestra di dialogo.
8. Selezionare l'ambiente **AzureMedia**.

    ![Caricare un file](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurare la raccolta

1. Creare un file .json che contenga la raccolta di **Postman** con tutte le operazioni necessarie per caricare un file in Servizi multimediali. Denominare il file (ad esempio, **AzureMediaServicesOperations.postman_collection.json**). Aprire il file e incollare il codice che definisce l'ambiente di **Postman** da [questo elenco di codici](postman-collection.md).
2. Fare clic su **Import** (Importa) per importare il file di raccolta.
3. Scegliere il file **AzureMediaServicesOperations.postman_collection.json**.

    ![Caricare un file](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo [Caricare gli asset](media-services-rest-upload-files.md).  
