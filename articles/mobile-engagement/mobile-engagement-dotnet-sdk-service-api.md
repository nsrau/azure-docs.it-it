---
title: Usare .NET SDK per accedere alle API del servizio Azure Mobile Engagement
description: Descrive come usare Mobile Engagement .NET SDK per accedere alle API del servizio Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6a497189268c5a1b7e269cc57904ebc77c1906fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Usare .NET SDK per accedere alle API del servizio Azure Mobile Engagement
Azure Mobile Engagement espone un set di API che consentono di gestire i dispositivi, le campagne Push e Reach e così via. Per interagire con queste API, viene fornito anche un [file Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) che è possibile usare con gli strumenti per la generazione degli SDK per la lingua preferita. Per generare l'SDK dal file Swagger è consigliabile usare lo strumento [AutoRest](https://github.com/Azure/AutoRest).

> [!NOTE]
> Il servizio Azure Mobile Engagement verrà ritirato a marzo 2018 ed è attualmente disponibile solo per i clienti esistenti. Per altre informazioni, vedere [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

In modo simile è stato creato un .NET SDK che consente di interagire con queste API tramite un wrapper C#. Non è necessario eseguire manualmente la negoziazione di token di autenticazione e l'aggiornamento.  

In questo esempio viene eseguita la serie di passaggi necessari per usare .NET SDK:

1. Prima di tutto è necessario configurare l'autenticazione per le API tramite Azure Active Directory, come descritto [qui](mobile-engagement-api-authentication.md#authentication). Al termine di questa procedura si dovrebbe disporre di **SubscriptionId**, **TenantId**, **ApplicationId** e **Secret** validi. 
2. Verrà usata una semplice app console Windows per illustrare l'uso di .NET SDK con lo scenario di creazione di una campagna di tipo annuncio. Aprire Visual Studio e creare un' **applicazione console**.   
3. È quindi necessario scaricare .NET SDK, disponibile come **Microsoft Azure Engagement Management Library** in NuGet Gallery [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Se si sta installando NuGet da Visual Studio, durante la ricerca del pacchetto è necessario assicurarsi di avere selezionato l'opzione **Includi versione preliminare** :
   
    ![][1]
4. Nel file `Program.cs` aggiungere gli spazi dei nomi seguenti:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. È quindi necessario definire le costanti seguenti, che verranno usate per l'autenticazione e l'interazione con l'app Mobile Engagement in cui si sta creando la campagna di tipo annuncio:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Definire la variabile `EngagementManagementClient` che useremo per chiamare i metodi di Mobile Engagement SDK:
   
        static EngagementManagementClient engagementClient; 
7. Aggiungere il codice seguente al metodo `Main` :
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Definire il metodo seguente, che esegue l'inizializzazione di `EngagementManagementClient` , prima autenticandosi e quindi associandosi all'app Mobile Engagement in cui si prevede di creare la campagna di tipo annuncio:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Si noti che è necessario usare il valore di **APP_RESOURCE_NAME** definito nel portale di gestione di Azure per il parametro AppName. 
   > 
   > 
9. Definire infine il metodo CreateCampaign, che usa EngagementClient inizializzato in precedenza, per creare una semplice campagna **con recapito in qualsiasi momento** &  e **di sola notifica** con un titolo e un messaggio: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Eseguire l'app console. Se la campagna viene creata correttamente, verrà visualizzato quanto segue:
    
    **Campaign Id '159' was created successfully and it is in 'draft' state**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
