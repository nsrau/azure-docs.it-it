---
title: Implementare il ripristino di emergenza usando il backup e il ripristino in gestione API
titleSuffix: Azure API Management
description: Informazioni su come usare il backup e il ripristino per eseguire il ripristino di emergenza in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: 8f748764d0f61e4932b2d4710f5a6805a5eddf0e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047478"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure

Pubblicando e gestendo le API tramite Gestione API di Azure è possibile sfruttare funzionalità di tolleranza di errore e di infrastruttura che sarebbe altrimenti necessario progettare, implementare e gestire manualmente. La piattaforma di Azure permette di mitigare una vasta gamma di potenziali errori a un costo nettamente inferiore.

Per risolvere i problemi di disponibilità che colpiscono l'area in cui è ospitato il servizio Gestione API, è necessario essere pronti a ripristinare il servizio in un'altra area in qualsiasi momento. A seconda dell'obiettivo del tempo di ripristino, è possibile che si desideri proteggere un servizio in standby in una o più aree. È anche possibile provare a mantenere sincronizzati la configurazione e il contenuto con il servizio attivo in base all'obiettivo del punto di ripristino. Le funzionalità di backup e ripristino del servizio forniscono i blocchi predefiniti necessari per l'implementazione della strategia di ripristino di emergenza.

È anche possibile usare le operazioni di backup e ripristino per la replica della configurazione del servizio gestione API tra ambienti operativi, ad esempio sviluppo e gestione temporanea. Tenere presente che verranno copiati anche i dati di runtime, ad esempio gli utenti e le sottoscrizioni, che potrebbero non essere sempre auspicabili.

In questa guida viene illustrato come automatizzare le operazioni di backup e ripristino e come garantire la corretta autenticazione delle richieste di backup e ripristino da Azure Resource Manager.

> [!IMPORTANT]
> L'operazione di ripristino non modifica la configurazione del nome host personalizzato del servizio di destinazione. Si consiglia di usare lo stesso nome host personalizzato e il certificato TLS per i servizi attivi e di standby, in modo che, dopo il completamento dell'operazione di ripristino, il traffico possa essere reindirizzato all'istanza di standby mediante una semplice modifica CNAME DNS.
>
> L'operazione di backup non acquisisce i dati di log pre-aggregati usati nei report mostrati nel pannello Analytics del portale di Azure.

> [!WARNING]
> Ogni backup scade dopo 30 giorni. Se si tenta di ripristinare un backup dopo la scadenza del periodo di 30 giorni, il ripristino avrà esito negativo e verrà visualizzato il messaggio `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticazione delle richieste di Gestione risorse di Azure

> [!IMPORTANT]
> L'API REST per il backup e ripristino usa Gestione risorse di Azure e include un meccanismo di autenticazione diverso rispetto alle API REST per la gestione delle entità di Gestione API. I passaggi descritti in questa sezione descrivono come autenticare le richieste di Gestione risorse di Azure. Per altre informazioni, vedere [Autenticazione delle richieste di Gestione risorse di Azure](/rest/api/index).

Tutte le attività che è possibile eseguire sulle risorse tramite Azure Resource Manager devono essere autenticate con Azure Active Directory usando la procedura seguente:

-   Aggiungere un'applicazione al tenant di Azure Active Directory.
-   Impostare le autorizzazioni per l'applicazione aggiunta.
-   Ottenere il token per autenticare le richieste a Gestione risorse di Azure.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Usare la sottoscrizione che include l'istanza del servizio Gestione API e passare alla scheda **Registrazione app** in **Azure Active Directory** (Azure Active Directory > Gestisci/Registrazione app).

    > [!NOTE]
    > Se la directory predefinita di Azure Active Directory non è visibile nel proprio account, contattare l'amministratore della sottoscrizione di Azure perché conceda le autorizzazioni necessarie per l'account.

3. Fare clic su **Registrazione nuova applicazione**.

    Su lato destro verrà visualizzata la finestra **Crea**. Immettere le informazioni rilevanti per l'app AAD in questa finestra.

4. Immettere un nome per l'applicazione.
5. Come tipo di applicazione selezionare **Nativo**.
6. Immettere un URL di segnaposto, ad esempio `http://resources` per **URI di reindirizzamento**, che è un campo obbligatorio, ma il valore non viene usato in seguito. Selezionare la casella di controllo per salvare l'applicazione.
7. Fare clic su **Crea**.

### <a name="add-an-application"></a>Aggiungere un'applicazione

