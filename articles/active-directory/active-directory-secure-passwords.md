---
title: Proteggere le password in Azure AD e reimpostare le password bloccate da Smart Password Lockout | Microsoft Docs
description: Illustra il significato di tenant di Azure AD e come gestire Azure con Azure Active Directory
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Proteggere le password in Azure AD e reimpostare le password bloccate da Smart Password Lockout
Questo articolo illustra le procedure consigliate che è possibile seguire come utente o amministratore per proteggere gli account di Azure Active Directory (Azure AD) e del servizio account Microsoft. 

 >[!NOTE]
 >Gli amministratori di Azure AD possono reimpostare le password utente facendo clic sul nome della directory. Nel [portale di gestione di Azure](https://manage.windowsazure.com) scegliere la pagina Utenti, fare clic sul nome dell'utente e reimpostare la password. 
 >

Azure AD adotta i seguenti approcci comuni per la protezione delle password:
 *    Requisiti di lunghezza delle password
 *    Requisiti di complessità delle password
 *    Scadenza regolare e periodica delle password 

Per informazioni sulle funzionalità di gestione delle password, vedere [Gestire password in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Protezione delle password di Azure AD
Azure AD e il sistema di account Microsoft usano approcci di settore comprovati per assicurare la protezione delle password di utenti e amministratori. 

Questa sezione descrive il modo in cui Azure AD protegge le password con i metodi seguenti:
 *    Password vietate in modo dinamico
 *    Smart Password Lockout

Per informazioni sulla gestione delle password in base alla ricerca corrente, vedere il white paper [Password Guidance](http://aka.ms/passwordguidance) (Indicazioni sulle password). 

### <a name="dynamically-banned-passwords"></a>Password vietate in modo dinamico
Azure AD e il sistema di account Microsoft salvaguardano la protezione delle password vietando in modo dinamico tutte le password usate comunemente. Il team di Azure AD Identity Protection analizza periodicamente gli elenchi delle password vietate, impedendo agli utenti di selezionare le password usate comunemente. Questo servizio è disponibile in Azure AD e per i clienti del servizio account Microsoft. 

Quando si creano password è importante che gli amministratori invitino gli utenti a scegliere password non comuni che includano una combinazione univoca di lettere, numeri e caratteri. In questo modo sarà pressoché impossibile compromettere le password degli utenti. 

**Elenchi di violazioni**

Azure AD cerca sempre di anticipare le mosse dei criminali informatici. Un modo per conseguire questo obiettivo è impedire agli utenti di creare password presenti nell'elenco corrente degli attacchi.

Il team di Azure AD Identity Protection analizza costantemente le password più comuni. Anche i criminali informatici usano strategie simili per gli attacchi, ad esempio creando un [tabella arcobaleno](https://en.wikipedia.org/wiki/Rainbow_table) per violare gli hash delle password. 

Microsoft analizza continuamente le [violazioni di dati](https://www.privacyrights.org/data-breaches) per mantenere un elenco di password da vietare aggiornato in modo dinamico che assicura l'esclusione delle password vulnerabili prima che diventino una minaccia reale per i clienti di Azure AD. Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx). 

### <a name="smart-password-lockout"></a>Smart Password Lockout

Quando Azure AD rileva un potenziale criminale informatico che prova a violare una password utente, l'account utente viene bloccato con Smart Password Lockout. Azure AD è progettato per determinare i rischi associati a sessioni di accesso specifiche. 

Usando i dati di sicurezza più aggiornati Microsoft applica la semantica di blocco alle minacce informatiche. In questo modo gli utenti non verranno bloccati se un criminale informatico viola le password nella rete.

Se un utente viene bloccato da Azure AD, la schermata sarà simile alla seguente:

  ![Bloccato da Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Per altri account Microsoft, la schermata sarà simile alla seguente:

  ![Bloccato da un account Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Per informazioni sulla gestione delle password in Azure Active Directory, vedere [Funzionamento della gestione delle password](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works).

  >![NOTA] Se si è amministratori di Azure AD è consigliabile usare [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) per evitare che gli utenti creino password tradizionali.
  >

## <a name="next-steps"></a>Passaggi successivi
[Come aggiornare la password](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Concetti fondamentali sulla gestione delle identità di Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Come ottenere informazioni operative con i report di gestione delle password](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



