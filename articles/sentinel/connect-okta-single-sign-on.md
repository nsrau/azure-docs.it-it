---
title: Connettere i dati Single Sign-on di okta ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Single Sign-on di okta ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531527"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Connettere l'accesso Single Sign-on di okta ad Azure Sentinel con la funzione di Azure

Il connettore Okta Single Sign-on (SSO) consente di connettere facilmente tutti i log della soluzione di sicurezza [Okta Single Sign-on (SSO)](https://www.okta.com/products/single-sign-on/) con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Okta Single Sign-on e Azure Sentinel usa funzioni di Azure per eseguire il pull dei dati di log tramite l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Configurare e connettere Okta Single Sign-on

Funzioni di Azure può integrare ed effettuare il pull di eventi e log direttamente da Okta Single Sign-on e inviarli a Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare connettore **Single Sign-on di okta** .

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina **Single Sign-on di okta** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics nella tabella **Okta_CL** .

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Okta Single Sign-on ad Azure Sentinel usando le app per le funzioni di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

