---
title: Informazioni sul trasferimento della proprietà della fatturazione per una sottoscrizione di Azure
description: Questo articolo descrive gli aspetti che è necessario conoscere prima di trasferire la proprietà della fatturazione di una sottoscrizione di Azure a un altro account.
keywords: trasferire la sottoscrizione di Azure, trasferimento della sottoscrizione di Azure, spostare una sottoscrizione di Azure in un altro account, cambiare il proprietario della sottoscrizione di Azure, trasferire la sottoscrizione di Azure in un altro account, trasferire la fatturazione di Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 237565a7b72c3317e2c443f86965634ed7c9942c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336956"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Informazioni sul trasferimento della proprietà della fatturazione per una sottoscrizione di Azure

Questo articolo descrive gli aspetti che è necessario conoscere prima di trasferire la proprietà della fatturazione di una sottoscrizione di Azure a un altro account. 

Si potrebbe avere l'esigenza di trasferire la proprietà della fatturazione della sottoscrizione di Azure nel caso in cui si lasci l'organizzazione o se si vuole che la sottoscrizione venga addebitata a un altro account. Il trasferimento della proprietà della fatturazione a un altro account fornisce agli amministratori del nuovo account le autorizzazioni necessarie per le attività di fatturazione. Tali amministratori possono cambiare il metodo di pagamento, visualizzare gli addebiti e annullare la sottoscrizione.

Se si vuole mantenere la proprietà della fatturazione, ma cambiare il tipo di sottoscrizione, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](../manage/switch-azure-offer.md). Per controllare chi può accedere alle risorse della sottoscrizione, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

Per i clienti con contratto Enterprise Agreement (EA), gli amministratori dell'organizzazione possono trasferire la proprietà della fatturazione delle sottoscrizioni tra gli account.

Solo l'amministratore della fatturazione di un account può trasferire la proprietà di una sottoscrizione.

## <a name="determine-account-billing-administrator"></a>Determinare l'amministratore della fatturazione dell'account

<a name="whoisaa"></a>

