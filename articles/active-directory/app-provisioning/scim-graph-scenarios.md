---
title: Usando SCIM, il Microsoft Graph e il servizio di provisioning Azure AD per eseguire il provisioning degli utenti e arricchire l'applicazione con i dati necessari | Microsoft Docs
description: Usare SCIM e il Microsoft Graph insieme per eseguire il provisioning degli utenti e arricchire l'applicazione con i dati necessari.
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
ms.date: 04/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ffe0474fcfeb28b49f5c2504ede86cd38459d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181835"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Uso di SCIM e Microsoft Graph insieme per eseguire il provisioning degli utenti e arricchire l'applicazione con i dati necessari

**Destinatari:** Questo documento è destinato agli sviluppatori che compilano applicazioni da integrare con Azure AD. Per gli altri utenti che vogliono usare le applicazioni già integrate con Azure AD, ad esempio zoom, ServiceNow e DropBox, è possibile ignorare questo aspetto ed esaminare le [esercitazioni](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) specifiche dell'applicazione oppure verificare il [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works).

**Scenari comuni**

Azure AD offre un servizio predefinito per il provisioning e una piattaforma estendibile per la creazione di applicazioni in. L'albero delle decisioni delinea il modo in cui uno sviluppatore utilizzerà [scim](https://aka.ms/scimoverview) e il [Microsoft Graph](https://docs.microsoft.com/graph/overview) per automatizzare il provisioning. 

> [!div class="checklist"]
> * Crea automaticamente utenti nell'applicazione
> * Rimuovere automaticamente gli utenti dall'applicazione quando non dovrebbero più accedere
> * Integrare l'applicazione con più provider di identità per il provisioning
> * Arricchisci l'applicazione con i dati dei servizi Microsoft, ad esempio i team, Outlook e Office.
> * Creazione, aggiornamento ed eliminazione automatica di utenti e gruppi in Azure AD e Active Directory

