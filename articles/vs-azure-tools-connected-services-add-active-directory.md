---
title: Aggiunta di Azure Active Directory tramite Servizi connessi di Visual Studio | Documentazione Microsoft
description: Aggiungere un'istanza di Azure Active Directory usando la finestra di dialogo Aggiungi Servizi connessi di Visual Studio
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Aggiunta di Azure Active Directory mediante servizi connessi in Visual Studio
Tramite Azure Active Directory (Azure AD), è possibile supportare Single Sign-On (SSO) per le applicazioni Web ASP.NET MVC o l'autenticazione di Active Directory nei servizi Web API. Con l'autenticazione Azure Active Directory, gli utenti possono usare gli account da Azure Active Directory per connettersi alle applicazioni Web. I vantaggi dell'autenticazione di Azure Active Directory con l'API Web comprendono la protezione avanzata dei dati quando si espone un'API da un'applicazione Web. Con Azure AD, non è necessario gestire un sistema di autenticazione distinto con la gestione di account e utente.

## <a name="prerequisites"></a>prerequisiti
- Account di Azure: se non si ha un account di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi della sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Connettersi ad Azure Active Directory usando la finestra di dialogo dei servizi connessi
1. In Visual Studio creare o aprire un progetto ASP.NET MVC o un progetto API Web ASP.NET.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo **Servizi connessi** e scegliere **Add Connected Services** (Aggiungi servizi connessi) dal menu di scelta rapida.

1. Nella pagina di **Servizi connessi** selezionare **Autenticazione con Azure Active Directory**.
   
    ![Pagina di Servizi connessi](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Nella pagina **Introduzione** della procedura guidata **Configura Autenticazione di Azure AD**, selezionare **Avanti**.
   
    ![Pagina Introduzione](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. Nella pagina **Single-Sign On** della procedura guidata **Configura Autenticazione di Azure AD** selezionare un dominio dall'elenco a discesa **Dominio**. L'elenco di domini contiene tutti i domini accessibili dagli account elencati nella finestra di dialogo Impostazioni Account. In alternativa, è possibile immettere un nome di dominio se non è possibile trovare quello che si sta cercando, ad esempio `mydomain.onmicrosoft.com`. È possibile scegliere l'opzione per creare una nuova app di Azure Active Directory o usare le impostazioni di un'app di Azure Active Directory esistente. Al termine scegliere **Avanti**.
   
    ![Pagina Single Sign-On](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. Nella pagina **Accesso alla directory** della procedura guidata **Configura Autenticazione di Azure AD**, assicurarsi che l'opzione **Lettura dati directory** sia selezionata. 
   
    ![Pagina di accesso alla directory](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Selezionare **Fine** per aggiungere il codice di configurazione necessario e i riferimenti per abilitare il progetto per l'autenticazione di Azure AD. È possibile visualizzare il dominio di Active Directory nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. In Visual Studio verrà visualizzato un articolo [What Happened](#how-your-project-is-modified) (Risultati) che mostra le modifiche apportate al progetto. Per controllare che tutte le operazioni siano state eseguite correttamente, aprire uno dei file di configurazione modificati, quindi verificare che le impostazioni indicate nell'articolo siano presenti. 

## <a name="how-your-project-is-modified"></a>Come viene modificato il progetto
Quando si esegue la procedura guidata, Visual Studio aggiunge Azure Active Directory e i riferimenti associati al progetto. Anche i file di configurazione e i file di codice nel progetto vengono modificati per aggiungere supporto per Azure AD. Le modifiche specifiche apportate in Visual Studio dipendono dal tipo di progetto. Per informazioni dettagliate sul modo in cui vengono modificati i progetti MVC ASP.NET, vedere [Risultati - Progetti MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Per i progetti Web API, vedere [Cosa è successo: Progetti Web API](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Passaggi successivi
* [Forum MSDN su Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Documentazione di Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Post di blog: Introduzione ad Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

