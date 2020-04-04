---
title: Distribuzione del proxy di applicazione di Azure AD per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come fornire l'accesso sicuro alle applicazioni interne per i lavoratori remoti distribuendo e configurando il proxy di applicazione di Azure Active Directory in un dominio gestito di Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: c1dc5216f758c2dda263e2f61b043dbde5f76604
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655495"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Distribuire il proxy di applicazione di Azure AD per l'accesso sicuro alle applicazioni interne in un dominio gestito di Servizi di dominio Azure ADDeploy Azure AD Application Proxy for secure access to internal applications in an Azure AD Domain Services managed domain

Con Servizi di dominio Azure AD è possibile integrare e spostare le applicazioni legacy in esecuzione in locale in Azure.With Azure AD Domain Services (Azure AD DS), you can lift-and-shift legacy applications running on-premises into Azure. Il proxy di applicazione di Azure Active Directory (AD) consente quindi di supportare i lavoratori remoti pubblicando in modo sicuro le applicazioni interne che fanno parte di un dominio gestito di Servizi di dominio Active Directory di Azure in modo che sia possibile accedervi tramite Internet.Azure Active Directory (AD) Application Proxy (AD) then helps you support remote workers by securely publishing those internal applications part of an Azure AD DS managed domain so can be accessed over the internet.

Se non si ha familiarità con il proxy di applicazione di Azure AD e si desidera ottenere altre informazioni, vedere [Come fornire accesso remoto sicuro alle applicazioni interne.](../active-directory/manage-apps/application-proxy.md)

Questo articolo illustra come creare e configurare un connettore proxy di applicazione di Azure AD per fornire l'accesso sicuro alle applicazioni in un dominio gestito di Servizi di dominio Active Directory di Azure.This article shows you how to create and configure an Azure AD Application Proxy connector to provide secure access to applications in an Azure AD DS managed domain.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
    * Per usare il proxy di applicazione di Azure AD, è necessaria una **licenza di Azure AD Premium.An Azure AD Premium license** is required to use the Azure AD Application Proxy.
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Creare una macchina virtuale Windows aggiunta a un dominioCreate a domain-joined Windows VM

Per instradare il traffico alle applicazioni in esecuzione nell'ambiente, installare il componente connettore proxy di applicazione di Azure AD. Questo connettore proxy di applicazione di Azure AD deve essere installato nelle macchine virtuali (VM) di Windows Server aggiunte al dominio gestito di Servizi di dominio Active Directory di Azure.This Azure AD Application Proxy connector must be installed on Windows Server virtual machines (VM) that's joined to the Azure AD DS managed domain. Per alcune applicazioni, è possibile distribuire più server in cui è installato il connettore. Questa opzione di distribuzione offre maggiore disponibilità e permette di gestire carichi di lavoro di autenticazione più gravosi.

The VM that runs the Azure AD Application Proxy connector must be on the same, or a peered, virtual network in which you have enabled Azure AD DS. Le macchine virtuali che ospitano le applicazioni pubblicate usando il proxy di applicazione devono essere distribuite anche nella stessa rete virtuale di Azure.The VMs that then host the applications you publish using the Application Proxy must also be deployed on the same Azure virtual network.

Per creare una macchina virtuale per il connettore del proxy di applicazione di Azure AD, completare i passaggi seguenti:To create a VM for the Azure AD Application Proxy connector, complete the following steps:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa unità organizzativa personalizzata agli utenti all'interno del dominio gestito di Servizi di dominio Active Directory di Azure.You can delegate permissions to manage this custom OU to users within the Azure AD DS managed domain. Le macchine virtuali per il proxy di applicazione di Azure AD e che eseguono le applicazioni devono far parte dell'unità organizzativa personalizzata, non dell'unità organizzativa *predefinita Computer controller di dominio AAD.*
1. [Aggiunta di un dominio alle macchine virtuali,][create-join-windows-vm]sia quella che esegue il connettore proxy di applicazione di Azure AD che quelle che eseguono le applicazioni, al dominio gestito di Servizi di dominio Active Directory di Azure. Creare questi account computer nell'unità organizzativa personalizzata del passaggio precedente.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Scaricare il connettore proxy di applicazione di Azure ADDownload the Azure AD Application Proxy connector

Eseguire la procedura seguente per scaricare il connettore del proxy di applicazione di Azure AD. Il file di installazione scaricato viene copiato nella macchina virtuale del proxy dell'app nella sezione successiva.

