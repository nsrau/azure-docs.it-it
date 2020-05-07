---
title: Avvio rapido all'aggiunta di criteri di denominazione per i gruppi - Azure Active Directory | Microsoft Docs
description: Viene illustrato come aggiungere nuovi utenti o eliminare utenti esistenti in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4253f5bd702abd061cf1cddd4badd68c9cd5d475
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582829"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Avvio rapido: Criteri di denominazione per i gruppi in Azure Active Directory

In questa guida di avvio rapido vengono configurati i criteri di denominazione nell'organizzazione di Azure Active Directory (Azure AD) per i gruppi di Office 365 creati dall'utente, con lo scopo di ordinare i gruppi dell'organizzazione ed eseguire ricerche al loro interno. È possibile, ad esempio, usare i criteri di denominazione per:

* Comunicare la funzione, l'appartenenza, l'area geografica o il creatore di un gruppo.
* Agevolare la classificazione dei gruppi nella Rubrica.
* Impedire l'uso di parole specifiche nei nomi dei gruppi e negli alias.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Configurare i criteri di denominazione dei gruppi nel portale di Azure

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account Amministratore utenti.
1. Selezionare **Gruppi** e quindi **Criteri di denominazione** per visualizzare la pagina corrispondente.

    ![aprire la pagina Criteri di denominazione nell'interfaccia di amministrazione](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Visualizzare o modificare i criteri di denominazione prefisso-suffisso

1. Nella pagina **Criteri di denominazione** fare clic su **Criteri di denominazione del gruppo**.
1. È possibile visualizzare o modificare singolarmente i criteri di denominazione correnti per il prefisso o il suffisso selezionando gli attributi o le stringhe da applicare come parte dei criteri di denominazione.
1. Per rimuovere un prefisso o un suffisso dall'elenco, selezionare il prefisso o il suffisso desiderato e quindi fare clic su **Elimina**. È possibile eliminare più elementi contemporaneamente.
1. Fare clic su **Salva** per rendere effettive le modifiche apportate ai criteri.

### <a name="view-or-edit-the-custom-blocked-words"></a>Visualizzare o modificare le parole bloccate personalizzate

1. Nella pagina **Criteri di denominazione** fare clic su **Parole bloccate**.

    ![modificare e caricare l'elenco delle parole bloccate per i criteri di denominazione](./media/groups-naming-policy/blockedwords.png)

1. Visualizzare o modificare l'elenco corrente di parole bloccate personalizzate facendo clic su **Scarica**.
1. Caricare il nuovo elenco di parole bloccate personalizzate facendo clic sull'icona del file.
1. Fare clic su **Salva** per rendere effettive le modifiche apportate ai criteri.

È tutto. Sono stati impostati criteri di denominazione e sono state aggiunte le parole bloccate personalizzate.

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="remove-the-naming-policy-using-azure-portal"></a>Rimuovere i criteri di denominazione con il portale di Azure

1. Nella pagina **Criteri di denominazione** fare clic su **Elimina criteri**.
1. Dopo la conferma dell'eliminazione, i criteri di denominazione vengono rimossi, inclusi tutti i criteri di denominazione prefisso-suffisso e le eventuali parole bloccate personalizzate.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come impostare i criteri di denominazione per l'organizzazione di Azure AD mediante il portale di Azure.

Per altre informazioni, inclusi i cmdlet di PowerShell per i criteri di denominazione, i vincoli tecnici, l'aggiunta di un elenco di parole bloccate personalizzate e le esperienze degli utenti finali nelle app di Office 365, passare all'articolo seguente.
> [!div class="nextstepaction"]
> [Criteri di denominazione con PowerShell](groups-naming-policy.md)