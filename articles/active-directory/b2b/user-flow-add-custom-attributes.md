---
title: Aggiungere attributi personalizzati per i flussi utente di Azure AD
description: Informazioni su come personalizzare gli attributi per i flussi utente di iscrizione self-service.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594710"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definire attributi personalizzati per i flussi utente (anteprima)
|     |
| --- |
| La funzionalità degli attributi utente personalizzati è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

È possibile definire requisiti diversi per le informazioni da raccogliere durante l'iscrizione alle singole applicazioni. Azure AD include un set predefinito di informazioni archiviate in attributi, ad esempio nome, cognome, città e codice postale. Con Azure AD è possibile estendere il set di attributi archiviati in un account Guest quando l'utente esterno si iscrive tramite un flusso utente.

È possibile creare attributi personalizzati nel portale di Azure e usarli nei flussi utente di iscrizione self-service. Questi attributi possono anche essere scritti e letti usando l'[API Microsoft Graph](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). L'API Microsoft Graph supporta la creazione e l'aggiornamento di un utente con attributi di estensione. Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_<Application-client-id>_attributename`. Ad esempio:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

È possibile trovare il valore di `<Application-client-id>` nella pagina **Registrazioni app** accanto a **ID applicazione (client)** . Questo ID è specifico del tenant.

## <a name="create-a-custom-attribute"></a>Creare un attributo personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **Attributi utente personalizzati (anteprima)** . Sono elencati gli attributi utente disponibili.

   ![Selezionare gli attributi utente per l'iscrizione](media/user-flow-add-custom-attributes/user-attributes.png)

5. Per aggiungere un attributo, selezionare **Aggiungi**.
6. Nel riquadro **Aggiungi un attributo** immettere i valori seguenti:

   - **Nome**: specificare un nome per l'attributo personalizzato, ad esempio "Shoesize".
   - **Tipo di dati**: scegliere un tipo di dati (**String**, **Boolean** o **Int**).
   - **Descrizione**: immettere una descrizione facoltativa dell'attributo personalizzato per uso interno. Questa descrizione non è visibile all'utente.

   ![Aggiungi un attributo](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selezionare **Crea**.

L'attributo personalizzato è ora disponibile nell'elenco degli attributi utente e per l'uso nei flussi utente. Un attributo personalizzato viene creato solo la prima volta che viene usato in un flusso utente e non quando viene aggiunto all'elenco di attributi utente.

Dopo aver creato un nuovo utente con un flusso utente che usa l'attributo personalizzato appena creato, è possibile eseguire query sull'oggetto in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Ora si dovrebbe vedere **ShoeSize** nell'elenco di attributi che vengono raccolti durante il percorso dell'utente e nel token inviato all'applicazione. Per informazioni su come includere queste attestazioni nel token restituito all'applicazione, vedere [Configurazione delle attestazioni facoltative dell'estensione di directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims).

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere un flusso utente di iscrizione self-service a un'app](self-service-sign-up-user-flow.md)