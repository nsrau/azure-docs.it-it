---
title: Modello di identità
titleSuffix: An Azure Communication Services concept
description: Informazioni sulle identità e sui token di accesso
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f172bfcb6e4f11520eb9082052968626efe6fecb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651244"
---
# <a name="identity-model"></a>Modello di identità

Servizi di comunicazione di Azure è un servizio indipendente dall'identità. Questa progettazione offre diversi vantaggi:

- Riutilizza le identità esistenti dal sistema di gestione delle identità
- Offre flessibilità per gli scenari di integrazione
- Mantiene le identità private nei servizi di comunicazione di Azure

Invece di duplicare le informazioni nel sistema, si manterrà la relazione di mapping richiesta dal business case. Ad esempio, è possibile eseguire il mapping delle identità 1:1, 1: N, N:1, N:M. Gli identificatori esterni, ad esempio numeri di telefono, utenti, dispositivi, applicazioni e GUID, non possono essere usati per l'identità nei servizi di comunicazione di Azure. I token di accesso generati per un'identità dei servizi di comunicazione di Azure vengono usati per accedere alle primitive, ad esempio la chat o la chiamata.

## <a name="identity"></a>Identità

È possibile creare identità tramite la libreria di amministrazione di servizi di comunicazione Azure. Un'identità funge da identificatore nelle conversazioni. Viene usato per creare i token di accesso. La stessa identità può partecipare a più sessioni simultanee tra più dispositivi. Un'identità potrebbe avere più token di accesso attivi contemporaneamente. 

L'eliminazione di un'identità, di una risorsa o di una sottoscrizione invalida tutti i relativi token di accesso. Questa azione Elimina anche tutti i dati archiviati per l'identità. Un'identità eliminata non può creare nuovi token di accesso o accedere a dati precedentemente archiviati, ad esempio messaggi di chat. 

Il numero di identità disponibili non viene addebitato. Vengono invece addebitati i costi per l'uso delle primitive. Il numero di identità non deve limitare la modalità di mapping delle identità dell'applicazione alle identità dei servizi di comunicazione di Azure. 

Con la libertà di mapping viene fornita la responsabilità della privacy. Se un utente desidera essere eliminato dal sistema, è necessario eliminare tutte le identità associate a tale utente.

I servizi di comunicazione di Azure non forniscono identità speciali per gli utenti anonimi. Non mantiene il mapping tra gli utenti e le identità e non è in grado di determinare se un'identità è anonima. È possibile progettare il concetto di identità in base alle esigenze. Si consiglia di creare una nuova identità per ogni utente anonimo in ogni applicazione. 

