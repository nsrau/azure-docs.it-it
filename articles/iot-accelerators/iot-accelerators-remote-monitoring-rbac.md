---
title: Controllo degli accessi di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo illustra come configurare il controllo degli accessi in base al ruolo nell'acceleratore di soluzioni di monitoraggio remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 56fbb5d45e55e63ae887d915367cfc649e531095
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820223"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Configurare il controllo degli accessi in base al ruolo nell'acceleratore di soluzioni di monitoraggio remoto

Questo articolo illustra come configurare il controllo degli accessi in base al ruolo nell'acceleratore di soluzioni di monitoraggio remoto. Il controllo degli accessi in base al ruolo consente di limitare l'accesso dei singoli utenti a funzionalità specifiche della soluzione.

## <a name="default-settings"></a>Impostazioni predefinite

Quando si distribuisce la soluzione di monitoraggio remoto per la prima volta, sono disponibili due ruoli: **Admin** e **Sola lettura**.

Gli utenti con il ruolo **Admin** hanno accesso completo alla soluzione, incluse le autorizzazioni seguenti, mentre gli utenti con il ruolo **Sola lettura** possono accedere solo per visualizzare la soluzione.

| Autorizzazione            | Admin | Sola lettura |
|----------------       |-------|-----------|
| Visualizza soluzione         | Yes   | Yes       |
| Aggiornare avvisi         | Yes   | No         |
| Eliminare avvisi         | Yes   | No         |
| Creare dispositivi        | Yes   | No         |
| Aggiornare dispositivi        | Yes   | No         |
| Eliminare dispositivi        | Yes   | No         |
| Creare gruppi di dispositivi  | Yes   | No         |
| Aggiornare gruppi di dispositivi  | Yes   | No         |
| Eliminare gruppi di dispositivi  | Yes   | No         |
| Creazione di regole          | Yes   | No         |
| Aggiornare regole          | Yes   | No         |
| Eliminare regole          | Yes   | No         |
| Creare processi           | Yes   | No         |
| Aggiornare la gestione di SIM | Yes   | No         |

Per impostazione predefinita, l'utente che ha distribuito la soluzione viene assegnato automaticamente al ruolo **Admin** ed è proprietario di un'applicazione di Azure Active Directory. Come proprietario dell'applicazione, può anche assegnare ruoli ad altri utenti tramite il portale di Azure. Se si vuole consentire a un altro utente di assegnare ruoli nella soluzione, è necessario impostare anche tale utente come proprietario dell'applicazione nel portale di Azure.

> [!NOTE]
> L'utente che ha distribuito la soluzione è l'**unica persona** che può visualizzarla immediatamente dopo essere stata creata. Per concedere anche ad altri utenti l'autorizzazione a visualizzare l'applicazione con il ruolo Sola lettura, Admin o Personalizzato, vedere le istruzioni seguenti su come aggiungere o rimuovere utenti.

## <a name="add-or-remove-users"></a>Aggiungere o rimuovere utenti

Gli utenti proprietari di un'applicazione di Azure Active Directory possono usare il portale di Azure per aggiungere (o rimuovere) un utente a un ruolo dalla soluzione di monitoraggio remoto. La procedura seguente usa l'[applicazione aziendale di Azure Active Directory](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) creata al momento della distribuzione della soluzione di monitoraggio remoto.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Verificare che l'[utente si trovi nella directory](../active-directory/fundamentals/add-users-azure-active-directory.md) in uso. La directory da usare è stata selezionata al momento dell'accesso al sito [Acceleratori di soluzione Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators). Il nome della directory è visibile nell'angolo in alto a destra della [pagina](https://www.azureiotsolutions.com/Accelerators).

