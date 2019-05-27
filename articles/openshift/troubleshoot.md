---
title: Risolvere i problemi di Azure Red Hat OpenShift | Microsoft Docs
description: Risolvere i problemi e risolvere i problemi comuni con Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 82678091c1d0b71e6209f6d03e9d1a0ca60fe03e
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992161"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Risoluzione dei problemi per Azure Red Hat OpenShift

Questo articolo illustra alcuni problemi comuni incontrati durante la creazione o la gestione dei cluster di Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Nuovo tentativo in corso la creazione di un cluster non riuscita

Se la creazione di un Azure Red Hat OpenShift cluster usando il `az` riga di comando non riesce, nuovo tentativo di creazione continuerà a non riuscire.
Usare `az openshift delete` per eliminare il cluster non riuscita, quindi creare un cluster completamente nuovo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Gruppo di risorse cluster di Azure Red Hat OpenShift nascosto

Attualmente, il `Microsoft.ContainerService/openShiftManagedClusters` risorsa che viene creato automaticamente tramite la CLI di Azure (`az openshift create` comando) è nascosta nel portale di Azure. Nel **gruppo di risorse** visualizzazione, check **Mostra tipi nascosti** per visualizzare il gruppo di risorse.

![Screenshot della casella di controllo di tipo nascosta nel portale](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Creazione di un cluster risultati errore trovato alcun provider di risorse registrato

Se la creazione di un cluster determina un errore `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, quindi si facevano parte della fase di anteprima ed è ora necessario a [macchina virtuale di Azure di acquisto di istanze riservate](https://aka.ms/openshift/buy) a usare il prodotto generalmente disponibile. Una prenotazione consente di ridurre la spesa correlata al pagamento anticipato per servizi di Azure completamente gestiti. Fare riferimento a [ *cosa sono Azure prenotazioni* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) per altre informazioni su prenotazioni e come essi risparmiare denaro.

## <a name="next-steps"></a>Passaggi successivi

- Provare il [Red Hat OpenShift Help Center](https://help.openshift.com/) ulteriori su OpenShift risoluzione dei problemi.

- Trova le risposte alle [domande frequenti su Azure Red Hat OpenShift](openshift-faq.md).