Chiunque disponga di un token di accesso valido può accedere al contenuto corrente dell'identità. Ad esempio, gli utenti possono accedere ai messaggi di chat inviati. L'accesso è limitato solo agli ambiti che fanno parte del token di accesso. Per altre informazioni, vedere la sezione [token di accesso](#access-tokens) in questo articolo.

### <a name="identity-mapping"></a>Mapping di identità

I servizi di comunicazione di Azure non replicano la funzionalità del sistema di gestione delle identità di Azure. Non fornisce un modo per i clienti di usare identità specifiche del cliente. I clienti, ad esempio, non possono usare un numero di telefono o un indirizzo di posta elettronica. I servizi di comunicazione di Azure forniscono invece identificatori univoci. È possibile assegnare questi identificatori univoci alle identità dell'applicazione. Servizi di comunicazione di Azure non archivia alcun tipo di informazioni che potrebbero rivelare l'identità reale degli utenti.

Per evitare di duplicare le informazioni nel sistema, pianificare come eseguire il mapping degli utenti dal dominio di identità alle identità dei servizi di comunicazione di Azure. È possibile seguire qualsiasi tipo di modello. Ad esempio, è possibile usare 1:1, 1: N, N:1 o M:N. Decidere se è stato eseguito il mapping di un singolo utente a una singola identità o a più identità. 

Quando viene creata una nuova identità, archivia il mapping all'utente o agli utenti dell'applicazione. Poiché le identità richiedono token di accesso per l'uso di primitive, l'identità deve essere nota all'utente o agli utenti dell'applicazione.

Se si usa un database relazionale per archiviare le informazioni sugli utenti, è possibile modificare la progettazione in base allo scenario di mapping. Per gli scenari con mapping 1:1 o N:1, potrebbe essere necessario aggiungere una `CommunicationServicesId` colonna alla tabella per archiviare l'identità dei servizi di comunicazione di Azure. Negli scenari in cui viene utilizzata la relazione 1: N o N:M, è possibile creare una tabella separata nel database relazionale.

## <a name="access-tokens"></a>Token di accesso

Un token di accesso è un token Web JSON (JWT) che può essere usato per ottenere l'accesso alle primitive del servizio di comunicazione di Azure. Un token di accesso emesso ha protezione dell'integrità. Ovvero, le attestazioni non possono essere modificate dopo l'emissione. Pertanto, una modifica manuale delle proprietà, ad esempio Identity, scad o Scopes, invalida il token di accesso. Se le primitive vengono usate con i token invalidati, l'accesso verrà negato alle primitive. 

Le proprietà di un token di accesso sono:
* Identità.
* Expiration.
* Ambiti.

Un token di accesso è sempre valido per 24 ore. Dopo la scadenza, il token di accesso viene invalidato e non può essere usato per accedere ad alcuna primitiva. 

Un'identità necessita di un modo per richiedere un nuovo token di accesso da un servizio lato server. Il parametro *scope* definisce un set non vuoto di primitive che è possibile utilizzare. Servizi di comunicazione Azure supporta i seguenti ambiti per i token di accesso.

|Nome|Descrizione|
|---|---|
|Chat|  Consente di partecipare a una chat|
|VoIP|  Concede la possibilità di chiamare identità e numeri di telefono|


Per revocare un token di accesso prima della data di scadenza, usare la libreria di amministrazione di servizi di comunicazione Azure. La revoca del token non è immediata. Sono necessari fino a 15 minuti per la propagazione. La rimozione di un'identità, di una risorsa o di una sottoscrizione revoca tutti i token di accesso. 

Se si vuole rimuovere la capacità di un utente di accedere a funzionalità specifiche, revocare tutti i token di accesso. Quindi, emettere un nuovo token di accesso con un set più limitato di ambiti.

Nei servizi di comunicazione di Azure una rotazione delle chiavi di accesso revoca tutti i token di accesso attivi creati usando una chiave di accesso precedente. Tutte le identità perdono l'accesso ai servizi di comunicazione di Azure e devono emettere nuovi token di accesso. 

È consigliabile rilasciare i token di accesso nel servizio lato server e non nell'applicazione del client. Il motivo è che l'emissione richiede una chiave di accesso o un'identità gestita. Per motivi di sicurezza, non è consigliabile condividere le chiavi di accesso con l'applicazione del client. 

L'applicazione client deve usare un endpoint di servizio attendibile in grado di autenticare i client. L'endpoint deve rilasciare i token di accesso per conto dell'utente. Per ulteriori informazioni, vedere [architettura client e server](./client-and-server-architecture.md).

Se si memorizzano nella cache i token di accesso a un archivio di backup, si consiglia di usare la crittografia. Un token di accesso è dati sensibili. Può essere usato per attività dannose se non è protetto. Un utente che dispone di un token di accesso può avviare l'SDK e accedere all'API. L'API accessibile è limitata solo in base agli ambiti di cui dispone il token di accesso. Si consiglia di rilasciare i token di accesso che dispongono solo degli ambiti richiesti.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla gestione dei token di accesso, vedere [creare e gestire i token di accesso](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
* Per un'introduzione all'autenticazione, vedere [eseguire l'autenticazione a servizi di comunicazione di Azure](https://docs.microsoft.com/azure/communication-services/concepts/authentication).
* Per un'introduzione alla privacy e alla residenza dei dati, vedere [disponibilità delle aree e residenza dei dati](https://docs.microsoft.com/azure/communication-services/concepts/privacy).