1. Una volta creata l'applicazione, fare clic su **autorizzazioni API**.
2. Fare clic su **+ Aggiungi un'autorizzazione**.
4. Premere **Seleziona API Microsoft**.
5. Scegliere **Gestione servizi di Azure**.
6. Fare clic su **Seleziona**.

    ![Aggiungere autorizzazioni](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Fare clic su **Autorizzazioni delegate** accanto all'applicazione appena aggiunta, selezionare la casella per **Accesso a Gestione dei servizi di Azure (anteprima)** .
8. Fare clic su **Seleziona**.
9. Fare clic su **Concedere le autorizzazioni**.

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
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Sostituire `{tenant id}`, `{application id}` e `{redirect uri}` usando le istruzioni seguenti:

1. Sostituire `{tenant id}` con l'ID tenant dell'applicazione Azure Active Directory creata. È possibile accedere all'ID facendo clic su **Registrazioni app** -> **Endpoint**.

    ![Endpoint][api-management-endpoint]

2. Sostituire `{application id}` con il valore visualizzato passando alla pagina **Impostazioni**.
3. Sostituire `{redirect uri}` con il valore proveniente dalla scheda **URL di reindirizzamento** dell'applicazione Azure Active Directory.

    Dopo avere specificato i valori, l'esempio di codice dovrebbe restituire un token simile all'esempio seguente:

    ![token][api-management-arm-token]

    > [!NOTE]
    > Il token può scadere dopo un certo periodo. Eseguire nuovamente l'esempio di codice per generare un nuovo token.

## <a name="calling-the-backup-and-restore-operations"></a>Chiamata delle operazioni di backup e ripristino

Le API REST sono [Api Management Service - Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) (Servizio Gestione API - Backup) e [Api Management Service - Restore](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore) (Servizio Gestione API - Ripristino).

Prima di chiamare le operazioni di backup e ripristino descritte nelle sezioni seguenti, impostare l'intestazione della richiesta di autorizzazione per la chiamata REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Eseguire il backup di un servizio gestione API

Per eseguire il backup di un servizio di gestione API, emettere la seguente richiesta HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

dove:

-   `subscriptionId`: ID della sottoscrizione contenente il servizio Gestione API di cui si sta tentando di eseguire il backup
-   `resourceGroupName`: nome del gruppo di risorse del servizio Gestione API di Azure
-   `serviceName` : il nome del servizio di Gestione API di cui sta eseguendo il backup specificato quando è stato creato
-   `api-version`-Sostituisci con `2018-06-01-preview`

Nel corpo della richiesta, specificare il nome dell'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di destinazione di Azure:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application/json`.

Il backup è un'operazione a lunga esecuzione che potrebbe richiedere più minuti per essere completata. Se la richiesta viene eseguita correttamente e il processo di backup è iniziato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del backup si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di backup.

Quando si esegue una richiesta di backup o ripristino, tenere presenti i vincoli seguenti:

-   Il **contenitore** specificato nel corpo della richiesta **deve esistere**.
-   Mentre è in corso il backup, **evitare le modifiche di gestione nel servizio** , ad esempio l'aggiornamento o il downgrade dello SKU, la modifica del nome di dominio e altro ancora.
-   Il ripristino di un **backup è garantito solo per 30 giorni** dal momento della sua creazione.
-   I **dati di utilizzo** usati per creare report analitici **non sono inclusi** nel backup. Usare l' [API REST di Gestione API di Azure][azure api management rest api] per recuperare periodicamente i report analitici e custodirli al sicuro.
-   Inoltre, gli elementi seguenti non fanno parte dei dati di backup: certificati SSL del dominio personalizzato e qualsiasi certificato intermedio o radice caricato dal cliente, dal contenuto del portale per sviluppatori e dalle impostazioni di integrazione della rete virtuale.
-   La frequenza con cui si eseguono i backup dei servizi influenzerà i propri obiettivi relativi ai punti di ripristino. Per ridurla al minimo, si consiglia di implementare backup regolari e di eseguire backup su richiesta dopo aver apportato modifiche al servizio di Gestione API.
-   Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri e all'aspetto del portale per sviluppatori) durante l'esecuzione del processo di backup **potrebbero essere escluse dal backup e potrebbero andare perse**.
-   **Consentire** l'accesso dal piano di controllo all'account di archiviazione di Azure, se è abilitato il [Firewall][azure-storage-ip-firewall] . Il cliente deve aprire il set di [indirizzi IP del piano di controllo di gestione API di Azure][control-plane-ip-address] nell'account di archiviazione per il backup o il ripristino da. 

> [!NOTE]
> Se si tenta di eseguire il backup o il ripristino da/verso un servizio gestione API usando un account di archiviazione con un [Firewall][azure-storage-ip-firewall] abilitato, nella stessa area di Azure, questa operazione non funzionerà. Ciò è dovuto al fatto che le richieste ad archiviazione di Azure non sono inviato tramite SNAT a un indirizzo IP pubblico dal > di calcolo (piano di controllo di gestione API di Azure). La richiesta di archiviazione tra aree sarà inviato tramite SNAT.

### <a name="step2"> </a>Ripristinare un servizio gestione API

Per ripristinare un servizio di Gestione API da un backup creato in precedenza, creare la seguente richiesta HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

dove:

-   `subscriptionId` : ID della sottoscrizione contenente il servizio di Gestione API in cui si sta ripristinando un backup
-   `resourceGroupName`: nome del gruppo di risorse contenente il servizio di Gestione API in cui si sta ripristinando un backup
-   `serviceName` : il nome del servizio di Gestione API in cui si sta effettuando il ripristino specificato quando è stato creato
-   `api-version`-Sostituisci con `2018-06-01-preview`

Nel corpo della richiesta, specificare il percorso del file di backup. Ovvero, aggiungere il nome dell'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di Azure:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application/json`.

Il ripristino è un'operazione a lunga esecuzione che potrebbe richiedere 30 minuti o più per essere completata. Se la richiesta viene eseguita correttamente e il processo di ripristino è iniziato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del ripristino si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di ripristino.

> [!IMPORTANT]
> Lo **SKU** del servizio in cui si effettua il ripristino **deve corrispondere** allo SKU del servizio sottoposto a backup da ripristinare.
>
> Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'operazione di ripristino **potrebbero essere sovrascritte**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Le operazioni di backup e ripristino possono essere eseguite anche con i comandi [_backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) e [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) rispettivamente di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse seguenti per procedure dettagliate diverse del processo di backup e ripristino.

-   [Replicare account di Gestione API di Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automating API Management Backup and Restore with Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps) (Automazione del backup e del ripristino di Gestione API con App per la logica)
-   [Azure API Management: Backing Up and Restoring Configuration](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx) (Gestione API di Azure: backup e ripristino della configurazione) 
    _L'approccio descritto da Stuart non corrisponde alle linee guida ufficiali, ma è molto interessante._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
