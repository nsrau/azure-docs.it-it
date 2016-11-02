<properties
pageTitle="Oggetti applicazione e oggetti entità servizio in Azure Active Directory | Microsoft Azure"
description="Descrizione della relazione tra oggetti applicazione e oggetti entità servizio in Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>  

# Oggetti applicazione e oggetti entità servizio in Azure Active Directory
Quando si legge di una "applicazione" di Azure Active Directory (AD), non è sempre esattamente chiaro a cosa si riferisca l'autore. L'obiettivo di questo articolo è offrire un chiarimento definendo gli aspetti concettuali e concreti dell'integrazione di applicazioni di Azure AD, con un esempio di registrazione e consenso per un'[applicazione multi-tenant](active-directory-dev-glossary.md#multi-tenant-application).

## Overview
Un'applicazione di Azure AD non è semplicemente un componente software. È un termine concettuale che fa riferimento non solo al software dell'applicazione ma anche alla relativa registrazione (detta anche configurazione di identità) in Azure AD, che ne consente la partecipazione a "conversazioni" di autenticazione e autorizzazione in fase di esecuzione. Per definizione, un'applicazione può funzionare in un ruolo [client](active-directory-dev-glossary.md#client-application) (che utilizza una risorsa), in un ruolo [server di risorse](active-directory-dev-glossary.md#resource-server) (che espone le API ai client) o anche in entrambi i ruoli. Il protocollo di conversazione è definito da un [flusso di concessione di autorizzazione OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), con l'obiettivo di consentire al client e alla risorsa di accedere e proteggere, rispettivamente, i dati di una risorsa. Verrà ora approfondito come il modello applicativo di Azure AD rappresenta internamente un'applicazione.

## Registrazione dell'applicazione
Quando si registra un'applicazione nel [portale di Azure classico][AZURE-Classic-Portal], nel tenant di Azure AD vengono creati due oggetti: un oggetto applicazione e un oggetto entità servizio.

#### Oggetto applicazione
Un'applicazione di Azure AD è *definita* da un solo oggetto applicazione che risiede nel tenant di Azure AD in cui l'applicazione è stata registrata, detto tenant "home" dell'applicazione. L'oggetto applicazione fornisce le informazioni relative all'identità dell'applicazione ed è il modello da cui *derivano* gli oggetti entità servizio corrispondenti usati in fase di esecuzione.

Si può considerare l'applicazione come la rappresentazione *globale* dell'applicazione (usata in tutti i tenant) e l'entità servizio come la rappresentazione *locale* (usata in uno specifico tenant). L'[entità Application][AAD-Graph-App-Entity] di Azure AD Graph definisce lo schema di un oggetto applicazione. Un oggetto applicazione ha quindi una relazione 1:1 con l'applicazione software e una relazione 1:*n* con gli *n* oggetti entità servizio corrispondenti.

#### Oggetto entità servizio
L'oggetto entità servizio definisce i criteri e le autorizzazioni per un'applicazione e costituisce la base per un'entità di sicurezza in cui verrà rappresentata l'applicazione al momento dell'accesso alle risorse in fase di esecuzione. L'[entità ServicePrincipal][AAD-Graph-Sp-Entity] di Azure AD Graph definisce lo schema di un oggetto entità servizio.

È necessario un oggetto entità servizio in ogni tenant per cui deve essere rappresentato l'utilizzo di un'istanza dell'applicazione, per consentire un accesso sicuro alle risorse di proprietà di account utente del tenant. Un'applicazione con tenant singolo disporrà solo di un'entità servizio (nel relativo tenant home). Un'[applicazione Web](active-directory-dev-glossary.md#web-client) multi-tenant avrà un'entità servizio in ogni tenant autorizzato ad accedere alle risorse dal relativo amministratore o utente. Dopo il consenso, l'oggetto entità servizio verrà consultato per le future richieste di autorizzazione.

> [AZURE.NOTE] Qualsiasi modifica apportata all'oggetto applicazione verrà riflessa solo nell'oggetto entità servizio nel tenant home dell'applicazione, ovvero nel tenant in cui è stata registrata. Per le applicazioni multi-tenant, le modifiche apportate all'oggetto applicazione non vengono riflesse negli oggetti entità servizio dei tenant consumer fino a quando il tenant consumer non rimuove e concede nuovamente l'accesso.

## Esempio
Il diagramma seguente illustra la relazione tra l'oggetto applicazione e i corrispondenti oggetti entità servizio di un'applicazione, nel contesto di un'applicazione multi-tenant di esempio denominata **app HR**. In questo scenario sono presenti tre tenant di Azure AD:

- **Adatum**, il tenant usato dalla società che ha sviluppato l'**app HR**
- **Contoso**, il tenant usato dall'organizzazione Contoso, che utilizza l'**app HR**
- **Fabrikam**, il tenant usato dall'organizzazione Fabrikam, che utilizza anch'essa l'**app HR**

![Relazione tra un oggetto applicazione e un oggetto entità servizio](./media/active-directory-application-objects/application-objects-relationship.png)  

Nel diagramma qui sopra, il passaggio 1 è il processo di creazione degli oggetti applicazione ed entità servizio nel tenant home dell'applicazione.

Nel passaggio 2, quando gli amministratori di Contoso e Fabrikam completano il consenso, nel tenant di Azure AD della rispettiva società viene creato un oggetto entità servizio a cui vengono assegnate le autorizzazioni concesse dall'amministratore. Si noti anche che l'app HR potrebbe essere configurata/progettata per permettere il consenso da parte di utenti per l'uso individuale.

Nel Passaggio 3, ogni tenant consumer dell'applicazione HR (Contoso e Fabrikam) dispone del proprio oggetto di entità servizio. Ognuno rappresenta l'uso di un'istanza dell'applicazione in fase di runtime, gestito tramite le autorizzazioni concesse dall'amministratore.

## Passaggi successivi
L'oggetto applicazione di un'applicazione è accessibile tramite l'API Graph di Azure AD, nella rappresentazione dell'[entità Application][AAD-Graph-App-Entity] di OData

L'oggetto entità servizio di un'applicazione è accessibile tramite l'API Graph di Azure AD, nella rappresentazione dell'[entità ServicePrincipal][AAD-Graph-Sp-Entity] di OData



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0810_2016-->