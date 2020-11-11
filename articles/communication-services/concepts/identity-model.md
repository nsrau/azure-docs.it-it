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
ms.openlocfilehash: 336e708334778e107331f5f393476c4b3dbb98a3
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507485"
---
# <a name="identity-model"></a>Modello di identità

Servizi di comunicazione di Azure è un servizio indipendente dall'identità. Questa progettazione offre diversi vantaggi:
- Riutilizzare le identità esistenti dal sistema di gestione delle identità
- Flessibilità per gli scenari di integrazione
- Mantiene le identità private per i servizi di comunicazione di Azure

Invece di duplicare le informazioni esistenti nel sistema, si manterrà la relazione di mapping specifica per il business case. Ad esempio, mapping delle identità 1:1, 1: N, N:1, N:M. Gli identificatori esterni (ad esempio numeri di telefono, utenti, dispositivi, applicazioni e GUID) non possono essere usati come identità di comunicazione di Azure. I token di accesso generati per l'identità del servizio di comunicazione di Azure vengono usati per accedere alle primitive, ad esempio la chat o la chiamata. 

## <a name="identity"></a>Identità

Le identità vengono create con la libreria di amministrazione del servizio di comunicazione di Azure. Identity funge da identificatore nelle conversazioni e viene usato per la creazione di token di accesso. La stessa identità può partecipare a più sessioni simultanee tra più dispositivi. L'identità potrebbe avere più token di accesso attivi contemporaneamente. L'eliminazione di identità, risorse o sottoscrizioni causa l'invalidamento di tutti i token di accesso e l'eliminazione di tutti i dati archiviati per l'identità. L'identità eliminata non può rilasciare nuovi token di accesso, né accedere ai dati archiviati in precedenza (ad esempio, i messaggi di chat). 

Il numero di identità disponibili non viene addebitato, ma in base all'utilizzo delle primitive. Non è necessario limitare il numero di identità, come eseguire il mapping delle identità dell'applicazione alle identità dei servizi di comunicazione di Azure. Con la libertà di mapping si assume la responsabilità dei termini di privacy. Quando l'utente dell'applicazione vuole essere eliminato dal sistema, è necessario eliminare tutte le identità associate a tale utente.

I servizi di comunicazione di Azure non forniscono identità speciali per gli utenti anonimi. Non mantiene il mapping tra gli utenti e le identità, ma non comprende se l'identità è anonima. È possibile progettare il concetto per adattarlo alle proprie esigenze. Si consiglia di creare una nuova identità per ogni utente anonimo di ogni applicazione. Con il possesso del token di accesso valido, chiunque può ottenere l'accesso al contenuto non eliminato dell'identità. Ad esempio, i messaggi di chat inviati dall'utente. L'accesso è limitato solo agli ambiti che fanno parte del token di accesso. Ulteriori informazioni sugli ambiti sono disponibili nella sezione *token di accesso*.

### <a name="mapping-of-identities"></a>Mapping delle identità

I servizi di comunicazione di Azure non eseguono la replica della funzionalità di IMS. Non fornisce un modo per i clienti di usare identità specifiche del cliente. Ad esempio, numero di telefono o indirizzo di posta elettronica. Fornisce invece identificatori univoci che è possibile assegnare alle identità dell'applicazione. I servizi di comunicazione di Azure non archiviano alcun tipo di informazioni che potrebbero rivelare l'identità reale degli utenti.

Invece di duplicare, è consigliabile progettare, in che modo gli utenti del dominio di identità verranno mappati alle identità del servizio di comunicazione di Azure. È possibile seguire qualsiasi tipo di modello 1:1, 1: N, N:1 o M:N. È possibile decidere se è stato eseguito il mapping di un singolo utente a un'identità singola o a più identità. Quando viene creata una nuova identità, si è invitati a archiviare il mapping di questa identità all'utente o agli utenti dell'applicazione. Poiché le identità richiedono i token di accesso per l'utilizzo delle primitive, è necessario conoscere l'identità per l'utente o gli utenti dell'applicazione.

Se si usa un database relazionale per l'archiviazione di utenti, l'implementazione può variare in base allo scenario di mapping. Per gli scenari con mapping 1:1 o N:1, è possibile aggiungere una colonna *CommunicationServicesId* alla tabella per archiviare l'identità dei servizi di comunicazione di Azure. Negli scenari con relazione 1: N o N:M, è possibile creare una tabella separata nel database relazionale.

## <a name="access-token"></a>Token di accesso

Il token di accesso è un token JWT che può essere usato per ottenere l'accesso alle primitive del servizio di comunicazione di Azure. Il token di accesso emesso dispone di protezione dell'integrità, le relative attestazioni non possono essere modificate dopo l'emissione. Ovvero, la modifica manuale delle proprietà, ad esempio Identity, scadenza o ambito, renderà il token di accesso non valido. L'utilizzo di primitive con token invalidati causerà la negazione dell'accesso alla primitiva. 

Le proprietà del token di accesso sono: *Identity, scadenza* e *Scopes*. Il token di accesso è sempre valido per 24 ore. Dopo questo periodo di tempo, il token di accesso viene invalidato e non può essere usato per accedere ad alcuna primitiva. L'identità deve avere un modo, come richiedere un nuovo token di accesso dal servizio lato server. L' *ambito* del parametro definisce un set non vuoto di primitive, che può essere usato. Servizi di comunicazione Azure supporta i seguenti ambiti per i token di accesso:

|Nome|Descrizione|
|---|---|
|Chat|  Consente di partecipare a una chat|
|VoIP|  Concede la possibilità di chiamare identità e numeri di telefono|


Per revocare il token di accesso prima della scadenza, è possibile usare la libreria di amministrazione del servizio di comunicazione di Azure. La revoca del token non è immediata e richiede fino a 15 minuti per la propagazione. La rimozione dell'identità, della risorsa o della sottoscrizione provocherà la revoca di tutti i token di accesso. Se si vuole rimuovere la capacità di un utente di accedere a funzionalità specifiche, revocare tutti i token di accesso. Quindi, emettere un nuovo token di accesso con un set più limitato di ambiti.
La rotazione delle chiavi di accesso del servizio di comunicazione di Azure provocherà la revoca di tutti i token di accesso attivi creati con una chiave di accesso precedente. Tutte le identità perderanno l'accesso al servizio di comunicazione di Azure e dovranno emettere nuovi token di accesso. 

È consigliabile rilasciare i token di accesso nel servizio lato server e non nell'applicazione del client. Il motivo è che l'emissione richiede la chiave di accesso o l'identità gestita. Per motivi di sicurezza, non è consigliabile condividere le chiavi di accesso con l'applicazione del client. L'applicazione client deve usare un endpoint di servizio attendibile in grado di autenticare i client ed emettere un token di accesso per loro conto. Altre informazioni sull'architettura sono disponibili [qui](./client-and-server-architecture.md).

Se si memorizzano nella cache i token di accesso a un archivio di backup, si consiglia di usare la crittografia. Il token di accesso è costituito da dati sensibili e può essere usato per attività dannose se non è protetto. Con il possesso del token di accesso, è possibile inizializzare l'SDK e ottenere l'accesso all'API. L'API accessibile è limitata solo in base agli ambiti, che hanno il token di accesso. È consigliabile rilasciare i token di accesso solo con gli ambiti, che sono obbligatori.