1. Trovare l'**applicazione aziendale** per la soluzione nel portale di Azure. Dopo aver eseguito l'accesso al portale, filtrare l'elenco impostando **Tipo di applicazione** su **Tutte le applicazioni**. Cercare il nome dell'applicazione. Il nome dell'applicazione corrisponde a quello della soluzione di monitoraggio remoto. Nello screenshot seguente il nome visualizzato della soluzione e dell'applicazione è **contoso-rm4**.

    ![Applicazione aziendale](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Verificare di essere un proprietario dell'applicazione facendo clic sull'applicazione e quindi su **Proprietari**. Nello screenshot seguente **Contoso admin** è un proprietario dell'applicazione **contoso-rm4**:

    ![Proprietari](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Se non si è uno dei proprietari, chiedere a un proprietario esistente di aggiungere il proprio nome all'elenco. Solo i proprietari possono assegnare i ruoli di un'applicazione, come **Admin** o **Sola lettura**, ad altri utenti.

1. Per visualizzare l'elenco degli utenti assegnati ai ruoli dell'applicazione, fare clic su **Utenti e gruppi**.

1. Per aggiungere un utente, fare clic su **Aggiungi un utente** e quindi su **Utenti e gruppi, Nessun elemento selezionato** per selezionare un utente dalla directory.

1. Per assegnare l'utente a un ruolo, fare clic su **Seleziona ruolo, Nessun elemento selezionato** e scegliere il ruolo **Admin** o **Sola lettura** per l'utente. Fare clic su **Seleziona** e quindi su **Assegna**.

    ![Selezionare il ruolo](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. L'utente può ora accedere alla soluzione di monitoraggio remoto con le autorizzazioni definite dal ruolo.

1. È possibile eliminare gli utenti dell'applicazione nella pagina **Utenti e gruppi** nel portale.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Quando viene distribuita per la prima volta, la soluzione di monitoraggio remoto include i ruoli **Admin** e **Sola lettura**. È possibile aggiungere ruoli personalizzati con diversi set di autorizzazioni. Per definire un ruolo personalizzato, è necessario:

- Aggiungere un nuovo ruolo all'applicazione nel portale di Azure.
- Definire i criteri per il nuovo ruolo nel microservizio di autenticazione e autorizzazione.
- Aggiornare l'interfaccia utente Web della soluzione.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definire un ruolo personalizzato nel portale di Azure

I passaggi seguenti descrivono come aggiungere un ruolo a un'applicazione in Azure Active Directory. In questo esempio si crea un nuovo ruolo che consente ai membri di creare, aggiornare ed eliminare i dispositivi nella soluzione di monitoraggio remoto.

1. Trovare la **registrazione per l'app** relativa alla soluzione nel portale di Azure. Il nome dell'applicazione corrisponde a quello della soluzione di monitoraggio remoto. Nello screenshot seguente il nome visualizzato della soluzione e dell'applicazione è **contoso-rm4**.

    ![Registrazione delle app](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Selezionare l'applicazione e quindi fare clic su **Manifesto**. È possibile visualizzare i due [ruoli di app](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) già definiti per l'applicazione:

    ![Visualizzare il manifesto](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Modificare il manifesto per aggiungere un ruolo denominato **ManageDevices**, come illustrato nel frammento di codice seguente. Per l'ID del nuovo ruolo è necessario specificare una stringa univoca, ad esempio un GUID. È possibile generare un nuovo GUID usando un servizio come [Online GUID Generator](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Salvare le modifiche.

### <a name="define-a-policy-for-the-new-role"></a>Definire i criteri per il nuovo ruolo

Dopo aver aggiunto il ruolo all'app nel portale di Azure, è necessario definire in [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) i criteri che assegnano le autorizzazioni necessarie per gestire i dispositivi.

1. Clonare il repository dei [microservizi di monitoraggio remoto](https://github.com/Azure/remote-monitoring-services-dotnet) di GitHub nel computer locale.

1. Modificare il file **auth/Services/data/policies/roles.json** per aggiungere i criteri per il ruolo **ManageDevices**, come illustrato nel frammento di codice seguente. I valori **ID** e **Role** devono corrispondere alla definizione di ruolo nel manifesto dell'app della sezione precedente. L'elenco di azioni consentite permette a un utente con il ruolo **ManageDevices** di creare, aggiornare ed eliminare i dispositivi connessi alla soluzione:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Al termine della modifica del file **Services/data/policies/roles.json**, ricompilare e ridistribuire il microservizio di autenticazione e autorizzazione per l'acceleratore di soluzioni.

### <a name="how-the-web-ui-enforces-permissions"></a>Come vengono applicate le autorizzazioni dall'interfaccia utente Web

L'interfaccia utente Web usa il [microservizio di autenticazione e autorizzazione](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) per determinare le azioni che un utente è autorizzato a eseguire e i controlli visibili nell'interfaccia utente. Se ad esempio la soluzione è denominata **contoso-rm4**, l'interfaccia utente Web recupera un elenco di azioni consentite per l'utente corrente inviando la richiesta seguente:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Per un utente chiamato **Device Manager** appartenente al ruolo **ManageDevices**, il corpo della risposta include il codice JSON seguente:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Il frammento di codice seguente di [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) nell'[interfaccia utente Web](https://github.com/Azure/pcs-remote-monitoring-webui/) mostra come vengono applicate le autorizzazioni in modo dichiarativo:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Per altre informazioni, vedere [Protected Components](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md) (Componenti protetti). È possibile definire autorizzazioni aggiuntive nel file [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js).

### <a name="how-the-microservices-enforce-permissions"></a>Come vengono applicate le autorizzazioni dai microservizi

Anche i microservizi controllano le autorizzazioni per la protezione da richieste API non autorizzate. Quando riceve una richiesta API, un microservizio decodifica e convalida il token JWT per ottenere l'ID utente e le autorizzazioni associate al ruolo dell'utente.

Il frammento di codice seguente del file [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) nel [microservizio IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) mostra come vengono applicate le autorizzazioni:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come viene implementato il controllo degli accessi in base al ruolo nell'acceleratore di soluzioni di monitoraggio remoto.

Vedere [Configurare il controllo degli accessi in base al ruolo nell'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-remote-monitoring-rbac-tsi.md) per ottenere informazioni su come gestire l'accesso a Time Series Insights Explorer nell'acceleratore di soluzioni per il monitoraggio remoto.

Per altre informazioni concettuali sull'acceleratore di soluzioni di monitoraggio remoto, vedere [Architettura della soluzione di monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Per altre informazioni sulla personalizzazione della soluzione di monitoraggio remoto, vedere [Personalizzare e ridistribuire un microservizio](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->