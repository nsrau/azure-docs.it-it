---
title: Installare Endpoint Protection nel Centro sicurezza di Azure | Documentazione Microsoft
description: "In questo documento è illustrato come implementare la raccomandazione \"**Installa Endpoint Protection**\" del Centro sicurezza di Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: efb86a0ae362c30a6772c391a499154b7ae2a697
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Installare Endpoint Protection nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia di installare Endpoint Protection nelle macchine virtuali di Azure se non è già abilitato. Questo suggerimento si applica solo alle macchine virtuali Windows.

> [!NOTE]
> Questo esempio di distribuzione usa Microsoft Antimalware. Vedere [Integrazione di partner nel Centro sicurezza di Azure](security-center-partner-integration.md#partners-that-integrate-with-security-center) per un elenco di partner integrati con il Centro sicurezza.  
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>
>

1. Nel pannello **Raccomandazioni** selezionare **Installa Endpoint Protection**.
   ![Selezionare Installa Endpoint Protection][1]
2. Viene visualizzato il pannello **Installa Endpoint Protection** che mostra un elenco di macchine virtuali senza Installa Endpoint Protection. Selezionare dall'elenco le macchine virtuali in cui si vuole installare Endpoint Protection e fare clic su **Install on VMs** (Installa nelle macchine virtuali).
   ![Selezionare le macchine virtuali su cui installare Endpoint Protection][2]
3. Viene visualizzato il pannello **Seleziona Endpoint Protection**, che consente di selezionare la soluzione di Endpoint Protection da usare. In questo esempio viene selezionato **Microsoft Antimalware**.
   ![Seleziona Endpoint Protection][3]
4. Vengono visualizzate altre informazioni sulla soluzione di Endpoint Protection selezionata. Selezionare **Crea**.
   ![Creare una soluzione antimalware][4]
5. Specificare le impostazioni di configurazione necessarie nel pannello **Aggiungi estensione** e quindi selezionare **OK**. Per altre informazioni sulle impostazioni di configurazione, vedere [Configurazione di Antimalware predefinita e personalizzata](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../security/azure-security-antimalware.md) è ora attivo nelle VM selezionate.

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Installa Endpoint Protection" del Centro sicurezza. Per altre informazioni sull'abilitazione di un programma antimalware di Microsoft in Azure, vedere i documenti seguenti:

* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](../security/azure-security-antimalware.md): informazioni su come distribuire la protezione antimalware Microsoft.

Per altre informazioni sul Centro sicurezza, vedere i documenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png

