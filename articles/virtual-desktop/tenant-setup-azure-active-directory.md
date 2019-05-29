---
title: Creare un tenant nell'anteprima di Desktop virtuale Windows - Azure
description: Descrive come configurare i tenant dell'anteprima di Desktop virtuale Windows in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2c9682746201306f1b99a04462819618225caa11
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164254"
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
   * L'account amministratore deve provenire dal tenant di Azure Active Directory in cui si sta provando a creare il tenant di Desktop virtuale Windows. Questa procedura non supporta gli account di Azure Active Directory B2B (guest).
   * L'account amministratore deve essere un account aziendale o dell'istituto di istruzione.
* Una sottoscrizione di Azure.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Concedere le autorizzazioni di Azure Active Directory all'anteprima del servizio Desktop virtuale Windows

Se sono già state concesse le autorizzazioni a Desktop virtuale Windows per questa istanza di Azure Active Directory, ignorare questa sezione.

La concessione di autorizzazioni al servizio Desktop virtuale Windows consente di eseguire query su Azure Active Directory per le attività amministrative e degli utenti finali.

Per concedere le autorizzazioni al servizio:

1. Aprire un browser e connettersi alla [pagina di consenso di Desktop virtuale Windows](https://rdweb.wvd.microsoft.com).
2. Per **Consent Option (Opzione consenso)**  > **Server App (App server)** immettere il nome del tenant di Azure Active Directory o l'ID directory, quindi selezionare **Submit** (Invia).
        Per i clienti dei provider di soluzioni cloud, l'ID corrisponde all'ID Microsoft del cliente recuperato dal portale per i partner. Per i clienti aziendali, l'ID si trova in **Azure Active Directory** > **Proprietà** > **ID directory**.
3. Accedere alla pagina di consenso di Desktop virtuale Windows con un account di amministratore globale. Ad esempio, nell'organizzazione Contoso l'account potrebbe essere admin@contoso.com o admin@contoso.onmicrosoft.com.  
4. Selezionare **Accetto**.
5. Attendere un minuto.
6. Tornare nella [pagina di consenso di Desktop virtuale Windows](https://rdweb.wvd.microsoft.com).
7. Scegliere **Consent Option (Opzione consenso)**  > **Client App (App client)** e immettere lo stesso nome di tenant di Azure Active Directory o ID directory, quindi selezionare **Submit** (Invia).
8. Accedere alla pagina di consenso di Desktop virtuale Windows con un account di amministratore globale come nel passaggio 3.
9. Selezionare **Accetto**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Assegnare il ruolo applicazione TenantCreator a un utente nel tenant di Azure Active Directory

L'assegnazione del ruolo applicazione TenantCreator a un utente di Azure Active Directory gli consente di creare un tenant in Desktop virtuale Windows associato ad Azure Active Directory. Per assegnare il ruolo TenantCreator, è necessario usare l'account di amministratore globale.

Per assegnare il ruolo applicazione TenantCreator con l'account di amministratore globale:

1. Aprire un browser e connettersi al [portale di Azure](https://portal.azure.com) con l'account di amministratore globale.
   - Se si usano più tenant di Azure Active Directory, è consigliabile aprire una sessione privata del browser, quindi copiare e incollare gli URL nella barra degli indirizzi.
2. Nella barra di ricerca del portale di Azure cercare **Applicazioni aziendali** e selezionare la voce visualizzata nella categoria **Servizi**.
3. All'interno di **Applicazioni aziendali** cercare **Desktop virtuale Windows**. Verranno visualizzate le due applicazioni per cui è stato fornito il consenso nella sezione precedente. Tra queste due app, selezionare **Desktop virtuale Windows**.
        ![Screenshot dei risultati della ricerca visualizzati per "Desktop virtuale Windows" in "Applicazioni aziendali". L'app denominata "Desktop virtuale Windows" è evidenziata.](media/tenant-enterprise-app.png)
4. Selezionare **Utenti e gruppi**. Come si può vedere, l'amministratore che ha concesso il consenso per l'applicazione è già presente nell'elenco con il ruolo **Accesso predefinito** assegnato. Questo ruolo non è sufficiente per creare un tenant di Desktop virtuale Windows. Continuare a seguire queste istruzioni per aggiungere il ruolo **TenantCreator** a un utente.
        ![Screenshot degli utenti e dei gruppi assegnati per gestire l'applicazione aziendale "Desktop virtuale Windows". Lo screenshot mostra solo un'assegnazione, relativa ad "Accesso predefinito".](media/tenant-default-access.png)
5. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.
6. Cercare un account utente che creerà il tenant di Desktop virtuale Windows. Per semplicità, può essere l'account di amministratore globale.

    ![Screenshot che mostra la selezione di un utente da aggiungere come "TenantCreator".](media/tenant-assign-user.png)

   > [!NOTE]
   > È necessario selezionare un utente (o un gruppo contenente un utente) che deriva da questa istanza di Azure Active Directory. Non è possibile scegliere un utente guest (B2B) o un'entità servizio.

7. Selezionare l'account utente, fare clic sul pulsante **Seleziona** e quindi selezionare **Assegna**.
8. Nella pagina **Desktop virtuale Windows - Utenti e gruppi** verificare se viene visualizzata una nuova voce con il ruolo **TenantCreator** assegnato all'utente che creerà il tenant di Desktop virtuale Windows.
        ![Screenshot degli utenti e dei gruppi assegnati per gestire l'applicazione aziendale "Desktop virtuale Windows". Lo screenshot ora include una seconda voce di un utente assegnato al ruolo "TenantCreator".](media/tenant-tenant-creator-added.png)

Prima di continuare con la creazione del tenant di Desktop virtuale Windows, sono necessarie due informazioni:
- ID tenant (o **ID directory**) di Azure Active Directory
- L'ID sottoscrizione di Azure

Per trovare l'ID tenant (o **ID directory**) di Azure Active Directory:
1. Nella stessa sessione del portale di Azure cercare **Azure Active Directory** nella barra di ricerca e selezionare la voce visualizzata nella categoria **Servizi**.
        ![Screenshot dei risultati della ricerca di "Azure Active Directory" nel portale di Azure. Il risultato nella categoria "Servizi" è evidenziato.](media/tenant-search-azure-active-directory.png)
2. Scorrere in basso fino a trovare e selezionare **Proprietà**.
3. Cercare **ID directory**, quindi selezionare l'icona degli Appunti. Incollare l'ID in una posizione da cui è possibile usarlo in seguito come **AadTenantId**.
        ![Screenshot delle proprietà di Azure Active Directory. Il puntatore del mouse passa sull'icona degli Appunti per copiare e incollare l'"ID directory".](media/tenant-directory-id.png)

Per trovare l'ID sottoscrizione di Azure:
1. Nella stessa sessione del portale di Azure cercare **Sottoscrizioni** nella barra di ricerca e selezionare la voce visualizzata nella categoria **Servizi**.
        ![Screenshot dei risultati della ricerca di "Azure Active Directory" nel portale di Azure. Il risultato nella categoria "Servizi" è evidenziato.](media/tenant-search-subscription.png)
2. Selezionare la sottoscrizione di Azure da usare per ricevere le notifiche del servizio Desktop virtuale Windows.
3. Cercare **ID sottoscrizione**, quindi passare il puntatore del mouse sul valore fino a visualizzare l'icona degli Appunti. Selezionare l'icona degli Appunti e incollare l'ID in una posizione da cui è possibile usarlo in seguito come **AzureSubscriptionId**.
        ![Screenshot delle proprietà della sottoscrizione di Azure. Il puntatore del mouse passa sull'icona degli Appunti per copiare e incollare l'"ID sottoscrizione".](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Creare un tenant dell'anteprima di Desktop virtuale Windows

Dopo aver concesso le autorizzazioni al servizio Desktop virtuale Windows per eseguire query su Azure Active Directory e aver assegnato il ruolo TenantCreator a un account utente, è possibile creare il tenant di Desktop virtuale Windows.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Accedere a Desktop virtuale Windows usando l'account utente TenantCreator con questo cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Creare quindi un nuovo tenant di Desktop virtuale Windows associato al tenant di Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

I valori tra parentesi dovranno essere sostituiti con i valori appropriati per l'organizzazione e per il tenant. Il nome scelto per il nuovo tenant di Desktop virtuale Windows deve essere un nome univoco globale. Ad esempio, per l'utente TenantCreator di Desktop virtuale Windows nell'organizzazione Contoso, il cmdlet da eseguire sarà come questo:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il tenant, sarà necessario creare un'entità servizio in Azure Active Directory a cui assegnare un ruolo in Desktop virtuale Windows. L'entità servizio consentirà di distribuire l'offerta di Azure Marketplace di Desktop virtuale Windows per creare un pool di host. Per altre informazioni sui pool di host, continuare con l'esercitazione per la creazione di pool di host in Desktop virtuale Windows.

> [!div class="nextstepaction"]
> [Creare entità servizio e assegnazioni di ruolo con PowerShell](./create-service-principal-role-powershell.md)
