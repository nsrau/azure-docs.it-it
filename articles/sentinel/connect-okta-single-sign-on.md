---
title: Connettere i dati di okta Single Sign-On ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di okta Single Sign-On a Sentinel di Azure.
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
ms.openlocfilehash: 05a9b8009d896a2ee87df3e1c4493d249a887566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083923"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Connettere il Sign-On Single Okta ad Azure Sentinel con la funzione di Azure

> [!IMPORTANT]
> Il Okta Single Sign-On Data Connector in Azure Sentinel è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore Okta Single Sign-On (SSO) consente di connettere facilmente tutti i log della soluzione di sicurezza [Okta single Sign-On (SSO)](https://www.okta.com/products/single-sign-on/) con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Okta Single Sign-On e Azure Sentinel usa funzioni di Azure per eseguire il pull dei dati di log tramite l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Configurare e connettere Okta Single Sign-On

Funzioni di Azure è in grado di integrare ed effettuare il pull di eventi e log direttamente da Okta Single Sign-On e di inviarli ad Azure Sentinel.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare connettore **Single Sign-on di okta** .

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina **Single Sign-on di okta** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics nella tabella **Okta_CL** .

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Okta Single Sign-On ad Azure Sentinel usando le app per le funzioni di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

