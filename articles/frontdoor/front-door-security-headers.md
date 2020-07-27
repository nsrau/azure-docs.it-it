---
title: Aggiungere intestazioni di sicurezza con il motore regole - Frontdoor di Azure
description: Questo articolo illustra come configurare un'intestazione di sicurezza tramite il motore regole nel servizio Frontdoor di Azure
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: 5f07a57e0d41a5e7c546bc89ca7a0b5ac5fce5de
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512232"
---
# <a name="add-security-headers-with-rules-engine"></a>Aggiungere intestazioni di sicurezza con il motore regole

Implementare le intestazioni di sicurezza per evitare vulnerabilità basate sul browser come HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy o X-Frame-Options. Gli attributi basati sulla sicurezza possono anche essere definiti con i cookie.

L'esempio seguente illustra come aggiungere un'intestazione Content-Security-Policy a tutte le richieste in ingresso che corrispondono al percorso definito nella route a cui è associata la configurazione del motore regole. In questo caso, nell'applicazione è consentita l'esecuzione di script solo dal sito attendibile **https://apiphany.portal.azure-api.net** .

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Aggiungere un'intestazione Content-Security-Policy nel portale di Azure

1. Prima di creare questa regola specifica, è possibile acquisire informazioni su come [creare una frontdoor](quickstart-create-front-door.md) o su come [creare un motore regole](front-door-tutorial-rules-engine.md) se è la prima volta che si usa il servizio Frontdoor di Azure o la funzionalità motore regole.

2. Fare clic su **Aggiungi** per aggiungere una nuova regola. Specificare un nome per la regola e quindi fare clic su **Aggiungi un'azione** > **Intestazione della risposta**.

3. Impostare l'operatore **Accoda** per aggiungere questa intestazione come risposta a tutte le richieste in ingresso a questa route.

4. Aggiungere il nome dell'intestazione: **Content-Security-Policy** e definire i valori che verranno accettati da questa intestazione. Per questo scenario, si sceglierà *"script-src 'self' https://apiphany.portal.azure-api.net."*

5. Dopo aver aggiunto tutte le regole desiderate alla configurazione, non dimenticare di passare alla route preferita e associare la configurazione del motore regole alla regola di route. Questo passaggio è obbligatorio per il funzionamento della regola. 

![esempio del portale](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> In questo scenario non sono state aggiunte [condizioni di corrispondenza](front-door-rules-engine-match-conditions.md) alla regola. Questa regola verrà applicata a tutte le richieste in ingresso che corrispondono al percorso definito nella regola di route. Per applicarla solo a un subset di tali richieste, assicurarsi di aggiungere le condizioni di corrispondenza specifiche a questa regola.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [motore regole di Frontdoor di Azure](front-door-rules-engine.md). 
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
- Altre informazioni sulle [condizioni di corrispondenza](front-door-rules-engine-match-conditions.md) del motore regole
- Vedere le [informazioni di riferimento dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest) per il motore regole di Frontdoor di Azure. 
- Vedere le [informazioni di riferimento di PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0) per il motore regole di Frontdoor di Azure. 
