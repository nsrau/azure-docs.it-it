---
title: Convalidare gli aggiornamenti software da Microsoft in Azure Stack di convalida come servizio | Microsoft Docs
description: Informazioni su come convalidare gli aggiornamenti software da Microsoft con la convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7fa03cdf52fc3218e9556c9664daafdc60243f3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593218"
---
# <a name="validate-software-updates-from-microsoft"></a>Convalidare gli aggiornamenti software da Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft rilascerà periodicamente aggiornamenti per il software Azure Stack. Questi aggiornamenti sono disponibili per Azure Stack coengineering partner. Gli aggiornamenti vengono forniti in anticipo di disponibili pubblicamente. È possibile controllare gli aggiornamenti con la soluzione e fornire commenti e suggerimenti a Microsoft.

Gli aggiornamenti software Microsoft per Azure Stack sono designati utilizzando una convenzione di denominazione, ad esempio, 1803 che indica che l'aggiornamento è per il mese di marzo 2018. Per informazioni sui criteri di aggiornamento di Azure Stack, sono disponibili note sulla versione e cadenza, vedere [criteri di manutenzione Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

## <a name="prerequisites"></a>Prerequisiti

Prima di verificare il processo di aggiornamento mensile VaaS, è necessario avere familiarità con gli elementi seguenti:

- [Convalida di un concetti chiave di servizio](azure-stack-vaas-key-concepts.md)
- [Test di verifica di caratteristiche interattive](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Test necessari

I test seguenti devono essere eseguiti nell'ordine seguente per la convalida del software mensile:

1. Verificare l'aggiornamento mensile Azure Stack
2. Motore di simulazione di cloud

## <a name="validating-software-updates"></a>Esegue la convalida degli aggiornamenti software

1. Creare una nuova **convalida dei pacchetti** flusso di lavoro.
1. Per i test necessari precedenti, seguire le istruzioni da [eseguire la convalida del pacchetto verifica](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Vedere la sezione seguente per istruzioni aggiuntive sul **mensile Azure Stack Update verifica** di test.

### <a name="apply-the-monthly-update"></a>Applicare l'aggiornamento mensile

1. Selezionare un agente per eseguire test su.
1. Pianificazione **mensile verifica di aggiornamento di Azure Stack**.
1. Specificare il percorso in cui il pacchetto di estensione OEM attualmente distribuito nel modulo di e il percorso in cui il pacchetto di estensione OEM che verrà applicato durante l'aggiornamento. Per configurare gli URL per questi pacchetti, vedere [la gestione dei pacchetti per la convalida](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Seguire i passaggi nell'interfaccia utente dall'agente selezionata.

Se hai domande o dubbi, contattare [VaaS Help](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)