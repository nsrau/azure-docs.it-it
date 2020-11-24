---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559775"
---
 I cluster di calcolo Azure Machine Learning supportano anche le [identità gestite](../articles/active-directory/managed-identities-azure-resources/overview.md) per autenticare l'accesso alle risorse di Azure senza includere le credenziali nel codice. Sono disponibili due tipi di identità gestite:

* Un' **identità gestita assegnata dal sistema** è abilitata direttamente nel cluster di calcolo Azure Machine Learning. Il ciclo di vita di un'identità assegnata dal sistema è direttamente associato al cluster di elaborazione. Se il cluster di calcolo viene eliminato, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.
* Un' **identità gestita assegnata dall'utente** è una risorsa di Azure autonoma fornita tramite il servizio di gestione delle identità di Azure. È possibile assegnare un'identità gestita assegnata dall'utente a più risorse e renderla permanente fino a quando si desidera.