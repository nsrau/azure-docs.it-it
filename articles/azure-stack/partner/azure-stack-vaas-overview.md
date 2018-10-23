---
title: Una panoramica della convalida come servizio per Azure Stack | Microsoft Docs
description: Panoramica della convalida dello Stack di Azure come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: fa2e85bb2b201fa8e83a03d24883a66a72910f11
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650180"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Che cos'è la convalida come servizio per Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La convalida come servizio (VaaS) è un servizio di Azure nativo progettato per i partner di soluzioni che stanno progettando condivise offerte di Azure Stack con Microsoft. Partner di soluzioni possono usare il servizio per verificare che le proprie soluzioni soddisfano i requisiti Microsoft e che funzionino come previsto con Azure Stack.

Gli impieghi principali per VaaS sono:

- La convalida di nuove soluzioni di Azure Stack
- Convalida delle modifiche al software Azure Stack
- Firma digitale usati durante la distribuzione di pacchetti della soluzione partner
- Visualizzazione dell'anteprima VaaS testare materiale aggiuntivo relativo a

## <a name="validate-a-new-azure-stack-solution"></a>Convalidare una nuova soluzione di Azure Stack

Partner di usare la **soluzione convalida** flusso di lavoro per la convalida di nuove soluzioni di Azure Stack. La soluzione deve superare i test di componenti Hardware Lab Kit HLK () Azure Stack richiesti. Per certificare una gamma di configurazioni hardware, il flusso di lavoro deve essere eseguito due volte per ogni nuova soluzione: una volta ciascuno per le configurazioni minime e massime.

Per altre informazioni, vedere [convalidare una nuova soluzione di Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Convalidare le modifiche al software Azure Stack

Partner di usare la **convalida dei pacchetti** flusso di lavoro per verificare che la soluzione funziona con gli aggiornamenti di software più recente di Azure Stack. Il flusso di lavoro di convalida del pacchetto deve essere eseguito in un ambiente hardware consigliato da Microsoft in cui patch e aggiornamento (P & U) viene usata per applicare l'aggiornamento. È consigliabile eseguire anche il flusso di lavoro in fase di compilazione la linea di base.

Per altre informazioni, vedere [convalidare gli aggiornamenti software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Ottenere i pacchetti di partner di soluzioni con firma digitale

Oltre alla convalida degli aggiornamenti di Azure Stack, partner di usare la **convalida dei pacchetti** flusso di lavoro per convalidare gli aggiornamenti per i pacchetti di personalizzazione OEM, che includono altri software, firmware e driver di specifiche del partner di Azure Stack usato durante la distribuzione del software Azure Stack. Distribuire il pacchetto che si esegue la convalida nella versione corrente del software Azure Stack con almeno la soluzione minimo che sarà supportata. Il pacchetto viene inviato a VaaS prima dell'esecuzione di test. Se il test ha esito positivo, notificare [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) che il pacchetto ha completato i test e deve essere firmato con la firma digitale di Azure Stack. Microsoft consente di firmare il pacchetto e notifica il partner di Azure Stack che il pacchetto è disponibile per il download nel portale di VaaS.

Per altre informazioni, vedere [pacchetti di convalidare OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Anteprima VaaS testare materiale aggiuntivo relativo a

Microsoft rende regolarmente nuove funzionalità disponibili in Azure Stack. Come parte del processo di sviluppo per la distribuzione di queste funzionalità-to-market, diventa disponibile nel nuovo materiale aggiuntivo relativo a test il **superamento Test** flusso di lavoro. Il flusso di lavoro di superamento Test include materiale aggiuntivo relativo a test da altri flussi di lavoro per consentire l'esecuzione di test non ufficiali. Non usare il flusso di lavoro di superamento Test per inviare i risultati per l'approvazione. Usare i flussi di lavoro di convalida di soluzioni e la convalida del pacchetto per ottenere l'approvazione ufficiale per la soluzione.

Per altre informazioni, vedere [Guida introduttiva: usare la convalida come portale del servizio per pianificare il primo test](azure-stack-vaas-schedule-test-pass.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la convalida come delle risorse del servizio](azure-stack-vaas-set-up-resources.md)
- Informazioni su [convalida come un concetti chiave di servizio](azure-stack-vaas-key-concepts.md)