![Albero delle decisioni del grafo SCIM](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: creare automaticamente gli utenti nell'app
Attualmente, gli amministratori IT eseguono il provisioning degli utenti creando manualmente gli account utente o caricando periodicamente i file CSV nell'applicazione. Il processo richiede molto tempo per i clienti e rallenta l'adozione dell'applicazione. Sono necessarie informazioni di base sull'utente, ad esempio nome, indirizzo di posta elettronica e userPrincipalName per creare un utente. 

**Consiglio**: 
* Se i clienti utilizzano diversi IDP e non si desidera mantenere un motore di sincronizzazione per l'integrazione con ognuno di essi, supportare un endpoint [/Users](https://aka.ms/scimreferencecode) conforme a scim. I clienti saranno in grado di usare facilmente questo endpoint per integrarsi con il servizio di provisioning Azure AD e creare automaticamente gli account utente quando devono accedere. È possibile compilare l'endpoint una sola volta e sarà compatibile con tutti gli IDP. Vedere la richiesta di esempio seguente per la modalità di creazione di un utente con SCIM.
* Se sono necessari dati utente trovati nell'oggetto utente in Azure AD e altri dati da Microsoft, è consigliabile creare un endpoint SCIM per il provisioning degli utenti e chiamare il Microsoft Graph per ottenere il resto dei dati. 


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
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: rimuovere automaticamente gli utenti dall'app
I clienti che usano l'applicazione sono incentrati sulla sicurezza e hanno requisiti di governance per rimuovere gli account quando i dipendenti non sono più necessari. Come è possibile automatizzare il deprovisioning dalla mia applicazione?

**Raccomandazione:** Supporta un endpoint/Users conforme a SCIM. Il servizio di provisioning Azure AD invierà le richieste di disabilitazione ed eliminazione quando l'utente non deve più avere accesso. È consigliabile supportare sia la disabilitazione che l'eliminazione degli utenti. Vedere gli esempi riportati di seguito per informazioni sull'aspetto di una richiesta di disabilitazione ed eliminazione. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: automatizzare la gestione delle appartenenze ai gruppi nell'app
L'applicazione si basa sui gruppi per l'accesso a diverse risorse e i clienti vogliono riutilizzare i gruppi in Azure AD. Come è possibile importare gruppi da Azure AD e mantenerli aggiornati con la modifica delle appartenenze?  

**Raccomandazione:** Supporta un [endpoint](https://aka.ms/scimreferencecode)/groups conforme a scim. Il servizio di provisioning Azure AD si occuperà della creazione di gruppi e della gestione degli aggiornamenti di appartenenza nell'applicazione. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: arricchire l'app con i dati dei servizi Microsoft, ad esempio teams, Outlook e OneDrive
L'applicazione è incorporata in Microsoft teams e si basa sui dati dei messaggi. Vengono inoltre archiviati i file per gli utenti in OneDrive. Come posso arricchire l'applicazione con i dati di questi servizi e tra Microsoft?

**Raccomandazione:** Il [Microsoft Graph](https://docs.microsoft.com/graph/) è il punto di ingresso per accedere ai dati Microsoft. Ogni carico di lavoro espone le API con i dati necessari. Microsoft Graph può essere usato insieme al [provisioning di SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) per gli scenari precedenti. È possibile usare SCIM per eseguire il provisioning degli attributi utente di base nell'applicazione e chiamare Graph per ottenere altri dati necessari. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: tenere traccia delle modifiche apportate ai servizi Microsoft, ad esempio i team, Outlook e Azure AD
È necessario essere in grado di tenere traccia delle modifiche apportate ai team e ai messaggi di Outlook e di reagire in tempo reale. Come è possibile eseguire il push di queste modifiche nell'applicazione?

**Raccomandazione:** Il Microsoft Graph fornisce le [notifiche delle modifiche](https://docs.microsoft.com/graph/webhooks) e il rilevamento delle [modifiche](https://docs.microsoft.com/graph/delta-query-overview) per le varie risorse. Tenere presenti le seguenti limitazioni delle notifiche di modifica:
- Se un ricevitore di eventi riconosce un evento, ma non è in grado di intervenire su di esso per qualsiasi motivo, è possibile che l'evento venga perso
- Se un ricevitore di eventi riconosce un evento, ma non è in grado di intervenire su di esso per qualsiasi motivo, è possibile che l'evento venga perso
- Le notifiche di modifica non sempre contengono i [dati della risorsa](https://docs.microsoft.com/graph/webhooks-with-resource-data) per i motivi precedenti, gli sviluppatori spesso utilizzano le notifiche di modifica insieme al rilevamento delle modifiche per gli scenari di sincronizzazione. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: effettuare il provisioning di utenti e gruppi in Azure AD
L'applicazione crea informazioni su un utente che i clienti hanno bisogno in Azure AD. Potrebbe trattarsi di un'applicazione HR rispetto alla gestione dell'assunzione, di un'app di comunicazione che crea numeri di telefono per gli utenti o di un'altra app che genera dati che potrebbero essere utili in Azure AD. Ricerca per categorie popolare il record utente in Azure AD con tali dati? 

**Indicazione** Microsoft Graph espone gli endpoint/Users e/groups che è possibile integrare oggi per eseguire il provisioning degli utenti in Azure AD. Si noti che Azure Active Directory non supporta la scrittura di tali utenti nel Active Directory. 

> [!NOTE]
> Microsoft dispone di un servizio di provisioning che effettua il pull dei dati provenienti da applicazioni HR quali giorni lavorativi e SuccessFactors. Queste integrazioni sono compilate e gestite da Microsoft. Per l'onboarding di una nuova applicazione HR per il servizio, è possibile richiederla in [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Articoli correlati

- [Esaminare la documentazione relativa alla sincronizzazione Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrazione di un'app SCIM personalizzata con Azure AD](use-scim-to-provision-users-and-groups.md)
