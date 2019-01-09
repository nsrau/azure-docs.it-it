---
title: Convalida di Azure Stack come un concetti chiave di servizio | Microsoft Docs
description: Descrive i concetti chiave per la convalida di Azure Stack come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 6a14a806b48b30d84b021a84a1ef0a2f4318ea94
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106472"
---
# <a name="validation-as-a-service-key-concepts"></a>Convalida di un concetti chiave di servizio

Questo articolo descrive i concetti chiave di convalida come servizio (VaaS).

## <a name="solutions"></a>Soluzioni

Una soluzione VaaS rappresenta una soluzione di Azure Stack con hardware specifiche di una distinta base (BoM). La soluzione VaaS funge da contenitore per i flussi di lavoro eseguiti sulla soluzione di Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Creare una soluzione nel portale di VaaS

1. Accedi per il [VaaS portale](https://azurestackvalidation.com).
2. Nel dashboard soluzioni, selezionare nella **nuova soluzione**.
3. Immettere un nome per la soluzione. Per suggerimenti sulla denominazione, vedere [convenzione di denominazione per le soluzioni VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Selezionare **salvare** per creare la soluzione.

## <a name="workflows"></a>Flussi di lavoro

Un flusso di lavoro VaaS opera all'interno del contesto di una soluzione VaaS. Rappresenta un set di gruppi di test che verificano la funzionalità di una distribuzione di Azure Stack. È necessario creare un flusso di lavoro per tutti gli aggiornamenti software o distribuzione di una soluzione di Azure Stack.

I flussi di lavoro sono classificati per tipo di scenario di test. Durante i test non ufficiali, il **superamento Test** flusso di lavoro consente di selezionare i test da tutte le informazioni collaterali di VaaS disponibili. Nei test ufficiale, la **convalida** i flussi di lavoro di destinazione specifici scenari di test selezionati da Microsoft.

![Riquadri del flusso di lavoro VaaS](media/tile_all-workflows.png)

> [!NOTE]
> Il **soluzione convalida** flusso di lavoro supporta attualmente due scenari: [Convalida pacchetti OEM](azure-stack-vaas-validate-oem-package.md) e [convalidare gli aggiornamenti software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Per altre informazioni sui tipi di flusso di lavoro, vedere [What ' s convalida come servizio per Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Guida introduttiva a VaaS i flussi di lavoro

1. Nel dashboard soluzioni, creare una nuova soluzione o selezionarne uno esistente. In questo viene aggiornato e i riquadri del flusso di lavoro.
2. Per creare un nuovo flusso di lavoro, selezionare nella **avviare** su qualsiasi riquadro. Per informazioni specifiche per ogni flusso di lavoro, vedere gli articoli seguenti:
    - Superamento test: [Avvio rapido: Usare la convalida di un portale del servizio per pianificare il primo test](azure-stack-vaas-schedule-test-pass.md)
    - Convalida di soluzioni: [Convalidare una nuova soluzione di Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Convalida di soluzioni: [Convalidare gli aggiornamenti software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Convalida di soluzioni: [Convalidare i pacchetti dell'OEM](azure-stack-vaas-validate-oem-package.md)

3. Per gestire o monitorare un flusso di lavoro esistente, selezionare nella **Gestisci** sul riquadro del flusso di lavoro. Selezionare il nome del flusso di lavoro e usare la **modifica** pulsante per visualizzare le proprietà o modificare i parametri comuni di test.

Per altre informazioni sulle proprietà del flusso di lavoro e i parametri, vedere [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Test

Un test in VaaS è costituito da un gruppo di azioni eseguite su una soluzione di Azure Stack. I test hanno diversi scopi designati identificati per categoria, ad esempio funzionale o l'affidabilità e uno o più servizi di Azure Stack di destinazione. Ogni test definisce il proprio set di parametri, alcuni dei quali sono specificati dai parametri comuni del flusso di lavoro che lo contiene.

Per altre informazioni sulla gestione e i test di monitoraggio, vedere [monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md).

Per altre informazioni sui parametri di test, vedere [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agents

Un agente VaaS determina l'esecuzione di test. Due tipi di agenti di eseguire i test VaaS:

- Il **agente cloud**. Si tratta dell'agente predefinito disponibile in VaaS. Nessun programma di installazione è obbligatorio, ma ciò richiede la connettività in ingresso all'ambiente e gli endpoint di Azure Stack devono essere risolvibili da internet. Alcuni test non sono compatibili con l'agente di cloud.
- Oggetto **agente locale**. In questo modo si per eseguire la convalida in scenari in cui la connettività in ingresso all'ambiente non è fattibile. Alcuni test richiedono l'esecuzione tramite l'agente locale.

Gli agenti locali non sono associati a qualsiasi soluzione di Azure Stack o VaaS particolare. Come procedura consigliata, deve essere eseguito all'esterno di un ambiente Azure Stack.

Per istruzioni sull'aggiunta di un agente locale, vedere [distribuire l'agente locale](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Passaggi successivi

- [Le procedure consigliate per la convalida come servizio](azure-stack-vaas-best-practice.md)