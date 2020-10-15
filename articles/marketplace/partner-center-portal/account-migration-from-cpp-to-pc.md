---
title: Eseguire la migrazione degli account dal portale Cloud Partner al marketplace commerciale Microsoft
description: Informazioni su come eseguire la migrazione dell'account dal portale Cloud Partner al Centro per i partner nel marketplace commerciale Microsoft per Azure
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 09/23/2019
ms.openlocfilehash: fbf76f846395d6bf0b02828ab1d6ad8b70c97784
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289339"
---
# <a name="how-to-migrate-your-account-from-cloud-partner-portal-to-partner-center"></a>Come eseguire la migrazione dell'account dal portale Cloud Partner al Centro per i partner

Se si ha un account del portale Cloud Partner (CPP), è necessario eseguire la migrazione delle impostazioni dell'account nel Centro per i partner.

## <a name="account-migration-process"></a>Processo di migrazione dell'account

Se si è un utente con il ruolo di proprietario nel portale Cloud Partner per un determinato account, nella pagina del profilo editore verrà visualizzato un banner giallo. Lo scenario può essere uno dei due casi seguenti:

- È già stata eseguita la migrazione dell'account e si è pronti per gestire le impostazioni dell'account nel Centro per i partner.
- Non è stata eseguita la migrazione nel Centro per i partner ed è necessario intervenire.

### <a name="your-account-has-been-migrated-to-partner-center"></a>È stata eseguita la migrazione dell'account nel Centro per i partner

È ora possibile gestire l'account nel centro per i partner. Le modifiche, ad esempio l'aggiunta o l'eliminazione di utenti, verranno sincronizzate di nuovo nel portale Cloud Partner.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Non è ancora stata eseguita la migrazione dell'account nel Centro per i partner

Fare clic sul banner per avviare il processo di migrazione dell'account. Sarà necessario immettere gli elementi seguenti:

