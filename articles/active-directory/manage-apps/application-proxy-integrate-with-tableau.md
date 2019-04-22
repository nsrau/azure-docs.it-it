---
title: Azure Active Directory Application Proxy e Tableau | Microsoft Docs
description: Informazioni su come usare Azure Active Directory Application Proxy per fornire l'accesso remoto alla distribuzione Tableau.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d80381815524658d91067ee880eba47c394058
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783554"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy e Tableau 

Azure Active Directory Application Proxy e Tableau hanno collaborato per permettere di usare con facilità il proxy di applicazione e fornire l'accesso remoto alla distribuzione Tableau. Questo articolo spiega come configurare questo scenario.  

## <a name="prerequisites"></a>Prerequisiti 

Per configurare lo scenario presentato in questo articolo si presuppone che l'utente abbia:

- Configurato [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure). 

- Installato un [connettore proxy di applicazione](application-proxy-add-on-premises-application.md). 

 
## <a name="enabling-application-proxy-for-tableau"></a>Abilitazione del proxy di applicazione per Tableau 

Il proxy di applicazione supporta il flusso di concessione di OAuth 2.0, necessario perché Tableau funzioni correttamente. Ciò significa che non sono più presenti passaggi speciali necessari per abilitare questa applicazione, ma è possibile configurarla seguendo la procedura seguente per la pubblicazione.


## <a name="publish-your-applications-in-azure"></a>Pubblicare applicazioni in Azure 

Per pubblicare Tableau è necessario pubblicare un'applicazione nel portale di Azure.

Per:

- Per istruzioni sui passaggi 1-8, vedere [Pubblicare applicazioni con Azure AD Application Proxy](application-proxy-add-on-premises-application.md). 
- Informazioni su come trovare i valori Tableau per i campi del proxy di applicazione, vedere la documentazione di Tableau.  

**Per pubblicare l'app**: 


1. Accedi per il [portale di Azure](https://portal.azure.com) come amministratore dell'applicazione. 

2. Selezionare **Azure Active Directory > Applicazioni aziendali**. 

3. Selezionare **Aggiungi** nella parte superiore del pannello. 

4. Selezionare **Applicazione locale**. 

5. Compilare i campi obbligatori con le informazioni della nuova app. Usare le seguenti linee guida per le impostazioni: 

    - **URL interno**: l'applicazione deve avere un URL interno, che corrisponde all'URL Tableau. Ad esempio: `https://adventure-works.tableau.com`. 

    - **Metodo di preautenticazione**: Azure Active Directory (consigliato ma non obbligatorio). 

6. Selezionare **Aggiungi** nella parte superiore del pannello. L'applicazione viene aggiunta e si apre il menu di avvio rapido. 

7. Nel menu di avvio rapido selezionare **Assegna utente per il test** e aggiungere almeno un utente all'applicazione. Assicurarsi che questo account di test abbia accesso all'applicazione locale. 

8. Selezionare **Assegna** per salvare l'assegnazione dell'utente di test. 

9. (Facoltativo) Nella pagina di gestione dell'app selezionare **Single Sign-On**. Scegliere **Autenticazione integrata di Windows** dal menu a discesa e compilare i campi obbligatori in base alla configurazione Tableau. Selezionare **Salva**. 

 

## <a name="testing"></a>Test 

L'applicazione è ora pronta per il test. Accedere all'URL esterno usato per pubblicare Tableau e accedere come utente assegnato a entrambe le applicazioni.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory Application Proxy, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](application-proxy.md).

