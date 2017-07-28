---
title: Abilitare Desktop remoto in un servizio cloud di Azure | Documentazione Microsoft
description: Come configurare l'applicazione del servizio cloud di Azure per consentire le connessioni Desktop remoto
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: f64c41733f8fa7e34a0b0dfbbff2b565af7cf7db
ms.contentlocale: it-it
ms.lasthandoff: 03/25/2017

---

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Impostare una connessione Desktop remoto per un ruolo nei servizi cloud di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Portale di Azure classico](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

È possibile abilitare una connessione Desktop remoto nel ruolo durante lo sviluppo includendo i moduli di Desktop remoto nella definizione del servizio o è possibile scegliere di abilitare Desktop remoto tramite la relativa estensione. L'approccio migliore consiste nell'usare l'estensione di Desktop remoto, in quanto è possibile abilitare Desktop remoto anche dopo che l'applicazione viene distribuita senza doverla ridistribuire.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurare Remote Desktop dal portale di Azure classico
Il portale di Azure classico usa l'approccio dell'estensione di Desktop remoto per poter abilitare Desktop remoto anche dopo la distribuzione dell'applicazione. La pagina **Configura** per il servizio cloud consente di abilitare Desktop remoto, modificare l'account amministratore locale usato per connettersi alle macchine virtuali e il certificato usato nell'autenticazione e impostare la data di scadenza.

1. Fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.
2. Fare clic sul pulsante **Remoto** nella parte inferiore.

    ![Servizi cloud remoti](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > Tutte le istanze del ruolo verranno riavviate la prima volta che si abilita Desktop remoto e si fa clic su OK (segno di spunta). Per evitare un riavvio, è necessario che nel ruolo sia installato il certificato usato per crittografare la password. Per evitare un riavvio, [caricare un certificato per il servizio cloud](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) e quindi tornare alla finestra di dialogo.

3. In **Ruolo** selezionare il ruolo da aggiornare oppure selezionare **Tutti** per tutti i ruoli.
4. Apportare una delle modifiche seguenti:

   * Per abilitare Desktop remoto, selezionare la casella di controllo **Enable Remote Desktop** . Per disabilitare Desktop remoto, deselezionare la casella di controllo.
   * Creare un account da usare nelle connessioni Desktop remoto alle istanze del ruolo.
   * Aggiornare la password dell'account esistente.
   * Selezionare un certificato caricato da usare per l'autenticazione (caricare il certificato scegliendo **Carica** nella pagina **Certificati**) oppure creare un nuovo certificato.
   * Cambiare la data di scadenza per la configurazione di Desktop remoto.

5. Dopo aver completato gli aggiornamenti della configurazione, fare clic su **OK** (segno di spunta).

## <a name="remote-into-role-instances"></a>Accedere in remoto alle istanze del ruolo
Dopo l'abilitazione di Desktop remoto per i ruoli è possibile accedere in remoto a un'istanza del ruolo tramite diversi strumenti.

Per connettersi a un'istanza del ruolo dal portale di Azure classico:

1. Fare clic su **Istanze** per aprire la pagina **Istanze**.
2. Selezionare un'istanza del ruolo per cui è configurato Desktop remoto.
3. Fare clic su **Connetti**e seguire le istruzioni visualizzate per aprire il desktop.
4. Fare clic su **Apri** e quindi su **Connetti** per avviare la connessione Desktop remoto.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Usare Visual Studio per collegarsi in remoto a un'istanza del ruolo
In Esplora server di Visual Studio:

1. Espandere il nodo **Azure** > **Servizi cloud** > **[nome servizio cloud]**.
2. Espandere **Gestione temporanea** o **Produzione**.
3. Espandere il singolo ruolo.
4. Fare clic con il pulsante destro del mouse su una delle istanze del ruolo, fare clic su **Connessione tramite desktop remoto**e quindi immettere il nome utente e la password.

![Desktop remoto di Esplora server](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Usare PowerShell per ottenere il file RDP
Per recuperare il file RDP è possibile usare il cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) . Sarà quindi possibile usare il file RDP con Connessione Desktop remoto per accedere al servizio cloud.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Scaricare il file RDP a livello di codice tramite l'API REST di gestione dei servizi
Per scaricare il file RDP, è possibile usare l'operazione REST [Scarica file RDP](https://msdn.microsoft.com/library/jj157183.aspx) .

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Per configurare Desktop remoto nel file di definizione del servizio
Questo metodo consente di abilitare Desktop remoto per l'applicazione durante lo sviluppo. Questo approccio richiede l'archiviazione delle password crittografate nel file cscfg ed eventuali aggiornamenti della configurazione di Desktop remoto richiederebbero una ridistribuzione dell'applicazione. Se si desidera evitare questi svantaggi, usare l'approccio basato sull'estensione di Desktop remoto descritto in precedenza.  

È possibile usare Visual Studio per [abilitare una connessione Desktop remoto](../vs-azure-tools-remote-desktop-roles.md) usando l'approccio del file di definizione del servizio.  
I passaggi seguenti descrivono le modifiche necessarie da apportare ai file del modello del servizio per abilitare Desktop remoto. Visual Studio eseguirà automaticamente queste modifiche durante la pubblicazione.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurazione della connessione nel modello del servizio
Usare l'elemento **Imports** per importare i moduli **RemoteAccess** e **RemoteForwarder** nel file [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef).

Il file di definizione del servizio dovrebbe essere simile all'esempio seguente con l'elemento `<Imports>` aggiunto.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Il file [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) dovrebbe essere simile all'esempio seguente. Si notino gli elementi `<ConfigurationSettings>` e `<Certificates>`. Il certificato specificato deve essere [caricato nel servizio cloud](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Risorse aggiuntive
[Come configurare i servizi cloud](cloud-services-how-to-configure.md)
[Domande frequenti sui servizi cloud - Desktop remoto](cloud-services-faq.md#remote-desktop)

