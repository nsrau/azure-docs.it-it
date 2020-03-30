---
title: Distribuire OpenShift Container Platform 4.x in AzureDeploy OpenShift Container Platform 4.x in Azure
description: Distribuire OpenShift Container Platform 4.x in Azure.Deploy OpenShift Container Platform 4.x in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035444"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Distribuire OpenShift Container Platform 4.x in AzureDeploy OpenShift Container Platform 4.x in Azure

La distribuzione di OpenShift Container Platform (OCP) 4.2 è ora supportata in Azure tramite il modello IPI (Installer-Provisioned Infrastructure).  La pagina di destinazione per provare OpenShift 4 è [try.openshift.com](https://try.openshift.com/). Per installare OCP 4.2 in Azure, visitare la pagina [Red Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Le credenziali Red Hat sono necessarie per accedere a questo sito.


## <a name="notes"></a>Note 

 - Per installare ed eseguire OCP 4.x in Azure è necessaria un'entità servizio (SP) di Azure Active Directory (AAD)
     - Al SP deve essere concessa l'autorizzazione API di **Application.ReadWrite.OwnedBy** per Azure Active Directory Graph
     - Un amministratore tenant AAD deve concedere il consenso di amministratore per l'autorizzazione api per avere effetto
     - Al SP devono essere concessi i ruoli **Di sp** e amministratore di **accesso utente** alla sottoscrizione
 - Il modello di installazione per OCP 4.x è diverso da 3.x e non sono disponibili modelli di Azure Resource Manager per la distribuzione di OCP 4.x in Azure
 - Se si verificano problemi durante il processo di installazione, contattare la società appropriata (Microsoft o Red Hat)

| Descrizione del problema | Punto di contatto |
|-------------------|---------------|
| Problemi specifici di Azure (AAD, SP, sottoscrizione di Azure e così via)Azure specific issues (AAD, SP, Azure Subscription, etc.)                              | Microsoft |
| Problemi specifici di OpenShift (Errori/errori di installazione, sottoscrizione Red Hat, ecc.) |  Red Hat  |




## <a name="next-steps"></a>Passaggi successivi

- [Getting started with OpenShift Container Platform](https://docs.openshift.com) (Introduzione a OpenShift Container Platform)
