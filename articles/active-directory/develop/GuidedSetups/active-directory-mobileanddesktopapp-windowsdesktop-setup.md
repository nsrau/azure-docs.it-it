---
title: Introduzione a Windows Desktop per Azure AD v2 - Installazione | Microsoft Docs
description: Come applicazioni .NET per Windows Desktop (XAML) possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: be00cb8f117fcbd2d137197f7dbe9713540cea61
ms.contentlocale: it-it


---

<a id="set-up-your-project" class="xliff"></a>

## Configurare il progetto

Questa sezione fornisce istruzioni dettagliate su come creare un nuovo progetto per illustrare come integrare un'applicazione .NET per Windows Desktop (XAML) con *Accedi con Microsoft* in modo da poter eseguire query su API Web che richiedono un token.

L'applicazione creata in questa guida include un pulsante che consente di generare un grafico dei risultati da visualizzare sullo schermo, oltre a un pulsante di disconnessione.

> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, è possibile [scaricare il progetto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) e passare direttamente al [configurazione](#create-an-application-express "passaggio di configurazione") per configurare il codice di esempio prima di eseguirlo.


<a id="create-your-application" class="xliff"></a>

### Creare l'applicazione
1. In Visual Studio: `File` > `New` > `Project`<br/>
2. In *Modelli* selezionare `Visual C#`
3. Selezionare `WPF App` (o *Applicazione WPF*, a seconda della versione di Visual Studio)

<a id="add-the-microsoft-authentication-library-msal-to-your-project" class="xliff"></a>

## Aggiungere Microsoft Authentication Library (MSAL) al progetto
1. In Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Nella finestra Console di Gestione pacchetti copiare e incollare il codice seguente:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> Questo pacchetto consente di installare Microsoft Authentication Library (MSAL), che gestisce l'acquisizione, la memorizzazione nella cache e l'aggiornamento dei token utente usati per accedere ad API protette da Azure Active Directory v2.

<a id="add-the-code-to-initialize-msal" class="xliff"></a>

## Aggiungere il codice per inizializzare MSAL
Questo passaggio consente di creare una classe per gestire l'interazione con la libreria MSAL, ad esempio per la gestione dei token.

1. Aprire il file `App.xaml.cs` e aggiungere alla classe il riferimento relativo alla libreria MSAL:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Aggiornare la classe App con il codice seguente:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

<a id="create-your-applications-ui" class="xliff"></a>

## Creare l'interfaccia utente dell'applicazione
La sezione seguente illustra come un'applicazione può eseguire una query su un server back-end protetto come Microsoft Graph. Nell'ambito del modello di progetto viene automaticamente creato un file MainWindow.xaml. Aprire il file e seguire le istruzioni seguenti:

1.  Sostituire il valore `<Grid>` dell'applicazione con il seguente:

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

