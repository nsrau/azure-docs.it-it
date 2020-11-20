---
title: Arcose Labs con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con arcose Labs per la protezione da attacchi bot, attacchi di acquisizione di account e aperture fraudolente degli account.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 333bb42643539cedec04d37680749c749a003536
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953883"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di arcose Labs con Azure Active Directory B2C

Questa esercitazione illustra come integrare Azure AD B2C autenticazione con arcose Labs. Arcose Labs consente alle organizzazioni di attacchi bot, attacchi di acquisizione di account e aperture fraudolente degli account.  

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* [Un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

## <a name="scenario-description"></a>Descrizione dello scenario

Il diagramma seguente illustra il modo in cui arcose Labs si integra con Azure AD B2C.

![Diagramma dell'architettura di arcose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Passaggio  | Descrizione |
|---|---|
|1     | Un utente accede con un account creato in precedenza. Quando l'utente seleziona Invia, viene visualizzata la richiesta di imposizione di arcose Labs. Dopo che l'utente ha completato la richiesta, lo stato viene inviato a arcose Labs per generare un token.        |
|2     |  Arcose Labs invia il token di nuovo al Azure AD B2C.       |
|3     |  Prima dell'invio del modulo di accesso, il token viene inviato a arcose Labs per la verifica.       |
|4     |  Arcose restituisce un esito positivo o negativo della richiesta.       |
|5     |  Se la richiesta viene completata correttamente, viene inviato un modulo di accesso a Azure AD B2C e Azure AD B2C completa l'autenticazione.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Onboarding con arcose Labs

1. Per iniziare, contattare [arcose Labs](https://www.arkoselabs.com/book-a-demo/) e creare un account.

2. Una volta creato l'account, passare a https://dashboard.arkoselabs.com/login .

3. All'interno del Dashboard passare a Impostazioni sito per trovare la chiave pubblica e la chiave privata. Queste informazioni saranno necessarie in un secondo momento per configurare Azure AD B2C.

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Parte 1: creare l'archiviazione BLOB per archiviare il codice HTML personalizzato

Per creare un account di archiviazione, seguire questa procedura:  

1. Accedere al portale di Azure.

2. Assicurarsi di usare la directory che contiene la sottoscrizione di Azure. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene la sottoscrizione. Questa directory è diversa da quella che contiene il tenant Azure B2C.

3. Scegliere **tutti i servizi** nell'angolo superiore sinistro del portale di Azure, quindi cercare e selezionare  **account di archiviazione**.

4. Selezionare  **Aggiungi**.

5. In  **gruppo di risorse** selezionare  **Crea nuovo**, immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.

6. Immettere il nome dell'account di archiviazione. Il nome deve essere univoco in Azure, deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.

7. Selezionare il percorso dell'account di archiviazione o accettare quello predefinito.

8. Accettare tutti gli altri valori predefiniti, selezionare   **Verifica & crea**  >  **creazione**.

9. Dopo aver creato l'account di archiviazione, selezionare  **Vai alla risorsa**.

#### <a name="create-a-container"></a>Creare un contenitore

1. Nella pagina Panoramica dell'account di archiviazione selezionare  **BLOB**.

2. Selezionare   **container**, immettere un nome per il contenitore, scegliere   **BLOB** (accesso in lettura anonimo solo per BLOB) e quindi fare clic su **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Abilitare la condivisione di risorse tra le origini (CORS)

Il codice Azure AD B2C in un browser usa un approccio moderno e standard per caricare il contenuto personalizzato da un URL specificato in un flusso utente. CORS consente la richiesta di risorse limitate in una pagina Web da altri domini.

1. Nel menu selezionare  **CORS**.

2. Per le  **origini consentite**, immettere  `https://your-tenant-name.b2clogin.com` . Sostituire-tenant-Name con il nome del tenant Azure AD B2C. Ad esempio,  `https://fabrikam.b2clogin.com`. Usare tutte le lettere minuscole quando si immette il nome del tenant.

3. Per i  **metodi consentiti**, selezionare  **Get**, **put** e  **options**.

4. Per **Intestazioni consentite** immettere un asterisco (*).

5. Per le **intestazioni esposte**, immettere un asterisco (*).

6. Per **Età massima** immettere 200.

   ![Iscrizione e accesso a arcose Labs](media/partner-arkose-labs/signup-signin-arkose.png)

7. Selezionare **Salva**.

### <a name="part-2--set-up-a-back-end-server"></a>Parte 2: configurare un server back-end

Scaricare git bash e seguire questa procedura:

1. Seguire le istruzioni per [creare un'app Web](../app-service/quickstart-php.md), fino al messaggio "complimenti!La prima app PHP viene distribuita nel servizio app.

2. Aprire la cartella locale e rinominare il file **index. php** in **Verify-token. php**.

3. Aprire il file Verify-token. php appena rinominato e:

   a. Sostituire il contenuto con il contenuto del file Verify-token. php trovato nel [repository GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Sostituire <private_key> alla riga 3 con la chiave privata ottenuta dal dashboard di arcose Labs.

4. Nella finestra del terminale locale eseguire il commit delle modifiche in git e quindi effettuare il push delle modifiche al codice in Azure digitando quanto segue in git bash:

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>Parte 3-installazione finale

#### <a name="store-the-custom-html"></a>Archiviare il codice HTML personalizzato

1. Aprire il file index.html archiviato nel [repository GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Sostituire tutte le istanze di `<tenantname>` con il nome del tenant b2C (in altre parole, `<tenantname>.b2clogin.com` ). Devono essere presenti quattro istanze.

3. Sostituire `<appname>` con il nome dell'app creato nella parte 2, passaggio 1.

   ![Screenshot dell'interfaccia della riga di comando di Azure per arcose Labs](media/partner-arkose-labs/arkose-azure-cli.png)

4. Sostituire alla `<public_key>` riga 64 con la chiave pubblica ottenuta dal dashboard di arcose Labs.

5. Caricare il file index.html nell'archivio BLOB creato in precedenza.

6. Passare al **Storage**  >  caricamento del **contenitore** di archiviazione  >  **Upload**.

#### <a name="set-up-azure-ad-b2c"></a>Configurare Azure AD B2C

> [!NOTE]
> In assenza di un tenant, [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

1. Creare un flusso utente in base alle informazioni riportate [qui](tutorial-create-user-flows.md). Arrestare quando si raggiunge la sezione **testare il flusso utente**.

2. Abilitare JavaScript nel [flusso utente](user-flow-javascript-overview.md).

3. Nella stessa pagina flusso utente, Abilita URL pagina personalizzata: Vai al layout della pagina **flusso utente** usa il contenuto della pagina  >  **page layout**  >  **personalizzata**  =  **Sì**  >  **Inserisci URL pagina personalizzata**.
Questo URL di pagina personalizzata viene ottenuto dal percorso del file index.html all'interno dell'archivio BLOB  

   ![Screenshot che mostra l'URL di archiviazione di arcose Labs](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in **criteri** selezionare **flussi utente**.

2. Selezionare il flusso utente creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione** : selezionare l'app registrata (l'esempio è JWT).

   b. **URL di risposta** : selezionare l'URL di reindirizzamento.

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account.

5. Uscire,

6. Esaminare il flusso di accesso.

7. Dopo aver selezionato **continua**, verrà visualizzato un puzzle di arcose Labs.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](custom-policy-get-started.md?tabs=applications)