---
title: Creare pool di risorse con il mapping della sottoscrizione di Azure
description: Viene descritto come creare pool di risorse per il cloud privato AVS tramite il mapping delle sottoscrizioni di Azure
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014964"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Creare pool di risorse per il cloud privato AVS con mapping della sottoscrizione di Azure
Il mapping delle sottoscrizioni di Azure consente di creare pool di risorse per il cloud privato AVS dai pool di risorse vSphere disponibili. Nel portale di AVS è possibile visualizzare e gestire la sottoscrizione di Azure per i cloud privati AVS.

> [!NOTE]
> Il mapping di un pool di risorse esegue anche il mapping dei pool di risorse figlio. Impossibile eseguire il mapping di un pool di risorse padre se è già stato eseguito il mapping di un pool di risorse figlio.

1. [Accedere al portale AVS](access-cloudsimple-portal.md).
2. Aprire la pagina **risorse** e selezionare **mapping sottoscrizioni di Azure**.  
3. Fare clic su **Modifica mapping della sottoscrizione di Azure**.  
4. Per mappare i pool di risorse disponibili, selezionarli a sinistra e fare clic sulla freccia rivolta verso destra. 
5. Per rimuovere i mapping, selezionarli a destra e fare clic sulla freccia rivolta verso sinistra. 

    ![Sottoscrizioni Azure](media/resources-azure-mapping.png)

6. Fare clic su **OK**.
