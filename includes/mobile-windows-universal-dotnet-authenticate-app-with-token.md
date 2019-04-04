---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890980"
---
1. Nel file di progetto MainPage.xaml.cs aggiungere le istruzioni **using** seguenti:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Sostituire il metodo **AuthenticateAsync** con il codice seguente:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    In questa versione di **AuthenticateAsync**, l'app tenta di usare le credenziali archiviate in **PasswordVault** per accedere al servizio. Un accesso normale viene eseguito anche quando non sono disponibili credenziali archiviate.
   
   > [!NOTE]
   > Un token memorizzato nella cache potrebbe essere scaduto, ma la scadenza del token può avvenire anche dopo l'autenticazione mentre l'app è in uso. Per informazioni su come determinare se un token è scaduto, vedere [Cercare i token di autenticazione scaduti](https://aka.ms/jww5vp). Per una soluzione relativa alla gestione degli errori di autorizzazione relativi ai token scaduti, vedere il post sulla [memorizzazione nella cache e la gestione dei token scaduti nell'SDK per il codice gestito di Servizi mobili di Azure](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Riavviare l'app due volte.
   
    Si noti che al primo avvio viene di nuovo richiesto l'accesso con il provider. Al secondo riavvio, invece, verranno usate le credenziali memorizzate nella cache e l'accesso sarà ignorato. 

