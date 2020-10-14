---
title: Azure Lighthouse e applicazioni gestite di Azure
description: Informazioni sul modo in cui Azure Lighthouse e le applicazioni gestite di Azure consentono di abilitare scenari diversi e di come possono essere usati insieme.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 144917716da38cc99078f663cc2f4cb8ba0199c8
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014988"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse e applicazioni gestite di Azure

Sia le applicazioni gestite di Azure che Azure Lighthouse funzionano abilitando un provider di servizi ad accedere a risorse che risiedono nel tenant del cliente. Può essere utile comprendere le differenze nel modo in cui funzionano e gli scenari che consentono di abilitare, nonché come possono essere utilizzati insieme.

> [!TIP]
> Sebbene si faccia riferimento ai provider di servizi e ai clienti in questo argomento, le [aziende che gestiscono più tenant](enterprise.md) possono utilizzare gli stessi processi e gli stessi strumenti.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Confronto tra Azure Lighthouse e le applicazioni gestite di Azure

### <a name="azure-lighthouse"></a>Azure Lighthouse

Con [Azure Lighthouse](../overview.md), un provider di servizi può eseguire un'ampia gamma di attività di gestione direttamente nella sottoscrizione o nel gruppo di risorse di un cliente. Questo accesso viene effettuato tramite una proiezione logica, consentendo ai provider di servizi di accedere al proprio tenant e accedere alle risorse che appartengono al tenant del cliente. Il cliente può determinare quali sottoscrizioni o gruppi di risorse delegare al provider di servizi e il cliente mantiene l'accesso completo a tali risorse. Possono anche rimuovere l'accesso del provider di servizi in qualsiasi momento.

Per usare Azure Lighthouse, i clienti vengono caricati per la [gestione delle risorse delegata di Azure](azure-delegated-resource-management.md) tramite la [distribuzione di modelli ARM](../how-to/onboard-customer.md) o tramite un' [offerta di servizio gestito in Azure Marketplace](managed-services-offers.md). È possibile tenere traccia dell'effetto sugli impegni dei clienti [collegando l'ID partner](../how-to/partner-earned-credit.md).

Azure Lighthouse viene in genere usato quando un provider di servizi eseguirà regolarmente attività di gestione per un cliente.

### <a name="azure-managed-applications"></a>Applicazioni gestite da Azure

[Le applicazioni gestite di Azure](../../azure-resource-manager/managed-applications/overview.md) consentono a un provider di servizi o a un ISV di offrire soluzioni cloud facili da distribuire e utilizzare nelle proprie sottoscrizioni.

In un'applicazione gestita, le risorse usate dall'applicazione vengono raggruppate e distribuite in un gruppo di risorse gestito dal server di pubblicazione. Questo gruppo di risorse è presente nella sottoscrizione del cliente, ma un'identità nel tenant del server di pubblicazione può accedervi. L'ISV continua a gestire e gestire l'applicazione gestita, mentre il cliente non ha accesso diretto per lavorare nel gruppo di risorse o qualsiasi accesso alle risorse.

Le applicazioni gestite supportano [esperienze di portale di Azure personalizzate](../../azure-resource-manager/managed-applications/concepts-view-definition.md) e l' [integrazione con i provider personalizzati](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Queste opzioni possono essere usate per offrire un'esperienza più personalizzata e integrata, semplificando l'esecuzione di alcune attività di gestione da parte dei clienti.

Le applicazioni gestite possono essere [pubblicate in Azure Marketplace](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md), come offerta privata per l'uso da parte di un cliente specifico, o come offerte pubbliche che possono essere acquistati da più clienti. Possono inoltre essere distribuiti agli utenti all'interno dell'organizzazione mediante la [pubblicazione di applicazioni gestite nel catalogo di servizi](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). È possibile distribuire sia il catalogo di servizi che le istanze del Marketplace usando i modelli ARM, che possono includere l'identificatore univoco di un partner del Marketplace commerciale per tenere traccia dell' [attribuzione dell'utilizzo del cliente](../../marketplace/azure-partner-customer-usage-attribution.md).

Le applicazioni gestite di Azure vengono in genere usate per esigenze specifiche del cliente, che possono essere realizzate tramite una soluzione chiavi in mano completamente gestita dal provider di servizi.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Uso combinato di Azure Lighthouse e delle applicazioni gestite di Azure

Sebbene il faro di Azure e le applicazioni gestite di Azure usino meccanismi di accesso diversi per raggiungere obiettivi diversi, possono verificarsi scenari in cui è opportuno che un provider di servizi li usi entrambi con lo stesso cliente.

Ad esempio, un cliente potrebbe volere i servizi gestiti distribuiti da un provider di servizi tramite Azure Lighthouse, in modo che abbiano visibilità sulle azioni del partner insieme al controllo continuo della sottoscrizione delegata. Tuttavia, il provider di servizi potrebbe non volere che il cliente acceda a determinate risorse che verranno archiviate nel tenant del cliente o consentire qualsiasi azione personalizzata su tali risorse. Per soddisfare questi obiettivi, il provider di servizi può pubblicare un'offerta privata come applicazione gestita. L'applicazione gestita può includere un gruppo di risorse che viene distribuito nel tenant del cliente, ma a cui non è possibile accedere direttamente dal cliente.

I clienti potrebbero anche essere interessati alle applicazioni gestite da più provider di servizi, indipendentemente dal fatto che usino anche servizi gestiti tramite Azure Lighthouse da uno di questi provider di servizi. Inoltre, i partner del programma Cloud Solution Provider (CSP) possono rivendere alcune applicazioni gestite pubblicate da altri ISV ai clienti che supportano tramite Azure Lighthouse. Con una vasta gamma di opzioni, i provider di servizi possono scegliere il giusto equilibrio per soddisfare le esigenze dei clienti, limitando l'accesso alle risorse quando appropriato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [applicazioni gestite di Azure](../../azure-resource-manager/managed-applications/overview.md).
- Informazioni su come eseguire [l'onboarding di una sottoscrizione al Faro di Azure](../how-to/onboard-customer.md).