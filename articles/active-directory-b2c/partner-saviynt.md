---
title: Esercitazione per la configurazione di Saviynt con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Esercitazione per la configurazione di Azure Active Directory B2C con Saviynt per l'integrazione tra applicazioni per semplificare la modernizzazione IT e promuovere una migliore sicurezza, governance e conformità. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8406074933489e53e9235a8a6a05b68f1dd42a85
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259137"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di Saviynt con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare Azure Active Directory (AD) B2C con [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). La piattaforma di gestione della sicurezza di Saviynt offre la visibilità, la sicurezza e la governance necessarie per le aziende di oggi, in una singola piattaforma unificata. Saviynt incorpora il rischio e la governance delle applicazioni, la gestione dell'infrastruttura, la gestione degli account con privilegi e l'analisi dei rischi per i clienti.

In questa esercitazione di esempio, si imposterà Saviynt per fornire l'amministrazione delegata basata sul controllo di accesso con granularità fine per gli utenti Azure AD B2C. Saviynt esegue i controlli seguenti per determinare se un utente è autorizzato a gestire Azure AD B2C utenti.

- Sicurezza a livello di funzionalità per determinare se un utente può eseguire un'operazione specifica. Ad esempio, creare un utente, aggiornare l'utente, reimpostare la password utente e così via.

- Sicurezza a livello di campo per determinare se un utente può leggere/scrivere un attributo specifico di un altro utente durante le operazioni di gestione degli utenti. Ad esempio, help desk agente può aggiornare solo il numero di telefono e tutti gli altri attributi sono di sola lettura.

- Sicurezza a livello di dati per determinare se un utente può eseguire una determinata operazione su un utente specifico. Ad esempio, help desk amministratore per l'area del Regno Unito può gestire solo gli utenti del Regno Unito.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Il tenant è collegato alla sottoscrizione di Azure.

- [Sottoscrizione](https://saviynt.com/contact-us/) di Saviynt

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di Saviynt include i componenti seguenti:

- [Azure ad B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) : identità business-to-Customer come servizio che consente il controllo personalizzato del modo in cui i clienti possono iscriversi, accedere e gestire i propri profili.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) : piattaforma di governance delle identità che fornisce amministrazione delegata con granularità fine per la gestione del ciclo di vita degli utenti e la governance degli accessi degli utenti Azure ad B2C.  

- [Api Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) : questa API fornisce le interfacce per Saviynt per gestire gli utenti Azure ad B2C e il relativo accesso in Azure ad B2C.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Immagine che mostra il diagramma dell'architettura di saviynt](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | Un amministratore delegato avvia un'operazione di gestione Azure AD B2C utente tramite Saviynt.
| 2. | Saviynt verifica con il motore di autorizzazione se l'amministratore delegato è in grado di eseguire l'operazione specifica.
| 3. | Il motore di autorizzazione di Saviynt invia una risposta di esito positivo o negativo dell'autorizzazione.
| 4. | Saviynt consente all'amministratore delegato di eseguire l'operazione richiesta.
| 5. | Saviynt richiama l'API Microsoft Graph insieme agli attributi utente per gestire l'utente in Azure AD B2C
| 6. | Microsoft Graph API creerà a sua volta la creazione/aggiornamento/eliminazione dell'utente in Azure AD B2C.
| 7. | Azure AD B2C invierà una risposta di esito positivo o negativo.
| 8. | Microsoft Graph API restituirà quindi la risposta a Saviynt.

## <a name="onboard-with-saviynt"></a>Onboarding con Saviynt

1. Per creare un account Saviynt, contattare [Saviynt](https://saviynt.com/contact-us/)

2. Creare criteri di Amministrazione delegati e assegnare gli utenti come [amministratori delegati](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-concept-delegation) con diversi ruoli.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Configurare Azure AD B2C con Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Creare un'applicazione Azure AD per Saviynt

1. Accedere al [portale di Azure](https://portal.azure.com/#home).

2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

3. Nella portale di Azure cercare e selezionare **Azure ad B2C**.

4. Selezionare **registrazioni app**  >  **nuova registrazione**.

5. Immettere un nome per l'applicazione. Ad esempio, Saviynt e selezionare **Crea**.

6. Passare a **autorizzazioni API** e selezionare **+ Aggiungi un'autorizzazione.**

7. Verrà visualizzata la pagina Richiedi le autorizzazioni dell'API. Selezionare la scheda **API Microsoft** e selezionare **Microsoft Graph** come API Microsoft di uso comune.

8. Passare alla pagina successiva e selezionare **Autorizzazioni applicazione**.

9. Selezionare **directory**e selezionare directory **. Read. All** e **Directory. ReadWrite. All** (caselle di controllo).

10. Selezionare **Aggiungi autorizzazioni**. Esaminare le autorizzazioni aggiunte.

11. Selezionare **concedi il consenso dell'amministratore per la directory predefinita**  >  **Save**.

12. Passare a **certificati e segreti** e selezionare **+ Aggiungi chiave privata client**. Immettere la descrizione del segreto client, selezionare l'opzione scadenza e selezionare **Aggiungi**.

13. La chiave privata viene generata e visualizzata nella sezione Secret client.

    >[!NOTE]
    > Il segreto client sarà necessario in un secondo momento.

14. Passare a **Panoramica** e ottenere l'ID **client** e l' **ID tenant**.

15. Per completare la configurazione in Saviynt, sarà necessario l'ID tenant, l'ID client e il segreto client.

### <a name="enable-saviynt-to-delete-users"></a>Abilitare Saviynt per eliminare gli utenti

I passaggi seguenti illustrano come abilitare Saviynt per eseguire operazioni di eliminazione utente in Azure AD B2C.

>[!NOTE]
>[Valutare il rischio prima di concedere ai ruoli amministrativi l'accesso a un'entità servizio.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

1. Installare la versione più recente del modulo MSOnline di PowerShell in una workstation/server Windows.

2. Connettersi al modulo AzureAD di PowerShell ed eseguire i comandi seguenti:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testare la soluzione

Passare al tenant dell'applicazione Saviynt e testare la gestione del ciclo di vita degli utenti e il caso d'uso della governance di accesso.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

- [Creare un'applicazione API Web](https://docs.microsoft.com/azure/active-directory-b2c/add-web-api-application)
