---
title: Migrazione degli account da portale Cloud Partner al centro per i partner-Marketplace commerciale per Azure
description: Come eseguire la migrazione dell'account da CPP al centro per i partner. -Marketplace commerciale per Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.openlocfilehash: 9ab9a57641e6b34942ae6d4293311714177aa012
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533195"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrazione degli account da portale Cloud Partner al centro per i partner

Quando si esegue la migrazione delle offerte dal portale Cloud Partner (CPP) al centro per i partner (PC), queste vengono bloccate per la modifica in CPP. A questo punto, è necessario eseguire la migrazione delle impostazioni dell'account al centro per i partner.  Le impostazioni dell'account e le offerte possono essere gestite anche nel centro per i partner.

## <a name="account-migration-process"></a>Processo di migrazione degli account

Quando viene eseguita la migrazione delle offerte da CPP, l'account è configurato per la migrazione. 
 
Se si è un utente con il ruolo di proprietario in CPP per un determinato account, nella pagina del profilo di pubblicazione verrà visualizzato un banner giallo.  Viene richiesto di spostare le impostazioni dell'account nel centro per i partner. 

Fare clic sul banner per avviare il processo di migrazione degli account. Si prevede di immettere gli elementi seguenti:

### <a name="work-e-mail-address"></a>**Indirizzo di posta elettronica di lavoro**

Nella maggior parte dei casi, accedere con l'indirizzo di posta elettronica usato per accedere a CPP. In alcuni casi, è necessario usare un indirizzo di posta elettronica diverso:

* Account Microsoft: Se l'account CPP è un account Microsoft, è necessario immettere un indirizzo di posta elettronica di lavoro valido associato al tenant, per il quale l'ID MPN è registrato. 

* Tenant non corrispondente: Se l'indirizzo di posta elettronica dell'ufficio non appartiene al tenant associato all'ID Microsoft Partner Network presente nell'account CPP, verrà visualizzato un errore. Per superare questo errore, immettere un indirizzo di posta elettronica associato al tenant. Un messaggio di errore fornirà il nome del tenant. 

### <a name="sign-up-for-microsoft-partner-network-program"></a>Iscriversi al programma Microsoft Partner Network

Nel caso in cui l'account CPP non disponga di un ID Microsoft Partner Network o di un account non valido, sarà necessario iscriversi al programma di Microsoft Partner Network come parte del processo di attivazione.

## <a name="account-activation-is-complete"></a>L'attivazione dell'account è stata completata

La migrazione dell'account deve essere eseguita solo una volta per un determinato account. Una volta che un determinato partner ha completato la migrazione per l'account, tutti i proprietari vedranno questo comportamento nella pagina del profilo di pubblicazione:

1. Verrà visualizzata la pagina Impostazioni partner in Microsoft Partner Network, in cui è possibile gestire le impostazioni dell'account del partner Microsoft. 
2. Al termine della migrazione dell'account, viene visualizzato un banner giallo nella pagina del profilo di pubblicazione per gli utenti appartenenti al ruolo proprietario in CPP per un determinato account, chiedendo loro di gestire le impostazioni dell'account nel centro per i partner. 
3. La pagina delle impostazioni dell'account in CPP viene quindi convertita in modalità di sola lettura. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Spostare soluzioni basate su Dynamics 365 nel centro per i partner

Se si dispone di Dynamics 365 per Customer Engagement o Dynamics 365 per le soluzioni Finance e Operations nel portale GTM di un partner commerciale, **seguire queste istruzioni entro il 31 agosto 2019** per spostare queste soluzioni nel centro per i partner.

> [!NOTE]
> Se l'account è stato originariamente creato in partner Membership Center (PMC), accedere a [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) per confermare che l'account è stato migrato prima di completare i passaggi seguenti. Se viene visualizzata una schermata del profilo con l'ID MPN, si è pronti per continuare. In caso contrario, è necessario avviare la migrazione dell'account seguendo le istruzioni riportate nel [centro di appartenenza partner](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Per assistenza, visitare il [supporto tecnico](https://partner.microsoft.com/support?issueid=100-0077).

1. Visitare la [pagina panoramica sul Marketplace commerciale nel centro per i partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se nel riquadro di spostamento a sinistra viene visualizzato "Marketplace commerciale", l'utente viene registrato ed è necessario procedere al passaggio successivo. In caso contrario, iscriversi subito al [Marketplace commerciale](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
2. Verificare che le offerte siano in AppSource [cercando le offerte](https://appsource.microsoft.com/). Se le offerte sono già presenti in AppSource, procedere al passaggio successivo. Per qualsiasi offerta non disponibile in AppSource, creare una [nuova offerta dynamics 365 for Customer Engagement](create-new-customer-engagement-offer.md) o una [nuova offerta Dynamics 365 for Operations](create-new-operations-offer.md).
3. Verificare la registrazione nel programma di Business Applications ISV Connect:
  
   * Nella pagina [contratti](https://partner.microsoft.com/dashboard/account/agreements) del centro per i partner verificare di aver accettato il **Business Applications addendum ISV** per la registrazione al programma.
   * Nella pagina [Impostazioni account](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) specificare le informazioni di fatturazione.

4. Invia ogni offerta nuova ed esistente per la certificazione, anche se le tue offerte sono state precedentemente certificate. **È consigliabile inviare il prima possibile per concedere tempo sufficiente per l'approvazione prima del 31 agosto 2019.**
5. Passare al [portale di GTM di un partner commerciale](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) e aggiungere l'URL dell'elenco di AppSource nella sezione collegamenti del Marketplace. Per informazioni su questo passaggio, inviare un messaggio di posta cosell@microsoft.comelettronica all'indirizzo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestisci l'account del Marketplace commerciale nel centro per i partner](./manage-account.md) 
