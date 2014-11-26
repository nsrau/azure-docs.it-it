<properties linkid="manage-linux-howto-service-management-api" urlDisplayName="Service Management API" pageTitle="How to use the service management API for VMs - Azure" metaKeywords="" description="Learn how to use the Azure Service Management API for a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use the Service Management API" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Come utilizzare l'API di gestione del servizio

## Inizializzazione

Per richiamare l'API di gestione del servizio IaaS di Azure da NodeJS, si usa il modulo `azure`.

    var azure = require('azure');

Creare innanzitutto un'istanza dell'oggetto ServiceManagementService. Tutte le chiamate all'API utilizzeranno questo oggetto. L'ID sottoscrizione, le credenziali e le altre opzioni di connessione vengono stabilite in questa fase. Per gestire più sottoscrizioni creare più oggetti ServiceManagementService.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid è una stringa obbligatoria e dovrebbe corrispondere all'ID sottoscrizione dell'account a cui si esegue l'accesso.
-   Auth è un oggetto facoltativo che consente di specificare la chiave privata e il certificato pubblico da utilizzare con questo account.

    -   keyfile: percorso del file PEM con chiave privata. Ignorato se si specifica keyvalue.
    -   keyvalue : valore effettivo della chiave privata così come archiviata in un file PEM.
    -   certfile: percorso del file PEM con certificato pubblico. Ignorato se si specifica certvalue.
    -   certvalue : valore effettivo del certificato pubblico così come archiviato in un file PEM.
    -   Se non si specificano i valori riportati sopra, verranno letti e usati i valori variabili `CLIENT_AUTH_KEYFILE` e `CLIENT_AUTH_CERTFILE`. Se questi ultimi non sono stati impostati, verrà effettuato un tentativo di leggere e utilizzare i valori predefiniti dei file: priv.pem e pub.pem.
    -   Se non fosse possibile caricare la chiave privata e il certificato pubblico verrà generato un errore.
-   Options è un oggetto facoltativo che può essere utilizzato per controllare le proprietà utilizzate dal client.

    -   host: nome host del server di gestione Azure. Se non specificato, verrà utilizzato l'host predefinito.
    -   apiversion: stringa di versione da utilizzare nell'intestazione HTTP. Se non specificata, verrà utilizzata la versione predefinita.
    -   serializetype: probabilmente XML o JSON. Se non specificato, verrà utilizzata la serializzazione predefinita.

Facoltativamente, è possibile utilizzare un proxy di tunneling per abilitare la il passaggio di una richiesta HTTPS attraverso un proxy. Quando si crea IaasClient verrà elaborata la variabile di ambiente `HTTPS_PROXY`. Se è impostata su un URL valido, il nome host e la porta verranno analizzati dall'URL e utilizzati nelle successive richieste di identificazione del proxy.

        iaasClient.SetProxyUrl(proxyurl)

È possibile chiamare SetProxyUrl per impostare esplicitamente l'host e la porta del proxy. Ciò avrà lo stesso effetto dell'impostare la variabile di ambiente `HTTPS_PROXY` ed eseguirà l'override dell'impostazione dell'ambiente.

## Callback

Tutte le API sono caratterizzate da un argomento di callback richiesto. Il completamento della richiesta viene segnalato dalla chiamata della funzione passata al callback.

    callback(rsp)

-   Il callback ha un unico parametro, che è l'oggetto response.
-   isSuccessful: true o false
-   statusCode: codice di stato HTTP dell'oggetto response
-   response: la risposta analizzata in un oggetto JavaScript. Impostare se isSuccessful è true.
-   error: un oggetto JavaScript che contiene informazioni sull'errore impostato se isSuccessful è false.
-   body: il corpo effettivo della risposta come stringa
-   headers: le effettive intestazioni HTTP della risposta
-   reqopts: le opzioni di richiesta HTTP del nodo utilizzare per inviare la richiesta.

Si noti che in alcuni casi il completamento potrebbe indicare solo che la richiesta è stata accettata. In questo caso, utilizzare **GetOperationStatus** per ottenere lo stato finale.

## API

**iaasClient.GetOperationStatus(requested, callback)**

-   Molte chiamate di gestione vengono restituite prima che l'operazione sia completata. Queste restituiscono il valore 202 - Accettato e inseriscono requested nell'intestazione HTTP ms-request-id. Per eseguire il polling per il completamento della richiesta, utilizzare questa API e passare il valore requested.
-   La stringa callback è necessaria.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename è un nome stringa richiesto dell'immagine.
-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà le proprietà dell'immagine denominata.

**iaasClient.ListOSImages(callback)**

-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà una matrice di oggetti immagine.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName è un nome stringa richiesto dell'immagine.
-   mediaLink è un nome stringa richiesto del mediaLink da utilizzare.
-   imageOptions è un oggetto facoltativo e può contenere le proprietà seguenti:

    -   Categoria
    -   Label: se non impostato, il valore predefinito è imageName.
    -   Percorso
    -   RoleSize
-   La stringa callback è necessaria. Se imageOptions non è impostato, questo potrebbe essere il terzo parametro.
-   Se corretto, l'oggetto response conterrà le proprietà dell'immagine creata.

**iaasClient.ListHostedServices(callback)**

