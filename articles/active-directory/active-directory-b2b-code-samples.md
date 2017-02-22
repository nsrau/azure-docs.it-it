---
title: Codici ed esempi di PowerShell per Collaborazione B2B in Azure Active Directory | Documentazione Microsoft
description: Codici ed esempi di PowerShell per Collaborazione B2B in Azure Active Directory
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 1287a44fcf450023d4544202bd5db51dc99768ab


---


# <a name="azure-active-directory-b2b-collaboration-code-and-powershell-samples"></a>Codici ed esempi di PowerShell per Collaborazione B2B in Azure Active Directory

## <a name="code-sample"></a>Esempio di codice
Questo esempio illustra come chiamare l'API di invito, in modalità "solo app", per ottenere l'URL di riscatto per la risorsa a cui si vuole invitare l'utente B2B. L'obiettivo consiste nell'inviare un messaggio di posta elettronica di invito personalizzato. Si osserverà come sia possibile comporre il messaggio di posta con un client HTTP, per poter personalizzarne l'aspetto e inviarlo tramite l'API Graph.

```
namespace SampleInviteApp
{
    using System.Globalization;
    using System;
    using System.Net;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.Graph;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    class Program
    {
        /// <summary>
        ///  Authentication endpoint to get token.
        /// </summary>
        static readonly string EstsLoginEndpoint = "https://login.microsoftonline.com";
        /// <summary>
        /// Initialize this to Tenant you want to invite users to.
        /// </summary>
        private static readonly string TenantID = "";
        /// <summary>
        /// Client id of the app.
        /// </summary>
        private static readonly string TestAppClientId = "";
        /// <summary>
        /// Client secret of the app.
        /// </summary>
        private static readonly string TestAppClientSecret = @"";
        /// <summary>
        /// Admin user principal name
        /// </summary>
        private static readonly string InviterUserPrincipalName = "";
        static void Main(string[] args)
        {
            CreateInvitation();
        }
        private static void CreateInvitation()
        {
            string accessToken = null;
            string GraphResource = string.Format("", "graph.microsoft.com");
            // Get the token for our app to talk to graph.
            try
            {
                AuthenticationContext testAuthContext = new AuthenticationContext(string.Format("{0}/{1}", EstsLoginEndpoint, TenantID));
                AuthenticationResult testAuthResult = testAuthContext.AcquireToken(
                    GraphResource,
                    new ClientCredential(TestAppClientId, TestAppClientSecret));;
                accessToken = testAuthResult.AccessToken;
            }
            catch (AdalException ex)
            {
                Console.WriteLine("An exception was thrown: {0}.", ex);
                throw;
            }
            // setup http client.
            HttpClient httpClient = new HttpClient();
            httpClient.Timeout = TimeSpan.FromSeconds(300);
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
            httpClient.DefaultRequestHeaders.Add("client-request-id", Guid.NewGuid().ToString());
            Console.WriteLine("CorrelationID for the request: {0}", httpClient.DefaultRequestHeaders.GetValues("client-request-id").Single());
            var inviteEndPoint = string.Format("https://graph.microsoft.com/beta/invitations");
            // Set the invitation object.
            Invitation invitation = new Invitation();
            invitation.InvitedUserDisplayName = "displayName";
            invitation.InvitedUserEmailAddress = "Give the invitee email";
            invitation.InviteRedirectUrl = "https://www.microsoft.com";
            invitation.SendInvitationMessage = false;
            // Make the invite call. Your app needs to have User.ReadWrite.All or Directory.ReadWrite.All scope to invite
            HttpContent content = new StringContent(JsonConvert.SerializeObject(invitation));
            content.Headers.Add("ContentType", "application/json");
            var postResponse = httpClient.PostAsync(inviteEndPoint, content).Result;
            string serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
            // Build the mail message. Here we are including the server response from inviation request, but you can customize it.
            // To send the email you need Mail.Send scope.
            var emailEndPoint = string.Format("https://graph.microsoft.com/beta/users/{0}/sendMail", InviterUserPrincipalName);
            Email mail = new Email();
            mail.message = new Message();
            mail.message.Subject = "Inviation Email";
            mail.message.Body = new ItemBody();
            mail.message.Body.Content = serverResponse;
            Recipient recipient = new Recipient();
            recipient.EmailAddress = new EmailAddress();
            recipient.EmailAddress.Address = invitation.InvitedUserEmailAddress;
            mail.recipients = new List<Recipient>();
            mail.recipients.Add(recipient);
            // Make the call to send email.
            content = new StringContent(JsonConvert.SerializeObject(mail));
            Console.WriteLine(content);
            content.Headers.Add("ContentType", "application/json");
            postResponse = httpClient.PostAsync(emailEndPoint, content).Result;
            serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
        }
        /// <summary>
        /// Invitation class.
        /// </summary>
        public class Invitation
        {
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserDisplayName { get; set; }
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserEmailAddress { get; set; }
            /// <summary>
            /// Gets or sets a value indicating whether Invitation Manager should send the email to InvitedUser.
            /// </summary>
            public bool SendInvitationMessage { get; set; }
            /// <summary>
            /// Gets or sets invitation redirect URL
            /// </summary>
            public string InviteRedirectUrl { get; set; }
        }
        /// <summary>
        /// The email message.
        /// </summary>
        public class Email
        {
            public Message message;
            public List<Recipient> recipients;
        }
    }
}
```

## <a name="powershell-example"></a>Esempio di PowerShell
L'esempio seguente illustra come sia possibile invitare in blocco utenti esterni a un'organizzazione dall'indirizzo di posta elettronica archiviato, ad esempio, in un file CSV.

1. Preparare il file CSV. Creare un nuovo file CSV e denominarlo invitations.csv. In questo esempio il file viene salvato in C:\data. Il file CSV avrà un aspetto simile al seguente:

  ```
    InvitedUserEmailAddress
    user1@contoso.com
    user2@outlook.com
    user3@gmail.com
    user4@yahoo.com
    ```

2. Ottenere la versione più recente di Azure AD PowerShell. Per usare i nuovi cmdlet, è necessario installare il modulo di Azure AD PowerShell aggiornato, che è possibile scaricare dalla [pagina della versione del modulo di Powershell](https://www.powershellgallery.com/packages/AzureADPreview).

3. Accedere alla tenancy

    ```
    Connect-AzureAd and login
    ```

4. Eseguire il cmdlet di PowerShell

    ```
    $Invitations = import-csv C:\data\invitations.csv
    foreach ($email in $invitations) {New-AzureADMSInvitation -InvitedUserEmailAddress $email.InvitedUserEmailAddress -InviteRedirectUrl http://microsoft.com -SendInvitationMessage $true}
  ```

Verrà inviato un invito all'indirizzo di posta elettronica in invitations.csv. Le funzionalità aggiuntive di questo cmdlet includono il testo personalizzato nel messaggio di posta elettronica, l'inserimento di un nome visualizzato per l'utente invitato, l'invio di messaggi agli utenti in Cc o l'eliminazione di tutti i messaggi di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


