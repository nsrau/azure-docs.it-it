---
title: Avviso di convalida (file di test EICAR) nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come convalidare gli avvisi di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626307"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Convalida degli avvisi (file di test EICAR) nel Centro sicurezza di Azure
Questo documento illustra come verificare che il sistema sia configurato correttamente per gli avvisi del Centro sicurezza di Azure.

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Gli avvisi sono notifiche che il Centro sicurezza viene generato l'errore quando rileva le minacce sulle risorse. Si assegna la priorità e vengono elencati gli avvisi e le informazioni necessarie per poter analizzare rapidamente il problema. Il Centro sicurezza fornisce inoltre consigli per come è possibile risolvere un attacco.
Per altre informazioni, vedere [avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md) e [gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Convalida degli avvisi

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Convalidare l'avviso nella macchina virtuale Windows <a name="validate-windows"></a>

Dopo il centro di sicurezza dell'agente è installato nel computer in uso, seguire questi passaggi dal computer in cui si desidera essere avvisati delle risorse dell'avviso:

1. Copiare un file eseguibile (ad esempio **calc.exe**) per il desktop del computer o qualsiasi altra directory di praticità e rinominarlo come **ASC_AlertTest_662jfi039N.exe**.
1. Aprire il prompt dei comandi ed eseguire questo file con un argomento (solo un nome argomento fittizio), ad esempio: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Un avviso simile al [esempio](#alert-validate) seguito deve essere visualizzato:

> [!NOTE]
> Quando si rivede questo avviso di test per Windows, assicurarsi che il campo **argomenti attivasse** viene **true**. Se si tratta **false**, quindi è necessario abilitare il controllo degli argomenti della riga di comando. Per abilitarla, usare la riga di comando seguente:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Convalidare l'avviso in VM Linux <a name="validate-linux"></a>

Dopo il centro di sicurezza dell'agente è installato nel computer in uso, seguire questi passaggi dal computer in cui si desidera essere avvisati delle risorse dell'avviso:
1. Copiare un file eseguibile in una posizione comoda e rinominarlo **. / asc_alerttest_662jfi039n**, ad esempio:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Aprire il prompt dei comandi ed eseguire il file:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Un avviso simile al [esempio](#alert-validate) seguito deve essere visualizzato:

### Esempio di avviso <a name="alert-validate"></a>

![Esempio di convalida degli avvisi](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Vedere anche
Questo articolo ha presentato il processo di convalida degli avvisi. Dopo aver acquisito familiarità con tale convalida, vedere gli articoli seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
