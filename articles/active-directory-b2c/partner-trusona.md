---
title: Trusona e Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Informazioni su come aggiungere Trusona come provider di identità in Azure AD B2C per abilitare l'autenticazione con password.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc0bcd4a978912dccc9f08802acbf2ec1151b3a1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170106"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integrazione di Trusona con Azure Active Directory B2C

Trusona è un provider di software indipendente (ISV, Independent Software Vendor) che consente di proteggere l'accesso abilitando l'autenticazione senza password, l'autenticazione a più fattori e l'analisi delle licenze digitali. In questo articolo si apprenderà come aggiungere Trusona come provider di identità in Azure AD B2C per abilitare l'autenticazione con password.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* [Un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
* Un [account di valutazione](https://www.trusona.com/aadb2c) in Trusona

## <a name="scenario-description"></a>Descrizione dello scenario

In questo scenario, Trusona funge da provider di identità per Azure AD B2C per abilitare l'autenticazione con password. I componenti seguenti costituiscono la soluzione:

* Un Azure AD B2C criteri di accesso e iscrizione combinati
* Trusona aggiunto al Azure AD B2C come provider di identità
* App Trusona scaricabile

![Diagramma dell'architettura di Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Passaggio | Descrizione |
|------|------|
|1     | Un utente tenta di accedere o di iscriversi con l'applicazione. L'utente viene autenticato tramite i criteri di iscrizione e accesso Azure AD B2C. Durante l'iscrizione, viene usato l'indirizzo di posta elettronica verificato in precedenza dall'utente dall'app Trusona.     |
|2     | Azure B2C reindirizza l'utente al provider di identità Trusona OpenID Connect (OIDC) usando il flusso implicito.     |
|3     | Per gli account di accesso basati su PC desktop, Trusona Visualizza un codice a matrice univoco, senza stato, animato e dinamico per l'analisi con l'app Trusona. Per gli account di accesso basati su dispositivi mobili, Trusona usa un "Deep link" per aprire l'app Trusona. Questi due metodi vengono usati per il dispositivo e infine per l'individuazione degli utenti.     |
|4     | L'utente analizza il codice QR visualizzato con l'app Trusona.     |
|5     | L'account dell'utente si trova nel servizio cloud Trusona e l'autenticazione è preparata.     |
|6     | Il servizio cloud Trusona invia una richiesta di autenticazione all'utente tramite una notifica push inviata all'app Trusona:<br>a. All'utente viene richiesto di eseguire la richiesta di autenticazione. <br> b. L'utente sceglie di accettare o rifiutare la richiesta. <br> c. All'utente viene richiesto di usare la sicurezza del sistema operativo (ad esempio, biometrico, di codice, PIN o modello) per confermare e firmare la richiesta con una chiave privata nell'enclave protetta o nell'ambiente di esecuzione attendibile. <br> d. L'app Trusona genera un payload di anti-riproduzione dinamico in base ai parametri dell'autenticazione in tempo reale. <br> e. L'intera risposta viene firmata (per la seconda volta) da una chiave privata nell'ambiente di esecuzione attendibile/enclave sicuro e restituita al servizio cloud Trusona per la verifica.      |
|7     |  Il servizio cloud Trusona reindirizza l'utente all'applicazione di avvio con un id_token. Azure AD B2C verifica la id_token usando la configurazione OpenID pubblicata di Trusona configurata durante la configurazione del provider di identità.    |
|  |  |

## <a name="onboard-with-trusona"></a>Onboarding con Trusona

1. Compilare il [modulo](https://www.trusona.com/aadb2c) per creare un account Trusona e iniziare.

2. Scaricare l'app per dispositivi mobili Trusona dall'App Store. Installare l'app e registrare la posta elettronica.

3. Verificare la posta elettronica tramite il "collegamento magico" protetto inviato dal software.  

4. Passare al [Dashboard per sviluppatori di Trusona](https://dashboard.trusona.com) per la modalità self-service.

5. Selezionare si è **pronti** e si esegue l'autenticazione con l'app Trusona.

6. Dal riquadro di spostamento a sinistra scegliere **OIDC Integrations (integrazioni**).

7. Selezionare **Crea integrazione OpenID Connect**.

8. Specificare il **nome** desiderato e utilizzare le informazioni di dominio fornite in precedenza (ad esempio, contoso) nel **campo host di reindirizzamento client**.  

   > [!NOTE]
   > Il nome di dominio iniziale del Azure Active Directory viene usato come host di reindirizzamento del client.

9. Seguire le istruzioni riportate nella [Guida all'integrazione di Trusona](https://docs.trusona.com/integrations/aad-b2c-integration/). Quando richiesto, usare il nome di dominio iniziale, ad esempio contoso, a cui si fa riferimento nel passaggio precedente.  

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Aggiungere un nuovo provider di identità

> [!NOTE]
> In assenza di un tenant, [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.

4. Passare a **Dashboard**  >  **Azure Active Directory B2C**  >  **provider di identità**.

3. Selezionare **provider di identità**.

4. Selezionare **Aggiungi**.

### <a name="configure-an-identity-provider"></a>Configurare un provider di identità  

1. Selezionare il **tipo di provider di identità**  >  **OpenID Connect (anteprima)**.

2. Compilare il modulo per configurare il provider di identità:  

   | Proprietà | Valore  |
   | :--- | :--- |
   | URL dei metadati | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | ID client | Verrà inviato tramite posta elettronica all'utente da Trusona |
   | Ambito | Posta elettronica profilo OpenID |
   | Tipo di risposta | Id_token |
   | Modalità di risposta  | Form_post |

3. Selezionare **OK**.  

4. Selezionare Esegui **mapping delle attestazioni del provider di identità**.  

5. Compilare il modulo per eseguire il mapping del provider di identità:

   | Proprietà | Valore  |
   | :--- | :--- |
   | UserID | Sub  |
   | Nome visualizzato | nickname |
   | Nome specificato | given_name |
   | Surname | Family_name |
   | Modalità di risposta | email |

6. Selezionare **OK** per completare la configurazione del nuovo provider di identità OIDC.

### <a name="create-a-user-flow-policy"></a>Creare un criterio di flusso utente

1. A questo punto dovrebbe essere visualizzato Trusona come **nuovo provider di identità OpenID Connect** elencato nei provider di identità B2C.

2. Selezionare **flussi utente (criteri)** dal pannello di navigazione sinistro.

3. Selezionare **Aggiungi**  >  **nuovo flusso utente**  >  **e accedi**.

### <a name="configure-the-policy"></a>Configurare i criteri

1. Assegnare un nome ai criteri.

2. Selezionare il provider di **identità Trusona**appena creato.

3. Poiché Trusona è intrinsecamente a più fattori, è preferibile lasciare disabilitata l'autenticazione a più fattori.

4. Selezionare **Crea**.

5. In **attributi utente e attestazioni**scegliere **Mostra altro**. Nel modulo selezionare almeno un attributo specificato durante l'installazione del provider di identità nella sezione precedente.

6. Selezionare **OK**.  

### <a name="test-the-policy"></a>Testare i criteri

1. Selezionare i criteri appena creati.

2. Selezionare **Esegui il flusso utente**.

3. Nel modulo immettere l'URL di risposta.

4. Selezionare **Esegui il flusso utente**. Si dovrebbe essere reindirizzati al gateway OIDC di Trusona. Sul gateway Trusona, eseguire la scansione del codice QR protetto visualizzato con l'app Trusona o con un'app personalizzata usando Trusona Mobile SDK.

5. Dopo aver analizzato il codice QR sicuro, è necessario essere reindirizzati all'URL di risposta definito nel passaggio 3.

## <a name="next-steps"></a>Passaggi successivi  

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in AAD B2C](custom-policy-get-started.md?tabs=applications)
