<properties 
pageTitle="Foglio informativo su XPath per la configurazione del ruolo di Servizi cloud | Microsoft Azure" 
description="Varie impostazioni di XPath che è possibile usare nella configurazione del ruolo del servizio cloud per esporre le impostazioni come una variabile di ambiente." 
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
ms.date="08/10/2016" 
ms.author="adegeo"/>  

# Esporre le impostazioni di configurazione del ruolo come una variabile di ambiente con XPath

Nel file di definizione del servzio del ruolo di lavoro o del ruolo Web del servizio cloud è possibile esporre i valori di configurazione di runtime come variabili di ambiente. Sono supportati i valori XPath seguenti, che corrispondono ai valori di API.

Questi valori XPath sono disponibili anche tramite la libreria [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx).

## App in esecuzione nell'emulatore

Indica che l'app è in esecuzione nell'emulatore.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Codice | var x = RoleEnvironment.IsEmulated; |


## ID distribuzione

Recupera l'ID distribuzione per l'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Codice | var deploymentId = RoleEnvironment.DeploymentId; |


## ID ruolo 

Recupera l'ID del ruolo corrente per l'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Codice | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## Aggiornamento dominio

Recupera il dominio di aggiornamento dell'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Codice | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## Dominio di errore

Recupera il dominio di errore dell'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Codice | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## Nome del ruolo

Recupera il nome del ruolo dell'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Codice | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |


## Impostazione di configurazione

Recupera il valore dell'impostazione di configurazione specificata.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Codice | var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## Percorso di archiviazione locale

Recupera il percorso di archiviazione locale per l'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Codice | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |


## Dimensioni di archiviazione locale

Recupera le dimensioni di archiviazione locale per l'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Codice | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## Protocollo di endpoint 

Recupera il protocollo di endpoint per l'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Codice | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## IP dell'endpoint

Ottiene l'indirizzo IP dell'endpoint specificato.

| Tipo | Esempio |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Codice | var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## Porta dell'endpoint 

Recupera la porta dell'endpoint per l'istanza.

| Tipo | Esempio |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Codice | var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |





## Esempio

Ecco un esempio di un ruolo di lavoro che crea un'attività di avvio con una variabile di ambiente denominata `TestIsEmulated` impostata sul [valore xpath @emulated](#app-running-in-emulator).

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## Passaggi successivi

Altre informazioni sul file [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).

Creare un pacchetto [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg).

Abilitare [Desktop remoto](cloud-services-role-enable-remote-desktop.md) per un ruolo.

<!---HONumber=AcomDC_0810_2016-->