---
title: Impostare i criteri di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Impostare i criteri di sicurezza nel Centro sicurezza di Azure
Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza mostrando i passaggi necessari per eseguire questa attività.


## <a name="how-security-policies-work"></a>Funzionamento dei criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare il criterio nel Centro sicurezza o usare i [criteri di Azure](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri a gruppi di gestione, che possono rappresentare l'intera organizzazione, una business unit specifica e così via, e infine monitorare la conformità a tali criteri negli ambiti.

> [!NOTE]
> Criteri di Azure è disponibile in anteprima limitata. Fare clic [qui](https://aka.ms/getpolicy) per partecipare. Per altre informazioni sui criteri di Azure, vedere [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (Creare e gestire criteri per applicare la conformità).

## <a name="how-to-change-security-policies-in-security-center"></a>Procedura per la modifica dei criteri di sicurezza nel Centro sicurezza
È possibile modificare i criteri di sicurezza predefiniti per ogni sottoscrizione di Azure nel Centro sicurezza. Per modificare i criteri di sicurezza, è necessario essere un proprietario, un collaboratore o un amministratore della sicurezza della sottoscrizione o del gruppo di gestione che la include. Accedere al portale di Azure e seguire questa procedura per visualizzare i criteri di sicurezza nel Centro sicurezza di Azure:

1. Nel dashboard **Centro sicurezza** in **Generale** fare clic su **Criteri di sicurezza**.
2. Selezionare la sottoscrizione in cui abilitare i criteri di sicurezza.

    ![Gestione dei criteri](./media/security-center-policies/security-center-policies-fig10.png)

3. Nella sezione **COMPONENTI DEI CRITERI** fare clic su **Criteri di sicurezza**.

    ![Componenti dei criteri](./media/security-center-policies/security-center-policies-fig12.png)

4. Questo è il criterio predefinito assegnato al Centro sicurezza tramite i criteri di Azure. È possibile eliminare elementi disponibili in **POLICIES AND PARAMETERS** (CRITERI E PARAMETRI) oppure aggiungere altre definizioni dei criteri disponibili in **OPZIONI DISPONIBILI**. A questo scopo, è sufficiente fare clic sul segno Più accanto al nome della definizione.

    ![Definizioni dei criteri](./media/security-center-policies/security-center-policies-fig11.png)

5. Per una spiegazione più dettagliata del criterio, fare clic sul criterio. Verrà visualizzata un'altra pagina che include i dettagli e il codice JSON con la struttura della [definizione del criterio(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. Al termine delle modifiche, fare clic su **Salva**.

## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come configurare i criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md). Informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md). Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
