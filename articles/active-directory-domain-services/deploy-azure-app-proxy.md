---
title: Distribuire Azure AD proxy di applicazione per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come fornire l'accesso sicuro alle applicazioni interne per i ruoli di lavoro remoti tramite la distribuzione e la configurazione di Azure Active Directory Application Proxy in un dominio gestito Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c0fcb8c2c5f9afa7fabe2ffa63a715ec24aa4a26
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720481"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Distribuire Azure AD proxy di applicazione per l'accesso sicuro alle applicazioni interne in un dominio gestito Azure AD Domain Services

Con Azure AD Domain Services (Azure AD DS) è possibile spostare in Azure le applicazioni legacy in esecuzione in locale. Il proxy di applicazione di Azure Active Directory (AD) consente quindi di supportare i ruoli di lavoro remoti pubblicando in modo sicuro le applicazioni interne parte di un dominio gestito Azure AD DS, in modo che sia possibile accedervi tramite Internet.

Se non si ha familiarità con il proxy dell'applicazione Azure AD e si desidera ottenere ulteriori informazioni, vedere [come fornire l'accesso remoto sicuro alle applicazioni interne](../active-directory/manage-apps/application-proxy.md).

Questo articolo illustra come creare e configurare un connettore del proxy di applicazione Azure AD per fornire un accesso sicuro alle applicazioni in un dominio gestito Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
    * Per usare il proxy di applicazione Azure AD, è necessaria una **licenza di Azure ad Premium** .
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Creare una macchina virtuale Windows aggiunta a un dominio

Per instradare il traffico alle applicazioni in esecuzione nell'ambiente, installare il componente connettore del proxy di applicazione Azure AD. Questo Azure AD connettore del proxy di applicazione deve essere installato in macchine virtuali (VM) Windows Server appartenenti al dominio gestito Azure AD DS. Per alcune applicazioni, è possibile distribuire più server in cui è installato il connettore. Questa opzione di distribuzione offre maggiore disponibilità e permette di gestire carichi di lavoro di autenticazione più gravosi.

La VM che esegue il connettore del proxy di applicazione Azure AD deve trovarsi nella stessa rete virtuale o in una rete virtuale con peering in cui è stato abilitato Azure AD DS. Anche le macchine virtuali che ospitano le applicazioni pubblicate con il proxy di applicazione devono essere distribuite nella stessa rete virtuale di Azure.

Per creare una macchina virtuale per il connettore del proxy di applicazione Azure AD, completare i passaggi seguenti:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa OU personalizzata agli utenti all'interno del dominio gestito Azure AD DS. Le macchine virtuali per Azure AD proxy di applicazione e che eseguono le applicazioni devono far parte dell'unità organizzativa personalizzata, non dell'unità organizzativa dei *computer DC AAD* predefinita.
1. [Aggiungere un dominio alle macchine virtuali][create-join-windows-vm], sia quello che esegue il Azure ad connettore del proxy di applicazione, che quelli che eseguono le applicazioni, nel dominio gestito Azure AD DS. Creare questi account computer nell'unità organizzativa personalizzata nel passaggio precedente.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Scaricare il connettore del proxy di applicazione Azure AD

