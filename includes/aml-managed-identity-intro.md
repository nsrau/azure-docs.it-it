---
title: includere file
description: includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147477"
---
 I cluster di calcolo Azure Machine Learning supportano anche le [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per autenticare l'accesso alle risorse di Azure senza includere le credenziali nel codice. Sono disponibili due tipi di identità gestite:

* Un' **identità gestita assegnata dal sistema** è abilitata direttamente nel cluster di calcolo Azure Machine Learning. Il ciclo di vita di un'identità assegnata dal sistema è direttamente associato al cluster di elaborazione. Se il cluster di calcolo viene eliminato, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.
* Un' **identità gestita assegnata dall'utente** è una risorsa di Azure autonoma fornita tramite il servizio di gestione delle identità di Azure. È possibile assegnare un'identità gestita assegnata dall'utente a più risorse e renderla permanente fino a quando si desidera.