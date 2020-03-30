---
title: Creare pool di risorse con il mapping della sottoscrizione di AzureCreate resource pools with Azure subscription mapping
titleSuffix: Azure VMware Solution by CloudSimple
description: Descrive come creare pool di risorse per il cloud privato tramite il mapping delle sottoscrizioni di AzureDescribes how to create resource pools for your Private Cloud through Azure subscription mapping
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014964"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Creare pool di risorse per il cloud privato con il mapping della sottoscrizione di AzureCreate resource pools for your Private Cloud with Azure subscription mapping
Il mapping delle sottoscrizioni di Azure consente di creare pool di risorse per il cloud privato dai pool di risorse vSphere disponibili. Nel portale CloudSimple è possibile visualizzare e gestire la sottoscrizione di Azure per i cloud privati.

> [!NOTE]
> Il mapping di un pool di risorse esegue anche il mapping di tutti i pool di risorse figlio. Non è possibile eseguire il mapping di un pool di risorse padre se sono già mappati pool di risorse figlio.

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md).
2. Aprire la pagina **Risorse** e selezionare **Mapping sottoscrizioni di Azure.**  
3. Fare clic su **Modifica mapping sottoscrizione di Azure**.  
4. Per eseguire il mapping dei pool di risorse disponibili, selezionarli a sinistra e fare clic sulla freccia rivolta a destra. 
5. Per rimuovere i mapping, selezionarli a destra e fare clic sulla freccia rivolta verso sinistra. 

    ![Sottoscrizioni Azure](media/resources-azure-mapping.png)

6. Fare clic su **OK**.
