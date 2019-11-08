---
title: Migrazione degli account da portale Cloud Partner al centro per i partner-Marketplace commerciale per Azure
description: Come eseguire la migrazione dell'account da CPP al centro per i partner. -Marketplace commerciale per Azure
author: ChJenk
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: f8644a2aa989b7013cbbd64e8a8194bc08f40251
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813110"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrazione degli account da portale Cloud Partner al centro per i partner

Se si dispone di un account portale Cloud Partner (CPP) esistente, è necessario eseguire la migrazione delle impostazioni dell'account nel centro per i partner.

## <a name="account-migration-process"></a>Processo di migrazione degli account

Se si è un utente con il ruolo di proprietario in CPP per un determinato account, nella pagina del profilo di pubblicazione verrà visualizzato un banner giallo. È possibile che si appartenga a uno dei due casi seguenti:

- È già stata eseguita la migrazione dell'account e si è pronti per gestire le impostazioni dell'account nel centro per i partner.
- L'account non è stato migrato al centro per i partner ed è necessario intervenire.

### <a name="your-account-has-been-migrated-to-partner-center"></a>È stata eseguita la migrazione dell'account al centro per i partner

Per tutti gli account che hanno completato la migrazione da CPP al centro per i partner, la gestione degli account viene eseguita nel centro per i partner. Le modifiche, ad esempio l'aggiunta o l'eliminazione dell'utente, verranno sincronizzate con CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>L'account non è ancora stato migrato al centro per i partner

Fare clic sul banner per avviare il processo di migrazione dell'account. Si prevede di immettere gli elementi seguenti:

1. Indirizzo di posta elettronica dell'ufficio: <br> <br> Nella maggior parte dei casi, accedere con l'indirizzo di posta elettronica usato per accedere a CPP. In alcuni casi, è necessario usare un indirizzo di posta elettronica diverso:

    * Account Microsoft: se l'account CPP è un account Microsoft, immettere un indirizzo di posta elettronica di lavoro valido associato al tenant per il quale viene registrato l'ID del Microsoft Partner Network (MPN). Per ulteriori informazioni, vedere la pagina [relativa all'iscrizione al programma Microsoft Partner Network](#sign-up-for-microsoft-partner-network-program).

    * Mancata corrispondenza del tenant: se l'indirizzo di posta elettronica dell'ufficio non appartiene al tenant associato all'ID Microsoft Partner Network presente nell'account CPP, verrà visualizzato un errore. Per superare questo errore, immettere un indirizzo di posta elettronica associato al tenant. Un messaggio di errore fornirà il nome del tenant.

2. Iscriversi al programma Microsoft Partner Network

    Se l'account CPP non dispone di un ID Microsoft Partner Network o non è valido, sarà necessario iscriversi al programma di Microsoft Partner Network come parte del processo di attivazione.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Iscriversi al programma Microsoft Partner Network

Le aziende che desiderano collaborare con Microsoft devono partecipare al Microsoft Partner Network (MPN) e ottenere un ID MPN. Se si è già membri del Microsoft Partner Network e si dispone di un ID MPN, è necessario tenere a portata di mano le informazioni necessarie durante il processo di attivazione dell'account.  

Se non si è membri del Microsoft Partner Network, è possibile [partecipare qui](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) per ottenere un ID MPN. Prendere nota dell'ID MPN, perché sarà necessario immetterlo durante il processo di attivazione dell'account.

Per ulteriori informazioni sulla Microsoft Partner Network, vedere [partecipare al Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) nel sito Web del partner. Per ulteriori informazioni sui vantaggi dell'ISV nella Microsoft Partner Network, vedere l' [Hub di risorse ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Sposta le offerte di Dynamics 365 e PowerApps al centro per i partner

Per semplificare la gestione di account e offerte per le operazioni di Dynamics 365 Customer Engagement, PowerApps e Dynamics 365, le offerte sono state spostate nel centro per i [partner](https://partner.microsoft.com/). Lo spostamento garantisce che lo stesso contenuto sia disponibile per i cataloghi Public e seller.

Per informazioni specifiche sulle operazioni che devono essere eseguite entro il **15 ottobre 2019** per le offerte di Dynamics 365 Customer Engagement, PowerApps e Dynamics 365, seguire le istruzioni riportate di seguito.

> [!NOTE]
> Questa operazione non si applica alle offerte di Dynamics 365 business Central.  

1. Se l'account di appartenenza MPN è stato originariamente creato in partner Membership Center (PMC), accedere a [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) per confermare che l'account è stato migrato. Se viene visualizzata una schermata del profilo con l'ID MPN, si è pronti per continuare. In caso contrario, è necessario avviare la migrazione dell'account seguendo le istruzioni riportate nel [centro di appartenenza partner](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Per assistenza, visitare il [supporto tecnico](https://partner.microsoft.com/support?issueid=100-0077).
2. Visitare la [pagina panoramica sul Marketplace commerciale nel centro per i partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se nel riquadro di spostamento a sinistra viene visualizzato "Marketplace commerciale", l'utente è registrato e dovrebbe continuare con il passaggio successivo. In caso contrario, [iscriversi subito al Marketplace commerciale](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
3. Verificare che le offerte siano in AppSource [cercando le offerte](https://appsource.microsoft.com/). Se le offerte sono già presenti in AppSource, continuare con il passaggio successivo. Per qualsiasi offerta non disponibile in AppSource, creare una [nuova offerta dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) o una [nuova offerta per le operazioni di Dynamics 365](create-new-operations-offer.md).
4. Nella [pagina dei contratti](https://partner.microsoft.com/dashboard/account/agreements)del centro per i partner verificare di aver esaminato e accettato il **Business Applications addendum ISV**.
5. In [Impostazioni account](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)del centro per i partner verificare che le informazioni di fatturazione siano complete.
6. Invia ogni offerta nuova ed esistente per la certificazione e la pubblicazione, anche se le tue offerte sono state precedentemente certificate.
    * Completare le schermate delle informazioni, inclusa la fornitura dell'app per la certificazione, nonché informazioni di marketing. Selezionare **Invia** (angolo superiore destro dello schermo) entro il **15 ottobre 2019**. Questi passaggi devono essere completati per evitare di incidere sulla disponibilità dell'offerta.
    * Se idoneo, è possibile richiedere di partecipare al livello Premium durante questo processo.
    * Per la certificazione o la ricertificazione è necessario che l'app supporti la versione più recente della piattaforma Business Applications.
    * Dopo che l'app è stata approvata, si riceverà un messaggio di posta elettronica per tornare all'offerta e selezionare "Go Live" per abilitare l'offerta per l'uso in Microsoft AppSource.

## <a name="additional-resources"></a>Risorse aggiuntive

Unisciti alla [chiamata della community di Dynamics ISV](https://aka.ms/DynamicsISV-CommunityCall) settimanale per il supporto e gli aggiornamenti.

Se è necessario assistenza per la pubblicazione, la certificazione o la gestione delle offerte del Marketplace, [inviare un ticket di supporto](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Passaggi successivi

- [Gestisci l'account del Marketplace commerciale nel centro per i partner](./manage-account.md)
