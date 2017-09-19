---
title: Convalida degli avvisi nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come convalidare gli avvisi di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d7aa8544f50b42bacfa1e1f16fdce468d8fc81ef
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="alerts-validation-in-azure-security-center"></a>Convalida degli avvisi nel Centro sicurezza di Azure
Questo documento illustra come verificare che il sistema sia configurato correttamente per gli avvisi del Centro sicurezza di Azure.

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni partner connesse, ad esempio soluzioni di protezione endpoint e firewall, per rilevare e segnalare all'utente le minacce. Vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) per altre informazioni sugli avvisi di sicurezza e [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) per altre informazioni sui diversi tipi di avvisi.

## <a name="alert-validation"></a>Convalida degli avvisi
Dopo aver installato l'agente del Centro sicurezza nel computer, seguire questa procedura nel computer in cui si vuole essere avvisati delle risorse che hanno subito attacchi:

1. Copiare un file eseguibile (ad esempio, calc.exe) sul desktop del computer o in un'altra directory per motivi di praticità.
2. Rinominare il file **ASC_AlertTest_662jfi039N.exe**.
3. Aprire il prompt dei comandi ed eseguire il file con un argomento (un semplice nome di argomento fittizio), ad esempio: *ASC_AlertTest_662jfi039N.exe -foo*
4. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Dovrebbe essere visualizzato un avviso simile al seguente:

    ![Convalida degli avvisi](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Esaminando questo avviso, verificare che il campo Arguments Auditing Enabled (Controllo argomenti abilitato) sia impostato su "vero". Se l'impostazione visualizzata è "falso", è necessario abilitare il controllo degli argomenti della riga di comando. È possibile abilitare questa opzione con la riga di comando seguente:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Per una dimostrazione di questa funzionalità, guardare il video [Alert Validation in Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) (Convalida degli avvisi nel Centro sicurezza di Azure). 

## <a name="see-also"></a>Vedere anche
Questo articolo ha presentato il processo di convalida degli avvisi. Dopo aver acquisito familiarità con tale convalida, vedere gli articoli seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.


