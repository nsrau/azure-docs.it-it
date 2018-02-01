---
title: Implementare il ripristino di emergenza usando il backup e il ripristino in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come usare il backup e il ripristino per eseguire il ripristino di emergenza in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: 3fcd2fc4162cfbf549be979e15745934c2e4c6ff
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure

Scegliendo di pubblicare e gestire le API tramite Gestione API di Azure è possibile sfruttare molte funzionalità di tolleranza di errore e di infrastruttura che sarebbe altrimenti necessario progettare, implementare e gestire. La piattaforma di Azure permette di mitigare una vasta gamma di potenziali errori a un costo nettamente inferiore.

Per risolvere i problemi di disponibilità che colpiscono l'area in cui è ospitato il servizio Gestione API, è necessario essere pronti a ripristinare il servizio in un'area diversa in qualsiasi momento. In base ai propri obiettivi in termini di disponibilità e tempi di ripristino, è consigliabile riservare un servizio di backup in una o più aree e provare a mantenere sincronizzati la configurazione e il contenuto con il servizio attivo. La funzionalità di backup e ripristino dei servizi fornisce il blocco predefinito necessario per implementare la propria strategia di ripristino di emergenza.

Questa guida descrive come autenticare le richieste di Azure Resource Manager e come eseguire il backup e il ripristino delle istanze del servizio Gestione API.

> [!NOTE]
> Il processo di backup e ripristino di un'istanza del servizio Gestione API per il ripristino di emergenza può essere usato anche per la replica delle istanze del servizio Gestione API per scenari quali la gestione temporanea.
>
> Ogni backup scade dopo 30 giorni. Se si tenta di ripristinare un backup dopo la scadenza del periodo di 30 giorni, il ripristino avrà esito negativo e verrà visualizzato il messaggio `Cannot restore: backup expired`.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticazione delle richieste di Gestione risorse di Azure

> [!IMPORTANT]
> L'API REST per il backup e ripristino usa Gestione risorse di Azure e include un meccanismo di autenticazione diverso rispetto alle API REST per la gestione delle entità di Gestione API. I passaggi descritti in questa sezione descrivono come autenticare le richieste di Gestione risorse di Azure. Per altre informazioni, vedere [Autenticazione delle richieste di Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Tutte le attività che è possibile eseguire sulle risorse tramite Azure Resource Manager devono essere autenticate con Azure Active Directory usando la procedura seguente:

* Aggiungere un'applicazione al tenant di Azure Active Directory.
* Impostare le autorizzazioni per l'applicazione aggiunta.
* Ottenere il token per autenticare le richieste a Gestione risorse di Azure.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Usando la sottoscrizione che contiene l'istanza del servizio Gestione API passare alla scheda **Registrazioni per l'app**.

    > [!NOTE]
    > Se la directory predefinita di Azure Active Directory non è visibile nel proprio account, contattare l'amministratore della sottoscrizione di Azure perché conceda le autorizzazioni necessarie per l'account.
3. Fare clic su **Registrazione nuova applicazione**.

    Su lato destro verrà visualizzata la finestra **Crea**. Immettere le informazioni rilevanti per l'app AAD in questa finestra.
4. Immettere un nome per l'applicazione.
5. Come tipo di applicazione selezionare **Nativo**.
6. Immettere un URL di segnaposto, ad esempio `http://resources` per **URI di reindirizzamento**, che è un campo obbligatorio, ma il valore non viene usato in seguito. Selezionare la casella di controllo per salvare l'applicazione.
7. Fare clic su **Crea**.

### <a name="add-an-application"></a>Aggiungere un'applicazione