L'amministratore della fatturazione è una persona autorizzata a gestire la fatturazione per un account. Ha l'autorizzazione per accedere alla fatturazione nel [portale di Azure](https://portal.azure.com) ed eseguire varie attività collegate, ad esempio creare sottoscrizioni, visualizzare e pagare fatture oppure aggiornare i metodi di pagamento.

Per identificare gli account per i quali si è amministratori della fatturazione, visitare la [pagina Gestione dei costi e fatturazione nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Selezionare quindi **Tutti gli ambiti di fatturazione** nel riquadro sinistro. La pagina delle sottoscrizioni illustra tutte le sottoscrizioni per le quali l'utente ha il ruolo di amministratore della fatturazione.

Se non si conosce chi ha il ruolo di amministratore dell'account per una sottoscrizione, visitare la [pagina Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Scegliere quindi la sottoscrizione da controllare e quindi analizzare la sezione **Impostazioni**. Selezionare **Proprietà** e l'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore dell'account**.


## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Il trasferimento di sottoscrizioni nel portale di Azure è disponibile per i tipi di sottoscrizione elencati di seguito. Attualmente il trasferimento non è supportato per le sottoscrizioni della [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) o [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/). Per aggirare questa limitazione, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Per trasferire altre sottoscrizioni, ad esempio i piani di supporto, [contattare il supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Sottoscrittori di Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Piano Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Con il portale EA.

<sup>2</sup> Supportato solo per gli account creati durante l'iscrizione nel sito Web di Azure.

## <a name="resources-transferred-with-subscriptions"></a>Risorse trasferite con le sottoscrizioni

Tutte le risorse, come VM, dischi e siti Web, vengono trasferite al nuovo account. Se tuttavia una sottoscrizione viene trasferita a un account in un altro tenant di Azure AD, i [ruoli di amministratore](../manage/add-change-subscription-administrator.md) e le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) della sottoscrizione non vengono trasferiti. Inoltre, le [registrazioni di app](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e gli altri servizi specifici del tenant non vengono trasferiti insieme alla sottoscrizione.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Trasferire la proprietà di un account in un paese o in un'area geografica distinta

Sfortunatamente, non è possibile trasferire le sottoscrizioni tra paesi o aree geografiche usando il portale di Azure. È tuttavia possibile trasferirle se si apre una richiesta di supporto tecnico di Azure. Per creare una richiesta di supporto, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Trasferire una sottoscrizione da un account all'altro

Se si è amministratore di due account, è possibile trasferire una sottoscrizione tra gli account. Gli account vengono considerati concettualmente come account di due utenti diversi ed è quindi possibile trasferire le sottoscrizioni tra gli account.
Per visualizzare i passaggi necessari per trasferire la sottoscrizione, vedere [Trasferire la proprietà della fatturazione di una sottoscrizione di Azure](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Il trasferimento di una sottoscrizione non deve creare tempi di inattività

Il trasferimento di una sottoscrizione in un account nello stesso tenant di Azure AD non ha alcun impatto sulle risorse in esecuzione nella sottoscrizione. Le informazioni di contesto salvate in PowerShell non vengono tuttavia aggiornate, pertanto potrebbe essere necessario cancellarle o modificare le impostazioni. Se la sottoscrizione viene trasferita a un account in un altro tenant e si decide di spostarla nel tenant, tutti gli utenti, i gruppi e le entità servizio che avevano le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per l'accesso alle risorse della sottoscrizione perderanno l'accesso. Questo si potrebbe tradurre in tempi di inattività del servizio.

## <a name="new-account-usage-and-billing-history"></a>Cronologia di utilizzo e di fatturazione del nuovo account

Le uniche informazioni relative al nuovo account disponibili per gli utenti sono quelle relative al costo dell'ultimo mese per la sottoscrizione. Il resto della cronologia di fatturazione e utilizzo non viene trasferito con la sottoscrizione.

## <a name="manually-migrate-data-and-services"></a>Eseguire manualmente la migrazione di dati e servizi

Quando si trasferisce una sottoscrizione, le relative risorse seguono la sottoscrizione. Se non è possibile trasferire la proprietà di una sottoscrizione, è possibile eseguire la migrazione delle risorse manualmente. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Credito sottoscrizione residuo 

Se si dispone di una sottoscrizione di Visual Studio o Microsoft Partner Network, si ottengono crediti mensili. Il credito non viene trasferito con la sottoscrizione nel nuovo account. Per accettare la richiesta di trasferimento, l'utente deve avere una licenza di Visual Studio. La sottoscrizione usa il credito di Visual Studio disponibile nell'account utente. Per altre informazioni, vedere [Trasferire le sottoscrizioni di Visual Studio e Partner Network](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Gli utenti mantengono l'accesso alle risorse trasferite

Tenere presente che gli utenti con accesso alle risorse in una sottoscrizione mantengono l'accesso quando viene trasferita la proprietà. Tuttavia, i [ruoli di amministratore](../manage/add-change-subscription-administrator.md) e le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) potrebbero essere rimossi. La perdita dell'accesso si verifica quando l'account si trova in un tenant di Azure AD diverso dal tenant della sottoscrizione e l'utente che ha inviato la richiesta di trasferimento sposta la sottoscrizione nel tenant dell'account. 

È possibile visualizzare gli utenti con le assegnazioni di ruolo di Azure per l'accesso alle risorse della sottoscrizione nel portale di Azure. Visitare la [pagina delle sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Selezionare la sottoscrizione da verificare e quindi **Controllo di accesso (IAM)** nel riquadro sinistro. Successivamente, selezionare **Assegnazioni di ruolo** nella parte superiore della pagina. La pagina Assegnazioni di ruolo contiene l'elenco di tutti gli utenti con accesso nella sottoscrizione.

Anche se le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) vengono rimosse durante il trasferimento, gli utenti nell'account proprietario originale potrebbero avere ancora accesso alla sottoscrizione tramite altri meccanismi di sicurezza, tra cui:

* Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../../cloud-services/cloud-services-certs-create.md).
* Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).
* Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare l'accesso alle risorse, deve prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornata. Accedere al [portale di Azure](https://portal.azure.com) e quindi nel menu Hub selezionare **Tutte le risorse**. Successivamente, selezionare la risorsa. Nella pagina della risorsa selezionare quindi **Impostazioni**. In questa area è possibile visualizzare e aggiornare i segreti esistenti.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Si paga per l'utilizzo quando si riceve la proprietà

L'account è responsabile del pagamento di eventuali utilizzi segnalati a partire dal momento del trasferimento. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. L'utilizzo è incluso nella fattura dell'account.

## <a name="use-a-different-payment-method"></a>Usare un metodo di pagamento diverso

Durante l'accettazione della richiesta di trasferimento, è possibile selezionare un metodo di pagamento esistente collegato all'account o aggiungerne uno nuovo.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Trasferire la proprietà della sottoscrizione con un contratto Enterprise Agreement

L'amministratore dell'organizzazione può aggiornare la proprietà dell'account per qualsiasi account anche dopo che il proprietario dell'account originale non fa più parte dell'organizzazione. Per altre informazioni sul trasferimento di account Enterprise Agreement di Azure, vedere[Trasferimenti di Azure Enterprise](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Trasferire la proprietà della fatturazione di una sottoscrizione di Azure](../manage/billing-subscription-transfer.md)
