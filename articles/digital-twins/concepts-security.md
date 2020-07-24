---
title: Sicurezza per le soluzioni di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Informazioni sulle procedure di sicurezza consigliate con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ec681d0af132d11e18703dce6105352651a70180
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131617"
---
# <a name="secure-azure-digital-twins-with-role-based-access-control"></a>Proteggere i dispositivi gemelli digitali di Azure con controllo degli accessi in base al ruolo

Per la sicurezza, i dispositivi gemelli digitali di Azure consentono un controllo di accesso preciso su dati, risorse e azioni specifici nella distribuzione. Questa operazione viene eseguita tramite un ruolo granulare e una strategia di gestione delle autorizzazioni denominata **controllo degli accessi in base al ruolo (RBAC)**. Per informazioni sui principi generali di RBAC per Azure, vedere [qui](../role-based-access-control/overview.md).

## <a name="rbac-through-azure-ad"></a>RBAC tramite Azure AD

Il controllo degli accessi in base al ruolo viene fornito ai dispositivi gemelli digitali di Azure tramite l'integrazione con Azure AD [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)

È possibile utilizzare RBAC per concedere le autorizzazioni a un' *entità di sicurezza*, che può essere un utente, un gruppo o un'entità servizio dell'applicazione. L'entità di sicurezza viene autenticata da Azure AD e riceve un token OAuth 2,0 in return. Questo token può essere usato per autorizzare una richiesta di accesso a un'istanza di Azure Digital gemelli.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Con Azure AD, l'accesso è un processo in due passaggi. Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere ai dispositivi gemelli digitali di Azure, è necessario *autenticare* e *autorizzare*la richiesta. 

1. In primo luogo, l'identità dell'entità di sicurezza viene *autenticata*e viene restituito un token OAuth 2,0.
2. Successivamente, il token viene passato come parte di una richiesta al servizio Azure Digital Twins, per *autorizzare* l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che qualsiasi richiesta dell'applicazione contenga un token di accesso OAuth 2,0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio un'app [funzioni di Azure](../azure-functions/functions-overview.md) , può usare un' **identità gestita** per accedere alle risorse. Per altre informazioni sulle identità gestite, vedere la sezione successiva.

Per il passaggio di autorizzazione è necessario assegnare un ruolo RBAC all'entità di sicurezza. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. I dispositivi gemelli digitali di Azure forniscono i ruoli RBAC che includono i set di autorizzazioni per le risorse di Azure Digital gemelli. Questi ruoli sono descritti più avanti in questo articolo.

Per altre informazioni sui ruoli e sulle assegnazioni di ruolo supportati in Azure, vedere informazioni sui [*diversi ruoli*](../role-based-access-control/rbac-and-directory-admin-roles.md) nella documentazione relativa a RBAC di Azure.

### <a name="authentication-with-managed-identities"></a>Autenticazione con identità gestite

[Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) è una funzionalità di Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare tale identità ai ruoli di controllo di accesso per concedere autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client dei dispositivi gemelli digitali di Azure in esecuzione all'interno di un'applicazione di servizio app Azure non deve gestire le regole e le chiavi di firma di accesso condiviso o altri token di accesso. L'app client necessita solo dell'indirizzo endpoint dello spazio dei nomi di Azure Digital gemelli. Quando l'app si connette, i dispositivi gemelli digitali di Azure associano il contesto dell'entità gestita al client. Una volta associato a un'identità gestita, il client di Azure Digital Twins può eseguire tutte le operazioni autorizzate. L'autorizzazione verrà quindi concessa associando un'entità gestita a un ruolo di controllo degli accessi in base al ruolo di Azure Digital Twins (descritto di seguito).

### <a name="authorization-rbac-roles-for-azure-digital-twins"></a>Autorizzazione: ruoli RBAC per i dispositivi gemelli digitali di Azure

Azure fornisce i ruoli RBAC predefiniti seguenti per autorizzare l'accesso a una risorsa di dispositivi gemelli digitali di Azure:
* *Proprietario di dispositivi gemelli digitali di Azure (anteprima)* : usare questo ruolo per concedere l'accesso completo alle risorse di Azure Digital gemelli.
* *Lettore di dispositivi gemelli digitali di Azure (anteprima)* : usare questo ruolo per concedere l'accesso in sola lettura alle risorse di Azure Digital gemelli.

> [!TIP]
> Il ruolo lettore di dispositivi *digitali gemelli di Azure (anteprima)* supporta ora anche le relazioni di esplorazione.

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti, vedere informazioni sulle [*definizioni di ruolo*](../role-based-access-control/role-definitions.md) nella documentazione relativa a RBAC di Azure. Per informazioni sulla creazione di ruoli personalizzati di Azure, vedere [*ruoli personalizzati di Azure*](../role-based-access-control/custom-roles.md).

È possibile assegnare i ruoli in due modi:
* tramite il riquadro controllo di accesso (IAM) per i dispositivi gemelli digitali di Azure nella portale di Azure (vedere [*aggiungere o rimuovere assegnazioni di ruolo con il controllo degli accessi in base al ruolo e il portale di Azure*](../role-based-access-control/role-assignments-portal.md))
* tramite i comandi dell'interfaccia della riga di comando per aggiungere o rimuovere un ruolo

Per istruzioni più dettagliate su come eseguire questa operazione, vedere l'esercitazione sui dispositivi gemelli di Azure per la [*connessione di una soluzione end-to-end*](tutorial-end-to-end.md).

## <a name="permission-scopes"></a>Ambiti delle autorizzazioni

Prima di assegnare un ruolo di controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate stabiliscono che è preferibile concedere solo l'ambito più ristretto possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse di Azure Digital gemelli.
* Modelli: le azioni per questa risorsa determinano il controllo sui [modelli](concepts-models.md) caricati nei dispositivi gemelli digitali di Azure.
* Grafico gemelli digitali delle query: le azioni per questa risorsa determinano la possibilità di eseguire [operazioni di query](concepts-query-language.md) sui gemelli digitali all'interno del grafo dei gemelli digitali di Azure.
* Digital Twin: le azioni per questa risorsa forniscono il controllo sulle operazioni CRUD sui [gemelli digitali](concepts-twins-graph.md) nel grafico gemello.
* Relazione digitale gemello: le azioni per questa risorsa definiscono il controllo sulle operazioni CRUD sulle [relazioni](concepts-twins-graph.md) tra i dispositivi gemelli digitali nel grafico gemello.
* Route dell'evento: le azioni per questa risorsa determinano le autorizzazioni per [indirizzare gli eventi](concepts-route-events.md) da dispositivi gemelli digitali di Azure a un servizio endpoint, ad esempio [Hub eventi](../event-hubs/event-hubs-about.md), [griglia di eventi](../event-grid/overview.md)o [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se un utente tenta di eseguire un'azione non consentita dal ruolo, può ricevere un errore dalla lettura della richiesta di servizio `403 (Forbidden)` . Per ulteriori informazioni e procedure per la risoluzione dei problemi, vedere [*risoluzione dei problemi: richiesta di Azure Digital Twins non riuscita con stato: 403 (accesso negato)*](troubleshoot-error-403.md).

## <a name="next-steps"></a>Passaggi successivi

* Vedere questi concetti in azione in [*procedura: configurare un'istanza di e l'autenticazione*](how-to-set-up-instance-scripted.md)di.

* Vedere come interagire con questi concetti dal codice dell'applicazione client in [*procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md).

* Scopri di più su [RBAC per Azure](../role-based-access-control/overview.md).
