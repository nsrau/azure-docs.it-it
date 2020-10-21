---
title: Convalida degli avvisi nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come verificare che gli avvisi di sicurezza siano configurati correttamente nel centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 999888b12f10c07f7d42f14289e88030f9542a36
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340819"
---
# <a name="alert-validation-in-azure-security-center"></a>Convalida degli avvisi nel centro sicurezza di Azure
Questo documento illustra come verificare che il sistema sia configurato correttamente per gli avvisi del Centro sicurezza di Azure.

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Gli avvisi sono le notifiche generate dal Centro sicurezza quando rileva minacce per le risorse. Assegna priorità ed elenca gli avvisi insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza offre anche raccomandazioni per la risoluzione di un attacco.
Per altre informazioni, vedere [avvisi di sicurezza nel centro sicurezza](security-center-alerts-overview.md) e [gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)

## <a name="validate-alerts-on-windows-vms"></a>Convalidare gli avvisi per le macchine virtuali Windows <a name="validate-windows"></a>

Dopo aver installato l'agente del Centro sicurezza nel computer, attenersi alla seguente procedura dal computer in cui si vuole essere la risorsa attaccata dell'avviso:

1. Copiare un eseguibile (ad esempio **calc.exe**) nel desktop del computer o in un'altra directory di praticità e rinominarlo come **ASC_AlertTest_662jfi039N.exe**.
1. Aprire il prompt dei comandi ed eseguire il file con un argomento (solo un nome di argomento fittizio), ad esempio: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Verrà visualizzato un avviso.

> [!NOTE]
> Quando si esamina questo avviso di test per Windows, verificare che gli argomenti di campo **controllo abilitato** sia **true**. Se è **false**, è necessario abilitare il controllo degli argomenti della riga di comando. Per abilitarlo, usare il comando seguente: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Convalidare gli avvisi nelle VM Linux <a name="validate-linux"></a>

Dopo aver installato l'agente del Centro sicurezza nel computer, attenersi alla seguente procedura dal computer in cui si vuole essere la risorsa attaccata dell'avviso:
1. Copiare un eseguibile in un percorso appropriato e rinominarlo in **./asc_alerttest_662jfi039n**, ad esempio:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Aprire il prompt dei comandi ed eseguire il file seguente:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Verrà visualizzato un avviso.


## <a name="validate-alerts-on-kubernetes"></a>Convalidare gli avvisi in Kubernetes <a name="validate-kubernetes"></a>

Se il servizio Azure Kubernetes è stato integrato con il Centro sicurezza, è possibile verificare che gli avvisi funzionino con il comando kubectl seguente:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Per altre informazioni su come difendere i cluster e i nodi Kubernetes, vedere [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha presentato il processo di convalida degli avvisi. Dopo aver acquisito familiarità con tale convalida, vedere gli articoli seguenti:

* [Convalidare il rilevamento delle minacce di Azure Key Vault nel Centro sicurezza di Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gestione e risposta agli avvisi di sicurezza nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti della sicurezza nel centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* Informazioni sugli [avvisi di sicurezza nel centro sicurezza di Azure](./security-center-alerts-overview.md) : informazioni sui diversi tipi di avvisi di sicurezza.