---
title: Una panoramica della convalida come servizio per Azure Stack | Microsoft Docs
description: Panoramica della convalida di Azure Stack come servizio problemi noti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 56251245a23df031f3bc8fe3d36de43e194fbcc7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159674"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Che cos'è la convalida come servizio per Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La convalida come servizio (VaaS) è un servizio di Azure nativo progettato per i partner di soluzioni che stanno progettando condivise offerte di Azure Stack con Microsoft. Partner di soluzioni possono usare il servizio per verificare che le proprie soluzioni soddisfano i requisiti Microsoft e che funzionino come previsto con Azure Stack.

L'uso principale VaaS è:

- Convalidare le nuove soluzioni di Azure Stack
- Convalidare le modifiche al software Azure Stack
- Ottenere i pacchetti usati durante la distribuzione di partner di soluzioni con firma digitale
- Garanzie di convalida di anteprima Azure Stack

## <a name="validate-new-azure-stack-solution"></a>Convalidare una nuova soluzione di Azure Stack

Partner di usano il flusso di lavoro di convalida di soluzioni per controllare le nuove soluzioni di Azure Stack. La soluzione deve superare i test di componente di test Hardware Lab Kit (HKL) Azure Stack richiesti. È necessario solo eseguire il flusso di lavoro due volte per ogni nuova soluzione: una volta per la configurazione minima e massima.

Per altre informazioni, vedere [convalidare una nuova soluzione di Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Convalidare le modifiche al software Azure Stack

Partner di usano il flusso di lavoro di convalida del pacchetto per verificare che la soluzione funziona con il recente aggiornamento del software Azure Stack. Come minimo, il flusso di lavoro di convalida del pacchetto deve essere eseguito nell'ambiente di hardware consigliato da Microsoft e su una soluzione in cui le patch e aggiornamento (P & U) utilizzati per applicare l'aggiornamento. È consigliabile eseguire la convalida del pacchetto in fase di compilazione la linea di base.

Per altre informazioni, vedere [convalidare gli aggiornamenti software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Ottenere i pacchetti di partner di soluzioni con firma digitale

Oltre alla convalida degli aggiornamenti di Azure Stack, è possibile usare il flusso di lavoro di convalida del pacchetto per verificare gli aggiornamenti per i pacchetti di personalizzazione OEM, tra cui i driver di specifiche del partner di Azure Stack, firmware e altro software usato durante la distribuzione di Azure Stack software. Distribuire il pacchetto che si intende archiviare nella versione corrente del software Azure Stack usando almeno la minima dimensioni soluzione che saranno supportati. Il pacchetto aggiornato deve essere caricato per il servizio prima di avviare il test. Se il test ha esito positivo, inviare una notifica vaashelp@microsoft.com. Informare il partner di Azure Stack che il pacchetto ha completato i test e deve essere firmato digitalmente con la firma digitale di Azure Stack. Notifica il partner di Azure Stack che il pacchetto è disponibile per il download nel portale di Microsoft consente di firmare il pacchetto.

Per altre informazioni, vedere [pacchetti di convalidare OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Garanzie di convalida di anteprima Azure Stack

Microsoft rende regolarmente nuove funzionalità disponibili in Azure Stack. Come parte del processo di sviluppo per la distribuzione di queste funzionalità-to-market, nuovo materiale aggiuntivo relativo a test viene reso disponibile nel flusso di lavoro di superamento test. Il flusso di lavoro di superamento test include materiale aggiuntivo relativo a test da altri flussi di lavoro per consentire l'esecuzione di test non ufficiali. Non usare il flusso di lavoro di superamento test per inviare i risultati per l'approvazione. Usare la soluzione convalida pacchetto convalide flusso di lavoro e ottenere l'approvazione ufficiale per la soluzione.

## <a name="next-steps"></a>Passaggi successivi

- Iniziare a usare, e [configurare la convalida come account del servizio](azure-stack-vaas-validate-solution-new.md)
