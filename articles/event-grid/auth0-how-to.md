---
title: Come inviare eventi da Auth0 ad Azure usando Griglia di eventi di Azure
description: Come terminare gli eventi da Auth0 ai servizi di Azure con Griglia di eventi di Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 950b17611ba4932cc986d1b6488b904f29fdf04b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690317"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integrare Griglia di eventi di Azure con Auth0

Questo articolo descrive come connettere gli account Auth0 e Azure creando un argomento partner di Griglia di eventi.

Per un elenco completo degli eventi supportati da Auth0, vedere [Codici di tipo di evento Auth0](https://auth0.com/docs/logs/references/log-event-type-codes).

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Inviare eventi da Auth0 a Griglia di eventi di Azure
Per inviare eventi Auth0 ad Azure:

1. Abilitare il provider di risorse di Griglia di eventi
1. Configurare un argomento partner Auth0 nel dashboard di Auth0
1. Attivare l'argomento partner in Azure
1. Sottoscrivere eventi da Auth0

Per altre informazioni su questi concetti, vedere [Concetti su Griglia di eventi](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi
Se non si è usato Griglia di eventi prima, sarà necessario registrare il provider di risorse Griglia di eventi. Se è già stato usato Griglia di eventi, passare alla sezione successiva.

Nel portale di Azure:
1. Selezionare Sottoscrizioni nel menu di sinistra.
1. Selezionare la sottoscrizione in uso per Griglia di eventi.
1. Nel menu di sinistra, in Impostazioni, scegliere Provider di risorse.
1. Cercare Microsoft.EventGrid.
1. Selezionare Registra.
1. Aggiornare per assicurarsi che lo stato venga modificato in Registrato.

### <a name="set-up-an-auth0-partner-topic"></a>Configurare l’argomento partner Auth0
Parte del processo di integrazione consiste nel configurare Auth0 per l'uso come origine evento. Questo passaggio viene eseguito nel dashboard di [Auth0](https://manage.auth0.com/).

1. Accedere al [Dashboard di Auth0](https://manage.auth0.com/).
1. Passare a Logs > Flussi.
1. Fare clic su + Crea flusso.
1. Selezionare Griglia di eventi di Azure e immettere un nome univoco per il nuovo flusso.
1. Creare l'origine evento specificando l'ID sottoscrizione di Azure, l'area di Azure e il nome di un gruppo di risorse. 
1. Fare clic su Salva.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Attivare l'argomento partner Auth0 in Azure
L'attivazione dell'argomento Auth0 in Azure consente il flusso degli eventi da Auth0 ad Azure.

1. Accedere al portale di Azure.
1. Cercare `Partner Topics` nella parte superiore e fare clic su `Event Grid Partner Topics` in servizi.
1. Fare clic sull'argomento corrispondente al flusso creato nel Dashboard di Auth0.
1. Verificare che il campo `Source` corrisponda al proprio account Auth0.
1. Fare clic su Attiva.

### <a name="subscribe-to-auth0-events"></a>Sottoscrivere eventi Auth0

#### <a name="create-an-event-handler"></a>Creare un gestore dell'evento
Per testare l'argomento partner, è necessario un gestore eventi. Passare alla sottoscrizione di Azure e creare un servizio supportato come [gestore eventi](event-handlers.md), ad esempio [Funzione di Azure](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Sottoscrivere l'argomento partner Auth0
La sottoscrizione dell'argomento partner Auth0 consente di indicare a Griglia di eventi la destinazione desiderata per gli eventi Auth0.

1. Nel pannello dell'argomento partner per l'integrazione Auth0 selezionare + Sottoscrizione di eventi nella parte superiore.
1. Nella pagina Crea sottoscrizione di eventi:
    1. Specificare un nome per la sottoscrizione di eventi.
    1. Selezionare il servizio di Azure o il webhook creato per il tipo di endpoint.
    1. Seguire le istruzioni per il servizio specifico.
    1. Fare clic su Crea.

## <a name="testing"></a>Test
L'integrazione dell'argomento partner Auth0 con Azure è pronta all'uso.

### <a name="verify-the-integration"></a>Verificare l'integrazione
Per verificare il corretto funzionamento dell’integrazione:

1. Accedere al Dashboard di Auth0.
1. Passare a Logs > Flussi.
1. Fare clic sul proprio flusso Griglia di eventi.
1. Nel flusso fare clic sulla scheda Integrità. Il flusso è essere attivo e, fintanto che non vengono visualizzati errori, funziona.

Provare a [richiamare una qualsiasi delle azioni Auth0 che attivano un evento da pubblicare](https://auth0.com/docs/logs/references/log-event-type-codes) per vedere il flusso degli eventi.

## <a name="delivery-attempts-and-retries"></a>Tentativi di recapito e nuovi tentativi
Gli eventi Auth0 vengono recapitati ad Azure tramite un meccanismo di streaming. Ogni evento viene inviato quando viene attivato in Auth0. Se Griglia di eventi non è in grado di ricevere l'evento, Auth0 tenterà di recapitare l'evento per tre volte. Se non riesce, Auth0 registrerà il mancato recapito nel sistema.

## <a name="next-steps"></a>Passaggi successivi

- [Argomento partner Auth0](auth0-overview.md)
- [Panoramica degli argomenti partner](partner-topics-overview.md)
- [Diventare un partner di Griglia di eventi](partner-onboarding-overview.md)