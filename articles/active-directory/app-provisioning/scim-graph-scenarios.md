---
title: Uso di SCIM, Microsoft Graph e del servizio di provisioning di Azure AD per eseguire il provisioning degli utenti e arricchire l'applicazione con i dati necessari. Documenti Microsoft
description: Utilizzo di SCIM e Microsoft Graph insieme per eseguire il provisioning degli utenti e arricchire l'applicazione con i dati necessari.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087621"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Utilizzo di SCIM e Microsoft Graph insieme per eseguire il provisioning degli utenti e arricchire l'applicazione con i dati necessari

**Destinatari:** Questo documento è destinato agli sviluppatori che creano applicazioni integrate con Azure AD. Per altri che desiderano integrare un'applicazione esistente, ad esempio, lo zoom, ServiceNow e DropBox è possibile ignorare questo e rivedere [le esercitazioni](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)specifiche dell'applicazione . 

**Scenari comuni**

> [!div class="checklist"]
> * Crea automaticamente utenti nell'applicazione
> * Rimuovere automaticamente gli utenti dall'applicazione quando non dovrebbero più avere accesso
> * Integrare l'applicazione con più provider di identità per il provisioning
> * Arricchisci la mia applicazione con i dati dei servizi Microsoft come Sharepoint, Outlook e Office.
> * Creare, aggiornare ed eliminare automaticamente utenti e gruppi in Azure AD e Active Directory

![Albero decisionale SCIM Graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: creare automaticamente utenti nell'app
Oggi, gli amministratori IT creano manualmente gli account utente nella mia applicazione ogni volta che qualcuno ha bisogno di accedere o caricare periodicamente i file CSV. Il processo richiede molto tempo per i clienti e rallenta l'adozione della mia applicazione. Tutto ciò di cui ho bisogno sono informazioni di base [sull'utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) come nome, posta elettronica e userPrincipalName per creare un utente. Inoltre, i miei clienti utilizzano vari IdP e non ho le risorse per mantenere un motore di sincronizzazione e integrazioni personalizzate con ogni IdP. 

**Consiglio:** supporta un endpoint [/Users](https://aka.ms/scimreferencecode) conforme a SCIM. I clienti saranno in grado di usare facilmente questo endpoint per l'integrazione con il servizio di provisioning di Azure AD e creare automaticamente gli account utente quando hanno bisogno di accesso. È possibile compilare l'endpoint una sola volta e sarà compatibile con tutti gli IdP, senza dover gestire un motore di sincronizzazione. Controllare la richiesta di esempio di seguito per come un utente sarebbe stato creato.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: Rimuovere automaticamente gli utenti dall'app
I clienti che utilizzano la mia applicazione sono focalizzati sulla sicurezza e hanno requisiti di governance per rimuovere gli account quando i dipendenti non ne hanno più bisogno. Come è possibile automatizzare il deprovisioning dall'applicazione?

**Raccomandazione:** Supportare un endpoint /Users conforme a SCIM. Il servizio di provisioning di Azure AD invierà richieste per disabilitare ed eliminare quando l'utente non deve più avere accesso. È consigliabile supportare sia la disabilitazione che l'eliminazione degli utenti. Vedere gli esempi seguenti per l'aspetto di una richiesta di disabilitazione ed eliminazione. 

Disabilitare l'utente
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Eliminare un utente
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Automatizzare la gestione delle appartenenze ai gruppi nell'appScenario 3: Automate managing group memberships in my app
L'applicazione si basa su gruppi per l'accesso a varie risorse e i clienti desiderano riutilizzare i gruppi di cui dispongono in Azure AD. Come è possibile importare gruppi da Azure AD e mantenerli aggiornati al variare delle appartenenze?  

**Raccomandazione:** Supportare un [endpoint](https://aka.ms/scimreferencecode)/Groups conforme a SCIM. Il servizio di provisioning di Azure AD si occuperà della creazione di gruppi e della gestione degli aggiornamenti delle appartenenze nell'applicazione. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: Arricchisci la mia app con i dati dei servizi Microsoft come Teams, Outlook e OneDrive.
La mia applicazione è integrata in Microsoft Teams e si basa sui dati del messaggio. Inoltre, archiviamo i file per gli utenti in OneDrive. Come è possibile arricchire l'applicazione con i dati di questi servizi e di Microsoft?

**Raccomandazione:** Microsoft Graph è il punto di ingresso per accedere ai dati Microsoft.The [Microsoft Graph](https://docs.microsoft.com/graph/) is your entry point to access Microsoft data. Ogni carico di lavoro espone le API con i dati necessari. Il grafico Microsoft può essere utilizzato insieme al [provisioning SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) per gli scenari precedenti. È possibile utilizzare SCIM per eseguire il provisioning degli attributi utente di base nell'applicazione chiamando il grafico per ottenere qualsiasi altro dato necessario. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: Tenere traccia delle modifiche nei servizi Microsoft, ad esempio Teams, Outlook e Azure AD.
Devo essere in grado di tenere traccia delle modifiche ai messaggi di Teams e Outlook e reagire in tempo reale. Come posso ottenere queste modifiche inviate alla mia applicazione?

**Raccomandazione:** Microsoft Graph fornisce notifiche di [modifica](https://docs.microsoft.com/graph/webhooks) e rilevamento delle modifiche per varie risorse. Tenere presenti le limitazioni seguenti delle notifiche di modifica:Note the following limitations of change notifications:
- Se un ricevitore di eventi riconosce un evento, ma non agisce su di esso per qualsiasi motivo, l'evento potrebbe essere perso
- Se un ricevitore di eventi riconosce un evento, ma non agisce su di esso per qualsiasi motivo, l'evento potrebbe essere perso
- Le notifiche di modifica non sempre contengono i [dati delle risorse](https://docs.microsoft.com/graph/webhooks-with-resource-data) Per i motivi precedenti, gli sviluppatori spesso usano le notifiche di modifica insieme al rilevamento delle modifiche per gli scenari di sincronizzazione. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: Effettuare il provisioning di utenti e gruppi in Azure AD.
L'applicazione crea informazioni su un utente di cui i clienti hanno bisogno in Azure AD. Potrebbe trattarsi di un'applicazione HR che gestisce le assunzioni, di un'app di comunicazione che crea numeri di telefono per gli utenti o di un'altra app che genera dati importanti in Azure AD. Come è possibile popolare il record utente in Azure AD con tali dati? 

**Raccomandazione** Il grafico di Microsoft espone gli endpoint /Users e /Groups che è possibile integrare con oggi per eseguire il provisioning degli utenti in Azure AD. Si noti che Azure Active Directory non supporta la scrittura di tali utenti in Active Directory. 

> [!NOTE]
> Microsoft has a provisioning service that pulls in data from HR applications such as Workday and SuccessFactors. Queste integrazioni vengono create e gestite da Microsoft.These integrations are built and managed by Microsoft. Per l'onboarding di una nuova applicazione HR al nostro servizio, è possibile richiederlo su [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Articoli correlati

- [Consultare la documentazione relativa alla sincronizzazione di Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrazione di un'app SCIM personalizzata con Azure ADIntegrating a custom SCIM app with Azure AD](use-scim-to-provision-users-and-groups.md)
