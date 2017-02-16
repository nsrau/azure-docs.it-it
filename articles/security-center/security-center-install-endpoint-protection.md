---
title: Installare Endpoint Protection nel Centro sicurezza di Azure | Documentazione Microsoft
description: "In questo documento è illustrato come implementare la raccomandazione &quot;**Installa Endpoint Protection**&quot; del Centro sicurezza di Azure."
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
ms.date: 11/23/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b8a69d89f335c00c5ddc3c201e33a66e1dea1da5
ms.openlocfilehash: 6ccb5367b1c7c6cae7b9d35b6a5471c8edfe6f85


---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Installare Endpoint Protection nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia il provisioning di un programma antimalware nelle macchine virtuali di Azure se la protezione antimalware non è ancora abilitata. Questo suggerimento si applica solo alle macchine virtuali Windows. Attualmente, questa raccomandazione comporta solo la verifica della presenza di Windows Defender o Trend Micro Deep Security. È prevista l'aggiunta in futuro di altre soluzioni di protezione endpoint.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Installa Endpoint Protection**.
   ![Selezionare Installa Endpoint Protection][1]
2. Viene visualizzato il pannello **Installa Endpoint Protection** che mostra un elenco di macchine virtuali per cui non è abilitato l'antimalware. Selezionare dall'elenco le VM in cui si vuole installare l'antimalware e fare clic su **Installa nelle VM**.
   ![Selezionare le VM su cui installare l'antimalware][2]
3. Viene visualizzato il pannello **Seleziona Endpoint Protection** , che consente di selezionare la soluzione antimalware da usare. In questo esempio viene selezionato **Microsoft Antimalware**.
   ![Seleziona Endpoint Protection][3]
4. Vengono visualizzate altre informazioni sulla soluzione antimalware selezionata. Selezionare **Crea**.
   ![Creare una soluzione antimalware][4]
5. Specificare le impostazioni di configurazione necessarie nel pannello **Aggiungi estensione** e quindi selezionare **OK**. Per altre informazioni sulle impostazioni di configurazione, vedere [Configurazione di Antimalware predefinita e personalizzata](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../security/azure-security-antimalware.md) è ora attivo nelle VM selezionate.

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Installa Endpoint Protection" del Centro sicurezza. Per altre informazioni sull'abilitazione di un programma antimalware in Azure, vedere gli argomenti seguenti:

* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](../security/azure-security-antimalware.md): informazioni su come distribuire la protezione antimalware Microsoft.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

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



<!--HONumber=Nov16_HO4-->


