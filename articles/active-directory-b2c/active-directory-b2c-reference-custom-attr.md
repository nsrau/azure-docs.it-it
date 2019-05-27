---
title: Definire attributi personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Definire attributi personalizzati per l'applicazione in Azure Active Directory B2C per raccogliere informazioni sui clienti.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 629b872f78a30592fc00cb268066970b12b20561
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952797"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definire attributi personalizzati in Azure Active Directory B2C

 Ogni applicazione che riguarda il cliente ha requisiti specifici per le informazioni che devono essere raccolte. Il tenant Azure Active Directory (Azure AD) B2C viene fornito con un set predefinito di informazioni archiviate in attributi, ad esempio nome, cognome, città e codice postale. Con Azure AD B2C, è possibile estendere il set di attributi archiviati in ogni account cliente. 
 
 È possibile creare attributi personalizzati nel [portale di Azure](https://portal.azure.com/) e usarli nei flussi utente di iscrizione, nei flussi utente di iscrizione o accesso oppure nei flussi utente di modifica del profilo. Questi attributi possono anche essere scritti e letti usando l' [API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md). Gli attributi personalizzati in Azure Active Directory B2C usano le [Estensioni dello schema di directory dell'API Graph di Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).  

> [!NOTE]
> Supporto per più recente [API Microsoft Graph](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) per le query di Azure AD B2C tenant è ancora in fase di sviluppo.
>

## <a name="create-a-custom-attribute"></a>Creare un attributo personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Attributi utente**, quindi selezionare **Aggiungi**.
5. Fornire un **Nome** per l'attributo personalizzato (ad esempio, "ShoeSize")
6. Scegliere un **Tipo di dati**. Sono disponibili solo **Stringa**, **Booleano** e **Int**.
7. Facoltativamente, immettere una **Descrizione** esclusivamente a scopo informativo. 
8. Fare clic su **Create**(Crea).

L'attributo personalizzato è ora disponibile nell'elenco degli **Attributi utente**e per l'uso nei flussi utente. Un attributo personalizzato viene creato solo la prima volta che viene usato in un flusso utente e non quando viene aggiunto all'elenco di **Attributi utente**. 


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Usare un attributo personalizzato nel flusso utente

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
2. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo. 
4. Fare clic su **Attributi utente** e selezionare l'attributo personalizzato (ad esempio, "ShoeSize"). Fare clic su **Save**.
5. Fare clic su **Attestazioni dell'applicazione** e selezionare l'attributo personalizzato. 
6. Fare clic su **Save**.

Dopo aver creato un nuovo utente utilizzando un flusso utente che usa l'attributo personalizzato appena creato, l'oggetto può essere interrogata [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). In alternativa è possibile usare la [ **eseguire il flusso utente** ](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) funzionalità sulla base del flusso utente per verificare l'esperienza dei clienti. Ora si dovrebbe vedere **ShoeSize** nell'elenco di attributi che vengono raccolti durante il percorso dell'utente e nel token inviato all'applicazione. 

