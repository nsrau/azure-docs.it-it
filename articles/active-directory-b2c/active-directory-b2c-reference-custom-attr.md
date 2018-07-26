---
title: Definire attributi personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Definire attributi personalizzati per l'applicazione in Azure Active Directory B2C per raccogliere informazioni sui clienti.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968775"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definire attributi personalizzati in Azure Active Directory B2C

 Ogni applicazione che riguarda il cliente ha requisiti specifici per le informazioni che devono essere raccolte. Il tenant Azure Active Directory (Azure AD) B2C viene fornito con un set predefinito di informazioni archiviate in attributi, ad esempio nome, cognome, città e codice postale. Con Azure AD B2C, è possibile estendere il set di attributi archiviati in ogni account cliente. 
 
 È possibile creare attributi personalizzati nel [portale di Azure](https://portal.azure.com/) e usarli nei criteri di iscrizione, criteri di iscrizione o accesso o di modifica del profilo. Questi attributi possono anche essere scritti e letti usando l' [API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md). Gli attributi personalizzati in Azure Active Directory B2C usano le [Estensioni dello schema di directory dell'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

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
8. Fare clic su **Crea**.

L'attributo personalizzato è ora disponibile nell'elenco degli **Attributi utente**e per l'uso nei criteri. Un attributo personalizzato viene creato solo la prima volta che viene usato in un criterio e non quando viene aggiunto all'elenco di **Attributi utente**.

## <a name="use-a-custom-attribute-in-your-policy"></a>Usare un attributo personalizzato nei criteri

1. Nel tenant di Azure AD B2C, selezionare **Criteri di iscrizione o accesso**.
2. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo. 
3. Fare clic su **Modifica**.
4. Fare clic su **Attributi iscrizione** e selezionare l'attributo personalizzato (ad esempio, "ShoeSize"). Fare clic su **OK**.
5. Fare clic su **Attestazioni dell'applicazione** e selezionare l'attributo personalizzato. Fare clic su **OK**.
6. Fare clic su **Save**.

È possibile utilizzare la funzionalità **Esegui ora** nei criteri per verificare l'esperienza utente. Ora si dovrebbe vedere **ShoeSize** nell'elenco di attributi che vengono raccolti durante il percorso dell'utente e nel token inviato all'applicazione.

