---
title: Migrazione degli account dal portale Cloud Partner al Centro per i partner - mercato commerciale per Azure
description: Come eseguire la migrazione dell'account da CPP al Centro per i partner - Marketplace commerciale per AzureHow to migrate your account from CPP to Partner Center - commercial marketplace for Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 49fb2da05b7284fff10e6271de177551000995ed
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024422"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migrazione degli account dal portale Cloud Partner al Centro per i partner

Se si dispone di un account Cloud Partner Portal (CPP) esistente, è necessario eseguire la migrazione delle impostazioni dell'account al Centro per i partner.

## <a name="account-migration-process"></a>Processo di migrazione degli account

Se sei un utente con il ruolo Proprietario in CPP per un determinato account, viene visualizzato un banner giallo nella pagina del profilo di publisher. È possibile appartenere a uno dei due casi seguenti:

- Il tuo account è già stato migrato e sei pronto per gestire le impostazioni dell'account nel Centro per i partner.
- Non è stata eseguita la migrazione dell'account al Centro per i partner ed è necessario eseguire ulteriori azioni.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Il tuo account è stato migrato nel Centro per i partner

Per tutti gli account che hanno completato la migrazione da CPP al Centro per i partner, la gestione degli account verrà eseguita nel Centro per i partner. Modifiche come l'aggiunta/eliminazione dell'utente verranno sincronizzate con CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Non è ancora stata eseguita la migrazione dell'account nel Centro per i partner

Fare clic sul banner per avviare il processo di migrazione dell'account. È necessario immettere i seguenti elementi:

