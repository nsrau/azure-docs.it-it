---
title: File di inclusione
description: File di inclusione
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204497"
---
Questi esempi illustrano varie attività che è possibile eseguire in scenari di gestione tra tenant.

| **Modello** | **Descrizione** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Crea un'istanza di Key Vault nel tenant del cliente e definisce i criteri di accesso.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Distribuisce account di archiviazione in due gruppi di risorse diversi.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Crea servizi di gestione di Azure, li collega e distribuisce soluzioni aggiuntive. Per una distribuzione end-to-end, usare il modello **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Abilita e configura Centro sicurezza di Azure all'interno della sottoscrizione di Azure di destinazione. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Distribuisce e abilita Azure Sentinel in un'area di lavoro Log Analytics esistente in una sottoscrizione delegata. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Questi modelli consentono di distribuire le estensioni VM di Log Analytics nelle VM Windows e Linux, connettendole all'area di lavoro Log Analytics designata |
