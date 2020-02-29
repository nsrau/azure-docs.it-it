---
title: Definire attributi personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Definire attributi personalizzati per l'applicazione in Azure Active Directory B2C per raccogliere informazioni sui clienti.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186050"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definire attributi personalizzati in Azure Active Directory B2C

 Ogni applicazione che riguarda il cliente ha requisiti specifici per le informazioni che devono essere raccolte. Il tenant di Azure Active Directory B2C (Azure AD B2C) viene fornito con un set predefinito di informazioni archiviate negli attributi, ad esempio nome, cognome, città e codice postale specificati. Con Azure AD B2C, è possibile estendere il set di attributi archiviati in ogni account cliente.

 È possibile creare attributi personalizzati nel [portale di Azure](https://portal.azure.com/) e usarli nei flussi utente di iscrizione, nei flussi utente di iscrizione o accesso oppure nei flussi utente di modifica del profilo. È anche possibile leggere e scrivere questi attributi usando l' [API Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Creare un attributo personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**.

    ![Passare al tenant di Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Tenant B2C evidenziato nel filtro di directory e sottoscrizione](./media/user-flow-custom-attributes/select-directory.PNG)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Attributi utente**, quindi selezionare **Aggiungi**.
5. Fornire un **Nome** per l'attributo personalizzato (ad esempio, "ShoeSize")
6. Scegliere un **Tipo di dati**. Sono disponibili solo **Stringa**, **Booleano** e **Int**.
7. Facoltativamente, immettere una **Descrizione** esclusivamente a scopo informativo.
8. Fare clic su **Crea**.

L'attributo personalizzato è ora disponibile nell'elenco degli **Attributi utente**e per l'uso nei flussi utente. Un attributo personalizzato viene creato solo la prima volta che viene usato in un flusso utente e non quando viene aggiunto all'elenco di **Attributi utente**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Usare un attributo personalizzato nel flusso utente

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Fare clic su **Attributi utente** e selezionare l'attributo personalizzato (ad esempio, "ShoeSize"). Fare clic su **Salva**.
1. Fare clic su **Attestazioni dell'applicazione** e selezionare l'attributo personalizzato.
1. Fare clic su **Salva**.

Dopo aver creato un nuovo utente usando un flusso utente che usa l'attributo personalizzato appena creato, l'oggetto può essere sottoposto a query in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). In alternativa, è possibile usare la funzionalità [Esegui flusso utente](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) nel flusso utente per verificare l'esperienza del cliente. Ora si dovrebbe vedere **ShoeSize** nell'elenco di attributi che vengono raccolti durante il percorso dell'utente e nel token inviato all'applicazione.