Per scaricare il connettore del proxy di applicazione Azure AD, seguire questa procedura. Il file di installazione scaricato viene copiato nella macchina virtuale del proxy dell'applicazione nella sezione successiva.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account utente con autorizzazioni di *amministratore dell'organizzazione* nell'Azure ad.
1. Cercare e selezionare **Azure Active Directory** nella parte superiore del portale, quindi scegliere **applicazioni aziendali**.
1. Selezionare **proxy di applicazione** dal menu sul lato sinistro. Per creare il primo connettore e abilitare il proxy applicazione, selezionare il collegamento per **scaricare un connettore**.
1. Nella pagina di download, accettare le condizioni di licenza e l'accordo sulla privacy, quindi selezionare **accetta termini & download**.

    ![Scaricare il connettore del proxy di App Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Installare e registrare il connettore del proxy di applicazione Azure AD

Con una macchina virtuale pronta per l'uso come connettore del proxy di applicazione Azure AD, ora copiare ed eseguire il file di installazione scaricato dal portale di Azure.

1. Copiare il file di installazione del connettore del proxy di applicazione Azure AD nella macchina virtuale.
1. Eseguire il file di installazione, ad esempio *AADApplicationProxyConnectorInstaller. exe*. Accettare le condizioni di licenza software.
1. Durante l'installazione, viene richiesto di registrare il connettore con il proxy dell'applicazione nella directory Azure AD.
   * Fornire le credenziali per un amministratore globale nella directory Azure AD. Le credenziali di amministratore globale Azure AD possono essere diverse dalle credenziali di Azure nel portale

        > [!NOTE]
        > L'account amministratore globale usato per registrare il connettore deve appartenere alla stessa directory in cui si Abilita il servizio proxy di applicazione.
        >
        > Se ad esempio il dominio Azure AD è *contoso.com*, l'amministratore globale deve essere `admin@contoso.com` o un altro alias valido in tale dominio.

   * Se è attivata la configurazione sicurezza avanzata di Internet Explorer per la macchina virtuale in cui si installa il connettore, la schermata di registrazione potrebbe essere bloccata. Per consentire l'accesso, seguire le istruzioni nel messaggio di errore o disattivare sicurezza avanzata di Internet Explorer durante il processo di installazione.
   * Se la registrazione del connettore non riesce, vedere [risolvere i problemi del proxy dell'applicazione](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. Al termine dell'installazione, viene visualizzata una nota per gli ambienti con un proxy in uscita. Per configurare il connettore del proxy di applicazione Azure AD per il funzionamento tramite il proxy in uscita, eseguire lo script specificato, ad esempio `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`.
1. Nella pagina proxy di applicazione del portale di Azure il nuovo connettore è elencato con lo stato *attivo*, come illustrato nell'esempio seguente:

    ![Il nuovo connettore del proxy di applicazione Azure AD visualizzato come attivo nella portale di Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Per garantire un'elevata disponibilità per le applicazioni che eseguono l'autenticazione tramite il proxy di applicazione Azure AD, è possibile installare i connettori in più macchine virtuali. Ripetere gli stessi passaggi elencati nella sezione precedente per installare il connettore in altri server aggiunti al dominio gestito di Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Abilitare la delega vincolata Kerberos basata sulle risorse

Se si desidera utilizzare Single Sign-On per le applicazioni che utilizzano l'autenticazione integrata di Windows (IWA), concedere al Azure AD le autorizzazioni dei connettori proxy applicazione per rappresentare gli utenti e inviare e ricevere token per loro conto. Per concedere queste autorizzazioni, configurare la delega vincolata Kerberos (delega vincolata Kerberos) per il connettore per accedere alle risorse nel dominio gestito Azure AD DS. Poiché non si dispone dei privilegi di amministratore di dominio in un dominio gestito Azure AD DS, non è possibile configurare delega vincolata Kerberos a livello di account tradizionali in un dominio gestito. Usare invece delega vincolata Kerberos basati sulle risorse.

Per ulteriori informazioni, vedere [configurare la delega vincolata Kerberos (delega vincolata Kerberos) in Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Per eseguire i cmdlet di PowerShell riportati di seguito, è necessario avere eseguito l'accesso a un account utente membro del gruppo *amministratori Azure ad controller* di dominio nel tenant del Azure ad.
>
> Gli account computer per le VM del connettore del proxy dell'applicazione e le macchine virtuali dell'applicazione devono trovarsi in un'unità organizzativa personalizzata in cui si hanno le autorizzazioni per configurare delega vincolata Kerberos basati sulle risorse. Non è possibile configurare delega vincolata Kerberos basato su risorse per un account computer nel contenitore dei *computer DC di AAD* incorporato.

Usare [Get-ADComputer][Get-ADComputer] per recuperare le impostazioni per il computer in cui è installato il connettore del proxy di applicazione Azure ad. Eseguire i cmdlet seguenti dalla macchina virtuale di gestione aggiunta al dominio e connessi come account utente membro del gruppo di *amministratori di Azure ad DC* .

Nell'esempio seguente vengono ottenute informazioni sull'account computer denominato *appproxy.contoso.com*. Specificare il nome computer per la macchina virtuale del proxy di applicazione Azure AD configurata nei passaggi precedenti.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.contoso.com
```

Per ogni server applicazioni che esegue le app dietro Azure AD proxy di applicazione usare il cmdlet di PowerShell [set-ADComputer][Set-ADComputer] per configurare delega vincolata Kerberos basato su risorse. Nell'esempio seguente, al connettore del proxy di applicazione Azure AD vengono concesse le autorizzazioni per l'uso del computer *AppServer.contoso.com* :

```powershell
Set-ADComputer appserver.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Se si distribuiscono più connettori proxy di applicazione Azure AD, è necessario configurare delega vincolata Kerberos basati sulle risorse per ogni istanza del connettore.

## <a name="next-steps"></a>Passaggi successivi

Con il Azure AD proxy di applicazione integrato con Azure AD DS, pubblicare le applicazioni per consentire agli utenti di accedere a. Per altre informazioni, vedere [pubblicare applicazioni con Azure ad proxy di applicazione](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
