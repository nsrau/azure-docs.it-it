---
title: Autenticazione con Microsoft Azure Maps
titleSuffix: Azure Maps
description: 'Informazioni su due modi per autenticare le richieste in Maps di Azure: autenticazione con chiave condivisa e autenticazione Azure Active Directory (Azure AD).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4a923fd34391137f2064cb338ea180ae3782f5e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88036845"
---
# <a name="authentication-with-azure-maps"></a>Autenticazione con Mappe di Azure

Azure Maps supporta due modi per autenticare le richieste: autenticazione con chiave condivisa e autenticazione [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) . Questo articolo illustra entrambi i metodi di autenticazione per semplificare l'implementazione dei servizi di Azure maps.

> [!NOTE]
> Per migliorare la comunicazione protetta con le mappe di Azure, è ora supportato Transport Layer Security (TLS) 1,2 e il supporto per TLS 1,0 e 1,1 è in fase di ritiro. Se attualmente si usa TLS 1. x, valutare la conformità di TLS 1,2 e sviluppare un piano di migrazione con i test descritti in [risoluzione del problema tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticazione con chiave condivisa

 Le chiavi primarie e secondarie vengono generate dopo la creazione dell'account Azure maps. Si consiglia di usare la chiave primaria come chiave di sottoscrizione quando si chiama Maps di Azure con l'autenticazione con chiave condivisa. L'autenticazione con chiave condivisa passa una chiave generata da un account Azure Maps a un servizio Maps di Azure. Per ogni richiesta ai servizi di Azure Maps, aggiungere la *chiave di sottoscrizione* come parametro all'URL. La chiave secondaria può essere usata in scenari come le modifiche delle chiavi in sequenza.  

Per informazioni sulla visualizzazione delle chiavi nell'portale di Azure, vedere [Manage Authentication](https://aka.ms/amauthdetails).

> [!TIP]
> Per motivi di sicurezza, si consiglia di ruotare tra le chiavi primarie e secondarie. Per eseguire la rotazione delle chiavi, aggiornare l'app per usare la chiave secondaria, distribuire, quindi premere il pulsante di ciclo/aggiornamento accanto alla chiave primaria per generare una nuova chiave primaria. La chiave primaria precedente verrà disabilitata. Per altre informazioni sulla rotazione delle chiavi, vedere [Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)

## <a name="azure-ad-authentication"></a>Autenticazione di Azure AD

Le sottoscrizioni di Azure sono fornite con un tenant di Azure AD per abilitare il controllo degli accessi con granularità fine. Azure Maps offre l'autenticazione per i servizi di Azure Maps usando Azure AD. Azure AD fornisce l'autenticazione basata sull'identità per gli utenti e le applicazioni registrate nel tenant di Azure AD.

Mappe di Azure accetta token di accesso **OAuth 2.0** per i tenant di Azure AD associati a una sottoscrizione di Azure contenente un account di Mappe di Azure. Azure Maps accetta anche i token per:

* Utenti Azure AD
* Applicazioni partner che usano le autorizzazioni delegate dagli utenti
* Identità gestite per le risorse di Azure

Mappe di Azure genera un *identificatore univoco (ID client)* per ogni account Mappe di Azure. È possibile richiedere token da Azure AD quando si combina questo ID client con parametri aggiuntivi.

Per altre informazioni su come configurare Azure AD e richiedere i token per Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Per informazioni generali sull'autenticazione con Azure AD, vedere [che cos'è l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identità gestite per risorse di Azure e Mappe di Azure

[Le identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) forniscono ai servizi di Azure un'entità di sicurezza basata su un'applicazione gestita automaticamente che può eseguire l'autenticazione con Azure ad. Con il controllo degli accessi in base al ruolo (RBAC), l'entità di sicurezza gestita di identità può essere autorizzata ad accedere ai servizi di Azure maps. Di seguito sono riportati alcuni esempi di identità gestite: servizio app Azure, funzioni di Azure e macchine virtuali di Azure. Per un elenco di identità gestite, vedere [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

### <a name="configuring-application-azure-ad-authentication"></a>Configurazione dell'autenticazione Azure AD delle applicazioni

Le applicazioni eseguiranno l'autenticazione con il tenant di Azure AD usando uno o più scenari supportati forniti da Azure AD. Ogni scenario di applicazione Azure AD rappresenta requisiti diversi in base alle esigenze aziendali. Alcune applicazioni possono richiedere esperienze di accesso dell'utente e altre applicazioni possono richiedere un'esperienza di accesso all'applicazione. Per altre informazioni, vedere [flussi di autenticazione e scenari di applicazione](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios).

Quando l'applicazione riceve un token di accesso, l'SDK e/o l'applicazione invia una richiesta HTTPS con il seguente set di intestazioni HTTP obbligatorie oltre ad altre intestazioni HTTP dell'API REST:

| Nome dell'intestazione    | Valore               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Autorizzazione  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` è il GUID basato sull'account di Mappe di Azure che compare nella pagina di autenticazione di Mappe di Azure.

Di seguito è riportato un esempio di una richiesta di route di Azure Maps che usa un token di porta di Azure AD OAuth:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Per informazioni sulla visualizzazione dell'ID client, vedere [Visualizzare i dettagli di autenticazione](https://aka.ms/amauthdetails).

## <a name="authorization-with-role-based-access-control"></a>Autorizzazione con il controllo degli accessi in base al ruolo

Azure Maps supporta l'accesso a tutti i tipi di entità per il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/overview) , tra cui singoli Azure ad utenti, gruppi, applicazioni, risorse di Azure e identità gestite di Azure. Ai tipi di entità viene concesso un set di autorizzazioni, noto anche come definizione di ruolo. Una definizione di ruolo fornisce le autorizzazioni per le azioni dell'API REST. L'applicazione dell'accesso a uno o più account di Azure Maps è nota come ambito. Quando si applica un'entità, una definizione di ruolo e un ambito, viene creata un'assegnazione di ruolo. 

Le sezioni successive illustrano i concetti e i componenti dell'integrazione di Azure Maps con Azure AD controllo degli accessi in base al ruolo. Nell'ambito del processo di configurazione dell'account Azure Maps, una directory Azure AD viene associata alla sottoscrizione di Azure in cui risiede l'account Azure maps. 

Quando si configura il controllo degli accessi in base al ruolo di Azure, scegliere un'entità di sicurezza e applicarla a un'assegnazione di ruolo Per informazioni su come aggiungere assegnazioni di ruolo nella portale di Azure, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

### <a name="picking-a-role-definition"></a>Selezione di una definizione di ruolo

Per supportare gli scenari di applicazione, sono disponibili i seguenti tipi di definizione di ruolo.

| Definizione di ruolo di Azure       | Descrizione                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Lettore di dati per Mappe di Azure      | Fornisce l'accesso alle API REST di Azure Maps non modificabili.                                                       |
| Collaboratore dati di Azure Maps | Fornisce l'accesso alle API REST di Azure Maps modificabili. La mutabilità è definita dalle azioni: Write ed Delete. |
| Definizione di ruolo personalizzato      | Creare un ruolo creato per abilitare l'accesso limitato flessibile alle API REST di Azure maps.                      |

Alcuni servizi di Azure Maps potrebbero richiedere privilegi elevati per eseguire azioni di scrittura o eliminazione sulle API REST di Azure maps. Il ruolo Collaboratore dati di Maps di Azure è necessario per i servizi che forniscono azioni di scrittura o eliminazione. La tabella seguente descrive i servizi a cui il collaboratore dati di Maps di Azure è applicabile quando si usano le azioni di scrittura o eliminazione nel servizio specificato. Se nel servizio vengono usate solo le azioni di lettura, è possibile usare il lettore dati di Azure Maps anziché il collaboratore dati di Azure maps.

| Servizio Maps di Azure | Definizione del ruolo mappe di Azure  |
| :----------------- | :-------------------------- |
| Dati               | Collaboratore dati di Azure Maps |
| Autore            | Collaboratore dati di Azure Maps |
| Spatial            | Collaboratore dati di Azure Maps |

Per informazioni su come visualizzare le impostazioni del controllo degli accessi in base al ruolo, vedere [Come configurare il controllo degli accessi in base al ruolo per Mappe di Azure](https://aka.ms/amrbac).

#### <a name="custom-role-definitions"></a>Definizioni di ruolo personalizzate

Un aspetto della sicurezza dell'applicazione consiste nell'applicare il principio dei privilegi minimi. Questo principio implica che l'entità di sicurezza deve essere consentita solo per l'accesso, che è necessario e non dispone di accesso aggiuntivo. La creazione di definizioni di ruolo personalizzate può supportare casi di utilizzo che richiedono una maggiore granularità per il controllo di accesso. Per creare una definizione di ruolo personalizzata, è possibile selezionare azioni dati specifiche da includere o escludere per la definizione.

La definizione di ruolo personalizzata può quindi essere utilizzata in un'assegnazione di ruolo per qualsiasi entità di sicurezza. Per altre informazioni sulle definizioni dei ruoli personalizzati di Azure, vedere [ruoli personalizzati di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Di seguito sono riportati alcuni scenari di esempio in cui i ruoli personalizzati possono migliorare la sicurezza dell'applicazione.

| Scenario                                                                                                                                                                                                                 | Azione/i dati del ruolo personalizzati                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Una pagina Web di accesso Interactive o pubblica con tessere mappa di base e senza altre API REST.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Un'applicazione che richiede solo la geocodifica inversa e nessun'altra API REST.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Ruolo per un'entità di sicurezza che richiede la lettura dei dati della mappa basati su Azure Maps Creator e delle API REST delle tessere mappa di base.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Ruolo per un'entità di sicurezza che richiede la lettura, la scrittura e l'eliminazione dei dati della mappa basati su Creatore. Questo può essere definito come un ruolo di Editor dati della mappa, ma non consente l'accesso ad altre API REST come tessere mappa di base. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Informazioni sull'ambito

Quando si crea un'assegnazione di ruolo, questo viene definito all'interno della gerarchia di risorse di Azure. Nella parte superiore della gerarchia è presente un [gruppo di gestione](https://docs.microsoft.com/azure/governance/management-groups/overview) e il valore minimo è una risorsa di Azure, ad esempio un account Azure maps.
L'assegnazione di un'assegnazione di ruolo a un gruppo di risorse può consentire l'accesso a più account o risorse di Azure Maps nel gruppo.

> [!TIP]
> Il suggerimento generale di Microsoft consiste nell'assegnare l'accesso all'ambito dell'account mappe di Azure perché impedisce **l'accesso imprevisto ad altri account di Azure Maps** presenti nella stessa sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su RBAC, vedere
> [!div class="nextstepaction"]
> [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview)

Per ulteriori informazioni sull'autenticazione di un'applicazione con Azure AD e mappe di Azure, vedere
> [!div class="nextstepaction"]
> [Gestire l'autenticazione in Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

Per ulteriori informazioni sull'autenticazione delle mappe di Azure controllo mappa con Azure AD, vedere
> [!div class="nextstepaction"]
> [Usare il controllo mappa di Mappe di Azure](https://aka.ms/amaadmc)
