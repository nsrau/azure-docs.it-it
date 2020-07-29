---
title: Creare pool di risorse con il mapping della sottoscrizione di Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Viene descritto come creare pool di risorse per il cloud privato tramite il mapping delle sottoscrizioni di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77014964"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Creare pool di risorse per il cloud privato con mapping della sottoscrizione di Azure
Il mapping delle sottoscrizioni di Azure consente di creare pool di risorse per il cloud privato dai pool di risorse vSphere disponibili. Nel portale di CloudSimple è possibile visualizzare e gestire la sottoscrizione di Azure per i cloud privati.

> [!NOTE]
> Il mapping di un pool di risorse esegue anche il mapping dei pool di risorse figlio. Impossibile eseguire il mapping di un pool di risorse padre se è già stato eseguito il mapping di un pool di risorse figlio.

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md).
2. Aprire la pagina **risorse** e selezionare **mapping sottoscrizioni di Azure**.  
3. Fare clic su **Modifica mapping della sottoscrizione di Azure**.  
4. Per mappare i pool di risorse disponibili, selezionarli a sinistra e fare clic sulla freccia rivolta verso destra. 
5. Per rimuovere i mapping, selezionarli a destra e fare clic sulla freccia rivolta verso sinistra. 

    ![Sottoscrizioni Azure](media/resources-azure-mapping.png)

6. Fare clic su **OK**.
