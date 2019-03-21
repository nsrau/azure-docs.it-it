---
title: Fornire il consenso per servizi di LinkedIn per l'organizzazione - Azure Active Directory | Microsoft Docs
description: Illustra come abilitare o disabilitare l'integrazione di LinkedIn per le app Microsoft in Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199699"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Fornire il consenso per l'integrazione di LinkedIn per l'organizzazione di Azure Active Directory

In questo articolo, è possibile informazioni su come abilitare o disabilitare l'integrazione di LinkedIn per l'organizzazione nell'interfaccia di amministrazione di Azure Active Directory (Azure AD).

> [!IMPORTANT]
> L'impostazione di integrazione di LinkedIn è attualmente in corso l'implementazione per organizzazioni di Azure AD. Si è implementato nell'organizzazione, viene abilitato per impostazione predefinita.
> 
> Eccezioni:
> * L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per il governo degli Stati Uniti, Microsoft Cloud per la Germania,oppure Azure e Office 365 gestito da 21Vianet in Cina.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Germania. L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per la Germania.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Francia.
>
> L'integrazione funziona solo se è stato abilitato *e* dopo che gli utenti fornire il consenso alle App che accedono ai dati aziendali per loro conto. Per informazioni sull'impostazione del consenso dell'utente, vedere [come rimuovere l'accesso un utente a un'applicazione](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Abilitare o disabilitare l'integrazione di LinkedIn per gli utenti nel portale di Azure

È possibile scegliere di abilitare o disabilitare l'integrazione di LinkedIn per l'intero tenant oppure solo per utenti selezionati nel tenant.

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/) con un account amministratore globale per il tenant di Azure AD.
2. Selezionare **Utenti**.
3. Nel pannello **Utenti** selezionare **Impostazioni utente**.
4. In **LinkedIn account connections** (Connessioni account LinkedIn):

   * Selezionare **Sì** fornire il consenso per tutti gli utenti dell'organizzazione connettere i propri account per accedere ai propri contatti LinkedIn in alcune App Microsoft.
   * Selezionare **Selected** fornire il consenso per solo determinati utenti nell'organizzazione connettere i propri account per accedere ai propri contatti LinkedIn in alcune App Microsoft.
   * Selezionare **No** per ritirare il consenso per gli utenti dell'organizzazione per connettere i propri account per accedere ai propri contatti LinkedIn in alcune App Microsoft.
    ![Abilitazione dell'integrazione di LinkedIn dell'organizzazione](./media/linkedin-integration/linkedin-integration.png)
5. Al termine salvare le impostazioni facendo clic su **Salva**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Abilitare o disabilitare l'integrazione di LinkedIn per gli utenti in Criteri di gruppo

1. Scaricare i [file dei modelli amministrativi di Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Estrarre i file **ADMX** e copiarli nel repository centrale.
3. Aprire Gestione Criteri di gruppo.
4. Creare un oggetto Criteri di gruppo con l'impostazione seguente: **Configurazione utente** > **Modelli amministrativi** > **Microsoft Office 2016** > **Varie** > **Mostra le funzionalità di LinkedIn nelle applicazioni di Office** .
5. Selezionare **Abilitato** o **Disabilitato**.
  
   Stato | Effetto
   ------ | ------
   **Enabled** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è abilitata nelle Opzioni di Office 2016. Gli utenti dell'organizzazione possono usare le funzionalità di LinkedIn nelle applicazioni di Office.
   **Disabilitato** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è disabilitata nelle Opzioni di Office 2016 e gli utenti finali non possono modificare questa impostazione. Gli utenti dell'organizzazione non possono usare le funzionalità di LinkedIn nelle applicazioni di Office 2016.

Questi Criteri di gruppo influiscono solo sulle app di Office 2016 per il computer locale. Gli utenti possono visualizzare in Office 365 le funzionalità di LinkedIn nella scheda del profilo anche se disabilitano LinkedIn per le app di Office 2016.

## <a name="learn-more"></a>Altre informazioni

* [Integrare LinkedIn all'interno dell'organizzazione](linkedin-user-consent.md)

* [About LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft)

* [Centro assistenza LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Passaggi successivi

Usare il collegamento seguente per visualizzare le impostazioni correnti dell'integrazione di LinkedIn nel portale di Azure:

[Visualizzare l'impostazione corrente dell'integrazione di LinkedIn nel portale di Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
