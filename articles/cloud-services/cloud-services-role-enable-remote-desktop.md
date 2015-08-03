<properties 
pageTitle="Impostare una connessione Desktop remoto per un ruolo nei servizi cloud di Azure" 
description="Come configurare l'applicazione del servizio cloud di Azure per consentire le connessioni Desktop remoto" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Impostare una connessione Desktop remoto per un ruolo in Azure
Dopo avere creato un servizio cloud che esegue l'applicazione, è possibile accedere in remoto a un'istanza del ruolo in tale applicazione per configurare le impostazioni o risolvere i problemi. È necessario che il servizio cloud sia stato configurato per Desktop remoto.

* È necessario un certificato per abilitare l'autenticazione di Desktop remoto? [Crearne](cloud-services-certs-create.md) uno e configurarlo.
* Desktop remoto è già stato configurato per il servizio cloud? [Connettersi](#remote-into-role-instances) al servizio cloud.

## Configurare una connessione Desktop remoto per i ruoli Web o i ruoli di lavoro
Per abilitare una connessione Desktop remoto per un ruolo Web o un ruolo di lavoro, è possibile impostare la connessione nel modello del servizio per l'applicazione oppure usare il portale di gestione di Microsoft Azure per impostare la connessione dopo che le istanze sono in esecuzione.

### Configurazione della connessione nel modello del servizio
L'elemento **Imports** deve essere aggiunto al file di definizione del servizio che importa il modulo **RemoteAccess** e il modulo **RemoteForwarder** nel modello del servizio. Quando si crea un progetto di Azure mediante Visual Studio, i file per il modello del servizio vengono creati automaticamente.

Il modello del servizio è costituito da un file [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) e un file [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg). Il file di definizione viene incluso nel pacchetto dei file binari del ruolo quando il servizio cloud viene preparato per la distribuzione. Il file ServiceConfiguration.cscfg viene distribuito con il pacchetto dell'applicazione e usato da Azure per determinare la modalità di esecuzione dell'applicazione.

Dopo avere creato il progetto, è possibile usare Visual Studio per [abilitare una connessione Desktop remoto](https://msdn.microsoft.com/library/gg443832.aspx).

Dopo che la connessione è stata configurata, il file di definizione del servizio dovrebbe essere simile al seguente esempio con l'elemento `<Imports>` aggiunto.

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

Il file di configurazione del servizio dovrebbe essere simile al seguente esempio con i valori specificati al momento dell'impostazione della connessione. Si notino gli elementi `<ConfigurationSettings>` e `<Certificates>`:

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

Quando si [crea il pacchetto](cloud-services-model-and-package.md#cspkg) dell'applicazione e la si [pubblica](cloud-services-how-to-create-deploy-portal.md), è necessario assicurarsi che la casella di controllo **Abilita Desktop remoto per tutti i ruoli** sia selezionata. [Questo](https://msdn.microsoft.com/library/ff683672.aspx) articolo elenca le attività comuni relative all'uso di Visual Studio e dei servizi cloud.

### Configurare la connessione nelle istanze in esecuzione
Nella pagina di configurazione del servizio cloud è possibile abilitare o modificare le impostazioni della connessione Desktop remoto. Per altre informazioni, vedere [Configurare l'accesso remoto a istanze del ruolo](cloud-services-how-to-configure.md).




## Accedere in remoto alle istanze del ruolo
Per accedere a istanze di ruoli Web o di ruoli di lavoro, è necessario usare un file RDP (Remote Desktop Protocol). È possibile scaricare il file dal portale di gestione o recuperarlo a livello di codice.

### Scaricare il file RDP tramite il portale di gestione

È possibile usare i passaggi seguenti per recuperare il file RDP dal portale di gestione e quindi usare Connessione Desktop remoto per connettersi all'istanza usando il file:

1.  Nella pagina Istanze selezionare l'istanza e quindi fare clic su **Connetti** sulla barra dei comandi.
2.  Fare clic su **Salva** per salvare il file RDP nel computer locale.
3.  Aprire **Connessione Desktop remoto**, fare clic su **Mostra opzioni** e quindi su **Apri**.
4.  Passare al percorso in cui è stato salvato il file RDP, selezionare il file, fare clic su **Apri** e quindi su **Connetti**. Seguire le istruzioni per completare la connessione.

### Usare PowerShell per ottenere il file RDP
Per recuperare il file RDP, è possibile usare il cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx).

### Usare Visual Studio per scaricare il file RDP
In Visual Studio è possibile usare Esplora server per creare una connessione Desktop remoto.

1.  In Esplora server espandere il nodo **Azure\Cloud Services\[nome servizio cloud]**.
2.  Espandere **Gestione temporanea** o **Produzione**.
3.  Espandere il singolo ruolo.
4.  Fare clic con il pulsante destro del mouse su una delle istanze del ruolo, scegliere **Connetti mediante Desktop remoto** e quindi immettere il nome utente e la password.

### Scaricare il file RDP a livello di codice tramite l'API REST di gestione dei servizi
È possibile usare l'operazione REST [Scarica file RDP](https://msdn.microsoft.com/library/jj157183.aspx) per scaricare il file RDP. Sarà quindi possibile usare il file RDP con Connessione Desktop remoto per accedere al servizio cloud.

## Passaggi successivi
Potrebbe essere necessario [creare il pacchetto](cloud-services-model-and-package.md) dell'applicazione oppure [caricarla (distribuirla)](cloud-services-how-to-create-deploy-portal.md).

<!---HONumber=July15_HO4-->