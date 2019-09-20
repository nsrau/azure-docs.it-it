---
title: Migrazione degli account da portale Cloud Partner al centro per i partner-Marketplace commerciale per Azure
description: Come eseguire la migrazione dell'account da CPP al centro per i partner. -Marketplace commerciale per Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147129"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrazione degli account da portale Cloud Partner al centro per i partner

Se si dispone di un account CPP esistente, è necessario eseguire la migrazione delle impostazioni dell'account nel centro per i partner.

## <a name="account-migration-process"></a>Processo di migrazione degli account

Se si è un utente con il ruolo di proprietario in CPP per un determinato account, nella pagina del profilo di pubblicazione verrà visualizzato un banner giallo. È possibile che si appartenga a uno dei due casi seguenti:

- È già stata eseguita la migrazione dell'account e si è pronti per gestire le impostazioni dell'account nel centro per i partner.
- Per completare la migrazione dell'account al centro per i partner, è necessario eseguire altre azioni.

### <a name="your-account-has-been-migrated-to-partner-center"></a>È stata eseguita la migrazione dell'account al centro per i partner

Per tutti gli account che hanno completato la migrazione da CPP al centro per i partner, la gestione degli account viene eseguita nel centro per i partner. Le modifiche, ad esempio l'aggiunta o l'eliminazione dell'utente, verranno sincronizzate con CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>L'account non è ancora stato migrato al centro per i partner

Fare clic sul banner per avviare il processo di migrazione degli account. Si prevede di immettere gli elementi seguenti:

1. Indirizzo di posta elettronica dell'ufficio: <br> <br> Nella maggior parte dei casi, accedere con l'indirizzo di posta elettronica usato per accedere a CPP. In alcuni casi, è necessario usare un indirizzo di posta elettronica diverso:

    * Account Microsoft: Se l'account CPP è una account Microsoft, è necessario immettere un indirizzo di posta elettronica di lavoro valido associato al tenant, per il quale l'ID MPN è registrato.

    * Tenant non corrispondente: Se l'indirizzo di posta elettronica dell'ufficio non appartiene al tenant associato all'ID Microsoft Partner Network presente nell'account CPP, verrà visualizzato un errore. Per superare questo errore, immettere un indirizzo di posta elettronica associato al tenant. Un messaggio di errore fornirà il nome del tenant.

2. Iscriversi al programma Microsoft Partner Network

    Nel caso in cui l'account CPP non disponga di un ID Microsoft Partner Network o di un account non valido, sarà necessario iscriversi al programma di Microsoft Partner Network come parte del processo di attivazione.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Iscriversi al programma Microsoft Partner Network

Le aziende che desiderano collaborare con Microsoft devono partecipare al Microsoft Partner Network (MPN) e ottenere un ID MPN. Se si è già membri del Microsoft Partner Network e si dispone di un ID MPN, assicurarsi di averlo a disposizione perché sarà necessario immetterlo durante il processo di attivazione dell'account.  

Se non si è ancora membri del Microsoft Partner Network, è possibile [partecipare ora](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) per ottenere un ID MPN. Assicurarsi di prendere nota dell'ID MPN, perché sarà necessario immetterlo durante il processo di attivazione dell'account.

Per ulteriori informazioni sulla Microsoft Partner Network, vedere [partecipare al Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) nel sito Web del partner. Per ulteriori informazioni su come partecipare ai Microsoft Partner Network i vantaggi degli ISV, vedere l' [Hub di risorse ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Spostare soluzioni basate su Dynamics 365 nel centro per i partner

Se è stato creato Dynamics 365 per Customer Engagement o Dynamics 365 per le soluzioni Finance e Operations nel portale GTM di un partner commerciale, **queste soluzioni verranno ora gestite nel centro per i partner**.

**Se non sono state spostate le soluzioni entro il 31 agosto 2019**, completare i passaggi seguenti appena possibile. Fino a quando non si esegue questa operazione:

- Gli ISV non avranno accesso ai vantaggi di marketing
- La co-selling con priorità perderà il proprio stato
- I requisiti di incorporamento del cloud non saranno conformi dopo il 15 ottobre 2019

> [!NOTE]
> Se l'account di appartenenza MPN è stato originariamente creato in partner Membership Center (PMC), accedere a [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) per confermare che l'account è stato migrato prima di completare i passaggi seguenti. Se viene visualizzata una schermata del profilo con l'ID MPN, si è pronti per continuare. In caso contrario, è necessario avviare la migrazione dell'account seguendo le istruzioni riportate nel [centro di appartenenza partner](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Per assistenza, visitare il [supporto tecnico](https://partner.microsoft.com/support?issueid=100-0077).

1. Visitare la [pagina panoramica sul Marketplace commerciale nel centro per i partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se nel riquadro di spostamento a sinistra viene visualizzato "Marketplace commerciale", l'utente viene registrato ed è necessario procedere al passaggio successivo. In caso contrario, [iscriversi subito al Marketplace commerciale](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .

2. Verificare che le offerte siano in AppSource [cercando le offerte](https://appsource.microsoft.com/). Se le offerte sono già presenti in AppSource, procedere al passaggio successivo. Per qualsiasi offerta non disponibile in AppSource, creare una [nuova offerta dynamics 365 for Customer Engagement](create-new-customer-engagement-offer.md) o una [nuova offerta Dynamics 365 for Operations](create-new-operations-offer.md).

3. Verificare la registrazione nel programma di Business Applications ISV Connect:
   * Nella pagina [contratti](https://partner.microsoft.com/dashboard/account/agreements) del centro per i partner verificare di aver accettato il **Business Applications addendum ISV** per la registrazione al programma.
   * Nella pagina [Impostazioni account](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) specificare le informazioni di fatturazione.

4. Invia ogni offerta nuova ed esistente per la certificazione, anche se le tue offerte sono state precedentemente certificate. Se idoneo, è possibile richiedere di partecipare al livello Premium durante questo processo. Se l'offerta è stata precedentemente certificata, **è necessario completare la ricertificazione dell'app entro il 15 ottobre 2019.** Per la certificazione o la ricertificazione è necessario che l'app supporti la versione più recente della piattaforma Business Applications.

5. Passare al [portale di GTM di un partner commerciale](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) e aggiungere l'URL dell'elenco di AppSource nella sezione collegamenti del Marketplace. Per informazioni su questo passaggio, inviare un messaggio di posta cosell@microsoft.comelettronica all'indirizzo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestisci l'account del Marketplace commerciale nel centro per i partner](./manage-account.md) 
