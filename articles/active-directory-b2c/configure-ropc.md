---
title: Configurare il flusso delle credenziali password del proprietario della risorsa in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare il flusso delle credenziali password del proprietario della risorsa in Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f6f9b9c7ae71697efb6d722eff55d9ee3f8746d5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712301"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Configurare il flusso delle credenziali password del proprietario della risorsa in Azure AD B2C

Il flusso delle credenziali password del proprietario della risorsa (ROPC) è un flusso di autenticazione OAUTH standard in cui l'applicazione, nota anche come relying party, scambia credenziali valide, ad esempio ID utente e password, con un token ID, un token di accesso e un token di aggiornamento. 

> [!NOTE]
> Questa funzionalità è in anteprima.

Azure AD B2C supporta le opzioni seguenti:

- **Client nativo**  - L'interazione utente durante l'autenticazione avviene mediante codice in esecuzione in un dispositivo lato utente. Può trattarsi di un'applicazione per dispositivi mobili in esecuzione nel sistema operativo nativo, ad esempio Android, o in esecuzione nel browser, ad esempio Javascript.
- **Flusso client pubblico** - Nella chiamata API vengono inviate solo le credenziali utente, raccolte da un'applicazione. Le credenziali dell'applicazione non vengono inviate.
- **Aggiunta di nuove attestazioni** - Il contenuto del token ID può essere modificato per aggiungere nuove attestazioni. 

Questi flussi non sono supportati:

- **Da server a server** - Il sistema di protezione delle identità (IDPS) richiede un indirizzo IP affidabile raccolto dal chiamante (il client nativo) come parte dell'interazione.  In una chiamata API lato server viene usato solo l'indirizzo IP del server e, in caso di superamento di una soglia dinamica di autenticazioni non riuscite, il sistema di protezione delle identità può identificare un indirizzo IP ripetuto come un utente malintenzionato.
- **Flusso client riservato** - L'ID client dell'applicazione viene convalidato, ma non viene convalidato il segreto dell'applicazione.

##  <a name="create-a-resource-owner-policy"></a>Creare un criterio per il proprietario della risorsa

1. Accedere al portale di Azure come amministratore globale del tenant di Azure AD B2C.
2. Per passare al tenant di Azure AD B2C, selezionare la directory B2C nell'angolo superiore destro del portale.
3. In **Criteri** selezionare **Criteri per il proprietario della risorsa**.
4. Specificare un nome per il criterio, ad esempio *ROPC_Auth*, quindi fare clic su **Attestazioni dell'applicazione**.
5. Selezionare le attestazioni necessarie per l'applicazione, ad esempio *Nome visualizzato*, *Indirizzo di posta elettronica* e *Provider di identità*.
6. Fare clic su **OK** e quindi su **Crea**.

Verrà visualizzato un endpoint come nell'esempio seguente:

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registrare un'applicazione

1. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **+ Aggiungi**.
2. Specificare un nome per l'applicazione, ad esempio *ROPC_Auth_app*.
3. Fare clic su **No** per **App Web/API Web** e fare clic su **Sì** per **Client nativo**.
4. Lasciare tutti gli altri valori così come sono e fare clic su **Crea**.
5. Selezionare la nuova applicazione e prendere nota dell'ID applicazione.

## <a name="test-the-policy"></a>Testare i criteri

Usare l'applicazione di sviluppo API preferita per generare una chiamata API ed esaminare la risposta per eseguire il debug del criterio. Costruire una chiamata di questo tipo con le informazioni riportate nella tabella seguente come corpo della richiesta POST:
- Sostituire *yourtenant.onmicrosoft.com* con il nome del proprio tenant B2C
- Sostituire *B2C_1A_ROPC_Auth* con il nome completo del criterio ROPC
- Sostituire *bef2222d56-552f-4a5b-b90a-1988a7d634c3* con l'ID dell'applicazione dalla registrazione.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Chiave | Valore |
| --- | ----- |
| username | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| scope | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | token id_token |

*Client_id* è il valore annotato in precedenza come ID dell'applicazione. *Offline_access* è facoltativo se si vuole ricevere un token di aggiornamento. 

La richiesta POST effettiva è simile alla seguente:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Una risposta con esito positivo con l'accesso offline è simile alla seguente:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Riscattare un token di aggiornamento

Costruire una chiamata POST di questo tipo con le informazioni riportate nella tabella seguente come corpo della richiesta:

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Chiave | Valore |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* e *resource* sono i valori annotati in precedenza come ID dell'applicazione. *Refresh_token* è il token ricevuto nella chiamata di autenticazione indicata in precedenza.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementare con l'SDK nativo preferito o usare AppAuth


L'implementazione di Azure AD B2C soddisfa gli standard OAuth 2.0 o ROPC con flusso client pubblico e dovrebbe essere compatibile con la maggior parte degli SDK client.  Questo flusso è stato testato in modo approfondito, in produzione, con AppAuth per iOS e AppAuth per Android.  Per le informazioni più recenti, vedere https://appauth.io/.

È possibile scaricare esempi funzionanti, configurati per l'uso con Azure AD B2C da GitHub alle pagine https://aka.ms/aadb2cappauthropc per Android e https://aka.ms/aadb2ciosappauthropc.