1. Accedere al [portale](https://portal.azure.com) di Azure con un account utente con autorizzazioni di *amministratore dell'organizzazione* in Azure AD.
1. Cercare e selezionare **Azure Active Directory** nella parte superiore del portale, quindi scegliere Applicazioni **aziendali**.
1. Selezionare **Proxy di applicazione** dal menu sul lato sinistro. Per creare il primo connettore e abilitare Proxy app, selezionare il collegamento per **scaricare un connettore.**
1. Nella pagina di download, accettare le condizioni di licenza e il contratto di privacy, quindi selezionare **Accetta termini & Scarica**.

    ![Scaricare il connettore proxy app di Azure ADDownload the Azure AD App Proxy connector](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Installare e registrare il connettore proxy di applicazione di Azure ADInstall and register the Azure AD Application Proxy connector

Con una macchina virtuale pronta per essere usata come connettore del proxy di applicazione di Azure AD, copiare ed eseguire il file di installazione scaricato dal portale di Azure.With a VM ready to be used as the Azure AD Application Proxy connector, now copy and run the setup file downloaded from the Azure portal.

1. Copiare il file di installazione del connettore proxy di applicazione di Azure AD nella macchina virtuale.
1. Eseguire il file di installazione, ad esempio *AADApplicationProxyConnectorInstaller.exe*. Accettare le condizioni di licenza software.
1. Durante l'installazione, viene richiesto di registrare il connettore con il proxy di applicazione nella directory di Azure AD.
   * Specificare le credenziali per un amministratore globale nella directory di Azure AD. Le credenziali di amministratore globale di Azure AD possono essere diverse dalle credenziali di Azure nel portaleThe Azure AD global administrator credentials may be different from your Azure credentials in the portal

        > [!NOTE]
        > L'account amministratore globale utilizzato per registrare il connettore deve appartenere alla stessa directory in cui si abilita il servizio proxy di applicazione.
        >
        > Ad esempio, se il *aaddscontoso.com*dominio di Azure AD `admin@aaddscontoso.com` è aaddscontoso.com , l'amministratore globale deve essere o un altro alias valido in tale dominio.

   * Se La Protezione avanzata di Internet Explorer è attivata per la macchina virtuale in cui si installa il connettore, la schermata di registrazione potrebbe essere bloccata. Per consentire l'accesso, seguire le istruzioni nel messaggio di errore o disattivare Protezione avanzata di Internet Explorer durante il processo di installazione.
   * Se la registrazione del connettore non riesce, vedere [Risoluzione dei problemi relativi al proxy di applicazione](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. Al termine dell'installazione, viene visualizzata una nota per gli ambienti con un proxy in uscita. Per configurare il connettore del proxy di applicazione di Azure `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`AD in modo che funzioni tramite il proxy in uscita, eseguire lo script fornito, ad esempio .
1. Nella pagina Proxy applicazione nel portale di Azure il nuovo connettore è elencato con lo stato *Attivo,* come illustrato nell'esempio seguente:

    ![Il nuovo connettore proxy di applicazione di Azure AD visualizzato come attivo nel portale di AzureThe new Azure AD Application Proxy connector shown as active in the Azure portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Per fornire disponibilità elevata per le applicazioni che eseguono l'autenticazione tramite il proxy di applicazione di Azure AD, è possibile installare i connettori in più macchine virtuali. Ripetere gli stessi passaggi elencati nella sezione precedente per installare il connettore in altri server aggiunti al dominio gestito di Servizi di dominio Active Directory di Azure.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Abilitare la delega vincolata Kerberos basata sulle risorseEnable resource-based Kerberos constrained delegation

Se si vuole usare l'accesso Single Sign-On alle applicazioni usando l'autenticazione integrata di Windows (IWA), concedere ai connettori del proxy di applicazione di Azure AD l'autorizzazione per rappresentare gli utenti e inviare e ricevere token per loro conto. Per concedere queste autorizzazioni, configurare la delega vincolata Kerberos per il connettore per accedere alle risorse nel dominio gestito di Servizi di dominio Active Directory di Azure.To grant these permissions, you configure Kerberos constrained delegation (KCD) for the connector to access resources on the Azure AD DS managed domain. Poiché non si dispone dei privilegi di amministratore di dominio in un dominio gestito di Servizi di dominio Active Directory di Azure, non è possibile configurare KCD a livello di account tradizionale in un dominio gestito. Utilizzare invece KCD basato sulle risorse.

Per altre informazioni, vedere Configurare la [delega vincolata Kerberos (KCD) in Servizi](deploy-kcd.md)di dominio Azure Active Directory.For more information, see Configure Kerberos constrained delegation (KCD) in Azure Active Directory Domain Services .

> [!NOTE]
> Per eseguire i cmdlet di PowerShell seguenti, è necessario accedere a un account utente membro del gruppo *Amministratori controller di dominio* di Azure AD nel tenant di Azure AD.
>
> Gli account computer per la macchina virtuale e le macchine virtuali dell'applicazione del connettore proxy app devono trovarsi in un'unità organizzativa personalizzata in cui si dispone delle autorizzazioni per configurare il kCD basato sulle risorse. Non è possibile configurare KCD basato sulle risorse per un account computer nel contenitore predefinito *AAD DC Computers.*

Usare [Get-ADComputer][Get-ADComputer] per recuperare le impostazioni per il computer in cui è installato il connettore proxy di applicazione di Azure AD. Dalla macchina virtuale di gestione aggiunta al dominio ed è stato eseguito l'accesso come account utente membro del gruppo di amministratori del controller di dominio di *Azure AD,* eseguire i cmdlet seguenti.

Nell'esempio riportato di seguito vengono ottenute informazioni sull'account computer denominato *appproxy.aaddscontoso.com*. Specificare il proprio nome computer per la macchina virtuale del proxy di applicazione di Azure AD configurata nei passaggi precedenti.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Per ogni server applicazioni che esegue le app dietro il proxy di applicazione di Azure AD, usare il cmdlet [PowerShell Set-ADComputer][Set-ADComputer] per configurare KCD basato sulle risorse. Nell'esempio seguente al connettore del proxy di applicazione di Azure AD vengono concesse le autorizzazioni per l'uso del computer *appserver.aaddscontoso.com:In* the following example, the Azure AD Application Proxy connector is granted permissions to use the appserver.aaddscontoso.com computer:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Se si distribuiscono più connettori proxy di applicazione di Azure AD, è necessario configurare il kCD basato sulle risorse per ogni istanza del connettore.

## <a name="next-steps"></a>Passaggi successivi

Con il proxy di applicazione di Azure AD integrato con Azure AD DS, pubblicare le applicazioni a cui gli utenti possono accedere. Per altre informazioni, vedere [pubblicare applicazioni con il proxy di applicazione](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)di Azure AD.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
