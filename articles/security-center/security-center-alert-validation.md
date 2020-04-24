---
title: Convalida avvisi (file di test EICAR) nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come convalidare gli avvisi di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139998"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Convalida avvisi (file di test EICAR) nel Centro sicurezza di Azure
Questo documento illustra come verificare che il sistema sia configurato correttamente per gli avvisi del Centro sicurezza di Azure.

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Gli avvisi sono le notifiche generate dal Centro sicurezza quando rileva minacce per le risorse. Assegna priorità ed elenca gli avvisi insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza offre anche raccomandazioni per la risoluzione di un attacco.
Per altre informazioni, vedere [avvisi di sicurezza nel centro sicurezza](security-center-alerts-overview.md) e [gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Convalida degli avvisi

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Convalidare gli avvisi per le macchine virtuali Windows<a name="validate-windows"></a>

Dopo aver installato l'agente del Centro sicurezza nel computer, attenersi alla seguente procedura dal computer in cui si vuole essere la risorsa attaccata dell'avviso:

1. Copiare un file eseguibile, ad esempio **Calc. exe**, nel desktop del computer o in un'altra directory di praticità, quindi rinominarlo come **ASC_AlertTest_662jfi039N. exe**.
1. Aprire il prompt dei comandi ed eseguire il file con un argomento (solo un nome di argomento fittizio), ad esempio:```ASC_AlertTest_662jfi039N.exe -foo```
1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Verrà visualizzato un avviso simile all' [esempio](#alert-validate) seguente:

> [!NOTE]
> Quando si esamina questo avviso di test per Windows, verificare che gli argomenti di campo **controllo abilitato** sia **true**. Se è **false**, è necessario abilitare il controllo degli argomenti della riga di comando. Per abilitarla, usare il comando seguente:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Convalidare gli avvisi nelle VM Linux<a name="validate-linux"></a>

Dopo aver installato l'agente del Centro sicurezza nel computer, attenersi alla seguente procedura dal computer in cui si vuole essere la risorsa attaccata dell'avviso:
1. Copiare un eseguibile in un percorso appropriato e rinominarlo in **./asc_alerttest_662jfi039n**, ad esempio:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Aprire il prompt dei comandi ed eseguire il file seguente:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Verrà visualizzato un avviso simile all' [esempio](#alert-validate) seguente:

### <a name="alert-example"></a>Esempio di avviso<a name="alert-validate"></a>

![Esempio di convalida degli avvisi](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Convalidare gli avvisi in Kubernetes<a name="validate-kubernetes"></a>

Se si usa la funzionalità di anteprima del Centro sicurezza per l'integrazione del servizio Kubernetes di Azure, eseguire il comando kubectl seguente per verificare che gli avvisi siano in funzione:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Per altre informazioni sull'integrazione del servizio Azure Kubernetes e del Centro sicurezza di Azure, vedere [questo articolo](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha presentato il processo di convalida degli avvisi. Dopo aver acquisito familiarità con tale convalida, vedere gli articoli seguenti:

* [Convalida Azure Key Vault rilevamento minacce nel centro sicurezza di Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gestione e risposta agli avvisi di sicurezza nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) : informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti della sicurezza nel centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* Informazioni sugli [avvisi di sicurezza nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) : informazioni sui diversi tipi di avvisi di sicurezza.
