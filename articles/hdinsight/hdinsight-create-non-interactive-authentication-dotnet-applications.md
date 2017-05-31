---
title: Creare applicazioni .NET HDInsight di autenticazione non interattive | Documentazione Microsoft
description: Informazioni su come creare applicazioni .NET HDInsight di autenticazione non interattive.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 2b8638ffc3287346a71f591370367655c450e376
ms.contentlocale: it-it
ms.lasthandoff: 04/18/2017


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Creare applicazioni .NET HDInsight di autenticazione non interattive
È possibile eseguire l'applicazione .NET Azure HDInsight con l'identità specifica dell'applicazione (non interattiva) o con l'identità dell'utente che ha eseguito l'accesso all'applicazione (interattiva). Per un esempio dell'applicazione interattiva, vedere [Connettersi ad Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). Questo articolo descrive come creare un'applicazione .NET di autenticazione non interattiva per connettersi ad Azure e gestire HDInsight.

Dall'applicazione .NET non interattiva, è necessario disporre degli elementi seguenti:

* ID tenant della sottoscrizione di Azure, ovvero l'ID di directory. Vedere [Ottenere l'ID tenant](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* ID client dell'applicazione di Azure Active Directory. Vedere [Creare un'applicazione Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) e [Ottenere un ID applicazione](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)
* Chiave privata dell'applicazione di Azure Active Directory. Vedere [Ottenere la chiave di autenticazione dell'applicazione](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)

## <a name="prerequisites"></a>Prerequisiti
* Cluster HDInsight. Vedere [Esercitazione introduttiva](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).



## <a name="assign-azure-ad-application-to-role"></a>Assegnare l'applicazione Azure AD a un ruolo
È necessario assegnare l'applicazione a un [ruolo](../active-directory/role-based-access-built-in-roles.md) per concederle autorizzazioni per l'esecuzione di azioni. È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione è in grado di leggere il gruppo di risorse e le risorse in esso contenute. In questa esercitazione verrà impostato l'ambito a livello di gruppo di risorse. Per altre informazioni, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../active-directory/role-based-access-control-configure.md)

**Per aggiungere il ruolo Proprietario all'applicazione Azure AD**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Gruppo di risorse** nel pannello di sinistra.
3. Fare clic sul gruppo di risorse contenente il cluster HDInsight in cui verrà eseguita la query Hive più avanti in questa esercitazione. Se è presente un numero eccessivo di gruppi di risorse, è possibile usare il filtro.
4. Scegliere **Controllo di accesso (IAM)** dal menu del gruppo di risorse.
5. Fare clic su **Aggiungi** nel pannello **Utenti**.
6. Seguire le istruzioni per aggiungere il ruolo **Proprietario** all'applicazione Azure AD creata nella procedura precedente. Al termine, l'applicazione verrà visualizzata nel pannello Utenti con il ruolo Proprietario.

## <a name="develop-hdinsight-client-application"></a>Sviluppare l'applicazione client HDInsight

1. Creare un'applicazione console C#.
2. Aggiungere i pacchetti NuGet seguenti:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Usare il codice di esempio seguente:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>Passaggi successivi
* [Creare un'applicazione e un'entità servizio di Azure Active Directory tramite il portale](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Autenticazione di un'entità servizio con Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md)