1. Indirizzo e-mail di lavoro: <br> <br> Nella maggior parte dei casi, accedere con l'indirizzo di posta elettronica utilizzato per accedere a CPP. In alcuni casi, è necessario utilizzare un indirizzo e-mail diverso:

    * Account Microsoft: se l'account CPP è un account Microsoft, immettere un indirizzo di posta elettronica di lavoro valido associato al tenant per il quale è registrato l'ID Microsoft Partner Network (MPN). Per ulteriori informazioni, vedere [Iscriversi al programma Microsoft Partner Network.](#sign-up-for-microsoft-partner-network-program)

    * Mancata corrispondenza del tenant: se l'indirizzo di posta elettronica di lavoro non appartiene al tenant associato all'ID Microsoft Partner Network presente nell'account CPP, verrà visualizzato un errore. Per spostarsi oltre questo errore, immettere un indirizzo di posta elettronica associato al tenant. Un messaggio di errore fornirà il nome del tenant.

2. Iscriversi al programma Microsoft Partner Network

    Se l'account CPP non dispone di un ID Microsoft Partner Network o ne ha uno non valido, sarà necessario iscriversi al programma Microsoft Partner Network come parte del processo di attivazione.

## <a name="publishers-moving-from-cpp"></a>Editori che si spostano da CPP

Se l'account è stato migrato dal [portale Cloud Partner (CPP),](https://cloudpartner.azure.com)non è necessario creare un nuovo account del Centro per i partner. Dovresti aver ricevuto un link personalizzato al tuo nuovo account del Centro per i partner tramite e-mail e in una notifica banner dopo aver effettuato l'accesso al tuo account CPP esistente.

Dopo aver abilitato il nuovo account del Centro per i partner visitando questo collegamento personalizzato, è possibile tornare al proprio account visitando il dashboard del [marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nel Centro per i partner.

Il contratto di pubblicazione e le informazioni del profilo aziendale verranno migrati nel nuovo account del Centro per i partner, insieme a tutte le informazioni del profilo di pagamento dell'account configurate in precedenza, agli account utente e alle autorizzazioni e alle offerte attive associate al tuo account CPP.

Dopo aver spostato le informazioni dell'account da CPP a Centro per i partner, non userai più CPP per effettuare aggiornamenti dell'account o gestire utenti, autorizzazioni e fatturazione. Per un periodo di tempo limitato, tutti gli aggiornamenti dell'account eseguiti nel Centro per i partner verranno aggiornati automaticamente nell'account CPP di sola lettura fino a quando il portale CPP non sarà deprecato.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Iscriversi al programma Microsoft Partner Network

Le aziende che desiderano collaborare con Microsoft devono partecipare al Microsoft Partner Network (MPN) e ottenere un ID MPN. Se sei già un membro del Microsoft Partner Network e hai un ID MPN, tieni a portata di mano le informazioni in base alle tue esigenze durante il processo di attivazione dell'account.  

Se non sei un membro del Microsoft Partner Network, puoi [unirti qui](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) per ottenere un ID MPN. Prendi nota del tuo ID MPN poiché dovrai inserirlo durante il processo di attivazione dell'account.

Per altre informazioni sul Microsoft Partner Network, vedere [Join the Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) on the partner website. Per ulteriori informazioni sui vantaggi di ISV nella Microsoft Partner Network, vedere [l'HUB delle risorse ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Spostare le offerte di Dynamics 365 e PowerApps nel Centro per i partner

Per semplificare la gestione di account e offerte per Dynamics 365 Customer Engagement, PowerApps e Dynamics 365 Operations, le offerte sono state spostate nel Centro per i [partner.](https://partner.microsoft.com/) La mossa garantisce che lo stesso contenuto sia disponibile sia per i cataloghi pubblici che per quelli venditori.

Per informazioni specifiche sulle operazioni da eseguire entro il **15 ottobre 2019** per le offerte di Dynamics 365 Customer Engagement, PowerApps e Dynamics 365 Operations, segui le istruzioni riportate di seguito.

> [!NOTE]
> Ciò non si applica alle offerte Dynamics 365 Business Central.  

1. Se l'account di appartenenza MPN è stato originariamente creato nel Partner Membership Center (PMC), accedi al [Centro per](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) i partner per verificare che sia stato migrato l'account. Se viene visualizzata una schermata del profilo con l'ID MPN, è possibile continuare. In caso contrario, è necessario avviare la migrazione dell'account seguendo le istruzioni visualizzate nel [Centro appartenenze](https://partners.microsoft.com/partnerprogram/Welcome.aspx)ai partner . Se hai bisogno di aiuto, visita [il supporto](https://partner.microsoft.com/support?issueid=100-0077).
2. Vai alla [pagina Panoramica del Marketplace commerciale nel Centro per i partner.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Se vedi "Mercato commerciale" nel riquadro di spostamento a sinistra, sei iscritto e dovresti continuare con il passaggio successivo. In caso contrario, [iscriviti subito al mercato commerciale.](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
3. Verifica che le tue offerte siano in AppSource [cercando le tue offerte.](https://appsource.microsoft.com/) Se le offerte sono già in AppSource, continuare con il passaggio successivo. Per qualsiasi offerta non presente in AppSource, crea una [nuova offerta Di Dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) o una nuova offerta Dynamics [365 Operations.](create-new-operations-offer.md)
4. Nella [pagina Accordi](https://partner.microsoft.com/dashboard/account/agreements)del Centro per i partner, verificare di aver esaminato e accettato l'Addendum **ISV delle applicazioni aziendali.**
5. Nelle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)del Centro per i partner, assicurati che le informazioni di fatturazione siano complete.
6. Invia ogni offerta nuova ed esistente per la certificazione e la pubblicazione, anche se le tue offerte sono state precedentemente certificate.
    * Completa le schermate informative, inclusa la fornitura dell'app per la certificazione, nonché le informazioni di marketing. Seleziona **Invia** (angolo in alto a destra dello schermo) entro il **15 ottobre 2019**. Questi passaggi devono essere completati per evitare di influire sulla disponibilità dell'offerta.
    * Se idoneo, puoi richiedere di partecipare al livello premium durante questo processo.
    * La certificazione o la ricertificazione richiede che la tua app supporti la versione più recente della nostra piattaforma di applicazioni aziendali.
    * Una volta che l'app è stata approvata, riceverai un'email per tornare all'offerta e seleziona "vai in diretta" per abilitare l'offerta per la trasmissione su Microsoft AppSource.

## <a name="additional-resources"></a>Risorse aggiuntive

Ottieni assistenza da esperti e colleghi nei forum e scopri blog, webinar, video, eventi e altro ancora in [Microsoft Dynamics CRM.](https://community.dynamics.com/crm?wa=wsignin1.0)

Se hai bisogno di aiuto per la pubblicazione, la certificazione o la gestione delle offerte del marketplace, invia un ticket di [supporto.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="next-step"></a>Passaggio successivo

- [Gestire l'account del marketplace commerciale nel Centro per i partner](./manage-account.md)
