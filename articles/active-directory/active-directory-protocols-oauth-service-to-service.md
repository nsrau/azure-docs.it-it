<properties
    pageTitle="Autorizzazione di Azure AD da servizio a servizio con OAuth2.0 | Microsoft Azure"
    description="Questo articolo illustra come usare i messaggi HTTP per implementare l'autenticazione da servizio a servizio usando il flusso di concessione di credenziali client OAuth2.0."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="service-to-service-calls-using-client-credentials"></a>Chiamate da servizio a servizio mediante le credenziali client

Il flusso di concessione delle credenziali client OAuth 2.0 consente a un servizio Web, ovvero un *client riservato*, di usare le proprie credenziali per l'autenticazione durante la chiamata a un altro servizio Web, invece di rappresentare un utente. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web.

## <a name="client-credentials-grant-flow-diagram"></a>Diagramma del flusso di concessione delle credenziali client

Il diagramma seguente illustra il funzionamento del flusso di concessione delle credenziali client in Azure Active Directory (Azure AD).

![Flusso di concessione delle credenziali client di OAuth 2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. L'applicazione client esegue l'autenticazione in corrispondenza dell'endpoint di emissione del token di Azure AD e richiede un token di accesso.
2. L'endpoint di emissione del token di Azure AD emette il token di accesso.
3. Il token di accesso viene usato per l'autenticazione nella risorsa protetta.
4. I dati della risorsa protetta vengono restituiti all'applicazione Web.

## <a name="register-the-services-in-azure-ad"></a>Registrare i servizi in Azure AD

Registrare il servizio chiamante e il servizio ricevente in Azure Active Directory (Azure AD). Per istruzioni dettagliate, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Richiedere un token di accesso

Per richiedere un token di accesso, usare una richiesta HTTP POST per l'endpoint di Azure AD specifico del tenant.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Richiesta del token di accesso da servizio a servizio

Un token di accesso da servizio a servizio contiene i parametri seguenti.

| Parametro | | Descrizione |
|-----------|------|------------|
| response_type | Obbligatoria | Specifica il tipo di risposta richiesto. In un flusso di concessione delle credenziali client il valore deve essere **client_credentials**.|
| client_id | Obbligatoria | Specifica l'ID client di Azure AD del servizio Web chiamante. Per trovare l'ID client dell'applicazione chiamante, nel portale di gestione di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sull'applicazione e infine su **Configura**.|
| client_secret | Obbligatoria |  Immettere una chiave registrata per il servizio Web chiamante in Azure AD. Per creare una chiave, nel portale di gestione di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sull'applicazione e infine su **Configura**. |
| resource | Obbligatoria | Immettere l'URI ID app del servizio Web ricevente. Per trovare l'URI ID app, nel portale di gestione di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sull'applicazione e infine su **Configura**. |

## <a name="example"></a>Esempio

La richiesta HTTP POST seguente richiede un token di accesso per il servizio Web https://service.contoso.com/. `client_id` identifica il servizio Web che richiede il token di accesso.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Risposta del token di accesso da servizio a servizio

Una risposta corretta contiene una risposta OAuth 2.0 JSON con i parametri seguenti.

| Parametro   | Descrizione |
|-------------|-------------|
|access_token |Token di accesso richiesto. Il servizio Web chiamante può usare questo token per l'autenticazione nel servizio Web ricevente. |
|access_type  | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è **Bearer**. Per altre informazioni sui token di connessione, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)(Framework di autorizzazione di OAuth2.0: uso dei token di connessione - RFC 6750).
|expires_in   | Validità del token di accesso (espressa in secondi).|
|expires_on   |Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache. |
|resource     | URI ID app del servizio Web ricevente. |

## <a name="example"></a>Esempio

L'esempio seguente mostra una risposta corretta a una richiesta di token di accesso per un servizio Web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Vedere anche

* [OAuth 2.0 in Azure AD](active-directory-protocols-oauth-code.md)



<!--HONumber=Oct16_HO2-->


