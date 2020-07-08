---
title: Come proteggere un'applicazione daemon
titleSuffix: Azure Maps
description: Usare il portale di Azure per gestire l'autenticazione per configurare un'applicazione daemon attendibile.
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4fa3492b0cd71e61900dc4be150cd0f0169379ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988476"
---
# <a name="secure-a-daemon-application"></a>Proteggere un'applicazione daemon

La guida seguente è relativa ai processi in background, ai timer e ai processi ospitati in un ambiente attendibile e protetto. Alcuni esempi sono i processi Web di Azure, le app per le funzioni di Azure, i servizi Windows e qualsiasi altro servizio in background affidabile.

> [!Tip]
> Microsoft consiglia di implementare Azure Active Directory (Azure AD) e il controllo degli accessi in base al ruolo (RBAC) per le applicazioni di produzione. Per una panoramica dei concetti, vedere [autenticazione di Azure Maps](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Scenario: autenticazione con chiave condivisa

Dopo aver creato un account Azure Maps, vengono generate le chiavi primarie e secondarie. Si consiglia di usare la chiave primaria come chiave di sottoscrizione quando si [Usa l'autenticazione con chiave condivisa per chiamare Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). È possibile usare una chiave secondaria in scenari quali le modifiche delle chiavi in sequenza. Per altre informazioni, vedere [autenticazione in mappe di Azure](https://aka.ms/amauth).

### <a name="securely-store-shared-key"></a>Archiviare in modo sicuro la chiave condivisa

La chiave primaria e la chiave secondaria consentono l'autorizzazione a tutte le API per l'account Maps. Le applicazioni devono archiviare le chiavi in un archivio protetto, ad esempio Azure Key Vault. L'applicazione deve recuperare la chiave condivisa come segreto Azure Key Vault per evitare di archiviare la chiave condivisa in testo normale nella configurazione dell'applicazione. Per informazioni su come configurare una Azure Key Vault, vedere [Guida](https://docs.microsoft.com/azure/key-vault/general/developers-guide)per gli sviluppatori di Azure Key Vault.

I passaggi seguenti illustrano questo processo:

1. Creare un insieme di credenziali delle chiavi di Azure.
2. Creare un'entità servizio Azure AD creando una registrazione dell'app o un'identità gestita, l'entità creata è responsabile dell'accesso a Azure Key Vault.
3. Assegnare l'accesso dell'entità servizio all'autorizzazione Azure Key Secrets `Get` .
4. Assegnare temporaneamente l'autorizzazione di accesso ai segreti `Set` come sviluppatore.
5. Impostare la chiave condivisa nel Key Vault Secrets e fare riferimento all'ID segreto come configurazione per l'applicazione daemon e rimuovere l'autorizzazione per i segreti `Set` .
6. Implementare l'autenticazione Azure AD nell'applicazione daemon per recuperare il segreto della chiave condivisa da Azure Key Vault.
7. Creare una richiesta API REST di Azure Maps con la chiave condivisa.

> [!Tip]
> Se l'app è ospitata in un ambiente Azure, è necessario implementare un'identità gestita per ridurre i costi e la complessità della gestione di un segreto per l'autenticazione Azure Key Vault. [Per la connessione tramite identità gestita](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app), vedere l'esercitazione seguente Azure Key Vault.

L'applicazione daemon è responsabile del recupero della chiave condivisa da un archivio protetto. Per l'implementazione con Azure Key Vault è necessaria l'autenticazione tramite Azure AD per accedere al segreto. Al contrario, viene incoraggiata l'autenticazione Azure AD diretta del controllo degli accessi in base al ruolo per le mappe di Azure, a causa dei requisiti di complessità e operativi aggiuntivi per l'uso dell'autenticazione

> [!IMPORTANT]
> Per semplificare la rigenerazione delle chiavi, è consigliabile usare una chiave alla volta per le applicazioni. Le applicazioni possono quindi rigenerare la chiave inutilizzata e distribuire la nuova chiave rigenerata in un archivio segreto protetto, ad esempio Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Scenario: Azure AD il controllo degli accessi in base al ruolo

Una volta creato un account Azure Maps, il valore Azure Maps `x-ms-client-id` è presente nella pagina dei dettagli di autenticazione portale di Azure. Questo valore rappresenta l'account che verrà usato per le richieste dell'API REST. Questo valore deve essere archiviato nella configurazione dell'applicazione e recuperato prima di effettuare richieste HTTP. L'obiettivo dello scenario è consentire all'applicazione daemon di eseguire l'autenticazione a Azure AD e chiamare le API REST di Azure maps.

> [!Tip]
> È consigliabile ospitare in macchine virtuali di Azure, set di scalabilità di macchine virtuali o servizi app per abilitare i vantaggi dei componenti di identità gestiti.

### <a name="daemon-hosted-on-azure-resources"></a>Daemon ospitato nelle risorse di Azure

Quando si eseguono le risorse di Azure, configurare le identità gestite di Azure per abilitare il costo ridotto e la gestione delle credenziali minime. 

Vedere [Panoramica delle identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per consentire all'applicazione di accedere a un'identità gestita.

Vantaggi dell'identità gestita:

* Autenticazione della crittografia a chiave pubblica del certificato X509 gestito dal sistema di Azure.
* Azure AD la sicurezza con i certificati X509 invece dei segreti client.
* Azure gestisce e rinnova tutti i certificati associati alla risorsa di identità gestita.
* Gestione delle credenziali semplificata rimuovendo la necessità di un servizio di archiviazione segreta protetto come Azure Key Vault. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Daemon ospitato su risorse non di Azure

Quando è in esecuzione in un ambiente non Azure, le identità gestite non sono disponibili. È pertanto necessario configurare un'entità servizio tramite una registrazione dell'applicazione Azure AD per l'applicazione daemon.

1. Nell'elenco dei servizi di Azure portale di Azure selezionare **Azure Active Directory**  >  **registrazioni app**  >  **nuova registrazione**.  

    > [!div class="mx-imgBorder"]
    > ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

2. Se l'app è già stata registrata, continuare con il passaggio successivo. Se l'app non è stata registrata, immettere un **nome**, scegliere un **tipo di account di supporto**e quindi selezionare **registra**.  

    > [!div class="mx-imgBorder"]
    > ![Dettagli registrazione app](./media/how-to-manage-authentication/app-create.png)

3. Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione. In **registrazioni app**selezionare **autorizzazioni API**  >  **Aggiungi un'autorizzazione**. In **API l'organizzazione USA**, cercare e selezionare **mappe di Azure**.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

4. Selezionare la casella di controllo accanto a **Accedi a mappe di Azure**e quindi selezionare **Aggiungi autorizzazioni**.

    > [!div class="mx-imgBorder"]
    > ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

5. Completare i passaggi seguenti per creare un segreto client o configurare il certificato.

    * Se l'applicazione usa l'autenticazione del server o dell'applicazione, nella pagina di registrazione dell'app passare a **certificati & segreti**. Caricare quindi un certificato di chiave pubblica o creare una password selezionando **nuovo segreto client**.

        > [!div class="mx-imgBorder"]
        > ![Creare un segreto client](./media/how-to-manage-authentication/app-keys.png)

    * Dopo aver selezionato **Aggiungi**, copiare il segreto e archiviarlo in modo sicuro in un servizio, ad esempio Azure Key Vault. Esaminare [Azure Key Vault guida](https://docs.microsoft.com/azure/key-vault/general/developers-guide) per gli sviluppatori per archiviare in modo sicuro il certificato o il segreto. Questo segreto verrà usato per ottenere i token da Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Aggiungere un segreto client](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Concedere l'accesso in base al ruolo per l'applicazione daemon ad Azure Maps

Il *controllo degli accessi in base al ruolo* viene concesso assegnando l'identità gestita creata o l'entità servizio a una o più definizioni di ruolo di controllo di accesso di Azure maps. Per visualizzare le definizioni di ruolo RBAC disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)**. Selezionare **Roles (ruoli**) e quindi cercare i ruoli che iniziano con *Maps di Azure*. Questi ruoli di Azure Maps sono i ruoli a cui è possibile concedere l'accesso.

> [!div class="mx-imgBorder"]
> ![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Passare all' **account Azure Maps**. Selezionare il **controllo di accesso (IAM)**  >  **assegnazioni di ruolo**.

    > [!div class="mx-imgBorder"]
    > ![Concedere il controllo degli accessi in base al ruolo](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella scheda **assegnazioni di ruolo** **aggiungere** un'assegnazione di ruolo. 
    
    > [!div class="mx-imgBorder"]
    > ![Aggiungere un'assegnazione di ruolo](./media/how-to-manage-authentication/add-role-assignment.png)

3. Selezionare una definizione di ruolo di Azure Maps predefinita, ad esempio **lettore dati** di Azure Maps o **collaboratore dati di Azure Maps**. In **assegna accesso a**selezionare **Azure ad utente, un gruppo o un'entità servizio** o un'identità gestita con identità gestita assegnata dal sistema di identità gestito assegnato **dall'utente**  /  **System assigned Managed identity**. Selezionare l'entità. Selezionare quindi **Salva**.

    > [!div class="mx-imgBorder"]
    > ![Aggiungere un'assegnazione di ruolo](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. È possibile verificare che l'assegnazione di ruolo sia stata applicata alla scheda assegnazione ruolo.

## <a name="request-token-with-managed-identity"></a>Token della richiesta con identità gestita

Una volta configurata un'identità gestita per la risorsa di hosting, usare Azure SDK o l'API REST per acquisire un token per le mappe di Azure, vedere informazioni dettagliate su come [acquisire un token di accesso](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token). Seguendo la guida, si prevede che verrà restituito un token di accesso che può essere usato nelle richieste API REST.

## <a name="request-token-with-application-registration"></a>Token della richiesta con registrazione dell'applicazione

Dopo aver registrato l'app e averla associata a Maps di Azure, è possibile richiedere i token di accesso.

* ID risorsa Azure AD`https://atlas.microsoft.com/`
* Azure AD App ID (ID App Azure AD)
* Azure AD Tenant ID (ID del tenant di Azure AD)
* Segreto client di registrazione App Azure AD

Richiesta:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Risposta:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Per esempi più dettagliati, vedere [scenari di autenticazione per Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="next-steps"></a>Passaggi successivi

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Scopri gli esempi che illustrano come integrare Azure AD con mappe di Azure:
> [!div class="nextstepaction"]
> [Esempi di mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