-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà una matrice di oggetti servizio ospitato.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà le proprietà del servizio ospitato.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   serviceOptions è un oggetto facoltativo e può contenere le proprietà seguenti:

    -   Description: il valore predefinito è "Service host"
    -   Label: se non impostato, il valore predefinito è serviceName
    -   Location: l'area in cui creare il servizio
-   La stringa callback è necessaria.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà le chiavi di accesso alle risorse di archiviazione.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà le proprietà della distribuzione denominata.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentSlot è un nome stringa richiesto dello slot (in fase di gestione temporanea o di distribuzione).
-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà le proprietà delle distribuzioni nello slot.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   VMRole è un oggetto richiesto per cui è necessario creare proprietà del ruolo per la distribuzione.
-   deployOptions è un oggetto facoltativo e può contenere le proprietà seguenti:

    -   DeploymentSlot: il valore predefinito è "Production"
    -   Label: se non impostato, il valore predefinito è deploymentName.
    -   UpgradeDomainCount: nessun valore predefinito
-   La stringa callback è necessaria.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleName è un nome stringa richiesto del ruolo.
-   La stringa callback è necessaria.
-   Se corretto, l'oggetto response conterrà le proprietà del ruolo denominato.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   VMRole è un oggetto richiesto per cui è necessario aggiungere proprietà del ruolo alla distribuzione.
-   La stringa callback è necessaria.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleName è un nome stringa richiesto del ruolo.
-   VMRole è un oggetto richiesto le cui proprietà devono essere modificate nel ruolo.
-   La stringa callback è necessaria.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleName è un nome stringa richiesto del ruolo.
-   La stringa callback è necessaria.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleName è un nome stringa richiesto del ruolo.
-   Datadisk è un oggetto richiesto, utilizzato per specificare quando verrà creato il disco dati.
-   La stringa callback è necessaria.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleName è un nome stringa richiesto del ruolo.
-   LUN è il numero che identifica il disco dati
-   Datadisk è un oggetto richiesto, utilizzato per specificare la modalità di modifica del disco dati.
-   La stringa callback è necessaria.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleName è un nome stringa richiesto del ruolo.
-   LUN è il numero che identifica il disco dati
-   La stringa callback è necessaria.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleInstance è un nome stringa richiesto dell'istanza del ruolo.
-   La stringa callback è necessaria.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleInstance è un nome stringa richiesto dell'istanza del ruolo.
-   La stringa callback è necessaria.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName è un nome stringa richiesto del servizio ospitato.
-   deploymentName è un nome stringa richiesto della distribuzione.
-   roleInstance è un nome stringa richiesto dell'istanza del ruolo.
-   captOptions è un oggetto richiesto che definisce le azioni di acquisizione

    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   La stringa callback è necessaria.

## Oggetti dati

Le API considerano gli oggetti come input durante la creazione o la modifica di una distribuzione, un ruolo o un disco. Altre API restituiranno oggetti simili in un'operazione Get o List.
Questa sezione illustra a grandi linee le proprietà dell'oggetto.
Distribuzione

-   Name: stringa
-   DeploymentSlot - "Staging" o "Production"
-   Status: stringa, solo output
-   PrivateID: stringa, solo output
-   Label: stringa
-   UpgradeDomainCount: numero
-   SdkVersion: stringa
-   Locked: true o false
-   RollbackAllowed: true o false
-   RoleInstance: oggetto, solo output
-   Role: oggetto VMRole
-   InputEndpointList: matrice di InputEndpoint

**VMRole**

-   RoleName: stringa. Richiesto per create.
-   RoleSize: stringa. Facoltativo per create.
-   RoleType: stringa. Se non altrimenti specificato in create, il valore predefinito è "PersistentVMRole".
-   OSDisk: oggetto. Richiesto per create
-   DataDisks: matrice di oggetti. Facoltativo per create.
-   ConfigurationSets: matrice di oggetti Configuration.

**RoleInstance**

-   RoleName: stringa
-   InstanceName: stringa
-   InstanceStatus: stringa
-   InstanceUpgradeDomain: numero
-   InstanceFaultDomain: numero
-   InstanceSize: stringa
-   IpAddress: stringa

**OSDisk**

-   SourceImageName: stringa. Richiesto per create
-   DisableWriteCache: true o false
-   DiskName: stringa
-   MediaLink: stringa

**DataDisk**

-   DisableReadCache: true o false
-   EnableWriteCache: true o false
-   DiskName : stringa
-   MediaLink: stringa
-   LUN: numero (0-15)
-   LogicalDiskSizeInGB: numero
-   SourceMediaLink: stringa
-   Durante la creazione è possibile specificare il disco in tre modi: per nome, per supporto o per dimensioni. Le opzioni specificate ne determineranno il funzionamento. Per ulteriori informazioni vedere la documentazione dell'API RDFE.

**ProvisioningConfiguration**

-   ConfigurationSetType: 'ProvisioningConfiguration'
-   AdminPassword: stringa
-   MachineName: stringa
-   ResetPasswordOnFirstLogon: true o false

**NetworkConfiguration**

-   ConfigurationSetType : 'NetworkConfiguration'
-   InputEndpoints : matrice di ExternalEndpoints

**InputEndpoint**

-   RoleName
-   Vip
-   Port

**ExternalEndpoint**

-   LocalPort
-   Nome
-   Port
-   Protocol

## Codice di esempio

Di seguito è riportato codice JavaScript di esempio che consente di creare un servizio ospitato e una distribuzione, quindi di eseguire il polling per lo stato di completamento della distribuzione.

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
