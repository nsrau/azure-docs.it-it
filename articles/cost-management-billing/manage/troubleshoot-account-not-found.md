---
title: Risolvere i problemi di visualizzazione dell'account di fatturazione nel portale di Azure
description: Questo articolo illustra come risolvere i problemi che possono verificarsi quando si cerca di visualizzare l'account di fatturazione nel portale di Azure.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: banders
ms.openlocfilehash: 671189a6a328a77653b61542e3e30c848a75cb65
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85364279"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Risolvere i problemi di visualizzazione dell'account di fatturazione nel portale di Azure

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. Si potrebbe avere accesso a più account di fatturazione, ad esempio se si è effettuata l'iscrizione ad Azure per uso personale. È anche possibile accedere ad Azure tramite il contratto Enterprise o il Contratto del cliente Microsoft dell'organizzazione. Per ognuno di questi scenari, si avrà un account di fatturazione distinto. Questo articolo illustra come risolvere i problemi che possono verificarsi quando si cerca di visualizzare l'account di fatturazione nel portale di Azure.

È possibile visualizzare gli account di fatturazione nella pagina [Gestione dei costi e fatturazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade).

Per altre informazioni sugli account di fatturazione e su come identificare il tipo di account di fatturazione, vedere [Visualizzare gli account di fatturazione nel portale di Azure](view-all-accounts.md).

Se non si riesce a visualizzare l'account di fatturazione nel portale di Azure, provare le opzioni seguenti:

## <a name="sign-in-to-a-different-tenant"></a>Accedere a un tenant diverso

L'account di fatturazione è associato a un singolo tenant di Azure Active Directory. Se si è eseguito l'accesso al tenant errato, l'account di fatturazione non viene visualizzato nella pagina Gestione dei costi e fatturazione. Seguire la procedura seguente per passare a un altro tenant nel portale di Azure e visualizzare l'account di fatturazione in tale tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il proprio profilo (indirizzo di posta elettronica) nell'angolo in alto a destra della pagina.
1. Selezionare **Cambia directory**.  
    ![Screenshot che mostra l'opzione Cambia directory selezionata nel portale](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Selezionare una directory nella sezione **Tutte le directory**.  
    ![Screenshot che mostra la selezione di una directory nel portale](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Accedere con un indirizzo di posta elettronica diverso

Alcuni utenti hanno più indirizzi di posta elettronica con cui accedere al [portale di Azure](https://portal.azure.com). Non tutti gli indirizzi di posta elettronica hanno però accesso a un account di fatturazione. Se si accede con un indirizzo di posta elettronica che dispone delle autorizzazioni per gestire le risorse ma non di quelle per visualizzare un account di fatturazione, l'account di fatturazione non viene visualizzato nella pagina [Gestione dei costi e fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) del portale di Azure.

Eseguire l'accesso al portale di Azure con un indirizzo di posta elettronica con l'autorizzazione per l'account di fatturazione per accedere al proprio account di fatturazione.

## <a name="sign-in-with-a-different-identity"></a>Accedere con un'identità diversa

Alcuni utenti hanno due identità con lo stesso indirizzo di posta elettronica, un account aziendale o dell'istituto di istruzione e un account personale. In genere, solo una delle identità dispone delle autorizzazioni per visualizzare un account di fatturazione. Si potrebbero avere due identità con un unico indirizzo di posta elettronica. Quando si esegue l'accesso con un'identità che non dispone dell'autorizzazione per visualizzare un account di fatturazione, questo non viene visualizzato nella pagina [Gestione dei costi e fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade). Per cambiare identità, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) in una finestra InPrivate/Incognito.
1. Se l'indirizzo di posta elettronica ha due identità, verrà visualizzata un'opzione per selezionare un account personale o un account aziendale o dell'istituto di istruzione. Selezionare uno degli account.
1. Se l'account di fatturazione non è visualizzato nella pagina Gestione dei costi e fatturazione del portale di Azure, ripetere i passaggi 1 e 2 e selezionare l'altra identità.

## <a name="contact-us-for-help"></a>Contattare Microsoft per richiedere assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come risolvere i problemi, leggere gli articoli seguenti relativi alla fatturazione e alle sottoscrizioni.

- [Carta rifiutata](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problemi relativi all'accesso alla sottoscrizione](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [Non sono state trovate sottoscrizioni](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Vista dei costi Enterprise disabilitata](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
