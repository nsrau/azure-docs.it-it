---
title: Usare il portale di Azure per risolvere i problemi di ordinamento Azure Stack Edge | Microsoft Docs
description: Viene descritto come risolvere i problemi di ordinamento Azure Stack Edge.
services: databox
author: twooley
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: twooley
ms.openlocfilehash: 226274c52610e24c305400d77dc7737d32c2b722
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783995"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Risolvere i problemi di ordinamento di Azure Stack Edge

Questo articolo descrive come risolvere i problemi di ordinamento Azure Stack Edge.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Risolvere i problemi di ordinamento

## <a name="unsupported-subscription-or-region"></a>Sottoscrizione o area non supportata

**Descrizione errore:** In portale di Azure, se si riceve l'errore:

*La sottoscrizione o l'area selezionata non è supportata. Scegliere una sottoscrizione o un'area diversa.*

![Sottoscrizione o area non supportata](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Soluzione suggerita:**  Assicurarsi di aver usato una sottoscrizione supportata, ad esempio [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Le sottoscrizioni con pagamento in base al consumo non sono supportate. Per altre informazioni, vedere [prerequisiti per le risorse di Azure stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).

È possibile che Microsoft possa consentire l'aggiornamento di un tipo di sottoscrizione caso per caso. Contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/) in modo che possa comprendere le proprie esigenze e modificare i limiti in modo appropriato.

## <a name="selected-subscription-type-not-supported"></a>Il tipo di sottoscrizione selezionato non è supportato

**Errore:** Si dispone di una sottoscrizione con contratto Enterprise, CSP o sponsorizzato e viene ricevuto l'errore seguente:

*Il tipo di sottoscrizione selezionato non è supportato. Assicurarsi di usare una sottoscrizione supportata. [Altre informazioni](azure-stack-edge-deploy-prep.md#prerequisites). Se si utilizza un tipo di sottoscrizione supportato, verificare che il `Microsoft.DataBoxEdge` provider sia registrato. Per informazioni su come eseguire la registrazione, vedere [registrare il provider di risorse](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Soluzione suggerita:** Per registrare il provider di risorse Azure Stack Edge, seguire questa procedura:

1. In portale di Azure passare a **Home**  >  **sottoscrizioni**Home.

2. Selezionare la sottoscrizione da usare per ordinare il dispositivo.

3. Selezionare **provider di risorse** e cercare **Microsoft. DataBoxEdge**.

    ![Registrare il provider di risorse](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Se non si dispone dell'accesso proprietario o collaboratore per registrare il provider di risorse, viene visualizzato l'errore seguente: *il nome della sottoscrizione di sottoscrizione &lt; &gt; non dispone delle autorizzazioni per registrare i provider di risorse: Microsoft. DataBoxEdge.*

Per altre informazioni, vedere [registrare i provider di risorse](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Microsoft. DataBoxEdge non registrato per la sottoscrizione

**Errore:** In portale di Azure selezionare una sottoscrizione da usare per Azure Stack Edge o Data Box Gateway e ottenere l'errore seguente:

*Provider di risorse: Microsoft. DataBoxEdge non è registrato per &lt; il nome della sottoscrizione di sottoscrizione &gt; e non si dispone delle autorizzazioni necessarie per registrare un provider di risorse per il &lt; nome &gt; della sottoscrizione di sottoscrizione*.

**Soluzione suggerita:** Elevare l'accesso alla sottoscrizione o trovare un utente con accesso proprietario o collaboratore per registrare il provider di risorse.

## <a name="resource-disallowed-by-policy"></a>Risorsa non consentita dai criteri

**Errore:** In portale di Azure si tenta di registrare un provider di risorse e si ottiene l'errore seguente:

*Il &lt; nome della risorsa risorsa non &gt; è consentito dai criteri. (Codice: RequestDisallowedByPolicy). Initiative: negare tipi di risorse generalmente indesiderati. Criteri: tipi di risorse non consentiti.*

**Soluzione suggerita:** Questo errore si verifica a causa di criteri di Azure esistenti che bloccano la creazione di risorse. I criteri di Azure vengono impostati dall'amministratore di sistema di un'organizzazione per garantire la conformità durante l'uso o la creazione di risorse di Azure. Se un criterio di questo tipo blocca la creazione di risorse Azure Stack Edge, contattare l'amministratore di sistema per modificare i criteri di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [risolvere i problemi di Azure stack Edge](azure-stack-edge-troubleshoot.md).
