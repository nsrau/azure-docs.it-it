---
title: Esercitazione per la configurazione di Azure Active Directory B2C con Nevis
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con Nevis per l'autenticazione senza password
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 470dc146d1658b97a707be8cb7ad868d943a8e60
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170898"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Esercitazione per configurare Nevis con Azure Active Directory B2C per l'autenticazione senza password

In questa esercitazione di esempio viene illustrato come estendere Azure AD B2C con  [Nevis](https://www.nevis.net/solution/authentication-cloud) per abilitare l'autenticazione senza password. Nevis offre un'esperienza utente finale per dispositivi mobili e completamente personalizzata con l'app di accesso Nevis per offrire un'autenticazione avanzata dei clienti e conforme ai requisiti delle transazioni PSD2 (Payment Services Directive 2).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Un [account di valutazione](https://www.nevis-security.com/aadb2c/) Nevis

- Una sottoscrizione di Azure AD. Se non si ha un account, ottenere un [account gratuito](https://azure.microsoft.com/free/).

- Un [tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

- Configurato Azure AD B2C ambiente per l'uso di [criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started), se si vuole integrare Nevis nel flusso dei criteri di iscrizione.

## <a name="scenario-description"></a>Descrizione dello scenario

In questo scenario, aggiungere un'app di Access completamente personalizzata all'applicazione back-end per l'autenticazione senza password. I componenti seguenti costituiscono la soluzione:

- Un tenant di Azure AD B2C con un criterio di accesso e iscrizione combinato al back-end
- Istanza di Nevis e relativa API REST per migliorare Azure AD B2C
- App con personalizzazione

Nel diagramma viene illustrata l'implementazione di.

![Flusso di accesso con password di alto livello con Azure AD B2C e Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | Un utente tenta di accedere o di accedere a un'applicazione tramite Azure AD B2C i criteri di accesso e di iscrizione.
| 2. | Durante l'iscrizione, l'app di accesso Nevis viene registrata nel dispositivo utente usando un codice a matrice. Nel dispositivo utente viene generata una chiave privata che viene usata per firmare le richieste dell'utente.
| 3. |  Azure AD B2C usa un profilo tecnico RESTful per avviare l'accesso con la soluzione Nevis.
| 4. | La richiesta di accesso viene inviata all'app Access, come messaggio push, codice a matrice o come collegamento profondo.
| 5. | L'utente approva il tentativo di accesso con la biometria. Un messaggio viene quindi restituito a Nevis, che verifica l'accesso con la chiave pubblica archiviata.
| 6. | Azure AD B2C Invia un'ultima richiesta a Nevis per verificare che l'account di accesso sia stato completato correttamente.
| 7. |In base al messaggio di esito positivo o negativo di Azure AD B2C utente viene concesso o negato l'accesso all'applicazione.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrare il tenant di Azure AD B2C

### <a name="onboard-to-nevis"></a>Onboarding in Nevis 

[Iscriversi per un account Nevis](https://www.nevis-security.com/aadb2c/).
Si riceveranno due messaggi di posta elettronica:

1. Notifica di un account di gestione

2. Invito A un'app per dispositivi mobili.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Aggiungere il tenant di Azure AD B2C al proprio account Nevis

1. Dalla versione di valutazione dell'account di gestione Nevis, copiare la chiave di gestione negli Appunti.

2. Apri https://console.nevis.cloud/ in un browser.

3. Accedere alla console di gestione con la chiave.

4. Selezionare **Aggiungi istanza**

5. Selezionare l'istanza appena creata per aprirla.

6. Nella barra di spostamento laterale selezionare **integrazioni personalizzate** .

7. Selezionare **Aggiungi integrazione personalizzata**.

8. Per nome integrazione immettere il nome del tenant Azure AD B2C.

9. Per URL/dominio immettere `https://yourtenant.onmicrosoft.com`

10. Selezionare **Avanti**

>[!NOTE]
>Il token di accesso Nevis sarà necessario in un secondo momento.

11. Selezionare **Fine**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Installare l'app di accesso Nevis sul telefono

1. Dalla versione di valutazione dell'app per dispositivi mobili di Nevis aprire l'invito all' **app di test Flight** .

2. Installare l'app.

3. Seguire le istruzioni fornite per installare l'app di Access Nevis.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integrare Azure AD B2C con Nevis

1. Aprire il [portale di Azure](https://portal.azure.com/).

2. Passa al tenant di Azure AD B2C. Assicurarsi di aver selezionato il tenant appropriato, perché il tenant Azure AD B2C in genere si trova in un tenant separato.

3. Nel menu selezionare Framework dell' **esperienza di identità (Framework dell'esperienza)**

4. Selezionare le **chiavi dei criteri**

5. Selezionare **Aggiungi** e creare una nuova chiave con le impostazioni seguenti:

      a. Selezionare le opzioni **manuali** in

      b. Impostare nome su **AuthCloudAccessToken**

      c. Incollare il token di **accesso Nevis** archiviato in precedenza nel campo Secret

      d. Per l'utilizzo della chiave selezionare **crittografia**

      e. Selezionare **Crea**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Configurare e caricare il nevis.html nell'archivio BLOB di Azure

1. Nell'ambiente di identità (IDE) passare alla cartella dei [**criteri**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Aprire il file  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) .

3. Sostituire il  **authentication_cloud_url** con l'URL della console di amministrazione di Nevis- `https://<instance_id>.mauth.nevis.cloud` .

4. **Salvare** le modifiche apportate al file.

5. Seguire le [istruzioni](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#2-create-an-azure-blob-storage-account) e caricare il file **nevis.html** nell'archivio BLOB di Azure.

6. Seguire le [istruzioni](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors) e abilitare la condivisione di risorse tra le origini (CORS) per questo file.

7. Una volta completato il caricamento e CORS è abilitato, selezionare il file **nevis.html** nell'elenco.

8. Nella scheda **Panoramica** , accanto all' **URL**, selezionare l'icona **copia collegamento** .

9. Aprire il collegamento in una nuova scheda del browser per assicurarsi che venga visualizzata una casella grigia.

>[!NOTE]
>Il collegamento al BLOB sarà necessario in un secondo momento.

### <a name="customize-your-trustframeworkbasexml"></a>Personalizzare la TrustFrameworkBase.xml

1. Nell'IDE passare alla cartella dei [**criteri**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Aprire il file di [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) .

3. Sostituire **tenantinuso** con il nome dell'account del tenant di Azure in **TenantId**.

4. Sostituire **tenantinuso** con il nome dell'account tenant di Azure in **PublicPolicyURI**.

5. Sostituire tutte le istanze di **authentication_cloud_url** con l'URL della console di amministrazione di Nevis

6. **Salvare** le modifiche apportate al file.

### <a name="customize-your-trustframeworkextensionsxml"></a>Personalizzare la TrustFrameworkExtensions.xml

1. Nell'IDE passare alla cartella dei [**criteri**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Aprire il file di [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) .

3. Sostituire **tenantinuso** con il nome dell'account del tenant di Azure in **TenantId**.

4. Sostituire **tenantinuso** con il nome dell'account tenant di Azure in **PublicPolicyURI**.

5. In **BasePolicy**, in **TenantId** sostituire _tenantinuso_ con il nome dell'account del tenant di Azure.

6. In **BuildingBlocks** sostituire **elemento LOADURI** con l'URL del collegamento BLOB della _nevis.html_ nell'account di archiviazione BLOB.

7. **Salvare** il file.

### <a name="customize-your-signuporsigninxml"></a>Personalizzare la SignUpOrSignin.xml

1. Nell'IDE passare alla cartella dei [**criteri**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Aprire il file di [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) .

3. Sostituire **tenantinuso** con il nome dell'account del tenant di Azure in **TenantId**.

4. Sostituire **tenantinuso** con il nome dell'account tenant di Azure in **PublicPolicyUri**.

5. In **BasePolicy**, in **TenantId**, sostituire anche **tenantinuso** con il nome dell'account tenant di Azure.

6. **Salvare** il file.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Caricare i criteri personalizzati in Azure AD B2C

1. Aprire la Home page del [tenant Azure ad B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Selezionare **Carica criteri personalizzati**.

4. Selezionare il file **TrustFrameworkBase.xml** modificato.

5. Selezionare la casella di controllo **Sovrascrivi il criterio personalizzato se esiste già** .
6. Selezionare **Carica**.

7. Ripetere i passaggi 5 e 6 per **TrustFrameworkExtensions.xml**.

8. Ripetere i passaggi 5 e 6 per **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Creazione dell'account di test e configurazione dell'app Access Nevis

1. Aprire la Home page del [tenant Azure ad B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Scorrere verso il basso fino a criteri personalizzati e selezionare **B2C_1A_signup_signin**.

4. Selezionare **Esegui adesso**.

5. Nella finestra popup fare clic **su Iscriviti ora**.

6. Aggiungere l'indirizzo di posta elettronica.

7. Selezionare **Invia codice di verifica**.

8. Copiare il codice di verifica dal messaggio di posta elettronica.

9. Selezionare **Verifica**.

10. Compilare il modulo con la nuova password e il nome visualizzato.

11. Selezionare **Crea**.

12. Si passerà alla pagina di analisi del codice a matrice.

13. Sul telefono aprire l'app di **Access Nevis**.

14. Selezionare **ID Face**.

15. Quando la schermata indica che la **registrazione dell'autenticatore è stata completata**, selezionare **continua**.

16. Sul telefono, eseguire di nuovo l'autenticazione con la faccia.

17. Si passerà alla pagina di destinazione [JWT.ms](https://jwt.ms) che Visualizza i dettagli del token decodificato.

### <a name="test-the-pure-passwordless-sign-in"></a>Testare l'accesso senza password puro

1. In **Framework dell'esperienza di identità** selezionare il **B2C_1A_signup_signin**.

2. Selezionare **Esegui adesso**.

3. Nella finestra popup selezionare autenticazione senza **password**.

4. Immettere l'indirizzo di posta elettronica.

5. Selezionare **Continua**.

6. Nel telefono, in notifiche, selezionare **Nevis Access notifica app**.

7. Eseguire l'autenticazione con la propria faccia.

8. Verrà automaticamente visualizzata la pagina di destinazione di [JWT.ms](https://jwt.ms) che Visualizza i token.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti.

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