1. Indirizzo di posta elettronica aziendale: <br> <br> Nella maggior parte dei casi, è possibile accedere con l'indirizzo di posta elettronica usato per accedere al portale Cloud Partner. In alcuni casi, è necessario usare un indirizzo di posta elettronica diverso:

    * Account Microsoft: se l'account del portale Cloud Partner è un account Microsoft, immettere un indirizzo di posta elettronica aziendale valido associato al tenant per il quale viene registrato l'ID Microsoft Partner Network (MPN). Per altre informazioni, vedere [Iscriversi al programma Microsoft Partner Network](#sign-up-for-microsoft-partner-network-program).

    * Tenant non corrispondente: se l'indirizzo di posta elettronica aziendale non appartiene al tenant associato all'ID Microsoft Partner Network presente nell'account del portale Cloud Partner, verrà visualizzato un errore. Per risolvere questo errore, immettere un indirizzo di posta elettronica associato al tenant. Un messaggio di errore fornirà il nome del tenant.

2. Iscriversi al programma Microsoft Partner Network

    Se l'account del portale Cloud Partner non dispone di un ID Microsoft Partner Network o ne ha uno non valido, sarà necessario iscriversi al programma Microsoft Partner Network come parte del processo di attivazione.

## <a name="publishers-moving-from-cpp"></a>Editori provenienti dal portale Cloud Partner

Se è stata eseguita la migrazione dell'account da CPP, non è necessario creare un nuovo account del centro per i partner. È necessario avere ricevuto un collegamento personalizzato al nuovo account del centro per i partner nella posta elettronica e in una notifica di banner dopo aver eseguito l'accesso all'account CPP esistente.

Dopo avere abilitato il nuovo account del Centro per i partner visitando questo collegamento personalizzato, è possibile tornare al proprio account visitando il [dashboard del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nel Centro per i partner.

Verrà eseguita la migrazione nel nuovo account del Centro per i partner del contratto di pubblicazione e delle informazioni sul profilo aziendale, insieme a tutte le informazioni sul profilo di pagamento dell'account configurate in precedenza, agli account utente e alle autorizzazioni, nonché alle offerte attive associate all'account del portale Cloud Partner.

Una volta che le informazioni sull'account sono state spostate dal portale Cloud Partner al Centro per i partner, il portale non verrà più usato per eseguire aggiornamenti dell'account o gestire utenti, autorizzazioni e fatturazione. Per un periodo di tempo limitato, gli aggiornamenti dell'account apportati nel Centro per i partner verranno automaticamente aggiornati nell'account del portale Cloud Partner di sola lettura fino a quando il portale Cloud Partner non è definitivamente deprecato.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Iscriversi al programma Microsoft Partner Network

Le aziende che vogliono collaborare con Microsoft devono partecipare al programma Microsoft Partner Network (MPN) e ottenere un ID MPN. Se si è già membri di Microsoft Partner Network e si ha un ID MPN, tenere a portata di mano le informazioni che saranno necessarie durante il processo di attivazione dell'account.  

Se non si è membri del Microsoft Partner Network, è possibile [iscriversi qui](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) per ottenere un ID MPN. Prendere nota dell'ID MPN, perché sarà necessario immetterlo durante il processo di attivazione dell'account.

Per altre informazioni su Microsoft Partner Network, vedere [Unisciti al Microsoft Partner Network](https://partner.microsoft.com/membership) nel sito Web dei partner. Per altre informazioni sui vantaggi ISV offerti da Microsoft Partner Network, vedere l'[hub di risorse ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Spostare le offerte Dynamics 365 e PowerApps nel Centro per i partner

Per semplificare la gestione di account e offerte per Dynamics 365 Customer Engagement, PowerApps e Dynamics 365 Operations, le offerte sono state spostate nel [Centro per i partner](https://partner.microsoft.com/). Lo spostamento garantisce che lo stesso contenuto sia disponibile per entrambi i cataloghi, per il pubblico e per i rivenditori.

Per informazioni specifiche su ciò che è necessario fare entro il **15 ottobre 2019** per le offerte Dynamics 365 Customer Engagement, PowerApps e Dynamics 365 Operations, seguire le istruzioni riportate di seguito.

> [!NOTE]
> Le informazioni non si applicano alle offerte Dynamics 365 Business Central.  

1. Se l'account di iscrizione a MPN è stato originariamente creato nel Partner Membership Center (PMC), accedere al [Centro per i partner](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) per verificare che sia stata eseguita la migrazione dell'account. Se viene visualizzata una schermata del profilo con l'ID MPN, si è pronti per continuare. In caso contrario, è necessario avviare la migrazione dell'account seguendo le istruzioni nel [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Per assistenza, vedere la pagina del [supporto](https://partner.microsoft.com/support?issueid=100-0077).
2. Passare alla [pagina di panoramica del marketplace commerciale nel Centro per i partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se nel riquadro di spostamento a sinistra viene visualizzato "Marketplace commerciale", l'utente è registrato e può continuare con il passaggio successivo. In caso contrario, [registrarsi al marketplace commerciale](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership).
3. Verificare che le proprie offerte siano disponibili in AppSource [eseguendo una ricerca delle offerte](https://appsource.microsoft.com/). Se le offerte sono già presenti in AppSource, continuare con il passaggio successivo. Per le offerte non disponibili in AppSource, creare una [nuova offerta Dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) o una [nuova offerta Dynamics 365 Operations](create-new-operations-offer.md).
4. Nella [pagina dei contratti](https://partner.microsoft.com/dashboard/account/agreements) del Centro per i partner verificare di aver esaminato e accettato l'**Addendum ISV di Business Applications**.
5. Nella pagina [Impostazioni account](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) del Centro per i partner assicurarsi che le informazioni di fatturazione siano complete.
6. Inviare ogni offerta nuova ed esistente per la certificazione e la pubblicazione, anche se le offerte sono state certificate in precedenza.
    * Completare le schermate di informazioni, tra cui la fornitura dell'app per la certificazione e le informazioni di marketing. Selezionare **Invia** (nell'angolo superiore destro dello schermo) entro il **15 ottobre 2019**. Completare questi passaggi per evitare qualsiasi impatto sulla disponibilità dell'offerta.
    * Se si è idonei, è possibile richiedere di partecipare al livello Premium durante questo processo.
    * Per la certificazione o la ricertificazione è necessario che l'app supporti la versione più recente della piattaforma Business Applications.
    * Dopo che l'app è stata approvata, si riceverà un messaggio di posta elettronica per tornare all'offerta e selezionare "Go Live" per abilitare l'offerta in Microsoft AppSource.

## <a name="additional-resources"></a>Risorse aggiuntive

Nel sito [Microsoft Dynamics CRM](https://community.dynamics.com/crm?wa=wsignin1.0) è possibile ottenere assistenza da esperti e colleghi, oltre che trovare blog, webinar, video, eventi e molto altro.

Se è necessaria assistenza per la pubblicazione, la certificazione o la gestione delle offerte del marketplace, [inviare un ticket di supporto](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-step"></a>Passaggio successivo

- [Gestire l'account del marketplace commerciale nel Centro per i partner](./manage-account.md)
