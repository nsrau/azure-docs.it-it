---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 04/10/2019
ms.date: 05/10/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: ae6d590cdada24638ec2d24c83609b8e6addfaf0
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416315"
---
## <a name="set-up-your-project"></a>Configurare il progetto

In questa sezione verrà creato un nuovo progetto per illustrare come integrare un'applicazione .NET per Windows Desktop (XAML) con *Accedi con Microsoft* in modo da poter eseguire query su API Web che richiedono un token.

L'applicazione creata in questa Guida consente di visualizzare un pulsante usato per chiamare un grafo, un'area per visualizzare i risultati sullo schermo e un pulsante di disconnessione.

> [!NOTE]
> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [scaricare un progetto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) e andare direttamente al [passaggio della configurazione](#register-your-application) per configurare il codice di esempio prima di eseguirlo.
>

Per creare l'applicazione, eseguire le operazioni seguenti:

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**.
2. In **Modelli** selezionare **Visual C#**.
3. Selezionare **App WPF (.NET Framework)**, a seconda della versione di Visual Studio in uso.

## <a name="add-msal-to-your-project"></a>Aggiungere MSAL al progetto

1. In Visual Studio selezionare **Strumenti** > **Gestione pacchetti NuGet**> **Console di Gestione pacchetti**.
2. Nella finestra Console di Gestione pacchetti incollare il seguente comando di Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Questo comando installa Microsoft Authentication Library. MSAL gestisce l'acquisizione, la memorizzazione nella cache e l'aggiornamento dei token utente usati per accedere alle API protette da Azure Active Directory 2.0
    >

## <a name="add-the-code-to-initialize-msal"></a>Aggiungere il codice per inizializzare MSAL

Questo passaggio consente di creare una classe per gestire l'interazione con la libreria MSAL, ad esempio per la gestione dei token.

1. Aprire il file *App.xaml.cs*, quindi aggiungere alla classe il riferimento relativo alla libreria MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Aggiornare la classe App con il codice seguente:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzureChina, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Creare l'interfaccia utente dell'applicazione

Questa sezione illustra come un'applicazione può eseguire una query su un server back-end protetto come Microsoft Graph. 

Nell'ambito del modello di progetto viene automaticamente creato un file *MainWindow.xaml*. Aprire il file e sostituire il nodo dell'applicazione *\<Griglia>* con il codice seguente:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

