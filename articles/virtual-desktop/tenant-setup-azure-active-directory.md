---
title: Creare un tenant nell'anteprima di Desktop virtuale Windows - Azure
description: Descrive come configurare i tenant dell'anteprima di Desktop virtuale Windows in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: b8b5c2cef1db5018ce0d61e1950f49a3bd215ac2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402899"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Esercitazione: Creare un tenant nell'anteprima di Desktop virtuale Windows

La creazione di un tenant nell'anteprima di Desktop virtuale Windows è il primo passaggio per lo sviluppo di una soluzione di virtualizzazione desktop. Un tenant è un gruppo di uno o più pool di host. Ogni pool di host è costituito da più host di sessione, in esecuzione come macchine virtuali in Azure e registrati nel servizio Desktop virtuale Windows. Ogni pool di host è inoltre costituito da uno o più gruppi di app usati per pubblicare le risorse delle applicazioni desktop e remote per gli utenti. Con un tenant è possibile creare pool di host e gruppi di app, assegnare utenti e stabilire connessioni tramite il servizio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere le autorizzazioni di Azure Active Directory al servizio Desktop virtuale Windows.
> * Assegnare il ruolo applicazione TenantCreator a un utente nel tenant di Azure Active Directory.
> * Creare un tenant in Desktop virtuale Windows.

Ecco i requisiti per configurare il tenant di Desktop virtuale Windows:

* L'ID tenant di [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per gli utenti di Desktop virtuale Windows.
* Un account di amministratore globale all'interno del tenant di Azure Active Directory.
   * Questo requisito si applica anche per le organizzazioni CSP (provider di soluzioni cloud) che creano un tenant in Desktop virtuale Windows per i loro clienti. Le organizzazioni CSP devono essere in grado di accedere come amministratore globale di Azure Active Directory dei clienti.
* Un ID sottoscrizione di Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Concedere le autorizzazioni di Azure Active Directory all'anteprima del servizio Desktop virtuale Windows

Se sono già state concesse le autorizzazioni a Desktop virtuale Windows per questa istanza di Azure Active Directory, ignorare questa sezione.

La concessione di autorizzazioni al servizio Desktop virtuale Windows consente di eseguire query su Azure Active Directory per le attività amministrative e degli utenti finali.

Per concedere le autorizzazioni al servizio:

1. Aprire un browser e connettersi alla [pagina di consenso di Desktop virtuale Windows](https://rdweb.wvd.microsoft.com).
2. Per **Consent Option (Opzione consenso)** > **Server App (App server)** immettere il nome del tenant di Azure Active Directory o l'ID directory, quindi selezionare **Submit** (Invia).
        Per i clienti dei provider di soluzioni cloud, l'ID corrisponde all'ID Microsoft del cliente recuperato dal portale per i partner. Per i clienti aziendali, l'ID si trova in **Azure Active Directory** > **Proprietà** > **ID directory**.
3. Accedere alla pagina di consenso di Desktop virtuale Windows con un account di amministratore globale. Ad esempio, nell'organizzazione Contoso l'account potrebbe essere admin@contoso.com o admin@contoso.onmicrosoft.com.  
4. Selezionare **Accetto**.
5. Attendere un minuto.
6. Tornare nella [pagina di consenso di Desktop virtuale Windows](https://rdweb.wvd.microsoft.com).
7. Scegliere **Consent Option (Opzione consenso)** > **Client App (App client)** e immettere lo stesso nome di tenant di Azure AD o ID directory, quindi selezionare **Submit** (Invia).
8. Accedere alla pagina di consenso di Desktop virtuale Windows con un account di amministratore globale come nel passaggio 3.
9. Selezionare **Accetto**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Assegnare il ruolo applicazione TenantCreator a un utente nel tenant di Azure Active Directory

L'assegnazione del ruolo applicazione TenantCreator a un utente di Azure Active Directory gli consente di creare un tenant in Desktop virtuale Windows associato ad Azure Active Directory. Per assegnare il ruolo TenantCreator, è necessario usare l'account di amministratore globale.

Per assegnare il ruolo applicazione TenantCreator con l'account di amministratore globale:

1. Aprire un browser e connettersi al [portale di Azure Active Directory](https://aad.portal.azure.com) con l'account di amministratore globale.
   - Se si usano più tenant di Azure AD, è consigliabile aprire una sessione privata del browser, quindi copiare e incollare gli URL nell'indirizzo.
2. Selezionare **Applicazioni aziendali** e cercare **Desktop virtuale Windows**. Verranno visualizzate le due applicazioni per cui è stato fornito il consenso nella sezione precedente. Tra queste due app, selezionare **Desktop virtuale Windows**.
3. Selezionare **Utenti e gruppi** e quindi **Aggiungi utente**.
4. Nel pannello **Aggiungi assegnazione** selezionare Utenti e gruppi.
5. Cercare un account utente che creerà il tenant di Desktop virtuale Windows.
   - Per semplicità, può essere l'account di amministratore globale.
6. Selezionare l'account utente, fare clic sul pulsante **Seleziona** e quindi selezionare **Assegna**.

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Creare un tenant dell'anteprima di Desktop virtuale Windows

Dopo aver concesso le autorizzazioni al servizio Desktop virtuale Windows per eseguire query su Azure Active Directory e aver assegnato il ruolo TenantCreator a un account utente, è possibile creare il tenant di Desktop virtuale Windows.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Accedere a Desktop virtuale Windows usando l'account utente TenantCreator con questo cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
```

Creare quindi un nuovo tenant di Desktop virtuale Windows associato al tenant di Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

I valori tra parentesi dovranno essere sostituiti con i valori appropriati per l'organizzazione e per il tenant. Ad esempio, per l'utente TenantCreator di Desktop virtuale Windows nell'organizzazione Contoso, il cmdlet da eseguire sarà come questo:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il tenant, è necessario creare un pool di host. Per altre informazioni sui pool di host, continuare con l'esercitazione per la creazione di pool di host in Desktop virtuale Windows.

> [!div class="nextstepaction"]
> [Esercitazione sui pool di host di Desktop virtuale Windows](./create-host-pools-azure-marketplace.md)
