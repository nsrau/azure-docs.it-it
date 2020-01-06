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
ms.openlocfilehash: a5532b7a4574f3c2cc057255742c72bf032d180b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456772"
---
Questi esempi illustrano varie attività che è possibile eseguire in scenari di gestione tra tenant.

| **Modello** | **Descrizione** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Distribuisce account di archiviazione in due gruppi di risorse diversi.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Crea servizi di gestione di Azure, li collega e distribuisce soluzioni aggiuntive. Per una distribuzione end-to-end, usare il modello **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Abilita e configura Centro sicurezza di Azure all'interno della sottoscrizione di Azure di destinazione. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Distribuisce e abilita Azure Sentinel in un'area di lavoro Log Analytics esistente in una sottoscrizione delegata. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Questi modelli consentono di distribuire le estensioni VM di Log Analytics nelle VM Windows e Linux, connettendole all'area di lavoro Log Analytics designata |
