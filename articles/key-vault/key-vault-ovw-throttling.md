---
title: Guida alla limitazione delle richieste per Azure Key Vault
description: La limitazione delle richieste di Key Vault consente di limitare il numero di chiamate simultanee per evitare l'uso eccessivo delle risorse.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 794e22b6fce77c0564a4db1b802c81513a36542f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747238"
---
# <a name="azure-key-vault-throttling-guidance"></a>Guida alla limitazione delle richieste per Azure Key Vault

La limitazione delle richieste è un processo che consente di limitare il numero di chiamate simultanee al servizio di Azure per prevenire l'uso eccessivo delle risorse. Azure Key Vault è progettato per gestire un volume di richieste elevato. In caso di un numero eccessivamente elevato di richieste, la limitazione delle richieste del client aiuta a mantenere le prestazioni ottimali e l'affidabilità del servizio di Azure Key Vault.

I limiti delle richieste variano in base allo scenario. Ad esempio, se si esegue un volume elevato di operazioni di scrittura, la possibilità di limitare le richieste è maggiore rispetto a quando si eseguono solo operazioni di lettura.

## <a name="how-does-key-vault-handle-its-limits"></a>Gestione dei limiti da parte di Key Vault

I limiti del servizio in Key Vault hanno lo scopo di evitare un uso improprio delle risorse e garantire la qualità del servizio per tutti i client di Key Vault. Quando si supera la soglia di un servizio, Key Vault limita le nuove richieste del client per un periodo di tempo. In questo caso, Key Vault restituisce il codice di stato HTTP 429, ovvero Too many requests (Numero di richieste eccessivo), e le richieste hanno esito negativo. Inoltre, le richieste non riuscite che restituiscono un codice 429 valgono per le limitazioni tracciate da Key Vault. 

Nel caso l'utente abbia delle esigenze aziendali per cui sono necessarie delle limitazioni maggiori, contattare Microsoft.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Come limitare le richieste per l'app in risposta ai limiti del servizio

Di seguito sono riportate le **pratiche ottimali** per la limitazione delle richieste per l'app:
- Ridurre il numero di operazioni per ogni richiesta.
- Ridurre la frequenza delle richieste.
- Evitare tentativi immediati. 
    - Tutte le richieste si accumulano per i limiti di consumo.

Quando si implementa la gestione degli errori dell'app, usare il codice di errore HTTP 429 per rilevare la necessità di limitazione delle richieste lato client. Se la richiesta ha di nuovo esito negativo con un codice di errore HTTP 429, vuol dire che ci sono ancora limiti nel servizio di Azure. Continuare a usare il metodo di limitazione delle richieste lato client consigliato, provando a inviare nuovamente la richiesta fino a quando non ha esito positivo.

Di seguito è riportato il codice che implementa il backoff esponenziale. 
```
     public async Task OnGetAsync()
     {
         Message = "Your application description page.";
         int retries = 0;
         bool retry = false;
         try
         {
             /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
             AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
             KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
             var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                     .ConfigureAwait(false);
             Message = secret.Value;

             /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
             do
             {
                 long waitTime = Math.Min(getWaitTime(retries), 2000000);
                 secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                     .ConfigureAwait(false);
                 retry = false;
             } 
             while(retry && (retries++ < 10));
         }
         /// <exception cref="KeyVaultErrorException">
         /// Thrown when the operation returned an invalid status code
         /// </exception>
         catch (KeyVaultErrorException keyVaultException)
         {
             Message = keyVaultException.Message;
             if((int)keyVaultException.Response.StatusCode == 429)
                 retry = true;
         }
     }

     // This method implements exponential backoff incase of 429 errors from Azure Key Vault
     private static long getWaitTime(int retryCount)
     {
         long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
         return waitTime;
     }
```

### <a name="recommended-client-side-throttling-method"></a>Metodo consigliato per la limitazione delle richieste lato client

Quando si genera il codice di errore HTTP 429, iniziare a limitare le richieste del client con un approccio di backoff esponenziale:

1. Attendere 1 secondo e ripetere la richiesta
2. Se viene ancora limitata, attendere 2 secondi e ripetere la richiesta
3. Se viene ancora limitata, attendere 4 secondi e ripetere la richiesta
4. Se viene ancora limitata, attendere 8 secondi e ripetere la richiesta
5. Se viene ancora limitata, attendere 16 secondi e ripetere la richiesta

A questo punto, il codice di risposta HTTP 429 dovrebbe non essere più visualizzato.

## <a name="see-also"></a>Vedere anche 

Per un approfondimento sulla limitazione delle richieste nel cloud di Microsoft, vedere [Throttling Pattern](https://docs.microsoft.com/azure/architecture/patterns/throttling) (Modello di limitazione delle richieste).

