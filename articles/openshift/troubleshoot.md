---
title: Risolvere i problemi di Azure Red Hat OpenShift
description: Risolvere i problemi comuni con Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76274920"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Risoluzione dei problemi per Azure Red Hat OpenShift

Questo articolo illustra alcuni problemi comuni riscontrati durante la creazione o la gestione di Microsoft Azure cluster Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Ripetizione del tentativo di creazione di un cluster non riuscito

Se la creazione di un cluster Azure Red Hat OpenShift `az` usando il comando dell'interfaccia della riga di comando non riesce, il tentativo di creazione continuerà a non riuscire.
Usare `az openshift delete` per eliminare il cluster in errore, quindi creare un cluster completamente nuovo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Gruppo di risorse del cluster OpenShift di Azure Red Hat nascosto

Attualmente, la `Microsoft.ContainerService/openShiftManagedClusters` risorsa creata automaticamente dall'interfaccia della riga di comando di`az openshift create` Azure (comando) è nascosta nella portale di Azure. Nella visualizzazione del **gruppo di risorse** selezionare Mostra **tipi nascosti** per visualizzare il gruppo di risorse.

![Screenshot della casella di controllo tipo nascosto nel portale](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>La creazione di un cluster genera un errore che non è stato trovato alcun provider di risorse registrato

Se la creazione di un cluster genera un errore `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, si fa parte dell'anteprima e ora è necessario [acquistare istanze riservate della macchina virtuale di Azure](https://aka.ms/openshift/buy) per usare il prodotto disponibile a livello generale. Una prenotazione riduce la spesa prepagando i servizi di Azure completamente gestiti. Vedere [*che cosa sono le prenotazioni di Azure*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) per altre informazioni sulle prenotazioni e su come risparmiano denaro.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla risoluzione dei problemi di OpenShift, provare il [centro di supporto di Red Hat OpenShift](https://help.openshift.com/) .

- Trova le risposte alle [domande frequenti su Azure Red Hat OpenShift](openshift-faq.md).
