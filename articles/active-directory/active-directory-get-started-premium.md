---
title: Effettuare l'iscrizione ad Azure Active Directory Premium
description: Descrive come effettuare l'iscrizione ad Azure Active Directory Premium.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: curtand
ms.custom: it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: fe9f4fa6f776a0c2edb572d0526c7520d1c1c7a0
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Guida introduttiva: Effettuare l'iscrizione ad Azure Active Directory Premium
Per iniziare a usare Azure Active Directory (Azure AD) Premium, è necessario acquistare licenze e associarle a una sottoscrizione di Azure. Se si crea una nuova sottoscrizione di Azure, è anche necessario attivare il piano di licenze e l'accesso al servizio Azure AD come descritto nelle sezioni seguenti. 

## <a name="sign-up-for-active-directory-premium"></a>Effettuare l'iscrizione ad Active Directory Premium
Sono disponibili diverse opzioni per l'iscrizione ad Active Directory Premium: 

### <a name="azure-or-office-365"></a>Azure o Office 365 
Un sottoscrittore di Azure o di Office 365 può acquistare Azure Active Directory Premium online. 

Per informazioni dettagliate vedere i video relativi a [come acquistare Azure Active Directory Premium (clienti esistenti)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) o [come acquistare Azure Active Directory Premium (nuovi clienti)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Enterprise Mobility + Security (in precedenza EMS) è una soluzione economicamente conveniente che consente alle organizzazioni di usare con un unico piano di licenze i servizi Azure Active Directory Premium, Azure Information Protection e Microsoft Intune. Altre informazioni su EMS sono disponibili nel [sito Web di Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security), mentre altre informazioni sui tipi di licenza EMS acquistabili sono disponibili nella pagina [Prezzi Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing).  

È possibile iniziare a usare Azure AD con le licenze di EMS usando una delle seguenti opzioni di licenza:

- Provare EMS con una [versione di valutazione gratuita di Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)
- Acquistare [licenze di Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Acquistare [licenze di Enterprise Mobility + Security E3](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Contratti multilicenza Microsoft
Azure Active Directory Premium è disponibile tramite un programma [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 o più licenze) o un programma [Open Volume License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (da 5 a 250 licenze).

Altre informazioni sulle opzioni di acquisto dei contratti multilicenza sono disponibili nella pagina [Come acquistare tramite i contratti multilicenza](https://www.microsoft.com/licensing/how-to-buy/how-to-buy.aspx).

> [!NOTE]
> Le edizioni Premium e Basic di Azure Active Directory sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

Se sono già state acquistate e attivate licenze di Azure AD per la stessa sottoscrizione di Azure usata nei passaggi precedenti, le licenze verranno attivate automaticamente nella stessa directory. Non sarà necessario proseguire con i passaggi descritti nella parte restante di questo articolo.

## <a name="activate-your-license-plan"></a>Attivare un piano di licenze
Se questo è il primo piano di licenze di Azure AD acquistato, verrà generato e inviato all'utente un messaggio di posta elettronica di conferma al completamento dell'acquisto, che è necessario per attivare il primo piano di licenze.

**Per attivare il piano di licenze, eseguire uno di questi passaggi:**

1. Per avviare l'attivazione, fare clic su **Accedi** o su **Iscriviti**.
   
    ![pagina di accesso][1]

    - Se è già disponibile un tenant, fare clic su **Accedi** per accedere con l'account amministratore esistente. Eseguire l'accesso con le credenziali di amministratore globale dalla directory in cui devono essere attivate le licenze.

    - Se si vuole creare un nuovo tenant di Azure AD da usare con il piano di licenze, fare clic su **Iscriviti** per aprire la finestra di dialogo **Creazione del profilo dell'account**.

        ![Creazione del profilo dell'account][2]

Al termine, viene visualizzata la finestra di dialogo seguente a conferma dell'attivazione del piano di licenze per il tenant:

![Conferma][3]

## <a name="activate-your-azure-active-directory-access"></a>Attivare l'accesso ad Azure Active Directory
Se si aggiungono nuove licenze di Azure AD Premium a una sottoscrizione esistente, l'accesso ad Azure AD dovrebbe già essere attivato. In caso contrario, sarà necessario attivare l'accesso ad Azure AD dopo aver ricevuto il **messaggio di posta elettronica di benvenuto**.  

Dopo il provisioning delle licenze acquistate nella directory, viene inviato all'utente un **messaggio di posta elettronica di benvenuto**. Il messaggio conferma che è possibile iniziare a gestire le licenze di Azure Active Directory Premium o Enterprise Mobility + Security e le relative funzionalità. 

> [!TIP]
> Non è possibile accedere ad Azure AD per il nuovo tenant fino a quando non si attiva l'accesso alla directory di Azure AD usando il messaggio di posta elettronica di benvenuto inviato automaticamente al termine del provisioning della licenza. 

**Per attivare l'accesso ad Azure AD, seguire questa procedura:**

1. Nell'**e-mail di benvenuto** fare clic su **Accedi**. 
   
    ![messaggio di posta elettronica di benvenuto][4]
2. Dopo aver eseguito l'accesso, sarà anche necessario completare l'autenticazione con un secondo fattore tramite un dispositivo mobile:
   
    ![Verifica per dispositivi mobili][5]

L'attivazione richiederà solo qualche minuto e si potrà poi accedere per gestire Azure AD. 

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come effettuare l'iscrizione ad Azure AD Premium e attivare l'accesso ad Azure Active Directory. 

Se è già disponibile una sottoscrizione di Azure, è possibile usare il collegamento seguente per avviare una versione di valutazione oppure acquistare licenze di Azure AD Premium dal portale di Azure.

> [!div class="nextstepaction"]
> [Attivare le licenze di Azure AD Premium](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade) 

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png
