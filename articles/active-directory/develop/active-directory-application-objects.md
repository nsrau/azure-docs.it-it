---
title: "Oggetti applicazione e oggetti entità servizio in Azure Active Directory | Microsoft Docs"
description: "Descrizione della relazione tra oggetti applicazione e oggetti entità servizio in Azure Active Directory"
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: bryanla;mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 8f70d9aeb0a407cdb76a5ce25eb620be58bb2659
ms.openlocfilehash: f453dcafe629c871dc29742208e4864454f4c57e


---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Oggetti applicazione e oggetti entità servizio in Azure Active Directory
In alcuni casi il significato del termine "applicazione"in Azure Active Directory (Azure AD) può essere facilmente frainteso, soprattutto se manca il contesto di supporto. Questo articolo offre un chiarimento definendo gli aspetti concettuali e concreti dell'integrazione dell'applicazione Azure AD, con un esempio di registrazione e consenso per un'[applicazione multi-tenant](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Panoramica
Un'applicazione di Azure AD non è semplicemente un componente software. È un termine concettuale che fa riferimento non solo al software dell'applicazione ma anche alla relativa registrazione (detta anche configurazione di identità) in Azure AD, che ne consente la partecipazione a "conversazioni" di autenticazione e autorizzazione in fase di esecuzione. Per definizione, un'applicazione può funzionare in un ruolo [client](active-directory-dev-glossary.md#client-application) (che utilizza una risorsa), in un ruolo [server di risorse](active-directory-dev-glossary.md#resource-server) (che espone le API ai client) o in entrambi i ruoli. Il protocollo di conversazione è definito da un [flusso di concessione di autorizzazione OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), con l'obiettivo di consentire al client e alla risorsa di accedere e proteggere, rispettivamente, i dati di una risorsa. Verrà ora approfondito come il modello applicativo di Azure AD rappresenta internamente un'applicazione. 

## <a name="application-registration"></a>Registrazione dell'applicazione
Quando si registra un'applicazione nel [portale di Azure][AZURE-Portal], nel tenant di Azure AD vengono creati due oggetti: un oggetto applicazione e un oggetto entità servizio.

#### <a name="application-object"></a>Oggetto applicazione
Un'applicazione di Azure AD è *definita* da un solo oggetto applicazione che risiede nel tenant di Azure AD in cui l'applicazione è stata registrata, noto come tenant "home" dell'applicazione. L'oggetto applicazione fornisce le informazioni relative all'identità dell'applicazione ed è il modello da cui *derivano* gli oggetti entità servizio corrispondenti usati in fase di esecuzione. 

Si può considerare l'oggetto applicazione come la rappresentazione *globale* dell'applicazione (usata in tutti i tenant) e l'entità servizio come la rappresentazione *locale* (usata in uno specifico tenant). L'[entità applicativa][AAD-Graph-App-Entity] di Azure AD Graph definisce lo schema di un oggetto applicazione. Un oggetto applicazione ha quindi una relazione 1:1 con l'applicazione software e una relazione 1:*n* con gli *n* oggetti entità servizio corrispondenti.

#### <a name="service-principal-object"></a>Oggetto entità servizio
L'oggetto entità servizio definisce i criteri e le autorizzazioni per un'applicazione e costituisce la base per un'entità di sicurezza in cui verrà rappresentata l'applicazione al momento dell'accesso alle risorse in fase di esecuzione. L'[entità ServicePrincipal][AAD-Graph-Sp-Entity] di Azure AD Graph definisce lo schema di un oggetto entità servizio. 

Prima che un tenant Azure AD consenta a un'applicazione di accedere alle risorse che protegge, è necessario creare un'entità servizio nel tenant specificato. L'entità servizio consente ad Azure AD di proteggere l'accesso dell'applicazione alle risorse di proprietà degli utenti dal tenant. Un'applicazione con tenant singolo disporrà solo di un'entità servizio (nel relativo tenant home). Un' [applicazione Web](active-directory-dev-glossary.md#web-client) multi-tenant avrà un'entità servizio in ogni tenant autorizzato ad accedere alle risorse dal relativo amministratore o utente. Dopo il consenso, l'oggetto entità servizio verrà consultato per le future richieste di autorizzazione. 

> [!NOTE]
> Qualsiasi modifica apportata all'oggetto applicazione verrà riflessa solo nell'oggetto entità servizio nel tenant home dell'applicazione, ovvero nel tenant in cui è stata registrata. Per le applicazioni multi-tenant, le modifiche apportate all'oggetto applicazione non vengono riflesse negli oggetti entità servizio dei tenant consumer fino a quando il tenant consumer non rimuove e concede nuovamente l'accesso.
> 
> 

## <a name="example"></a>Esempio
Il diagramma seguente illustra la relazione tra l'oggetto applicazione e i corrispondenti oggetti entità servizio di un'applicazione, nel contesto di un'applicazione multi-tenant di esempio denominata **app HR**. In questo scenario sono presenti tre tenant di Azure AD: 

* **Adatum**, il tenant usato dalla società che ha sviluppato l'**app HR**
* **Contoso**, il tenant usato dall'organizzazione Contoso, che utilizza l'**app HR**
* **Fabrikam**, il tenant usato dall'organizzazione Fabrikam, che utilizza anch'essa l'**app HR**

![Relazione tra un oggetto applicazione e un oggetto entità servizio](./media/active-directory-application-objects/application-objects-relationship.png)

Nel diagramma qui sopra, il passaggio 1 è il processo di creazione degli oggetti applicazione ed entità servizio nel tenant home dell'applicazione.

Nel passaggio 2, quando gli amministratori di Contoso e Fabrikam completano il consenso, nel tenant di Azure AD della rispettiva società viene creato un oggetto entità servizio a cui vengono assegnate le autorizzazioni concesse dall'amministratore. Si noti anche che l'app HR potrebbe essere configurata/progettata per permettere il consenso da parte di utenti per l'uso individuale.

Nel Passaggio 3, ogni tenant consumer dell'applicazione HR (Contoso e Fabrikam) dispone del proprio oggetto di entità servizio. Ognuno rappresenta l'uso di un'istanza dell'applicazione in fase di runtime, gestito tramite le autorizzazioni concesse dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi
L'oggetto applicazione di un'applicazione è accessibile tramite l'API Graph di Azure AD, nella rappresentazione dell'[entità applicativa][AAD-Graph-App-Entity] di OData

L'oggetto entità servizio di un'applicazione è accessibile tramite l'API Graph di Azure AD, nella rappresentazione dell'[entità ServicePrincipal][AAD-Graph-Sp-Entity] di OData

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://portal.azure.com



<!--HONumber=Feb17_HO2-->


