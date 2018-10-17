---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019093"
---
1. Aprire Visual Studio 2017 Community Edition.
1. Creare un nuovo progetto **App console (.Net Core)** e assegnare il nome `QnaMakerQuickstart` al progetto. Accettare le impostazioni predefinite per le altre impostazioni.
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto, **QnaMakerQuickstart**, quindi scegliere **Gestisci pacchetti NuGet**.
1. Nella finestra NuGet selezionare **Browser**, quindi cercare **Newtonsoft.JSON** e installare il pacchetto. Questo pacchetto viene usato per analizzare il codice JSON restituito dalla risposta HTTP di Qna Maker. 