1. Dopo aver creato l'applicazione, fare clic su **Impostazioni**.
2. Fare clic su **Autorizzazioni necessarie**.
3. Fare clic su **+Aggiungi**.
4. Fare clic su **Selezionare un'API**.
5. Scegliere **API Gestione dei servizi di** **Windows Azure**.
6. Fare clic su **Seleziona**. 

    ![Aggiungere autorizzazioni](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Fare clic su **Autorizzazioni delegate** accanto all'applicazione appena aggiunta, selezionare la casella per **Accesso a Gestione dei servizi di Azure (anteprima)**.
8. Fare clic su **Seleziona**.

### <a name="configuring-your-app"></a>Configurazione dell'app

Prima di richiamare le API che generano il backup e ripristino, è necessario ottenere un token. L'esempio seguente usa il pacchetto NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) per recuperare il token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Sostituire `{tentand id}`, `{application id}` e `{redirect uri}` usando le istruzioni seguenti:

1. Sostituire `{tenant id}` con l'ID tenant dell'applicazione Azure Active Directory creata. È possibile accedere all'ID facendo clic su **Registrazioni per l'app** -> **Endpoint**.

    ![Endpoint][api-management-endpoint]
2. Sostituire `{application id}` con il valore visualizzato passando alla pagina **Impostazioni**.
3. Sostituire l'URL dalla scheda **URI di reindirizzamento** nell'applicazione Azure Active Directory.

    Dopo avere specificato i valori, l'esempio di codice dovrebbe restituire un token simile all'esempio seguente:

    ![token][api-management-arm-token]

## <a name="calling-the-backup-and-restore-operations"></a>Chiamata delle operazioni di backup e ripristino

Prima di chiamare le operazioni di backup e ripristino descritte nelle sezioni seguenti, impostare l'intestazione della richiesta di autorizzazione per la chiamata REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Backup di un servizio di Gestione API
Per eseguire il backup di un servizio di gestione API, emettere la seguente richiesta HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

dove:

* `subscriptionId`: ID della sottoscrizione contenente il servizio Gestione API di cui si sta tentando di eseguire il backup.
* `resourceGroupName`: una stringa nel formato "Api-Default-{service-region}", dove `service-region` identifica l'area di Azure in cui è ospitato il servizio Gestione API di cui si sta tentando di eseguire il backup, ad esempio `North-Central-US`.
* `serviceName` : il nome del servizio di Gestione API di cui sta eseguendo il backup specificato quando è stato creato.
* `api-version` - sostituire con `2014-02-14`

Nel corpo della richiesta, specificare il nome dell'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di destinazione di Azure:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application/json`.

Il backup è un'operazione a lunga esecuzione che potrebbe richiedere diversi minuti per essere completata.  Se la richiesta viene eseguita correttamente e il processo di backup viene avviato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`.  Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del backup si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di backup.

Quando si crea una richiesta di backup, occorre attenersi ai vincoli seguenti.

* Il **contenitore** specificato nel corpo della richiesta **deve esistere**.
* Mentre il backup è in corso, **non tentare di eseguire alcuna operazione di gestione dei servizi** , ad esempio l'aggiornamento o il downgrade di SKU, la modifica di nomi di dominio e così via.
* Il ripristino di un **backup è garantito solo per 30 giorni** dal momento della sua creazione.
* I **dati di utilizzo** usati per creare report analitici **non sono inclusi** nel backup. Usare l'[API REST di Gestione API di Azure][Azure API Management REST API] per recuperare periodicamente i report analitici e custodirli al sicuro.
* La frequenza con cui si eseguono i backup dei servizi influenzerà i propri obiettivi relativi ai punti di ripristino. Per ridurla al minimo, si consiglia di implementare backup regolari e di eseguire backup su richiesta dopo aver apportato modifiche importanti al servizio Gestione API.
* Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'esecuzione del processo di backup **potrebbero non essere incluse nel backup e potrebbero quindi andare perse**.

### <a name="step2"></a>Ripristino di un servizio di Gestione API
Per ripristinare un servizio di Gestione API da un backup creato in precedenza, creare la seguente richiesta HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

dove:

* `subscriptionId` : ID della sottoscrizione contenente il servizio di Gestione API in cui si sta ripristinando un backup.
* `resourceGroupName`: una stringa nel formato "Api-Default-{service-region}", dove `service-region` identifica l'area di Azure in cui è ospitato il servizio Gestione API in cui si sta ripristinando un backup, ad esempio `North-Central-US`.
* `serviceName` : il nome del servizio di Gestione API in cui si sta effettuando il ripristino specificato quando è stato creato.
* `api-version` - sostituire con `2014-02-14`

Nel corpo della richiesta specificare la posizione del file di backup, ad esempio il nome dell'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di Azure:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application/json`.

Il ripristino è un'operazione a lunga esecuzione che potrebbe richiedere 30 minuti o più per essere completata. Se la richiesta viene eseguita correttamente e il processo di ripristino viene avviato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del ripristino si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di ripristino.

> [!IMPORTANT]
> Lo **SKU** del servizio in cui si effettua il ripristino **deve corrispondere** allo SKU del servizio sottoposto a backup da ripristinare.
>
> Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'operazione di ripristino **potrebbero essere sovrascritte**.
>
>

## <a name="next-steps"></a>Passaggi successivi
Consultare i blog Microsoft seguenti per due diverse procedure dettagliate del processo di backup e ripristino.

* [Replicare account di Gestione API di Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Gestione API di Azure: Backup e ripristino della configurazione](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * L'approccio descritto da Stuart non corrisponde alle linee guida ufficiali, ma è molto interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
