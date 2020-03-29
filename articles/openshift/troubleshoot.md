---
title: Risolvere i problemi relativi a Azure Red Hat OpenShiftTroubleshoot Azure Red Hat OpenShift
description: Risolvere e risolvere i problemi comuni con Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274920"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Risoluzione dei problemi per Azure Red Hat OpenShiftTroubleshooting for Azure Red Hat OpenShift

Questo articolo descrive in dettaglio alcuni problemi comuni riscontrati durante la creazione o la gestione di cluster Microsoft Azure Red Hat OpenShift.This article details some common issues encountered while creating or managing Microsoft Azure Red Hat OpenShift clusters.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Riprovare la creazione di un cluster non riuscitoRetrying the creation of a failed cluster

Se la creazione di un cluster `az` Azure Red Hat OpenShift tramite il comando CLI ha esito negativo, riprovare la creazione continuerà a non riuscire.
Utilizzare `az openshift delete` per eliminare il cluster danneggiato, quindi creare un cluster completamente nuovo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Gruppo di risorse cluster Nascosto Azure Red Hat OpenShift

Attualmente, `Microsoft.ContainerService/openShiftManagedClusters` la risorsa creata automaticamente dall'interfaccia`az openshift create` della riga di comando di Azure (comando) è nascosta nel portale di Azure.Currently, the resource that's automatically created by the Azure CLI ( command) is hidden in the Azure portal. Nella visualizzazione **Gruppo di** risorse selezionare Mostra **tipi nascosti** per visualizzare il gruppo di risorse.

![Screenshot della casella di controllo del tipo nascosto nel portale](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>La creazione di un cluster comporta l'errore che nessun provider di risorse registrato trovatoCreating a cluster results in error that no registered resource provider found

Se la creazione di un `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`cluster genera un errore, significa che si faceva parte dell'anteprima e ora è necessario [acquistare istanze riservate](https://aka.ms/openshift/buy) della macchina virtuale di Azure per usare il prodotto disponibile in generale. Una prenotazione riduce la spesa pagando in base al pagamento anticipato per i servizi di Azure completamente gestiti. Fare riferimento a [*Quali sono le prenotazioni*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) di Azure per altre informazioni sulle prenotazioni e su come consentono di risparmiare denaro.

## <a name="next-steps"></a>Passaggi successivi

- Prova il [Centro assistenza Red Hat OpenShift](https://help.openshift.com/) per ulteriori informazioni sulla risoluzione dei problemi di OpenShift.

- Risposte alle [domande frequenti su Azure Red Hat OpenShift](openshift-faq.md).
