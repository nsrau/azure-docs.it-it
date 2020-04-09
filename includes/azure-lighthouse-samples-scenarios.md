---
title: File di inclusione
description: File di inclusione
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4c50ca2449787d681d8b9571083a92077d95dcd8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986595"
---
Questi esempi illustrano varie attività che è possibile eseguire in scenari di gestione tra tenant.

| **Modello** | **Descrizione** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Distribuisce account di archiviazione in due gruppi di risorse diversi.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Crea servizi di gestione di Azure, li collega e distribuisce soluzioni aggiuntive. Per una distribuzione end-to-end, usare il modello **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Abilita e configura Centro sicurezza di Azure all'interno della sottoscrizione di Azure di destinazione. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Distribuisce e abilita Azure Sentinel in un'area di lavoro Log Analytics esistente in una sottoscrizione delegata. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Questi modelli consentono di distribuire le estensioni VM di Log Analytics nelle VM Windows e Linux, connettendole all'area di lavoro Log Analytics designata |
