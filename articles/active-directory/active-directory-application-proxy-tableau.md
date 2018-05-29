---
title: Azure Active Directory Application Proxy e Tableau | Microsoft Docs
description: Informazioni su come usare Azure Active Directory Application Proxy per fornire l'accesso remoto alla distribuzione Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7fa8654e413ac337994d35afdce7bd3478127f64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070873"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy e Tableau 

Azure Active Directory Application Proxy e Tableau hanno collaborato per permettere di usare con facilità il proxy di applicazione e fornire l'accesso remoto alla distribuzione Tableau. Questo articolo spiega come configurare questo scenario.  

## <a name="prerequisites"></a>prerequisiti 

Per configurare lo scenario presentato in questo articolo si presuppone che l'utente abbia:

- Configurato [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure). 

- Installato un [connettore proxy di applicazione](manage-apps/application-proxy-enable.md). 

 

## <a name="enabling-application-proxy-for-tableau"></a>Abilitazione del proxy di applicazione per Tableau 

Per usare il proxy di applicazione per Tableau, è necessario inviare un'e-mail all'indirizzo [aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) per abilitare questo scenario.
Nel messaggio di posta elettronica:

-   Come oggetto, usare Abilitare il proxy di applicazione per Tableau
-   Includere l'ID del tenant nel corpo del messaggio    

Quando si è pronti a usare l'applicazione viene visualizzato un messaggio di conferma. In genere, è necessaria una settimana. In attesa della conferma, sarà tuttavia possibile completare le configurazioni.


 

## <a name="publish-your-applications-in-azure"></a>Pubblicare applicazioni in Azure 

Per pubblicare Tableau è necessario pubblicare un'applicazione nel portale di Azure.

Per:

- Per istruzioni sui passaggi 1-8, vedere [Pubblicare applicazioni con Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md). 
- Informazioni su come trovare i valori Tableau per i campi del proxy di applicazione, vedere la documentazione di Tableau.  

**Per pubblicare l'app**: 


1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale. 

2. Selezionare **Azure Active Directory > Applicazioni aziendali**. 

3. Selezionare **Aggiungi** nella parte superiore del pannello. 

4. Selezionare **Applicazione locale**. 

5. Compilare i campi obbligatori con le informazioni della nuova app. Usare le seguenti linee guida per le impostazioni: 

    - **URL interno**: questa applicazione deve avere un URL interno, che corrisponde all'URL Tableau. Ad esempio, `https://adventure-works.tableau.com`. 

    - **Metodo di autenticazione preliminare**: Azure Active Directory (consigliato ma non obbligatorio). 

6. Selezionare **Aggiungi** nella parte superiore del pannello. L'applicazione viene aggiunta e si apre il menu di avvio rapido. 

7. Nel menu di avvio rapido selezionare **Assegna utente per il test** e aggiungere almeno un utente all'applicazione. Assicurarsi che questo account di test abbia accesso all'applicazione locale. 

8. Selezionare **Assegna** per salvare l'assegnazione dell'utente di test. 

9. (Facoltativo) Nella pagina di gestione dell'app selezionare **Single Sign-On**. Scegliere **Autenticazione integrata di Windows** dal menu a discesa e compilare i campi obbligatori in base alla configurazione Tableau. Selezionare **Salva**. 

 

## <a name="testing"></a>Test 

L'applicazione è ora pronta per il test. Accedere all'URL esterno usato per pubblicare Tableau e accedere come utente assegnato a entrambe le applicazioni.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory Application Proxy, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](manage-apps/application-proxy.md).

