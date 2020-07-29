---
title: Esercitazione per la configurazione di Azure Active Directory B2C con LexisNexis
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con LexisNexis che è un servizio di convalida dell'identità e di profilatura e viene usato per verificare l'identificazione degli utenti e fornire valutazioni complete dei rischi in base al dispositivo dell'utente.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6f5b6bfdb523a22fc4dd9593bfec556da7493aa9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371193"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di LexisNexis con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare Azure AD B2C con [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). LexisNexis offre un'ampia gamma di soluzioni, che è possibile trovare [qui](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). In questa esercitazione di esempio verrà illustrata la soluzione **ThreatMetrix** di LexisNexis. ThreatMetrix è un servizio di convalida dell'identità e di profilatura. Viene usato per verificare l'identificazione degli utenti e fornire valutazioni complete dei rischi in base al dispositivo dell'utente.

Questa integrazione esegue la profilatura in base a alcune informazioni utente, fornite dall'utente durante il flusso di iscrizione. ThreatMetrix determina se l'utente deve essere autorizzato a continuare ad accedere o meno. Gli attributi seguenti vengono presi in considerazione nell'analisi dei rischi di ThreatMetrix:

- Posta elettronica
- Numero di telefono
- Informazioni di profilatura raccolte dal computer dell'utente

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di ThreatMetrix include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente, noto anche come provider di identità

- ThreatMetrix: il servizio ThreatMetrix accetta gli input forniti dall'utente e li combina con le informazioni di profilatura raccolte dal computer dell'utente per verificare la sicurezza dell'interazione dell'utente.

- API REST personalizzata: questa API implementa l'integrazione tra Azure AD B2C e il servizio ThreatMetrix.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![screenshot per LexisNexis-Architecture-diagram](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Passaggio | Descrizione |
|:--------------|:-------------|
|1. | L'utente arriva a una pagina di accesso. L'utente seleziona Iscriviti per creare un nuovo account e immettere le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente.
| 3. | L'API del livello intermedio raccoglie gli attributi utente e li trasforma in un formato utilizzabile dall'API LexisNexis. Quindi lo invia a LexisNexis.  
| 4. | LexisNexis utilizza le informazioni e le elabora per convalidare l'identificazione degli utenti in base all'analisi dei rischi. Restituisce quindi il risultato all'API del livello intermedio.
| 5. | L'API del livello intermedio elabora le informazioni e invia le informazioni rilevanti a Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una risposta di errore, viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con esito positivo, l'utente viene autenticato e concesso l'accesso.

## <a name="onboard-with-lexisnexis"></a>Onboarding con LexisNexis

1. Per creare un account LexisNexis, contattare [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Creare un criterio LexisNexis che soddisfi i requisiti. Usare la documentazione disponibile [qui](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd).

>[!NOTE]
> Il nome del criterio verrà usato in un secondo momento.

Una volta creato un account, si riceveranno le informazioni necessarie per la configurazione dell'API. Le sezioni seguenti descrivono il processo.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Configurare Azure AD B2C con LexisNexis

### <a name="part-1---deploy-the-api"></a>Parte 1: distribuire l'API

Distribuire il codice API fornito a un servizio di Azure. Il codice può essere pubblicato da Visual Studio, seguendo queste [istruzioni](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Per configurare Azure AD con le impostazioni necessarie, è necessario l'URL del servizio distribuito.

### <a name="part-2---configure-the-api"></a>Parte 2: configurare l'API

Le impostazioni dell'applicazione possono essere [configurate nel servizio app in Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings).  Con questo metodo, le impostazioni possono essere configurate in modo sicuro senza archiviarle in un repository. È necessario specificare le impostazioni seguenti per l'API REST:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | Configurazione dell'account ThreatMetrix |     |
|ThreatMetrix: OrgId | Configurazione dell'account ThreatMetrix |     |
|ThreatMetrix: ApiKey |Configurazione dell'account ThreatMetrix|  |
|ThreatMetrix: criteri | Nome dei criteri creati in ThreatMetrix | |
| BasicAuth: ApiUsername |Definire un nome utente per l'API| Il nome utente verrà usato nella configurazione del Azure AD B2C
| BasicAuth: ApiPassword | Definire una password per l'API | La password verrà usata nella configurazione del Azure AD B2C

### <a name="part-3---deploy-the-ui"></a>Parte 3: distribuire l'interfaccia utente

Questa soluzione USA modelli di interfaccia utente personalizzati caricati da Azure AD B2C. Questi modelli di interfaccia utente eseguono la profilatura inviata direttamente al servizio ThreatMetrix.

Fare riferimento a queste [istruzioni](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#custom-page-content-walkthrough) per distribuire i file dell'interfaccia utente inclusi in un account di archiviazione BLOB. Le istruzioni includono la configurazione di un account di archiviazione BLOB, la configurazione di CORS e l'abilitazione dell'accesso pubblico.

L'interfaccia utente è basata sul modello di pagina blu oceano. Tutti i collegamenti all'interno dell'interfaccia utente devono essere aggiornati in modo da fare riferimento al percorso distribuito. Nella cartella dell'interfaccia utente trovare e sostituire https://yourblobstorage/blobcontainer con il percorso distribuito.

### <a name="part-4---create-api-policy-keys"></a>Parte 4: creare chiavi dei criteri API

Fare riferimento a questo [documento](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) e creare due chiavi dei criteri, una per il nome utente dell'API e una per la password dell'API definita in precedenza.

Il criterio di esempio utilizza questi nomi di chiave:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Parte 5: aggiornare l'URL dell'API

Nel criterio TrustFrameworkExtensions specificato trovare il profilo tecnico denominato `Rest-LexisNexus-SessionQuery` e aggiornare l' `ServiceUrl` elemento dei metadati con il percorso dell'API distribuita in precedenza.

### <a name="part-6---update-ui-url"></a>Parte 6: aggiornare l'URL dell'interfaccia utente

Nei criteri TrustFrameworkExtensions specificati, eseguire una ricerca e sostituire da cercare https://yourblobstorage/blobcontainer/ con la posizione in cui vengono distribuiti i file dell'interfaccia utente.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Parte 7: configurare i criteri di Azure AD B2C

Fare riferimento a questo [documento](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) per scaricare gli [account locali Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) e configurare i criteri per il tenant Azure ad B2C.

>[!NOTE]
>Aggiornare i criteri specificati per la correlazione con il tenant specifico.

>[!NOTE]
> Come procedura consigliata, è consigliabile che i clienti aggiungano una notifica di consenso nella pagina della raccolta di attributi. Notificare agli utenti che le informazioni verranno inviate a servizi di terze parti per la verifica dell'identità.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **flussi utente**.

2. Selezionare il **flusso utente**creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Disconnetti

6. Esaminare il flusso di accesso  

7. Il puzzle ThreatMetrix verrà visualizzato dopo aver immesso **continua**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
